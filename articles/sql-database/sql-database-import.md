<properties
	pageTitle="匯入 BACPAC 檔案以建立新的 Azure SQL Database"
	description="匯入現有的 BACPAC 檔案以建立新的 Azure SQL Database。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/17/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 匯入 BACPAC 檔案以建立新的 Azure SQL Database


**單一資料庫**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

本文提供使用 [Azure 入口網站](https://portal.azure.com) 自 BACPAC 檔案建立新的 Azure SQL Database 的說明。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。如需詳細資訊，請參閱[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)中的備份封裝 (.bacpac)。

資料庫是透過由 Azure 儲存體 Blob 容器匯入的 BACPAC 來建立。如果您的 Azure 儲存體沒有 .bacpac 檔案，可依照[建立和匯出 Azure SQL Database 的 BACPAC](sql-database-export.md) 中的步驟，加以建立。


> [AZURE.NOTE]Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。


若要從 .bacpac 匯入 SQL Database，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database V12 伺服器。如果沒有 V12 伺服器，請遵循本文中以下的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- 您想要匯入 [Azure 儲存體帳戶 (傳統)](storage-create-storage-account.md) Blob 容器之資料庫的 .bacpac 檔案。


## 選取將包含資料庫的伺服器

開啟您要匯入資料庫的 SQL Server 刀鋒視窗：

1.	移至 [Azure 入口網站](https://portal.azure.com)。
2.	按一下 [全部瀏覽]。
3.	按一下 [SQL Server]。
2.	按一下伺服器，以將資料庫還原至該伺服器。
3.	在 SQL Server 刀鋒視窗中，按一下 [匯入資料庫] 以開啟 [匯入資料庫] 刀鋒視窗：

    ![匯入資料庫][1]

1.  按一下 [儲存體]，並選取您的儲存體帳戶、Blob 容器以及 .bacpac 檔，然後按一下 [確定]。

    ![設定儲存體選項][2]

1.  選取新資料庫的定價層，然後按一下 [選取]。

    ![選取定價層][3]

1.  輸入**資料庫名稱**。
2.  針對您要匯入資料庫的 Azure SQL Server，輸入 **Server 系統管理員登入**和**密碼**。
1.  按一下 [建立]，以從 BACPAC 建立資料庫。

    ![建立資料庫][4]

按一下 [建立] 時，會將匯入資料庫要求提交給服務。視資料庫大小而定，匯入作業可能需要一些時間才能完成。

## 監視匯入作業的進度

2.	按一下 [全部瀏覽]。
3.	按一下 [SQL Server]。
2.	按一下您要還原的目的地伺服器。
3.	在 SQL Server 刀鋒視窗中，按一下 [匯入/匯出記錄]：

    ![匯入匯出記錄][5] ![匯入匯出記錄][6]





## 確認伺服器上的資料庫為線上狀態

2.	按一下 [全部瀏覽]。
3.	按一下 [SQL 資料庫] 並確認新的資料庫為 [線上] 狀態。



## 後續步驟

- [連接 SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)



## 其他資源

- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=AcomDC_1223_2015-->