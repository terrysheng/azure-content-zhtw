<properties
	pageTitle="停用 Stretch Database 並恢復遠端資料 | Microsoft Azure"
	description="了解如何停用資料表的 Stretch Database，並選擇性地恢復遠端資料。"
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

# 停用 Stretch Database 並恢復遠端資料

若要停用資料表的 Stretch Database，請在 SQL Server Management Studio 中為資料表選取 [Stretch]。然後選取下列其中一個選項。

-   [停用 | 從 Azure 恢復資料]。從 Azure 將資料表的遠端資料複製到 SQL Server，然後停用該資料表的 Stretch Database。這項操作會產生資料傳輸成本，且無法取消。

-   [停用 | 將資料留在 Azure]。停用資料表的 Stretch Database。放棄 Azure 中的資料表遠端資料。

當您停用資料表的 Stretch Database 之後，資料移轉將會停止，而查詢結果將不再包含來自遠端資料表的結果。

您也可以使用 Transact-SQL 以停用資料表或資料庫的 Stretch Database。

如果您只是想要暫停資料移轉，請參閱 [Pause and resume data migration (暫停和繼續 Stretch Database)](sql-server-stretch-database-pause.md)。

注意：停用 Stretch 並不會移除遠端物件。如果您想要刪除遠端資料表或遠端資料庫，您必須使用 Azure 管理入口網站加以卸除。

## 停用資料表的 Stretch Database

### 使用 SQL Server Management Studio 以停用資料表的 Stretch Database

1.  在 SQL Server Management Studio 中，於 [物件總管] 中選取想要停用 Stretch Database 的資料表。

2.  以滑鼠右鍵按一下並選取 [Stretch]，然後選取下列其中一個選項。

    -   [停用 | 從 Azure 恢復資料]。從 Azure 將資料表的遠端資料複製到 SQL Server，然後停用該資料表的 Stretch Database。這個命令無法取消。

        這項操作會產生資料傳輸成本。如需詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)。

        當所有遠端資料都從 Azure 複製到 SQL Server 之後，資料表的 Stretch 功能將會停用。

    -   [停用 | 將資料留在 Azure]。停用資料表的 Stretch Database。放棄 Azure 中的資料表遠端資料。

    停用 Stretch 並不會移除遠端資料表。如果您想要刪除遠端資料表，您必須使用 Azure 管理入口網站加以卸除。

### 使用 Transact-SQL 以停用資料表的 Stretch Database

-   若要停用資料表的 Stretch，並將資料表的遠端資料從 Azure SQL Database 複製到 SQL Server，請執行下列命令。這個命令無法取消。

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
    ```
    這項操作會產生資料傳輸成本。如需詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)。

    當所有遠端資料都從 Azure 複製到 SQL Server 之後，資料表的 Stretch 功能將會停用。

-   若要停用資料表的 Stretch，並放棄遠端資料，請執行下列命令。

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```
停用 Stretch 並不會移除遠端資料表。如果您想要刪除遠端資料表，您必須使用 Azure 管理入口網站加以卸除。

## 停用資料庫的 Stretch Database
在您可以停用資料庫的 Stretch Database 之前，您必須停用資料庫中已啟用 Stretch 之個別資料表上的 Stretch Database。

停用 Stretch 並不會移除遠端資料庫。如果您想要刪除遠端資料庫，您必須使用 Azure 管理入口網站加以卸除。

### 使用 SQL Server Management Studio 以停用資料庫的 Stretch Database

1.  在 SQL Server Management Studio 中，於 [物件總管] 中選取想要停用 Stretch Database 的資料庫。

2.  以滑鼠右鍵按一下並選取 [工作]，然後選取 [Stretch]，並選取 [停用]。

### 使用 Transact-SQL 以停用資料庫的 Stretch Database
執行下列命令。

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

## 另請參閱
[ALTER DATABASE SET 選項 (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) [Pause and resume Stretch Database (暫停和繼續 Stretch Database)](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0309_2016-->