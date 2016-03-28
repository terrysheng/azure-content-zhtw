<properties
   pageTitle="使用「將資料庫部署到 Microsoft Azure Database 精靈」將 SQL Database 移轉到 SQL Database"
   description="Microsoft Azure SQL Database, 資料庫移轉, Microsoft Azure 資料庫精靈"
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

# 使用「將資料庫部署到 Microsoft Azure Database 精靈」將 SQL Database 移轉到 SQL Database

SQL Server Management Studio 中的「將資料庫部署至 Microsoft Azure Database 精靈」會直接將[相容](sql-database-cloud-migrate.md) SQL Server 移轉到 Azure SQL Database 伺服器。

## 使用「將資料庫部署到 Microsoft Azure Database 精靈」

> [AZURE.NOTE] 下列步驟假設您已[佈建 SQL Database 伺服器](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)。

1. 請確認您有最新版本的 SQL Server Management Studio。新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

    > [AZURE.IMPORTANT] 建議您一律使用最新版本的 Management Studio 保持與 Microsoft Azure 及 SQL Database 更新同步。[更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中要管理的 SQL Server 資料庫。
3. 以滑鼠右鍵按一下 [物件總管] 中的來源資料庫，指向 [工作]，並按一下 [將資料庫部署至 Microsoft Azure SQL Database]。

	![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	在部署精靈中，按一下 [下一步]，然後按一下 [連接] 以設定與 SQL Database 伺服器的連接。

	![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. 在 [連接到伺服器] 對話方塊中，輸入連接到 SQL Database 伺服器的連接資訊。

	![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	提供資料庫名稱的 [新資料庫名稱]，設定 [Microsoft Azure SQL Database 的版本] ([服務層](sql-database-service-tiers.md))、[資料庫大小上限]、[服務目標] (效能等級) 和精靈在移轉程序期間建立之 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案的 [暫存檔名稱]。

	![匯出設定](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	完成精靈以移轉資料庫。移轉時間取決資料庫部署的大小及複雜性，可能需要數分鐘到數小時。如果此精靈偵測到相容性問題，螢幕會顯示錯誤，且不會繼續移轉。如需如何修正資料庫相容性問題的指引，請移至[修正資料庫相容性問題](sql-database-cloud-migrate-fix-compatibility-issues.md)。

7.	使用 [物件總管] 時，請連接到 Azure SQL Database 伺服器中已移轉的資料庫。
8.	使用 Azure 入口網站時，請檢視您的資料庫和它的屬性。

## 下一步：修正相容性問題 (如果有)

[修正資料庫相容性問題](sql-database-cloud-migrate-fix-compatibility-issues.md) (如果有)。

<!---HONumber=AcomDC_0316_2016-->