<properties
   pageTitle="最佳化 SQL 資料倉儲的交易 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中撰寫有效率交易更新的最佳作法指引"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="jrj;barbkess"/>

# 最佳化 SQL 資料倉儲的交易

本文說明如何確保寫入您的交易程式碼以充分運用變更的效率。

## 交易和記錄的概念理解

交易是關聯式資料庫引擎的重要元件。SQL 資料倉儲會在資料修改期間使用交易。這些交易可以是明確或隱含的。單一 `INSERT`、`UPDATE` 和 `DELETE` 陳述式都是隱含交易的範例。明確交易由使用 `BEGIN TRAN`、`COMMIT TRAN` 或 `ROLLBACK TRAN` 的開發人員明確撰寫，且通常用於多個修改陳述式必須一起連結為單一不可部分完成單位的時候。

Azure SQL 資料倉儲認可使用交易記錄檔之資料庫的變更。每個散發套件都有自己的交易記錄檔。交易記錄檔寫入是自動的。不需要任何組態。不過，儘管這個程序可保證寫入，但是它會在系統中引進額外負荷。您可以藉由撰寫交易式的有效程式碼，將影響降到最低。交易式的有效程式碼大致分為兩個類別。

- 盡可能使用最低限度的記錄建構
- 使用已設定範圍的批次處理資料，以避免單數的長時間執行交易
- 採用分割切換模式進行指定分割的大規模修改

## 最低限度記錄 vs. 完整記錄
完整記錄作業使用交易記錄檔追蹤每個資料列的變更，最低限度記錄作業不一樣，它只會追蹤程度配置與中繼資料變更。因此，最低限度記錄只會記錄在失敗事件或明確要求 (`ROLLBACK TRAN`) 中回復交易所需的資訊。因為在交易記錄檔中追蹤較少的資訊，最低限度記錄作業的執行效果優於大小類似的完整記錄作業。此外，因為交易記錄檔中較少寫入，所以產生更少量的記錄檔資料，因此有更多有效的 I/O。

>[AZURE.NOTE] 最低限度記錄作業可以加入明確交易。配置結構中的所有變更都會受到追蹤，就可以回復最低限度記錄作業。請務必了解變更為「最低限度」記錄，而不是未記錄。

## 最低限度記錄作業

下列作業也能以最低限度記錄︰

- CREATE TABLE AS SELECT (CTAS)
- INSERT..SELECT
- CREATE INDEX
- ALTER INDEX REBUILD
- DROP INDEX
- TRUNCATE TABLE
- DROP TABLE
- ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

## 大量載入作業的最低限度記錄條件。

`CTAS` 和 `INSERT...SELECT` 都是大量載入作業。不過，兩者都會受到目標資料表定義的影響，取決於載入案例。以下是說明大量作業是否為完全或最低限度記錄的資料表︰

| 主要索引 | 載入案例 | 記錄模式 |
| --------------------------- | -------------------------------------------------------- | ------------ |
| 堆積 | 任意 | **最低限度** |
| 叢集索引 | 空的目標資料表 | **最低限度** |
| 叢集索引 | 載入的資料列不會與目標中的現有頁面重疊 | **最低限度** |
| 叢集索引 | 載入的資料列會與目標中的現有頁面重疊 | 完整 |
| 叢集資料行存放區索引 | 每個與分割對齊的散發套件之批次大小 >= 102,400 | **最低限度** |
| 叢集資料行存放區索引 | 批次大小 < 每個與分割對齊的散發套件 102,400 | 完整 |

值得注意的是任何更新次要或非叢集索引的寫入一定是完整記錄作業。

> [AZURE.IMPORTANT] SQL 資料倉儲有 60 個散發套件。因此，假設所有資料列平均散發，並位於單一分割中，您的批次必須包含 6,144,000 個資料列或更大刑，才能在寫入叢集資料行存放區索引時進行最低限度記錄。如果資料表已分割，且插入的資料列跨越分割界限，每個假設平均資料散發的分割界限將需要 6,144,000 個資料列。每個散發套件中的每個分割必須獨立超過 102,400 的資料列臨界值，才能讓插入以最低限度記錄在散發套件中。

利用叢集索引將資料載入非空白資料表中，通常會混合包含完整記錄和最低限度記錄資料列。叢集索引是頁面的平衡樹狀結構 (b 型樹狀目錄)。如果寫入的頁面中已包含另一個交易的資料列，則這些寫入將會完整記錄。不過，如果頁面是空的，則該頁面的寫入將會以最低限度記錄。

## 完整記錄作業
除了次要索引的更新之外，還有其他陳述式是完整記錄作業。
 
`UPDATE` 和 `DELETE` 陳述式**一律**為完整記錄作業。

不過，這些陳述式可以最佳化，讓它們可以更有效率地執行。

以下四個範例說明如何最佳化您的程式碼以進行完整記錄作業︰

- `CTAS`
- 資料表分割
- 批次作業

