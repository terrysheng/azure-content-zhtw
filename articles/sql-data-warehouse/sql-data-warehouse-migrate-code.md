<properties
   pageTitle="將您的 SQL 程式碼移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將 SQL 程式碼移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。"
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
   ms.date="09/09/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# 將您的 SQL 程式碼移轉至 SQL 資料倉儲

為了確保您的程式碼符合 SQL 資料倉儲，您很可能需要變更您的程式碼基底。某些 SQL 資料倉儲功能設計為直接以分散式方式運作，可以大幅改善效能。不過，為了維持效能和延展性，某些功能也無法使用。

## Transact-SQL 程式碼變更

下列清單摘要說明 Azure SQL 資料倉儲中不支援的主要功能。此連結會帶您前往不支援功能的因應措施：

- [更新時的 ANSI 聯結][]
- [刪除時的 ANSI 聯結][]
- [merge 陳述式][]
- 跨資料庫聯結
- [資料指標][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- output 子句
- 內嵌使用者定義函數
- 多重陳述式函式
- [遞迴通用資料表運算式 (CTE)](#Recursive-common-table-expressions-(CTE)
- [透過 CTE 的更新](#Updates-through-CTEs)
- CLR 函式和程序
- $partition 函式
- 資料表變數
- 資料表值參數
- 分散式交易
- 認可/回復工作
- 儲存交易
- 執行內容 (EXECUTE AS)
- [group by 子句搭配 rollup / cube / grouping sets 選項][]
- [巢狀層級超過 8][]
- [透過檢視表更新][]
- [使用 select 進行變數指派][]
- [動態 SQL 字串沒有 MAX 資料類型][]

幸好這些限制大部分都可以克服。上面提及的相關開發文章中已提供說明。

### 遞迴通用資料表運算式 (CTE)

這是沒有快速修正的複雜案例。CTE 必須被細分並依序處理。您可以像往常一樣使用相當複雜的迴圈；當您逐一查看遞迴暫時查詢時填入暫存資料表。一旦暫存資料表填完之後，您可以將資料傳回做為單一結果集。類似的方法已被用來解決在[由含有 rollup / cube / grouping sets options 的子句組成的群組][]文章中的 `GROUP BY WITH CUBE`。

### 透過 CTE 更新

當 CTE 為非遞迴時，您可以重新撰寫查詢來使用子查詢。對於遞迴 CTE，您必須如上所述先建立結果集；然後將最終結果集加入目標資料表中並執行更新。

### 系統函數

另外還有一些不支援的系統函式。您通常可能會發現資料倉儲中使用的主要函式包括：

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

同樣地許多這些問題都可以克服。

例如，下列程式碼擷取 @@ROWCOUNT 資訊的替代解決方案：

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## 後續步驟
如需有關開發程式碼的建議，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[更新時的 ANSI 聯結]: sql-data-warehouse-develop-ctas.md
[刪除時的 ANSI 聯結]: sql-data-warehouse-develop-ctas.md
[merge 陳述式]: sql-data-warehouse-develop-ctas.md
[INSERT..EXEC]: sql-data-warehouse-develop-temporary-tables.md

[資料指標]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[group by 子句搭配 rollup / cube / grouping sets 選項]: sql-data-warehouse-develop-group-by-options.md
[由含有 rollup / cube / grouping sets options 的子句組成的群組]: sql-data-warehouse-develop-group-by-options.md
[巢狀層級超過 8]: sql-data-warehouse-develop-transactions.md
[透過檢視表更新]: sql-data-warehouse-develop-views.md
[使用 select 進行變數指派]: sql-data-warehouse-develop-variable-assignment.md
[動態 SQL 字串沒有 MAX 資料類型]: sql-data-warehouse-develop-dynamic-sql.md
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO2-->