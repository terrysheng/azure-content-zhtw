<properties
   pageTitle="使用 SqlPackage.exe 判斷 SQL Database 相容性"
   description="Microsoft Azure SQL Database, 資料庫移轉, SQL Database 相容性, SqlPackage"
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

# 使用 SqlPackage.exe 判斷 SQL Database 相容性

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

在本文中，您將學習判斷 SQL Server 資料庫是否相容，以使用 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式將其移轉到 SQL Database。

## 使用 SqlPackage.exe

1. 開啟命令提示字元並變更包含最新版 sqlpackage.exe 的目錄。此公用程式隨附於 Visual Studio 和 SQL Server。下載[最新版本 Visual Studio 的 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) 以取得最新版本的 SqlPackage 公用程式。
2. 以環境的下列引數執行下列 SqlPackage 命令：

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| 引數 | 說明 |
	|---|---|
	| < server_name > | 來源伺服器名稱 |
	| < database_name > | 來源資料庫名稱 |
	| < target_file > | BACPAC 檔案的檔案名稱和位置 |
	| < schema_name.table_name > | 其資料將會輸出到目標檔案的資料表 |
	| < output_file > | 具有錯誤的輸出檔案的檔案名稱和位置 (若有的話) |

	使用 /P:TableName 引數的原因是我們只想要測試資料庫相容性以匯出至 Azure SQL DB V12，而不是從所有資料表匯出資料。不幸的是，sqlpackage.exe 的匯出引數不支援不擷取任何資料表，因此您必須指定單一的小型資料表。< output_file > 將包含任何錯誤的報告。"> 2>&1" 字串會將從命令執行產生的標準輸出和標準錯誤輸送到指定的輸出檔。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 開啟輸出檔案並檢視相容性錯誤 (如果有的話)。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 下一步：修正相容性問題 (如果有)

[修正資料庫相容性問題](sql-database-cloud-migrate-fix-compatibility-issues.md) (如果有)。

<!---HONumber=AcomDC_0316_2016-->