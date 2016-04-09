<properties
   pageTitle="SQL 資料倉儲中的交易 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中實作交易以便開發解決方案的秘訣。"
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
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 資料倉儲中的交易

如您所預期，SQL 資料倉儲可提供所有交易屬性的支援。不過，為了確保 SQL 資料倉儲的效能維持在一定的程度，某些功能會受到限制 (相較於 SQL Server)。本文特別強調差異，並列出其他交易。

## 交易隔離層級
SQL 資料倉儲實作 ACID 交易。不過，交易支援的隔離僅限於 `READ UNCOMMITTED`，這無法變更。您可以實作許多編碼方法，以避免中途讀取 (dirty read) 資料 (如果您對此有所考量的話)。大多數受歡迎的方法會運用 CTAS 和資料表分割切換 (通常也稱為滑動視窗模式)，以防止使用者查詢仍正準備中的資料。預先篩選資料的檢視也是常用的方法。

## 交易大小
單一資料修改交易的大小是有限制的。「每個散發」都會套用目前的限制。為了取得總數據，因此我們必須將限制乘以散發計數。若要大致估計交易中的資料列總數，請將散發容量除以每個資料行的大小總計。針對可變動的長度資料行，請考慮取得平均資料行長度，而不是使用大小上限。

在下表中，已進行下列假設：
* 已發生的資料平均散發 
* 平均資料列長度是 250 個位元組

| DWU | 每個散發的容量 (GiB) | 散發的數目 | 交易大小上限 (GiB) | 每個散發的資料列數 | 每個交易的資料列數上限 |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4,000,000 | 240,000,000 |
| DW200 | 1\.5 | 60 | 90 | 6,000,000 | 360,000,000 |
| DW300 | 2\.25 | 60 | 135 | 9,000,000 | 540,000,000 |
| DW400 | 3 | 60 | 180 | 12,000,000 | 720,000,000 |
| DW500 | 3\.75 | 60 | 225 | 15,000,000 | 900,000,000 |
| DW600 | 4\.5 | 60 | 270 | 18,000,000 | 1,080,000,000 |
| DW1000 | 7\.5 | 60 | 450 | 30,000,000 | 1,800,000,000 |
| DW1200 | 9 | 60 | 540 | 36,000,000 | 2,160,000,000 |
| DW1500 | 11\.25 | 60 | 675 | 45,000,000 | 2,700,000,000 |
| DW2000 | 15 | 60 | 900 | 60,000,000 | 3,600,000,000 |

系統會針對每個交易或作業套用交易大小限制。它不會套用到所有並行交易。因此允許每一個交易在記錄檔中寫入這個資料量。

若要最佳化寫入記錄檔的資料量並降到最低，請參閱[交易的最佳做法][]一文。

> [AZURE.WARNING] 交易大小上限僅可針對 HASH 或 ROUND\_ROBIN 散發資料表 (資料會平均分佈) 來達成。如果交易是以扭曲方式將資料寫入散發，則可能會在到達交易大小上限之前就先達到限制。
<!--REPLICATED_TABLE-->

## 交易狀態
SQL 資料倉儲會使用 XACT\_STATE() 函式 (採用值 -2) 來報告失敗的交易。這表示交易已失敗並標示為僅可復原

> [AZURE.NOTE] XACT\_STATE 函式使用 -2 表示失敗的交易，以代表 SQL Server 中不同的行為。SQL Server 使用值 -1 來代表無法認可的交易。SQL Server 可以容忍交易內的某些錯誤，而不需將其標示為無法認可。例如，SELECT 1/0 會導致錯誤，但不會強制交易進入無法認可的狀態。SQL Server 也允許讀取無法認可的交易。不過，在 SQLDW 中，情況並非如此。如果 SQLDW 交易內發生錯誤，它就會自動進入 -2 狀態：包含 SELECT 1/0 錯誤。因此，一定要查看您的應用程式程式碼是否使用 XACT\_STATE()。

在 SQL Server 中，您可能會看到如下所示的程式碼片段：

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

請注意，`SELECT` 陳述式發生在 `ROLLBACK` 陳述式之前。另外請注意，`@xact` 變數的設定會使用 DECLARE 而非 `SELECT`。

在 SQL 資料倉儲中，程式碼必須看起來像這樣：

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

請注意，交易的復原必須發生於在 `CATCH` 區塊中讀取錯誤資訊之前。

## Error\_Line() 函式
另外值得注意的是，SQL 資料倉儲不會實作或支援 ERROR\_LINE() 函式。如果您的程式碼中有此函式，您必須將它移除才能符合 SQL 資料倉儲。在程式碼中使用查詢標籤，而不需實作對等的功能。如需這項功能的詳細資訊，請參閱 [查詢標籤] 一文。

## 使用 THROW 和 RAISERROR
THROW 是在 SQL 資料倉儲中引發例外狀況的新式作法，但也支援 RAISERROR。不過，有一些值得注意的差異。

- 對於 THROW，使用者定義的錯誤訊息數目不能在 100,000 - 150,000 範圍內
- RAISERROR 錯誤訊息固定為 50,000
- 不支援使用 sys.messages

## 限制
SQL 資料倉儲有一些與交易相關的其他限制。

如下所示：

- 沒有分散式交易
- 不允許巢狀交易
- 不允許儲存點

## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md
[交易的最佳做法]: sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->