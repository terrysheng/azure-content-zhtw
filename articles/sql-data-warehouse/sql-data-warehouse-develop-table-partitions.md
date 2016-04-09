<properties
   pageTitle="SQL 資料倉儲中的資料表分割 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用資料表分割開發解決方案的秘訣。"
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
   ms.date="03/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 資料倉儲中的資料表分割

若要將 SQL Server 分割定義移轉至 SQL 資料倉儲：

- 移除 SQL Server 分割函式和配置，因為這會在您建立資料表時為您管理。
- 在建立資料表時定義分割。只需指定分割界限點，以及您希望邊界點為有效的 `RANGE RIGHT` 或 `RANGE LEFT`。

注意︰若要深入了解 SQL Server 中的資料分割，請參閱[資料分割資料表和索引](https://msdn.microsoft.com/library/ms190787.aspx)。


### 分割大小
SQL DW 為 DBA 提供數個資料表類型選項：堆積 (CI)、叢集索引和叢集資料行存放區索引 (CCI)。DBA 也可以針對每個資料表類型分割資料表，也就是將其分割成多個區段，以提升效能。不過，建立具有太多分割區的資料表，實際上有可能會導致效能降低，或導致查詢在某些情況下失敗。CCI 資料表尤其堪慮。若要讓資料分割有所助益， DBA 務必要了解使用資料分割的時機，以及要建立的分割區數目。這些指南是為了協助 DBA 在遇到狀況時做出最佳選擇。

資料表分割區通常在兩種主要方式下會很有用：

1. 切換分割區以快速截斷資料表的某個區段。常用的設計是針對包含只供某段預先決定期限內使用之資料列的事實資料表。例如，銷售事實資料表可能僅包含過去 36 個月的資料。在每個月月底，便會從資料表刪除最舊月份的銷售資料。只要刪除最舊月份的所有資料列即可完成此作業，但逐列刪除大量資料可能會花費很多時間。為了最佳化此狀況，SQL DW 支援分割區交換，讓您只要執行一項作業，就能快速卸除分割區中的整個資料列集。   

2. 如果查詢在資料分割資料行上放置述詞，資料分割便可讓查詢輕易排除處理大量的資料列集 (亦即分割區)。例如，如果使用銷售日期欄位將銷售事實資料表分割成 36 個月，以銷售日期進行篩選的查詢便可略過處理不符合篩選條件的分割區。實際上，以這種方式使用的資料分割是粒度粗糙的索引。

在 SQL DW 中建立叢集資料行存放區索引時，DBA 需考量另一個因素：資料列數目。CCI 資料表可以達成高度壓縮，並協助 SQL DW 加速查詢效能。由於壓縮會在 SQL DW 內部運作，因此 CCI 資料表中的每個分割區在壓縮資料前都必須擁有相當大量的資料列。此外，SQL DW 會在大量的散發之間分散資料，而且分割區會進一步分割每個散發。為了最佳壓縮和效能，每個散發與分割區都需要至少 100,000 個資料列。依據上述範例，如果銷售事實資料表包含 36 個月的分割區，並假設 SQL DW 有 60 個散發，則銷售事實資料表每個月應包含 6 百萬個資料列，或是在填入所有月份時包含 216 百萬個資料列。如果資料表包含的資料列遠少於建議的最小值，DBA 就應該考慮建立具有較少分割區的資料表，以增加每個散發的資料列數目。


若要在分割層級調整目前資料庫的大小，請使用類似以下的查詢：

```
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

散發的總數等於我們建立資料表時所使用的儲存體位置數目。也就是，每個資料表會根據每一個散發建立一次。

您也可以大致預測有多少個資料列，因而預測每個分割有多大。來源資料倉儲中的分割會再細分為每個散發。

決定分割大小時，請使用下列計算方式來引導您：

MPP 分割大小 = SMP 分割大小 / 散發數目

您可以使用下列查詢，找出您的 SQL 資料倉儲資料庫有多少個散發：

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

您現在知道來源系統中的每個分割有多大，以及您預期的 SQLDW 大小為何，即可決定分割界限。

### 工作負載管理
您需要納入資料表分割決策的最後一項資訊是工作負載管理。在 SQL 資料倉儲中，此功能會控管在查詢執行期間配置給每個散發的最大記憶體。如需[工作負載管理]的詳細資訊，請參閱下面的文章。理想上，調整您的分割大小時，會將 inmemory 作業 (如資料行存放區索引重建) 列入考量。重建索引會使用大量記憶體。因此，您會想要確定重建分割索引不會耗盡記憶體。從預設角色切換到其中一個可用的其他角色，即可增加您的查詢可用的記憶體數量。

查詢資源管理員動態管理檢視，即可取得每個散發的記憶體配置資訊。事實上，記憶體授與會小於下列數據。不過，這會提供指導方針，以便在針對資料管理作業調整分割大小時使用。

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

> [AZURE.NOTE] 盡量避免將分割大小調整超過超大型資源類別所提供的記憶體授與。如果分割成長超過此數據，您就會冒著記憶體壓力的風險，進而導致比較不理想的壓縮。

## 分割切換
若要切換兩個資料表間的分割，您必須確定分割對齊其各自的界限，而且資料表定義相符。檢查條件約束不適用於強制資料表中的值範圍，來源資料表必須包含與目標資料表相同的分割界限。如果情況不是如此，則分割切換將會失敗，因為分割中繼資料不會同步處理。

### 如何分割包含資料的分割
使用 `CTAS` 陳述式是分割已含資料之分割的最有效方法。如果分割資料表是叢集式資料行存放區，則資料表分割必須空的，才可加以分割。

下列範例顯示每個分割包含一個資料列的分割資料行存放區資料表：

```
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] 藉由建立統計資料物件，我們確定資料表中繼資料更加精確。如果我們省略了建立統計資料，SQL 資料倉儲將會使用預設值。如需統計資料的詳細資訊，請檢閱[統計資料][]。