### 使用 CTAS 最佳化大量刪除作業
如果您需要刪除資料表或分割中的大量資料，比較理想的作法通常是改為 `SELECT` 您想要保留的資料；利用 [CTAS][] 建立新資料表。建立之後，使用一組 [RENAME OBJECT][] 命令來切換資料表的名稱。

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(	CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([ProductKey])
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20000101, 20010101, 20020101, 20030101, 20040101, 20050101
												,	20060101, 20070101, 20080101, 20090101, 20100101, 20110101
												,	20120101, 20130101, 20140101, 20150101, 20160101, 20170101
												,	20180101, 20190101, 20200101, 20210101, 20220101, 20230101
												,	20240101, 20250101, 20260101, 20270101, 20280101, 20290101
												)
)
AS
SELECT 	*
FROM 	[dbo].[FactInternetSales]
WHERE	[PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

### 使用 CTAS 最佳化大量更新
如果您需要更新資料表或分割中的大量資料列，更有效率的方法通常是使用最低限度記錄作業，例如利用 [CTAS][] 這麼做。

在下列範例中，完整的資料表更新已轉換成 `CTAS`，以便進行最低限度記錄。

在此情況下，我們反而要將折扣金額新增到資料表中的銷售額︰

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(	CLUSTERED INDEX
,	DISTRIBUTION = HASH([ProductKey])
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20000101, 20010101, 20020101, 20030101, 20040101, 20050101
												,	20060101, 20070101, 20080101, 20090101, 20100101, 20110101
												,	20120101, 20130101, 20140101, 20150101, 20160101, 20170101
												,	20180101, 20190101, 20200101, 20210101, 20220101, 20230101
												,	20240101, 20250101, 20260101, 20270101, 20280101, 20290101
												)
				)
)
AS 
SELECT
	[ProductKey]  
,	[OrderDateKey] 
,	[DueDateKey]  
,	[ShipDateKey] 
,	[CustomerKey] 
,	[PromotionKey] 
,	[CurrencyKey] 
,	[SalesTerritoryKey]
,	[SalesOrderNumber]
,	[SalesOrderLineNumber]
,	[RevisionNumber]
,	[OrderQuantity]
,	[UnitPrice]
,	[ExtendedAmount]
,	[UnitPriceDiscountPct]
,	ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,	[ProductStandardCost]
,	[TotalProductCost]
,	ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
		 ELSE [SalesAmount] - 5
		 END AS MONEY),0) AS [SalesAmount]
,	[TaxAmt]
,	[Freight]
,	[CarrierTrackingNumber] 
,	[CustomerPONumber]
FROM	[dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] 使用 SQL 資料倉儲工作負載管理功能有助於重新建立大型資料表。如需詳細資料，請參閱[並行][]一文中的工作負載管理一節。

### 分割切換以更新資料
面臨分割內的大規模修改時，分割切換模式相當實用。如果大量修改資料而且跨越多個分割，則只逐一查看分割也可達到相同的結果。

執行分割切換的步驟如下︰
1. 建立空白分割
2. 執行「更新」做為 CTAS
3. 將現有資料切換出至外資料表
4. 切換入新資料
5. 清除資料

不過，若要協助識別要切換的分割，我們必須先建置如下的協助程式程序。

