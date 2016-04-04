<properties
   pageTitle="管理資料表散發扭曲 |Microsoft Azure"
   description="協助使用者識別散發資料表中散發扭曲的指引"
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

# 管理資料行存放區索引
資料行存放區索引是 Azure SQL 資料倉儲的骨幹。藉由讓索引保持在最佳狀態，您將可以充分運用系統的效能。

這篇文章說明如何詢問資料行存放區索引的中繼資料以找尋資料表；協助您診斷問題並快速解決問題。

## 查詢資料行存放區中繼資料
若要了解資料行存放區索引的密度，必須查詢系統中繼資料。以下是您能夠發現的資訊類型範例。

```
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

在建立檢視之後，可以輕鬆地分析資料行存放區中繼資料。範例查詢如下所示。

```
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## 改善資料行存放區密度
一旦您執行查詢，您可以開始查看資料，並分析您的結果。

有幾件事要查看︰

| 資料欄 | 如何使用這項資料 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | 如果資料表已分割，您可能會預期看到較高的開放資料列群組計數。散發套件中的每個分割在理論上有與其相關聯的開放資料列群組。將這個因素納入您的分析。已分割的小型資料表可以藉由移除分割進行最佳化，因為這樣會改善壓縮。 |
| [row\_count\_total] | 這個值可以用來計算壓縮狀態資料列百分比 (舉例而言) 的資料表之資料列總計數 |
| [row\_count\_per\_distribution\_MAX] | 如果所有資料列平均分配，這個值會是每個散發的目標資料列數目。比較此值與 compressed\_rowgroup\_count。 |
| [COMPRESSED\_rowgroup\_rows] | 資料表的資料行存放區格式中的資料列總數 |
| [COMPRESSED\_rowgroup\_rows\_AVG] | 如果平均資料列數目遠小於資料群組最大的資料列數目，則可考慮使用 CTAS 或 ALTER INDEX REBUILD 重新壓縮資料 |
| [COMPRESSED\_rowgroup\_count] | 資料行存放區格式中的資料列群組數目。如果相對於資料表的這個數目很高，表示資料行存放區密度很低。 |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | 資料行存放區格式中的資料列會以邏輯方式刪除。如果相對於資料表大小的這個數目很高，請考慮重新建立分割或重建索引，因為這樣會將其實際移除。 |
| [COMPRESSED\_rowgroup\_rows\_MIN] | 將它與 AVG 和 MAX 資料行搭配使用，以了解資料行存放區中資料列群組的值範圍。載入臨界值上較低的數目 (每個分割對齊散發套件 102,400) 表示資料載入可進行最佳化 |
| [COMPRESSED\_rowgroup\_rows\_MAX] | 同上。 |
| [OPEN\_rowgroup\_count] | 開放資料列群組都正常。每個資料表散發都應該有一個開放資料列群組 (60)。過多的數目表示資料跨分割載入。重複檢查分割策略並確定它是正確的 |
| [OPEN\_rowgroup\_rows] | 每個資料列群組可以有最多 1,048,576 個資料列。使用這個值查看開放資料列群組目前的飽和度 |
| [OPEN\_rowgroup\_rows\_MIN] | 開放群組會指出資料是傳遞載入資料表，或是先前的載入將剩餘的資料列溢出到此資料列群組。使用 MIN、MAX、AVG 欄位查看多少資料位於開放資料列群組。對於小型資料表，可能是所有資料的 100%！ 在此情況下，ALTER INDEX REBUILD 來強制資料進入資料行存放區。 |
| [OPEN\_rowgroup\_rows\_MAX] | 同上。 |
| [OPEN\_rowgroup\_rows\_AVG] | 同上。 |
| [CLOSED\_rowgroup\_rows] | 查看關閉的資料列群組資料列做為例行性檢查。如果 |
| [CLOSED\_rowgroup\_count] | 如果發現任何關閉資料列群組，其數目應該很小。關閉資料列群組可以使用 ALTER INDEX 轉換成壓縮資料列群組...REORGANISE 命令。不過，通常並不需要。關閉群組會透過背景 "tuple mover" 程序自動轉換成資料行存放區的資料列群組。 |
| [CLOSED\_rowgroup\_rows\_MIN] | 關閉資料列群組應該具有極高的填滿率。如果關閉資料列群組的填滿率很低，就需要進一步分析資料行存放區。 |
| [CLOSED\_rowgroup\_rows\_MAX] | 同上。 |
| [CLOSED\_rowgroup\_rows\_AVG] | 同上。 |

## 索引管理
重新壓縮資料列群組的達成方法包括建立使用 [CTAS][] 的分割，或使用 [ALTER INDEX][] 重建索引本身。[CTAS][] 的執行速度通常比 [ALTER INDEX][] 快速 - 尤其是大型資料表或分割。不過，對於較小的資料表或分割，[ALTER INDEX][] 通常更方便。

>[AZURE.NOTE] ALTER INDEX...REBUILD 是離線作業。ALTER INDEX...REORGANISE 是線上作業。不過 REORGANISE 並未接觸開放資料列群組。若要包含開放資料列群組，就需要 ALTER INDEX...REBUILD。

重建索引，特別是大型資料表，通常受益於其他資源。Azure SQL 資料倉儲有工作負載管理功能，可以用來配置更多記憶體給使用者。若要了解如何保留更多記憶體給您的索引重建，請參閱[並行][]一文的工作負載管理一節。

## 後續步驟
如需使用 `CTAS` 重新建立分割的詳細資料，請參閱下列文章︰

* [資料表分割][]
* [並行][]

如需索引管理的詳細建議，請檢閱[管理索引][]一文。

如需更多管理祕訣，請前往[管理][]概觀

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[資料表分割]: sql-data-warehouse-develop-table-partitions.md
[並行]: sql-data-warehouse-develop-concurrency.md
[管理]: sql-data-warehouse-manage-monitor.md
[管理索引]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/zh-TW/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->