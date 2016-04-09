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

# 管理資料表散發扭曲
若資料表的資料是使用雜湊散發方法來散發，資料的散發就有機會呈現「扭曲」狀態。

如果您發現相較於其他散發，某些散發所含的資料量顯然多出很多，則會將該資料表視為扭曲。視扭曲的程度而定，您可能要想要處理它。由於將不會平均取用已散發的運算資源，因此，過度的資料扭曲將會對查詢效能造成影響。

本文旨在協助您識別出已散發叢集之資料表上的資料扭曲。

## 尋找散發扭曲

類似下列檢視的查詢可協助您識別出已扭曲的資料表。

```
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
select 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1024		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1048576		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1073741824	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1024		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1048576		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1073741824	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1024		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1048576		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1073741824	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1024		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1048576		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1073741824	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

建立檢視之後，我們只要查詢該檢視，就能使用類似下列資料表的查詢來驗證資料表中的扭曲。

```
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] 已散發 ROUND\_ROBIN 的資料表不應被扭曲。依照設計，資料會平均散發於節點上。

## 解決資料扭曲
有時扭曲是值得保留的。通常這會在要於共用散發金鑰上聯結資料表時發生。

不過，若要解決資料扭曲，通常會選擇不同的資料行。使用 ROUND\_ROBIN 而不使用 HASH 也是一個選項。

請參閱[雜湊散發][]一文中的建議小節，以取得選取不同資料行的進一步指引。

## 後續步驟
如需資料表散發的詳細資訊，請參閱下列文章：

* [資料表設計][]
* [雜湊散發][]

<!--Image references-->

<!--Article references-->
[資料表設計]: sql-data-warehouse-develop-table-design.md
[雜湊散發]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->