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
	ms.date="12/11/2015"
	ms.author="jodebrui"/>


# 在 SQL Database 中開始使用 In-Memory (預覽)


In-Memory 功能大幅提升適當情況下交易和分析工作負載的效能。

本主題強調兩個示範，一個適用於 In-Memory OLTP，一個適用於 In-Memory Analytics。每個示範都有完整的步驟以及執行示範所需的程式碼。您可以：

- 使用程式碼來測試變化以查看效能結果差異；或
- 閱讀程式碼來了解案例，以及了解如何建立和利用 In-Memory 物件。

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

#### In-Memory OLTP

In-Memory [OLTP](#install_oltp_manuallink) (線上交易處理) 的功能如下：

- 記憶體最佳化資料表。
- 原生編譯預存程序。


除了硬碟上的標準表示法以外，記憶體最佳化資料表本身在使用中記憶體中有一個表示法。資料表的商務交易執行速度更快，因為這些交易只會直接與使用中記憶體中的表示法互動。

透過 In-Memory OLTP，視工作負載的詳細規格而定，最高可以達到 30 倍的交易輸送量。


相較於建立為傳統解譯預存程序，原生編譯預存程序在執行階段需要較少的機器指示。我們已看見 1/100 解譯持續時間的持續時間中的原生編譯結果。


#### In-Memory Analytics 

In-Memory [Analytics](#install_analytics_manuallink) 的功能如下：

- 資料行存放區索引


資料行存放區索引藉由奇特的資料壓縮來改善查詢工作負載效能。

在其他服務中，資料行存放區索引必定會進行記憶體最佳化。不過，Azure SQL Database 中的資料行存放區索引可以與其檢索的傳統資料表一起存在於硬碟機上。


#### 即時分析

如需[即時分析](http://msdn.microsoft.com/library/dn817827.aspx)，您可結合 In-Memory OLTP 和 Analytics 來取得：

- 以作業資料為基礎的即時商務深入解析。


#### Availability


公開上市 (GA)：

- *磁碟上*的[資料行存放區索引](http://msdn.microsoft.com/library/dn817827.aspx)。


預覽：

- In-Memory OLTP
- 具有記憶體最佳化資料行存放區索引的 In-Memory Analytics
- Real-Time Operational Analytics


[本主題稍後](#preview_considerations_for_in_memory)會描述 In-Memory 功能在預覽階段的注意事項。


> [AZURE.NOTE]這些預覽階段功能僅適用於 [Premium](sql-database-service-tiers.md) Azure SQL 資料庫，不適用於標準或基本服務層上的資料庫。



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A.安裝 In-Memory OLTP 範例

在 [Azure 入口網站](http://portal.azure.com/)中按幾下滑鼠，即可建立 AdventureWorksLT [V12] 範例資料庫。本節中的步驟說明如何擴充 AdventureWorksLT 資料庫：

- 記憶體內資料表。
- 原生編譯預存程序。


#### 安裝步驟

1. 在 [Azure 入口網站](http://portal.azure.com/)中，在 V12 伺服器上建立高階資料庫。將 [來源] 設定為 AdventureWorksLT [V12] 範例資料庫。
 - 如需詳細指示，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

2. 使用 SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx) 連接到資料庫。

3. 將 [In-Memory OLTP Transact-SQL 指令碼](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql)複製到剪貼簿。
 - T-SQL 指令碼會在步驟 1 建立的 AdventureWorksLT 範例資料庫中建立所需的 In-Memory 物件。

4. 將 T-SQL 指令碼貼到 SSMS，然後執行此指令碼。
 - 重要的是 `MEMORY_OPTIMIZED = ON` 子句 CREATE TABLE 陳述式，如下所示：


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### 錯誤 40536


如果您在執行 T-SQL 指令碼時收到錯誤 40536，請執行下列 T-SQL 指令碼來確認資料庫是否支援 In-Memory：


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


結果為 **0** 表示不支援 In-Memory，而 1 表示提供支援。若要診斷此問題：

- 確保資料庫建立於 In-Memory OLTP 功能可用於預覽版本之後。
- 確保資料庫位於「高階」服務層。


#### 關於已建立的記憶體最佳化項目

**資料表**：此範例包含下列記憶體最佳化資料表：

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


您可以透過 SSMS 中的**物件總管**，檢查記憶體最佳化資料表，方法是：

- 以滑鼠右鍵按一下 [資料表] > [篩選] > [篩選設定] > [記憶體最佳化] 等於 1。


或者您可以查詢目錄檢視，例如：


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**原生編譯預存程序**：SalesLT.usp\_InsertSalesOrder\_inmem 可以透過目錄檢視查詢來檢查：


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## 執行範例 OLTP 工作負載

下列兩個預存程序的唯一差別在於第一個程序會使用記憶體最佳化資料表版本，而第二個程序會使用一般磁碟資料表：

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


在本節中，您會了解如何使用便利的 **ostress.exe** 公用程式，在壓力層級執行兩個預存程序。您可以比較完成兩個壓力回合所需的時間。


當您執行 ostress.exe 時，建議您將指定的參數值傳遞至兩者：

- 執行大量的並行連線 (或許使用 -n100)。
- 讓每個連線執行迴圈數百次 (或許使用 -r500)。


不過，您可能想從較小的值 (-n10 和 -r50) 開始，以確保一切都運作正常。


### Ostress.exe 的指令碼


本節顯示 ostress.exe 命令列中內嵌的 T-SQL 指令碼。此指令碼會使用您稍早安裝的 T-SQL 指令碼所建立的項目。


下列指令碼會在下列記憶體最佳化資料表中插入有 5 個細項的範例銷售訂單：

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


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
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
end
```


若要製作上述適用於 ostress.exe 的 T-SQL 的 \_ondisk 版本，您只需以 *\_ondisk* 取代兩著出現的 *\_inmem* 子字串。這類取代會影響資料表和預存程序的名稱。


### 安裝 RML 公用程式和 ostress


您最好規劃在 Azure VM 上執行 ostress.exe。您會在 AdventureWorksLT 資料庫所在的相同 Azure 地理區域中建立 [Azure 虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)。但是您可以改在您的膝上型電腦上執行 ostress.exe。


在 VM 上，或在任何選擇的主機上，安裝包含 ostress.exe 的 Replay Markup Language (RML) 公用程式。

- 請參閱 [In-Memory OLTP 的範例資料庫](http://msdn.microsoft.com/library/mt465764.aspx)中的 ostress.exe 討論。
 - 或參閱 [In-Memory OLTP 的範例資料庫](http://msdn.microsoft.com/library/mt465764.aspx)。
 - 或參閱[安裝 ostress.exe 的部落格](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### 先執行 \_inmem 壓力工作負載


您可以使用 RML 命令提示字元 視窗來執行 ostress.exe 命令列。命令列參數會將 ostress 導向至：

- 同時執行 100 個連線 (-n100)。
- 每個連線會執行 T-SQL 指令碼 50 次 (-r50)。


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


若要執行上述的 ostress.exe 命令列：


1. 在 SSMS 中執行下列命令來重設資料庫資料內容，以刪除先前執行插入的所有資料：
```
EXECUTE Demo.usp_DemoReset;
```

2. 將上述 ostress.exe 命令列的文字複製到剪貼簿。

3. 以正確的實數值取代參數 -S -U -P -d 的 <placeholders>。

4. 在 [RML 命令] 視窗中執行已編輯的命令列。


#### 結果是持續時間


當 ostress.exe 完成時，它會在 RML 命令視窗中寫入執行持續時間做為輸出的最後一行。例如，較短的測試回合持續大約 1.5 分鐘：

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### 重設，針對 \_ondisk 編輯，然後重新執行


在獲得 \_inmem 執行的結果之後，請針對 \_ondisk 執行回合執行下列步驟：


1. 在 SSMS 中執行下列命令來重設資料庫，以刪除先前執行插入的所有資料：
```
EXECUTE Demo.usp_DemoReset;
```

2. 編輯 ostress.exe 命令列，以 *\_ondisk* 取代所有的 *\_inmem*。

3. 第二次重新執行 ostress.exe，並擷取持續時間結果。

4. 再次重設資料庫，以負責刪除可能的大量測試資料。


#### 預期的比較結果

在與資料庫相同的 Azure 區域的 Azure VM 上執行 ostress，In-Memory 測試已顯示此簡化工作負載大約有 **9 倍**的效能改善。


加入轉換成原生編譯的預存程序時，效能可以獲得更大改善。


## B.安裝 In-Memory Analytics 範例


在本節中，您會比較使用資料行存放區索引與一般索引時的 IO 和統計資料結果。


資料行存放區索引與一般索引在邏輯上相同，但實體上卻不同。資料行存放區索引會以非原生方式組織資料，大幅壓縮資料。如此即可大幅改進效能。


針對 OLTP 工作負載的即時分析，通常最好使用非叢集式資料行存放區索引。如需詳細資訊，請參閱[已描述的資料行存放區索引](http://msdn.microsoft.com/library/gg492088.aspx)。



### 準備資料行存放區分析測試


1. 使用 Azure 入口網站，從範例建立全新的 AdventureWorksLT 資料庫。
 - 使用相同的名稱。
 - 選擇任何進階服務層。

2. 將 [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) 複製到剪貼簿。
 - T-SQL 指令碼會在步驟 1 建立的 AdventureWorksLT 範例資料庫中建立所需的 In-Memory 物件。
 - 此指令碼會建立維度資料表和兩個事實資料表。每個事實資料表會填入 350 萬個資料列。
 - 此指令碼可能需要 15 分鐘才能完成。

3. 將 T-SQL 指令碼貼到 SSMS，然後執行此指令碼。
 - 重要的是 **CREATE INDEX** 陳述式上的 **COLUMNSTORE** 關鍵字，如下所示：<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. 將 AdventureWorksLT 設為相容性層級 130：<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - 層級 130 並未與 In-Memory 功能直接相關。但層級 130 通常會提供比層級 120 更快的查詢效能。


#### 重要資料表和資料行存放區索引


- dbo.FactResellerSalesXL\_CCI 是具有叢集式**資料行存放區**索引的資料表，已在*資料*層級進一步壓縮。

- dbo.FactResellerSalesXL\_PageCompressed 是具有對等一般叢集式索引的資料表，只會在*頁面*層級壓縮。


#### 用來比較資料行存放區索引的重要查詢


[這裡](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql)是您可以執行的數種 T-SQL 查詢類型，以便查看效能改進。在 T-SQL 指令碼的步驟 2 中，有一組直接相關的查詢。這兩個查詢的不同之處只有一行：


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


叢集式資料行存放區索引位於 FactResellerSalesXL**\_CCI** 資料表上。

下列 T-SQL 指令碼摘錄列出每個資料表查詢的 IO 和 TIME 統計資料。


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## In-Memory OLTP 預覽版本注意事項


Azure SQL Database 中的 In-Memory OLTP 功能[於 2015 年 10 月 28 日進入預覽階段](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/)。


在公開上市 (GA) 前的預覽階段期間，只對下列資料庫支援 In-Memory OLTP：

- 在*進階*服務層的資料庫。

- 在 In-Memory OLTP 功能生效後建立的資料庫。
 - 如果從 In-Memory OLTP 功能生效前建立的資料庫還原，則新的資料庫無法支援 In-memory OLTP。


如有疑問，您一律可以執行下列 T-SQL SELECT 來確定您的資料庫是否支援 In-memory OLTP。結果為 **1** 表示資料庫可支援 In-Memory OLTP：

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


如果查詢傳回 **1**，此資料庫即可支援 In-Memory OLTP，以及根據此資料庫建立的任何資料庫複製和資料庫還原。


#### 只允許進階的物件


如果資料庫包含下列任何種類的 In-Memory OLTP 物件或類型，則不支援將資料庫的服務層從進階降級為基本或標準。若要將資料庫降級，您必須先卸除下列物件：

- 記憶體最佳化資料表
- 記憶體最佳化資料表類型
- 原生編譯模組


#### 其他關聯性


- 在預覽期間不支援將 In-Memory OLTP 功能使用於彈性集區中的資料庫，但未來可提供支援：

- 不支援使用 In-Memory OLTP 搭配 SQL 資料倉儲。
 - SQL 資料倉儲支援 In-Memory Analytics 的資料行存放區索引功能。

- 在預覽期間「查詢存放區」不會擷取原生編譯模組內的查詢，但未來可能會這麼做。

- In-Memory OLTP 不支援某些 TRANSACT-SQL 功能。這適用於 Microsoft SQL Server 和 Azure SQL Database。如需詳細資料，請參閱：
 - [記憶體中 OLTP 的 Transact-SQL 支援](http://msdn.microsoft.com/library/dn133180.aspx)
 - [In-Memory OLTP 不支援的 Transact-SQL 建構。](http://msdn.microsoft.com/library/dn246937.aspx)


## 後續步驟


- 嘗試[在現有的 Azure SQL 應用程式中使用 In-Memory OLTP](sql-database-in-memory-oltp-migration.md)。


## 其他資源

#### 更深入的資訊

- [了解 In-Memory OLTP (這適用於 Microsoft SQL Server 和 Azure SQL Database)](http://msdn.microsoft.com/library/dn133186.aspx)

- [深入了解 MSDN 上的 Real-Time Operational Analytics](http://msdn.microsoft.com/library/dn817827.aspx)

- [一般工作負載模式和移轉考量白皮書](http://msdn.microsoft.com/library/dn673538.aspx)，其中描述 In-Memory OLTP 經常提供顯著效能改善的工作負載模式。

#### 應用程式設計

- [In-Memory OLTP (In-Memory Optimization)](http://msdn.microsoft.com/library/dn133186.aspx)

- [在現有的 Azure SQL 應用程式中使用 In-Memory OLTP。](sql-database-in-memory-oltp-migration.md)

#### 工具

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)，最新的每月版本。

- [SQL Server 的 Replay Markup Language (RML) 公用程式說明](http://support.microsoft.com/zh-TW/kb/944837)

- 適用於 In-Memory OLTP 的[監視記憶體內部儲存體](sql-database-in-memory-oltp-monitoring.md)。

<!---HONumber=AcomDC_1217_2015-->