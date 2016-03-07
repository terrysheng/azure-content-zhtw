<properties
	pageTitle="In-Memory OLTP 可改善 SQL 交易效能 | Microsoft Azure"
	description="採用 In-Memory OLTP 來改善現有 SQL Database 的交易效能。"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor="MightyPen"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="jodebrui"/>


# 使用 In-Memory OLTP (預覽) 改善 SQL Database 中的應用程式效能

[In-Memory OLTP](sql-database-in-memory.md) 可在[進階](sql-database-service-tiers.md) Azure SQL Database 中，用來改善 OLTP 工作負載的效能，但是不會增加效能層級。

請依照下列步驟，在您現有的資料庫中採用 In-Memory OLTP。

## 步驟 1：確定您的進階資料庫支援 In-Memory OLTP

建立於 2015 年 11 月或之後的進階資料庫支援 In-Memory 功能。您可以執行下列 Transact-SQL 陳述式，確認您的進階資料庫是否支援 In-Memory 功能。如果傳回的結果為 1 (不是 0)，則支援 In-Memory：

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* 代表*極端交易處理 (Extreme Transaction Processing)*

如果您現有的資料庫必須移至新的 V12 進階資料庫，您可以使用下列技術將資料先行匯出再匯入。

#### 匯出步驟

使用下列其中一項，將生產資料庫匯出至 bacpac：

