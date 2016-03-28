<properties
	pageTitle="為資料表啟用 Stretch Database | Microsoft Azure"
	description="了解如何為資料表設定 Stretch Database。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 為資料表啟用 Stretch Database

若要為資料表設定 Stretch Database，請在 SQL Server Management Studio 中選取資料表的 [Stretch | 啟用]，以開啟 [為資料表啟用 Stretch] 精靈。您也可以使用 Transact-SQL 以在資料表上啟用 Stretch Database。

-   如果您將歷程記錄資料儲存在個別的資料表上，您可以移轉整個資料表。

-   如果您的資料表同時包含歷程記錄資料及目前的資料，您可以指定篩選述詞以選取要移轉的資料列。在透過 RC0 使用的 CTP 3.1 中，指定篩選述詞的選項於 [為資料庫啟用 Stretch] 精靈中已不再提供。您必須使用 ALTER TABLE 陳述式以利用此選項為資料表設定 Stretch Database。如需詳細資訊，請參閱 [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)。

**必要條件**。如果您選取資料表的 [Stretch | 啟用]，且沒有為資料庫啟用 Stretch Database，精靈將會先為資料庫設定 Stretch Database。請遵循 [[為資料庫啟用 Stretch] 精靈](sql-server-stretch-database-wizard.md)中的步驟，而非本主題中的步驟。

**權限**。在資料庫或資料表上啟用 Stretch Database 需要 db\_owner 權限。在資料表上啟用 Stretch Database 也需要資料表上的 ALTER 權限。

## <a name="EnableWizardTable"></a>使用精靈以在資料表上啟用 Stretch Database
**啟動精靈**
1.  在 SQL Server Management Studio 中，於 [物件總管] 中選取想要啟用 Stretch 的資料表。

2.  以滑鼠右鍵按一下並選取 [Stretch]，然後選取 [啟用] 以啟動精靈。

**簡介** 檢閱精靈的用途及必要條件。

**選取資料庫資料表** 確認您想要啟用的資料表皆已顯示且被選取。

在透過 RC0 使用的 CTP 3.1 中，您必須使用精靈才能移轉整個資料表。如果您想要指定述詞以從同時包含歷程記錄資料與目前資料的資料表中選取要移轉的資料列，請執行 ALTER TABLE 陳述式以在結束精靈後指定述詞，或是如本主題稍後所說明的方式，結束精靈並執行 ALTER TABLE 陳述式。

**摘要** 檢閱您輸入的值，以及您在精靈中選取的選項。然後選取 [完成] 以啟用 Stretch。

**結果** 檢閱結果。

## <a name="EnableTSQLTable"></a>使用 Transact-SQL 以在資料表上啟用 Stretch Database
若要為資料表設定 Stretch Database，請執行 ALTER TABLE 命令。

1.  (選擇性) 如果資料表同時包含歷程記錄資料和目前資料，請使用 `FILTER_PREDICATE = <predicate>` 子句以指定述詞來選取要移轉的資料列。述詞必須呼叫嵌入資料表值函式。如需詳細資訊，請參閱[撰寫嵌入資料表值函式以選取資料列 (Stretch Database)](sql-server-stretch-database-predicate-function.md)。如果您不指定篩選述詞，便會移轉整個資料表。

    > [!重要事項] 如果您提供執行不良的篩選述詞，則資料移轉也會執行不良。Stretch Database 使用 CROSS APPLY 運算子將篩選述詞套用至資料表。

    在透過 RC0 使用的 CTP 3.1 中，此選項於 [為資料庫啟用 Stretch] 精靈中已不再提供。您必須使用 ALTER TABLE 陳述式以利用此選項為資料表設定 Stretch Database。如需詳細資訊，請參閱 [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)。

2.  指定 `MIGRATION_STATE = OUTBOUND` 以立即開始資料移轉，或 `MIGRATION_STATE = PAUSED` 以延後資料移轉的開始時間。

以下是移轉整個資料表並立即開始資料移轉的範例。

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
GO;
```
以下是只移轉由 `dbo.fn_stretchpredicate` 嵌入資料表值函式識別的資料列，並延後資料移轉的範例。如需篩選述詞的詳細資訊，請參閱[撰寫嵌入資料表值函式以選取資料列 (Stretch Database)](sql-server-stretch-database-predicate-function.md)。

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) )
```

## 另請參閱

[ALTER TABLE (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0316_2016-->