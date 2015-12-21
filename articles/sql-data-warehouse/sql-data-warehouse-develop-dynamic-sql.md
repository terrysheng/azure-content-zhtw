<properties
   pageTitle="SQL 資料倉儲中的動態 SQL | Microsoft Azure"
   description="使用 Azure SQL 資料倉儲中的動態 SQL 以開發解決方案的秘訣。"
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 資料倉儲中的動態 SQL
開發 SQL 資料倉儲的應用程式程式碼時，您可能須使用動態 SQL，以協助提供有彈性的泛型模組化解決方案。不過，SQL 資料倉儲目前不支援 Blob 資料類型。這可能會限制字串的大小，因為 blob 類型包括 varchar(max) 和 nvarchar(max) 類型。如果您在建立超大型字串時，曾在應用程式的程式碼中使用這些類型，您必須將這些程式碼分成許多區塊，並以 EXEC 陳述式取代。

以下是簡單的範例：

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp\_executesql][]。


## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_1210_2015-->