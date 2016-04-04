<properties
   pageTitle="管理索引 |Microsoft Azure"
   description="協助使用者管理其索引的指引"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 管理索引
本文將示範一些關鍵技巧來管理您的索引。

## 最佳化索引重建
您可以使用下列其中一種方式來最佳化您的索引重建︰

1. 分割資料表並執行資料分割層級的索引重建
2. 使用 [CTAS][]，在新資料表中重新建立資料的分割；以及新資料表中的資料分割切換

## 資料分割的索引重建

以下是如何重建單一資料分割的範例 ︰

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

針對較小的資料磁碟區，最佳做法是使用 ALTER INDEX..REBUILD - 特別是針對資料行存放區索引。開啟、關閉及壓縮的資料列群組全都包含於重建中。不過，如果資料分割相當大，則您會發現 `CTAS` 是更有效率的作業。以下是完整索引重建的範例

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

如需更多關於此語法的詳細資訊，請參閱 [ALTER INDEX][] 一文。

## 使用 CTAS 重建資料分割

以下是如何使用 CTAS 重建資料分割的範例︰

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## 後續步驟
如需如何建立資料分割並調整大小的詳細指引，請參閱關於[資料表分割][]的文章。該文章也會包含範例，以協助找出資料分割的界限。

如需更多管理祕訣，請前往[管理][]概觀

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[資料表分割]: sql-data-warehouse-develop-table-partitions.md
[管理]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/zh-TW/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->