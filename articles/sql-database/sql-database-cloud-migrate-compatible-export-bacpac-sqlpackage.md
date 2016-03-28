<properties
   pageTitle="使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案"
   description="Microsoft Azure SQL Database, 資料庫移轉, 匯出資料庫, 匯出 BACPAC 檔案, sqlpackage"
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

# 使用 SqlPackage 將的 SQL Server Database 匯出到 BACPAC 檔案

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

本文說明如何使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式，將 SQL Server 資料庫匯出成 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。此公用程式隨附於 Visual Studio 和 SQL Server。您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版的 SQL Server Data Tools 以取得此公用程式。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。使用電腦上的搜尋功能來尋找您環境中的路徑。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| 引數 | 說明 |
	|---|---|
	| < server_name > | 來源伺服器名稱 |
	| < database_name > | 來源資料庫名稱 |
	| < target_file > | BACPAC 檔案的檔案名稱和位置 |

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 下一步：從 BACPAC 檔案匯入 SQL Database

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_0316_2016-->