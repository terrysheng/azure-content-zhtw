<properties
   pageTitle="將範例資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="將範例資料載入 SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#將範例資料載入 SQL 資料倉儲

一旦您[建立 SQL 資料倉儲資料庫執行個體][create a SQL Data Warehouse database instance]之後，下一步便是建立並載入某些資料表。您可以使用我們為 SQL 資料倉儲建立的 Adventure Works 範例指令碼，建立並載入名為 Adventure Works 之虛構公司的資料表。這些指令碼使用 sqlcmd 來執行 SQL，並使用 bcp 載入資料。如果您還沒有安裝這些工具，請依照下列連結[安裝 bcp][] 和[安裝 sqlcmd][]。

請遵循下列簡單的步驟，將 Adventure Works 範例資料庫載入至 SQL DW...

1. 下載[適用於 SQL 資料倉儲的 Adventure Works 範例指令碼][]。

2. 請從下載的 zip 將檔案解壓縮到本機電腦上的目錄。

3. 編輯解壓縮的 aw\_create.bat 檔案，並設定下列可在檔案頂端找到的變數。請務必不要在 "=" 和參數之間留有空白。以下是您可能會想看看如何編輯的範例。

    	server=mylogicalserver.database.windows.net
    	user=mydwuser
    	password=Mydwpassw0rd
    	database=mydwdatabase

4. 從 Windows 命令提示字元執行編輯過的 aw\_create.bat。請確定您所在的目錄是您儲存編輯過之 aw\_create.bat 版本的位置。此指令碼將...
	* 卸除任何 Adventure Works 資料表或任何已存在資料庫中的檢視表
	* 建立 Adventure Works 資料表和檢視表
	* 使用 bcp 載入每個 Adventure Works 資料表
	* 驗證每個 Adventure Works 資料表的資料列計數
	* 收集每個 Adventure Works 資料表之所有資料行上的統計資料


##查詢範例資料

一旦已經將某些範例資料載入您的 SQL 資料倉儲中，就可以快速地執行幾個查詢。若要執行查詢，請使用 Visual Studio 和 SSDT 連接 Azure SQL DW 中新建立的 Adventure Works 資料庫，如[連接][]文件中所描述。

可取得員工之所有資訊的簡單 select 陳述式範例：

	SELECT * FROM DimEmployee;

使用建構 (例如 GROUP BY) 來查看每一天所有銷售之總金額的較複雜查詢範例：

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

可篩選掉特定日期前之訂單的 SELECT 搭配 WHERE 子句的範例：

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

SQL 資料倉儲幾乎支援所有 SQL Server 支援的 T-SQL 建構。所有的差異都記錄在[程式碼移轉][]文件中。

## 後續步驟
現在您已經有機會搭配範例資料嘗試一些查詢，請參閱以了解如何[開發][]、[載入][]，或[移轉][]到 SQL 資料倉儲。

<!--Image references-->

<!--Article references-->
[移轉]: ./sql-data-warehouse-overview-migrate.md
[開發]: ./sql-data-warehouse-overview-develop.md
[載入]: ./sql-data-warehouse-overview-load.md
[連接]: ./sql-data-warehouse-get-started-connect.md
[程式碼移轉]: ./sql-data-warehouse-migrate-code.md
[create a SQL Data Warehouse database instance]: ./sql-data-warehouse-get-started-provision.md
[安裝 bcp]: ./sql-data-warehouse-load-with-bcp.md
[安裝 sqlcmd]: ./sql-data-warehouse-get-started-connect-query-sqlcmd.md

<!--Other Web references-->
[適用於 SQL 資料倉儲的 Adventure Works 範例指令碼]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0309_2016-->