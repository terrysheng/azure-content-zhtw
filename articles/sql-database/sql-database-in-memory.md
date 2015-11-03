<properties
	pageTitle="開始使用 SQL In-Memory | Microsoft Azure"
	description="SQL In-Memory 技術大幅提升交易和分析工作負載的效能。了解如何利用這些技術。"
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


# 開始使用 In-Memory (預覽)


SQL In-Memory 技術大幅提升交易和分析工作負載的效能。視工作負載而定，使用 In-Memory OLTP，可以提升高達 30 倍的交易輸送量，而使用 Memory Analytics，可以提升高達 100 倍的查詢效能。

In-Memory Analytics 已正式上市。In-Memory OLTP 處於預覽階段 (適用於 Premium Azure SQL Database)。


## 開始使用

嘗試將 In-Memory OLTP 用於交易工作負載：


- [以 In-Memory OLTP 範例擴充 AdventureWorksLT 資料庫](#Enrich-an-AdventureWorksLT-database-with-In-Memory-OLTP-samples)。
- [在現有的 Azure SQL 應用程式中使用 In-Memory OLTP](sql-database-in-memory-oltp-migration.md)。
- [監視記憶體內部儲存體](sql-database-in-memory-oltp-monitoring.md)。


嘗試將 In-Memory Analytics 用於交易工作負載：

- [安裝 In-Memory Analytics](#Install-the-In-Memory-Analytics-sample)。
- 在 MSDN 上深入了解[資料行存放區索引](https://msdn.microsoft.com/library/gg492088.aspx)。


## 以 In-Memory OLTP 範例擴充 AdventureWorksLT 資料庫

在 Azure Preview 入口網站中按幾下滑鼠，即可建立 AdventureWorksLT [V12] 範例資料庫。下列步驟說明如何利用資料表和原生編譯預存程序擴充 AdventureWorksLT 資料庫，以說明 In-Memory OLTP 物件。


1. 在 [Azure Preview 入口網站](https://portal.azure.com/)中，在 V12 伺服器上建立 Premium 資料庫。將 [來源] 設定為 AdventureWorksLT[V12] 範例資料庫。
 - 如需此步驟的詳細的指示，請參閱[這篇文章](sql-database-get-started.md)。

2. 使用 [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) 或類似的公用程式，連接到資料庫。

3. 將 [In-Memory OLTP 指令碼](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql)複製到剪貼簿。
 - 此指令碼會在步驟 1 的建立 AdventureWorksLT 範例資料庫中建立所需的 In-Memory 物件。

4. 將 Transact-SQL 指令碼貼到 SSMS.exe 中，然後執行此指令碼。



&nbsp;

此範例包含下列記憶體最佳化資料表：

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

透過物件總管或透過目錄檢視查詢，檢查記憶體最佳化資料表。

範例：


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


同樣地，可以透過物件總管或目錄檢視的查詢來檢查原生編譯預存程序 SalesLT.usp\_InsertSalesOrder\_inmem。

範例：


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## 執行範例工作負載

使用預存程序 SalesLT.usp\_InsertSalesOrder\_inmem 和 SalesLT.usp\_InsertSalesOrder\_ondisk 來比較記憶體最佳化與磁碟型資料表的插入效能。

我們建議使用數個來自與範例資料庫位於相同 Azure 區域之應用程式的並行用戶端連線，執行工作負載。

### 範例銷售訂單插入

下列指令碼會在記憶體最佳化資料表 SalesLT.SalesOrderHeader\_inmem 和 SalesLT.SalesOrderDetail\_inmem 中插入有 5 個細項的範例銷售訂單：


```
		DECLARE
			@i int = 0,
			@od SalesLT.SalesOrderDetailType_inmem,
			@SalesOrderID int,
			@DueDate datetime2 = sysdatetime(),
			@CustomerID int = rand() * 8000,
			@BillToAddressID int = rand() * 10000,
			@ShipToAddressID int = rand() * 10000;

		INSERT INTO @od
		SELECT OrderQty, ProductID
		FROM Demo.DemoSalesOrderDetailSeed
		WHERE OrderID= cast((rand()*60) as int);

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### 執行範例壓力工作負載

若要執行範例壓力工作負載，請在與範例資料庫的相同區域中建立 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。使用 ostress 命令列工具來執行工作負載。在 MSDN 上可以找到[安裝及執行 ostress](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx) 的指示。

從 RML 命令提示字元執行時，下列命令會使用 100 個並行連線，在記憶體最佳化資料表中插入 100 張銷售訂單，而每張訂單有 5 個細項：


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


務必要將 <servername> 取代成您的伺服器名稱、將 <database> 取代成您的資料庫名稱，以及將 <login> 和 <password> 取代成您的登入資訊。

若要比較記憶體最佳化資料表與傳統磁碟型資料表的插入效能，請使用下列命令在磁碟型資料表中插入同樣一百萬份銷售訂單：


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


相較於此工作負載下的磁碟型資料表，在與資料庫相同的 Azure 區域中的 VM 中執行 ostress，測試已顯示記憶體最佳化資料表大約有 9 倍的效能改善。

在每個測試回合之後，務必重設範例，以防止記憶體內部儲存空間不足。在您的資料庫中執行下列 T-SQL 陳述式。插入 100 萬張銷售訂單的單一測試回合會導致記憶體最佳化資料表中有超過 500 MB 的資料。


```
EXECUTE Demo.usp_DemoReset;
```


## 安裝 In-Memory Analytics 範例

**首先**，建立新的 Azure SQL Database。

- 選擇任何 Premium Edition (資料行存放區必須使用 Premium)
- 確定您從範例建立資料庫。

- 為了簡化起見，將資料庫命名為 AdventureworksLT。



**接著**，透過 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015 年 9 月預覽版本或更新版本，連接到 Azure SQL Database。


- 執行安裝指令碼 [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql)


- 執行此指令碼，以建立維度所需的結構描述和事實資料表。此指令碼會建立 2 個事實資料表，每個資料表最多包含 350 萬個資料列。


- 具有資料行存放區資料表的 FactResellerSales\_CCI


- FactResellerSalesXL\_PageCompressed，這是已壓縮頁面的對等 B 型樹狀結構資料表。**注意：**此指令碼可能需要 15 分鐘的時間來執行及產生資料。


**執行** 檔案 [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) 中的示範查詢並探索此功能。

## 深入了解 In-Memory OLTP

[In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)

[一般工作負載模式和移轉考量白皮書](https://msdn.microsoft.com/library/dn673538.aspx)描述 In-Memory OLTP 經常提供顯著效能改善的工作負載模式。

## 預覽版本注意事項

**只有** Premium Edition 資料庫支援 In-Memory OLTP。

只有新建立的資料庫支援 In-Memory OLTP。在透過複製或還原功能並根據現有資料庫建立的資料庫中不提供支援。不過，一旦擁有新的資料庫，您就可以複製或還原此資料庫，同時保有 In-Memory OLTP 的完整功能。

若要驗證指定的資料庫中是否支援 In-Memory OLTP，請執行下列查詢：


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


如果查詢傳回 **1**，此資料庫即可支援 In-Memory OLTP，以及根據此資料庫建立的任何資料庫複製和資料庫還原。

如果資料庫包含下列任何種類的物件或類型，則不支援將資料庫的服務層變更為基本或標準。若要將資料庫降級，您必須先卸除物件。

- 記憶體最佳化資料表
- 記憶體最佳化資料表類型
- 原生編譯模組。不支援使用 In-memory OLTP 搭配彈性集區中的資料庫。

不支援使用 In-Memory OLTP 搭配 SQL 資料倉儲。

「查詢存放區」不會擷取原生編譯模組內的查詢。

In-Memory OLTP 不支援某些 TRANSACT-SQL 功能。如需詳細資訊，請參閱 [In-Memory OLTP 的 Transact-SQL 支援](https://msdn.microsoft.com/library/dn133180.aspx)。

## 支援的工具

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015 年 9 月預覽版本或更新版本。

[SQL Server Data Tools Preview (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 2015 年 9 月預覽版本或更新版本。

## 其他資源

深入了解 In-Memory OLTP 和 Analytics：[In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)

[在 MSDN 上了解資料行存放區索引](https://msdn.microsoft.com/library/gg492088.aspx)

[一般工作負載模式和移轉考量白皮書](https://msdn.microsoft.com/library/dn673538.aspx)描述 In-Memory OLTP 經常提供顯著效能改善的工作負載模式。

## 後續步驟

嘗試[在現有的 Azure SQL 應用程式中使用 In-Memory OLTP](sql-database-in-memory-oltp-migration.md)。

[監視記憶體內部儲存體](sql-database-in-memory-oltp-monitoring.md) (適用於 In-Memory OLTP)。

<!---HONumber=Nov15_HO1-->