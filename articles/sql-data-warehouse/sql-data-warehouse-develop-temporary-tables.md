<properties
   pageTitle="SQL 資料倉儲中的暫存資料表 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中使用暫存資料表開發解決方案的秘訣。"
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
   ms.author="mausher;jrj;barbkess;sonyama"/>

# SQL 資料倉儲中的暫存資料表
暫存資料表在處理資料時非常有用 - 尤其是具有暫時性中繼結果的轉換期間。在 SQL 資料倉儲中，暫存資料表存在於工作階段層級。不過，這類資料表仍會定義為本機暫存資料表，但與 SQL Server 資料表不同的是，它們可以從工作階段內的任何位置存取。

本文包含使用暫存資料表的一些基本指引，並強調說明工作階段層級暫存資料表的原則。使用此資訊，可協助您將程式碼模組化。請務必進行程式碼模組化，以方便維護和重複使用程式碼。

## 建立暫存資料表
建立暫存資料表是非常直截了當的。您所要做的就只是在資料表名稱前面加上 #，如下列範例所示 ︰

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

暫存資料表也可以使用完全相同的方法，利用 `CTAS` 來建立。

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS` 是一個非常強大的命令，其附加優點是能夠非常有效率地使用交易記錄空間。


## 捨棄暫存資料表

為了確保您的 `CREATE TABLE` 陳述式會成功，請務必確保資料表尚未存在於工作階段中。這可以使用下列模式，透過預先存在的簡單檢查來處理：

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] 若要以一致性方式編寫程式碼，建議針對資料表和暫存資料表使用此模式。

當您在程式碼中完成使用暫存資料表之後，使用 `DROP TABLE` 加以移除也是一個很好的做法。

```sql
DROP TABLE #stats_ddl
```

在預存程序開發期間，在程序結尾一併搭配 drop 命令以確保會清除這些物件，也是相當常見的做法。

## 模組化程式碼

在使用者工作階段中的任何位置均可看見暫存資料表的這項事實，可用於實際協助您將應用程式的程式碼模組化。

讓我們舉個可行的範例。

下列預存程序結合了先前所述的範例。此程式碼可用來產生更新資料庫中每個資料行的統計資料所需的 DDL：

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

在這個階段中，不需對資料表進行任何動作。程序只會產生更新統計資料所需的 DDL，並將該程式碼儲存於暫存資料表中。

不過，也請注意，預存程序不會在結尾包含 `DROP TABLE` 命令。不過，我們已將預先存在的檢查包含於預存程序中，讓程式碼穩固且可重複；如此可確保我們的 `CTAS` 在產生工作階段中現有的重複物件時將不會失敗。

現在進入最有趣的部分！

在 SQL 資料倉儲中，可能會在建立暫存資料表的程序外部使用它。這與 SQL Server 有所不同。事實上，暫存資料表可以使用於工作階段內的**任何位置**。

這可能會導致更多模組化和可管理的程式碼。查看下方範例：

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

產生的程式碼會更加精簡。

在某些情況下，也可以使用這項技術來取代內嵌和多重陳述式的函式。

> [AZURE.NOTE] 您也可以擴充此解決方案。如果您只想要更新單一資料表，您只需要篩選 #stats\_ddl 資料表

## 暫存資料表限制
SQL 資料倉儲在實作暫存資料表時的確有一些限制。

主要限制如下：

- 不支援全域暫存資料表
- 無法在暫存資料表上建立檢視

## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->