- [入口網站](https://portal.azure.com/)中的[匯出](sql-database-export.md)功能。

- [最新 SSMS.exe](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio) 中的**匯出資料層應用程式**功能。
 1. 在 [物件總管] 中，展開 [資料庫] 節點。
 2. 以滑鼠右鍵按一下您的資料庫節點。
 3. 按一下 [工作] > [匯出資料層應用程式]。
 4. 操作顯示的精靈視窗。


#### 匯入步驟

將 bacpac 匯入到新的進階資料庫。

1. 在 Azure [入口網站](https://portal.azure.com/)中，
 - 導覽至伺服器。
 - 選取 [匯入資料庫][](sql-database-import.md) 選項。
 - 選取進階定價層。

2. 使用 SSMS 匯入 bacpac：
 - 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫] 節點。
 - 按一下 [匯入資料層應用程式]。
 - 操作顯示的精靈視窗。


## 步驟 2：識別要移轉至 In-Memory OLTP 的物件

SSMS 包含您可以對具有作用中工作負載的資料庫執行的 [交易效能分析概觀] 報告。此報告會識別要移轉至 In-Memory OLTP 的候選資料表和預存程序。

在 SSMS 中，若要產生報告，請：-在 [物件總管] 中，以滑鼠右鍵按一下您的資料庫節點。- 按一下 [報告] > [標準報告] > [交易效能分析概觀]。

如需詳細資訊，請參閱[判斷資料表或預存程序是否應該移植到 In-Memory OLTP](http://msdn.microsoft.com/library/dn205133.aspx)。


## 步驟 3：建立可比較的測試資料庫

假設報告指出您的資料庫的某個資料表若轉換成記憶體最佳化的資料表將會有好處。我們建議您先測試以確認這項指示。

您需要實際執行資料庫的測試複本。測試資料庫必須位於與實際執行資料庫相同的服務層級。

為了簡化測試，請依照下列方式調整測試資料庫：

1. 使用 SSMS 連接到測試資料庫。

2. 若要避免在查詢中用到 WITH (SNAPSHOT) 選項，請依照下列 T-SQL 陳述式中所示設定資料庫選項：```
ALTER DATABASE CURRENT
	SET
		MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## 步驟 4：移轉資料表

您必須建立並填入您想要測試之資料表的記憶體最佳化複本。您可以使用下列其中一種方式加以建立：

- SSMS 中好用的記憶體最佳化精靈。
- 手動 T-SQL。


#### SSMS 中的記憶體最佳化精靈

若要使用此移轉選項：

1. 使用 SSMS 連接到測試資料庫。

2. 在 [物件總管] 中，以滑鼠右鍵按一下資料表，然後按一下 [記憶體最佳化建議程式]。
 - [資料表記憶體最佳化建議程式] 精靈隨即顯示。

3. 在此精靈中按一下 [移轉驗證] (或 [下一步] 按鈕)，以查看資料表是否有任何在記憶體最佳化資料表中不受支援的功能。如需詳細資訊，請參閱：
 - [記憶體最佳化建議程式](http://msdn.microsoft.com/library/dn284308.aspx)中的*記憶體最佳化檢查清單*。
 - [In-Memory OLTP 不支援的 Transact-SQL 建構](http://msdn.microsoft.com/library/dn246937.aspx)。
 - [移轉至 In-Memory OLTP](http://msdn.microsoft.com/library/dn247639.aspx)。

4. 如果資料表沒有不受支援的功能，建議程式可以為您執行實際的結構描述和資料移轉。


#### 手動 T-SQL

若要使用此移轉選項：

1. 使用 SSMS (或類似的公用程式) 連接到您的測試資料庫。

2. 為您的資料表及其索引取得完整 T-SQL 指令碼。
 - 在 SSMS 中，以滑鼠右鍵按一下資料表節點。
 - 按一下 [產生資料表的指令碼為] > [建立] > [新增查詢視窗]。

3. 在指令碼視窗中，將 WITH (MEMORY\_OPTIMIZED = ON) 新增至 CREATE TABLE 陳述式。

4. 如果有 CLUSTERED 索引，請將其變更為 NONCLUSTERED。

5. 使用 SP\_RENAME 重新命名現有的資料表。

6. 執行您已編輯的 CREATE TABLE 指令碼，以建立新的記憶體最佳化資料表複本。

7. 使用 INSERT...SELECT * INTO，將資料複製到您的記憶體最佳化資料表：
	
```
INSERT INTO <new_memory_optimized_table>
		SELECT * FROM <old_disk_based_table>;
```


## 步驟 5 (選擇性)：移轉預存程序

In-Memory 功能也可以修改預存程序，以改善效能。


### 原生編譯預存程序的考量

原生編譯預存程序的 T-SQL WITH 子句必須具有下列選項：

- NATIVE\_COMPILATION

- SCHEMABINDING：表示除非您捨棄預存程序，否則無法由預存程序以任何會影響到預存程序的方式變更其資料行定義的資料表。


原生模組必須使用一個大型 [ATOMIC 區塊](http://msdn.microsoft.com/library/dn452281.aspx)進行交易管理。沒有明確 BEGIN TRANSACTION 或 ROLLBACK TRANSACTION 的角色。如果您的程式碼偵測到違反商務規則，它可以利用 [THROW](http://msdn.microsoft.com/library/ee677615.aspx) 陳述式終止不可部分完成的區塊。


### 原生編譯的一般 CREATE PROCEDURE

建立原生編譯預存程序的 T-SQL 通常會類似於下列範本：

```
CREATE PROCEDURE schemaname.procedurename
	@param1 type1, …
	WITH NATIVE_COMPILATION, SCHEMABINDING
	AS
		BEGIN ATOMIC WITH
			(TRANSACTION ISOLATION LEVEL = SNAPSHOT,
			LANGUAGE = N'your_language__see_sys.languages'
			)
		…
		END;
```

- 就 TRANSACTION\_ISOLATION\_LEVEL 而言，SNAPSHOT 是原生編譯預存程序最常用的值。不過，也支援其他值的子集：
 - REPEATABLE READ
 - SERIALIZABLE


- sys.languages 檢視中必須有 LANGUAGE 值存在。


### 如何移轉預存程序

移轉步驟如下：


1. 取得規則解譯之預存程序的 CREATE PROCEDURE 指令碼。

2. 請重寫其標頭，以符合先前的範本。

3. 確認預存程序 T-SQL 程式碼是否使用任何不支援原生編譯預存程序的功能。視需要實作因應措施。
 - 如需詳細資訊，請參閱[原生編譯預存程序的移轉問題](http://msdn.microsoft.com/library/dn296678.aspx)。

4. 使用 SP\_RENAME 重新命名舊的預存程序。或直接加以捨棄。

5. 執行已編輯的 CREATE PROCEDURE T-SQL 指令碼。


## 步驟 6：在測試環境中執行您的工作負載

在測試資料庫中，執行與您在實際執行資料庫中執行的工作負載類似的工作負載。如此，將 In-Memory 功能用於資料表和預存程序所達到的效能改善應可顯現出來。

工作負載的主要屬性包括：

- 並行連線數目。

- 讀取/寫入比率。


若要修改並執行測試工作負載，請考慮使用方便的 ostress.exe 工具，如[這裡](sql-database-in-memory.md)所說明。


為了盡可能減少網路延遲，請在資料庫所在的相同 Azure 地理區域中執行您的測試。


## 步驟 7：實作後的監視

請考慮監視您在實際執行環境中實作 In-Memory 的效能影響：

- [監視記憶體內部儲存體](sql-database-in-memory-oltp-monitoring.md)。

- [使用動態管理檢視監視 Azure SQL Database](sql-database-monitoring-with-dmvs.md)


## 相關連結

- [In-Memory OLTP (In-Memory Optimization)](http://msdn.microsoft.com/library/dn133186.aspx)

- [原生編譯預存程序簡介](http://msdn.microsoft.com/library/dn133184.aspx)

- [記憶體最佳化建議程式](http://msdn.microsoft.com/library/dn284308.aspx)

<!---HONumber=AcomDC_0224_2016-->