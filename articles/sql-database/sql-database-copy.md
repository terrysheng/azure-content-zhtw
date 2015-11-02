<properties
	pageTitle="複製 Azure SQL Database"
	description="建立 Azure SQL Database 的複本"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/16/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 複製 Azure SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

下列步驟說明如何利用 [Azure Preview 入口網站](https://portal.azure.com)複製 Azure SQL Database。資料庫複製作業會建立新的 SQL Database。副本是您在同一部伺服器或不同伺服器上建立的資料庫快照備份。

> [AZURE.NOTE]Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

複製程序完成時，新的資料庫是功能完整的資料庫，獨立於來源資料庫。複製完成時，新資料庫與來源資料庫在交易上一致。資料庫副本與來源資料庫的服務層和效能層級 (定價層) 相同。複製完成之後，副本會變成功能完整的獨立資料庫。可以個別管理登入、使用者和權限。


當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。


若要複製 SQL Database，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- 要複製的 SQL Database。如果沒有 SQL Database，請遵循本文中以下的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。



## 複製您的 SQL Database

針對您要複製的資料庫開啟 SQL Database 刀鋒視窗：

1.	移至 [Azure Preview 入口網站](https://portal.azure.com)。
2.	移至您要複製的資料庫：瀏覽 > SQL Database
3.	在 SQL Database 刀鋒視窗中，按一下 [複製] 以開啟 [複製] 刀鋒視窗：

    ![複製資料庫][1]

1.  輸入資料庫副本的名稱。系統會提供預設名稱，但您可視需要加以變更。
2.  選取 [目標伺服器]。目標伺服器就是要建立資料庫副本的位置。您可以建立新的伺服器，或從清單中選取現有的伺服器。
3.  按一下 [確定] 開始複製程序。

    ![資料庫名稱和伺服器][2]




## 監視複製作業的進度

2.	開始複製後，按一下入口網站通知以取得詳細資訊。


    ![通知][3]

 
    ![monitor][4]





## 確認伺服器上的資料庫為線上狀態

- 按一下 [瀏覽] > [SQL Database]，並確認新的資料庫為 [線上] 狀態。



## 後續步驟

- [連接 SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)
- [將資料庫匯出至 BACPAC](sql-database-export.md)



## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=Oct15_HO4-->