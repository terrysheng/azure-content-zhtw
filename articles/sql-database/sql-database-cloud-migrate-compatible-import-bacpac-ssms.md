<properties
   pageTitle="將 SQL Server Database 移轉到 Azure SQL Database"
   description="Microsoft Azure SQL Database、資料庫部署、資料庫移轉、匯入資料庫，匯出資料庫、移轉精靈"
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

# 使用 SSMS 從 BACPAC 匯入到 SQL Database

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

本文說明如何使用 SQL Server Management Studio 中的「匯出資料層應用程式精靈」從 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案匯入到 SQL Database。

> [AZURE.NOTE] 下列步驟假設您已佈建 Azure SQL 邏輯執行個體並且手邊有連接資訊。

1. 請確認您有最新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

	 > [AZURE.IMPORTANT] 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。

	![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 一旦建立 BACPAC 之後，請連接至您的 Azure SQL Database 伺服器，以滑鼠右鍵按一下 [資料庫] 資料夾，然後按一下 [匯入資料層應用程式...]。

    ![匯入資料層應用程式功能表項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	在匯入精靈中，選擇剛才匯出的 BACPAC 檔案，即可在 Azure SQL Database 中建立新的資料庫 。

    ![匯入設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	在 Azure SQL DB 上提供資料庫的**新資料庫名稱**，設定 **Microsoft Azure SQL Database 的版本** (服務層)、**資料庫大小上限**和**服務目標** (效能等級)。

    ![資料庫設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	按一下 [下一步]，然後按一下 [完成] 以將 BACPAC 檔案匯入到 Azure SQL Database 伺服器中的新資料庫。

7. 使用 [物件總管] 時，請連接到 Azure SQL Database 伺服器中已移轉的資料庫。

8.	使用 Azure 入口網站時，請檢視您的資料庫和它的屬性。

<!---HONumber=AcomDC_0316_2016-->