```sql
CREATE PROCEDURE dbo.partition_data_get
	@schema_name		   NVARCHAR(128)
,	@table_name			   NVARCHAR(128)
,	@boundary_value		   INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
	DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH	(	DISTRIBUTION = ROUND_ROBIN
		,	HEAP
		)
AS
WITH CTE
AS
(
SELECT 	s.name							AS [schema_name]
,		t.name							AS [table_name]
, 		p.partition_number				AS [ptn_nmbr]
,		p.[rows]						AS [ptn_rows]
,		CAST(r.[value] AS INT)			AS [boundary_value]
FROM		sys.schemas					AS s
JOIN		sys.tables					AS t	ON  s.[schema_id]		= t.[schema_id]
JOIN		sys.indexes					AS i	ON 	t.[object_id]		= i.[object_id]
JOIN		sys.partitions				AS p	ON 	i.[object_id]		= p.[object_id] 
												AND i.[index_id]		= p.[index_id] 
JOIN		sys.partition_schemes		AS h	ON 	i.[data_space_id]	= h.[data_space_id]
JOIN		sys.partition_functions		AS f	ON 	h.[function_id]		= f.[function_id]
LEFT JOIN	sys.partition_range_values	AS r 	ON 	f.[function_id]		= r.[function_id] 
												AND r.[boundary_id]		= p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT	*
FROM	CTE
WHERE	[schema_name]		= @schema_name
AND		[table_name]		= @table_name
AND		[boundary_value]	= @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

此程序會最大化程式碼的重複使用，並且讓分割切換範例更加精簡。

下列程式碼示範上述達到完整分割切換例行工作的五個步驟。

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
	DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(	DISTRIBUTION = HASH([ProductKey])
,	CLUSTERED COLUMNSTORE INDEX
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20020101, 20030101
												)
				)
)
AS
SELECT *
FROM	[dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
	DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(	DISTRIBUTION = HASH([ProductKey])
,	CLUSTERED COLUMNSTORE INDEX
, 	PARTITION 	(	[OrderDateKey] RANGE RIGHT 
									FOR VALUES	(	20020101, 20030101
												)
				)
)
AS 
SELECT
	[ProductKey]  
,	[OrderDateKey] 
,	[DueDateKey]  
,	[ShipDateKey] 
,	[CustomerKey] 
,	[PromotionKey] 
,	[CurrencyKey] 
,	[SalesTerritoryKey]
,	[SalesOrderNumber]
,	[SalesOrderLineNumber]
,	[RevisionNumber]
,	[OrderQuantity]
,	[UnitPrice]
,	[ExtendedAmount]
,	[UnitPriceDiscountPct]
,	ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,	[ProductStandardCost]
,	[TotalProductCost]
,	ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
		 ELSE [SalesAmount] - 5
		 END AS MONEY),0) AS [SalesAmount]
,	[TaxAmt]
,	[Freight]
,	[CarrierTrackingNumber] 
,	[CustomerPONumber]
FROM	[dbo].[FactInternetSales]
WHERE	OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]	SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))	+' TO [dbo].[FactInternetSales_out] PARTITION '	+CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))	+' TO [dbo].[FactInternetSales] PARTITION '		+CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

### 將資料修改作業批次處理成可管理的區塊
針對大型資料修改作業，適合將作業分成區塊或批次來指定工作單位的範圍。

以下提供實用的範例。批次大小設為簡單數字來醒目提示此技術。事實上，批次大小明顯大很多。

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
	DROP TABLE #t;
	PRINT '#t dropped';
END

CREATE TABLE #t
WITH	(	DISTRIBUTION = ROUND_ROBIN
		,	HEAP
		)
AS
SELECT	ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,		SalesOrderNumber
,		SalesOrderLineNumber
FROM	dbo.FactInternetSales
WHERE	[OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE	@seq_start		INT = 1
,		@batch_iterator	INT = 1
,		@batch_size		INT = 50
,		@max_seq_nmbr	INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE	@batch_count	INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,		@seq_end		INT = @batch_size
;

SELECT COUNT(*)
FROM	dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE	@batch_iterator <= @batch_count
BEGIN
	DELETE
	FROM	dbo.FactInternetSales
	WHERE EXISTS
	(
			SELECT	1
			FROM	#t t
			WHERE	seq_nmbr BETWEEN  @seq_start AND @seq_end
			AND		FactInternetSales.SalesOrderNumber		= t.SalesOrderNumber
			AND		FactInternetSales.SalesOrderLineNumber	= t.SalesOrderLineNumber
	)
	;

	SET @seq_start = @seq_end
	SET @seq_end = (@seq_start+@batch_size);
	SET @batch_iterator +=1;
END
```

## 「暫停」和「調整」作業的其他指引
Azure SQL 資料倉儲可讓您暫停、繼續及調整需要的資料倉儲。當您暫停或調整您的 SQL 資料倉儲，請務必了解任何進行中的交易都會立即終止；導致所有開放的交易都會回復。如果您的工作負載在暫停或調整作業之前發出長時間執行且不完整的資料修改，這項工作必須復原。這可能會影響完全暫停 Azure SQL 資料倉儲資料庫的時間。

> [AZURE.IMPORTANT] `UPDATE` 和 `DELETE` 都是完整記錄作業，因此這些復原/重做作業花費的時間可能會比對等的最低限度記錄作業長的多。

最佳案例是在暫停或調整 SQL 資料倉儲之前，讓進行中的資料修改交易完成。但是，這不一定都可行。若要降低長時間回復的風險，請考慮下列其中一個選項︰

- 使用 [CTAS][] 重新撰寫長時間執行的作業
- 將作業分成多個區塊；在資料列子集上運作

## 後續步驟
如需更多開發祕訣和顯示範例的相關內容，請參閱下列文章︰

- [開發][]
- [交易][]
- [資料表分割][]
- [並行][]
- [CTAS][]
- [RENAME OBJECT][]

<!--Image references-->

<!--ACOM references-->
[開發]: sql-data-warehouse-overview-develop.md
[交易]: sql-data-warehouse-develop-transactions.md
[資料表分割]: sql-data-warehouse-develop-table-partitions.md
[table partition]: sql-data-warehouse-develop-table-partitions.md
[並行]: sql-data-warehouse-develop-concurrency.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[RENAME OBJECT]: sql-data-warehouse-develop-rename.md

<!--MSDN references-->
[alter index]: https://msdn.microsoft.com/zh-TW/library/ms188388.aspx

<!---HONumber=AcomDC_0330_2016-->