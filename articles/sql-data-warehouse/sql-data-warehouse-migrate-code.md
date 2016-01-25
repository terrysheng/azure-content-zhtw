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
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

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
- [通用資料表運算式](#Common-table-expressions)
- [遞迴通用資料表運算式 (CTE)](#Recursive-common-table-expressions-(CTE)
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

### 通用資料表運算式
目前在 SQL 資料倉儲內實作的通用資料表運算式 (CTE) 具有下列功能和限制：

**CTE 功能** + CTE 可以指定於 SELECT 陳述式中。+ CTE 可以指定於 CREATE VIEW 陳述式中。+ CTE 可以指定於 CREATE TABLE AS SELECT (CTAS) 陳述式中。+ CTE 可以指定於 CREATE REMOTE TABLE AS SELECT (CRTAS) 陳述式中。+ CTE 可以指定於 CREATE EXTERNAL TABLE AS SELECT (CETAS) 陳述式中。+ 遠端資料表可以參考自 CTE。+ 外部資料表可以參考自 CTE。+ CTE 中可以定義多個 CTE 查詢定義。

**CTE 限制** + CTE 後面必須接著單一 SELECT 陳述式。INSERT、UPDATE、DELETE 和 MERGE 陳述式不受支援。+ 包含自身參考 (遞迴通用資料表運算式) 的通用資料表運算式不受支援 (請參閱下一節)。+ 不允許在 CTE 中指定多個 WITH 子句。例如，如果 CTE\_query\_definition 包含子查詢，這個子查詢就不能包含定義另一個 CTE 的巢狀 WITH 子句。+ ORDER BY 子句不能用於 CTE\_query\_definition 中，除非有指定 TOP 子句。+ 當某批次中的陳述式使用 CTE 時，它前面的陳述式後面必須接著分號。+ 在用於 sp\_prepare 所準備的陳述式時，CTE 的行為會與 PDW 中的其他 SELECT 陳述式相同。不過，如果 CTE 是做為 sp\_prepare 所準備之 CETAS 中的一部分時，就會因為針對 sp\_prepare 實作繫結的方式而導致其行為與 SQL Server 和其他 PDW 陳述式不同。如果參考 CTE 的 SELECT 使用不存在於 CTE 的錯誤資料行，sp\_prepare 將會通過而不會偵測到錯誤，但在 sp\_execute 期間則會擲回錯誤。

### 遞迴通用資料表運算式 (CTE)

這是複雜的移轉案例，最佳的程序是拆解 CTE 並以多個步驟進行處理。一般來說，您可以使用迴圈，並在逐一執行遞迴中間的查詢時填入暫存資料表。一旦暫存資料表填完之後，您可以將資料傳回做為單一結果集。類似的方法已被用來解決在[由含有 rollup / cube / grouping sets options 的子句組成的群組][]文章中的 `GROUP BY WITH CUBE`。

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

<!---HONumber=AcomDC_0114_2016-->