我們可以接著運用 `sys.partitions` 目錄檢視，查詢資料列計數：

```
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

如果我們嘗試分割此資料表，我們會收到錯誤：

```
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

訊息 35346、層級 15、狀態 1、行 44：ALTER PARTITION 陳述式的 SPLIT 子句失敗，因為分割不是空的。只有在資料表上存在資料行存放區索引時，才可以分割空的分割。請考慮在發出 ALTER PARTITION 陳述式前停用資料行存放區索引，然後在 ALTER PARTITION 完成後重建資料行存放區索引。

不過，我們可以使用 `CTAS` 建立新資料表以保存資料。

```
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

分割界限已對齊，所以允許切換。這會讓來源資料表有空白分割可供我們接著分割。

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

接下來只需使用 `CTAS` 將我們的資料對齊新的分割界限，並將我們的資料切換回到主資料表

```
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

完成資料移動後，最好能重新整理目標資料表上的統計資料，確保統計資料可在其各自的分割中精確地反映出資料的新散發：

```
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### 資料表分割原始檔控制
若要避免您的資料表定義在您的原始檔控制系統中**失效**，您可以考慮下列方法：

1. 將資料表建立為分割資料表，但沒有分割值

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. 在部署過程中 `SPLIT` 資料表：

```
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

利用這種方法，原始檔控制中的程式碼會保持靜態，並允許動態的分割界限值；隨著時間與倉儲一起發展。

>[AZURE.NOTE] 相較於 SQL Server，分割切換有一些差異。務必閱讀[移轉程式碼][]，以深入了解這個主題。


## 後續步驟
一旦成功將資料庫結構描述移轉到 SQL 資料倉儲，您就可以繼續閱讀下列其中一篇文章：

- [移轉資料][]
- [移轉程式碼][]

<!--Image references-->

<!-- Article references -->
[移轉程式碼]: sql-data-warehouse-migrate-code.md
[移轉資料]: sql-data-warehouse-migrate-data.md
[統計資料]: sql-data-warehouse-develop-statistics.md
[工作負載管理]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=AcomDC_0316_2016-->