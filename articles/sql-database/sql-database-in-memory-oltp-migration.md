<properties
	pageTitle="使用 In-Memory OLTP 來改善 Azure SQL 交易效能 | Microsoft Azure"
	description="採用 In-Memory OLTP 來改善現有 SQL Database 的交易效能。"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# 使用 In-Memory (預覽) 來改善 Azure SQL 應用程式的效能

請遵循下列步驟，使用 [In-Memory](sql-database-in-memory.md) 來最佳化現有 Premium Azure SQL Database 的交易效能。

如需比較，請選擇類似您的生產工作負載的工作負載，而且並行連接數目和讀/寫比率相似。為了減少網路延遲，建議您在與資料庫相同的 Azure 區域中執行測試工作負載。

## 步驟 1：將資料複製到新的 Premium 資料庫
1.	使用下列其中一項，將生產資料庫匯出至 bacpac：

	答：[入口網站](https://portal.azure.com/)中的 [匯出] 功能，或

	B.SQL Server Management Studio 中的 [匯出資料層應用程式] 功能

2.	將 bacpac 匯入到 V12 伺服器中新的 Premium 資料庫：

	答：在入口網站中：瀏覽至伺服器並選取 [匯入資料庫] 選項。務必選取 Premium 定價層。

	B.在 SQL Server Management Studio (SSMS) 中：連接到伺服器、以滑鼠右鍵按一下 [資料庫] 節點，並選取 [匯入資料層應用程式]。


## 步驟 2：識別要移轉至 In-Memory OLTP 的物件
SQL Server Management Studio (SSMS) 包含一份交易效能分析報表，該報表可針對具有作用中工作負載的資料庫執行，以及識別適合移轉至 In-Memory OLTP 的資料表和預存程序。如需詳細資訊，請參閱[判斷資料表或預存程序是否應該移植到 In-Memory OLTP](https://msdn.microsoft.com/library/dn205133.aspx)。

1.	使用 SSMS 連接到生產資料庫。或者，如果您有針對新的測試資料庫執行的工作負載，您也可以連接到該資料庫。
2.	以滑鼠右鍵按一下此資料庫並選取 [報表] -> [標準報表]-> [交易效能分析報表]。報表可讓您根據使用方式，識別可能受益於 In-Memory OLTP 的資料表和預存程序。


## 步驟 3：移轉資料表
1.	使用 SSMS 連接到新的測試資料庫。若要避免在查詢中使用 WITH (SNAPSHOT) 選項的需求，我們建議設定資料庫選項 MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT。
2.	一旦連接到新的測試資料庫，請執行：

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	使用任一種方法，將磁碟型資料表移轉至記憶體最佳化資料表：

	答：SSMS 記憶體最佳化精靈：連接到測試資料庫後，以滑鼠右鍵按一下資料表並選取 [記憶體最佳化建議程式]。使用建議程式來判斷資料表是否有記憶體最佳化資料表所不支援的功能。如果沒有，建議程式可以執行實際的結構描述和資料移轉。如需詳細資訊，請檢閱 [MSDN 上的記憶體最佳化建議程式主題](https://msdn.microsoft.com/library/dn284308.aspx)。

	B.手動移轉：使用 SSMS 連接到新的測試資料庫。

請遵循下列步驟來移轉資料表：

1.	在資料表上按一下滑鼠右鍵並選取 [編寫程序的指令碼為] -> [CREATE 至] -> [新增查詢視窗]，以編寫資料表的指令碼。
2.	將 CLUSTERED 索引變更為 NONCLUSTERED 並加入 WITH 選項 (MEMORY\_OPTIMIZED = ON)。
3.	如果資料表使用任何不支援的功能，請實作各項因應措施。MSDN 記載如何處理[常見不支援的功能](https://msdn.microsoft.com/library/dn247639.aspx)。
4.	使用 sp\_rename 重新命名現有的資料表。
5.	執行 CREATE TABLE 指令碼，以建立新的記憶體最佳化資料表。
6.	執行下列陳述式來複製資料：``INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>

## 步驟 4 (選擇性)：移轉預存程序

使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015 年 9 月預覽版本或更新版本，連接到新的測試資料庫。

在舊程序上執行[原生編譯建議程式](https://msdn.microsoft.com/library/dn284308.aspx)，以識別原生編譯預存程序中所有不支援的功能。常見不支援功能的因應措施都記載於 [MSDN](https://msdn.microsoft.com/library/dn296678.aspx)。

將預存程序移轉至原生模組時要考量兩件事：

- 原生模組需要下列選項：

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- 原生模組使用 [ATOMIC 區塊](https://msdn.microsoft.com/library/dn452281.aspx)進行交易管理；不需要明確的 BEGIN TRAN/COMMIT/ROLLBACK 陳述式。

典型的原生編譯預存程序如下所示：


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



請注意，雖然 SNAPSHOT 是記憶體最佳化資料表最常使用的隔離層級，但也支援 REPEATABLE READ 和 SERIALIZABLE。

##### 請遵循下列步驟來移轉預存程序：

1.	在舊的預存程序上按一下滑鼠右鍵並選取 [編寫程序的指令碼為] -> [CREATE 至] -> [新增查詢視窗]，以編寫舊預存程序的指令碼。
2.	使用上述範本重寫程序標頭，並移除任何 BEGIN TRAN/ROLLBACK/COMMIT 陳述式。
3.	如果預存程序使用任何不支援的功能，請實作各項因應措施。MSDN 記載如何處理[常見不支援的功能](https://msdn.microsoft.com/library/dn296678.aspx)。
4.	使用 sp\_rename 卸除此程序或重新命名舊程序。
5.	執行 CREATE PROCEDURE 指令碼，以建立新的原生編譯預存程序。

## 步驟 5：執行您的工作負載
針對記憶體最佳化資料表和原生編譯預存程序執行您的測試工作負載，以及測量效能改善。

## 後續步驟

[監視記憶體內部儲存體](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/)。

[使用動態管理檢視監視 Azure SQL Database](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->