<properties
   pageTitle="使用 SqlPackage 從 BACPAC 檔案匯入 SQL Database"
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 匯入 BACPAC 檔案, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# 使用 SqlPackage 從 BACPAC 檔案匯入 SQL Database

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

本文說明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式，從 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案匯入 SQL 資料庫。此公用程式隨附於 Visual Studio 和 SQL Server。您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版的 SQL Server Data Tools 以取得此公用程式。

> [AZURE.NOTE] 下列步驟假設您已佈建 SQL Database 伺服器、手上有連接資訊並已確認來源資料庫相容。

## 使用 SqlPackage 從 BACPAC 檔案匯入至 Azure SQL Database

使用下列步驟來使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式，從 BACPAC 檔案匯入相容的 SQL Server 資料庫 (或 Azure SQL Database)。

> [AZURE.NOTE] 下列步驟假設您已佈建 Azure SQL Database 伺服器並且手邊有連接資訊。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| 引數 | 說明 |
	|---|---|
	| < server_name > | 目標伺服器名稱 |
	| < database_name > | 目標資料庫名稱 |
	| < user_name > | 目標伺服器中的使用者名稱 |
	| < password > | 使用者的密碼 |
	| < source_file > | 匯入的 BACPAC 檔案的檔案名稱和位置 |

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

<!---HONumber=AcomDC_0316_2016-->