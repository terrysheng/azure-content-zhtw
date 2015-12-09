<properties
	pageTitle="SQL Database 教學課程：建立 SQL Database | Microsoft Azure"
	description="在 Azure 入口網站中使用 Microsoft 的關聯式資料庫管理系統 (RDBMS)，於幾分鐘內建立第一個 SQL Database。"
	keywords="sql database 教學課程,建立 sql database"	
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="12/01/2015"
	ms.author="jeffreyg"/>

# SQL Database 教學課程：使用範例資料和 Azure 入口網站在幾分鐘內建立 SQL database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

本 SQL Database 教學課程示範如何在 Azure 入口網站中使用範例資料，在短短幾分鐘內建立第一個 SQL 資料庫。您將學習如何：

- 建立伺服器以裝載您建立的資料庫，然後為其設定防火牆規則。
- 從 AdventureWorks 範例中建立 SQL Database，其中包含您可以處理的資料

在開始之前，您需要有 Azure 帳戶和訂用帳戶。如果您沒有帳戶，請註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/)。

> [AZURE.NOTE]本 SQL Database 教學課程涵蓋如何使用雲端、Azure SQL Database 中的 Microsoft 關聯性資料庫管理服務設定資料庫。另一個選項是在 Azure 虛擬機器上執行 SQL Server。請參閱[了解 Azure SQL Database 和 Azure VM 中的 SQL Server](data-management-azure-sql-database-and-sql-server-iaas.md) 以進行快速比較，或者您可以參閱[佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)以開始使用虛擬機器。

## 步驟 1：登入並開始設定 SQL Database
1. 登入 [Azure 入口網站](http://portal.azure.com/)。
2. 按一下 [新增] > [資料 + 儲存體] > [SQL Database]。

	![SQL Database 教學課程：建立新的 SQL Database。](./media/sql-database-get-started/create-db.png)
	
	您會在出現的 **SQL Database** 設定刀鋒視窗中，設定伺服器和資料庫詳細資料。

	![適用於 SQL Database 的資料庫與伺服器設定](./media/sql-database-get-started/get-started-dbandserversettings.png)

## 步驟 2：選擇伺服器設定
Azure 中的 SQL Database 會存留在資料庫伺服器中。伺服器可以裝載多個資料庫。設定資料庫時，您也可以建立並設定將裝載該資料庫的伺服器，或者可以使用先前建立的伺服器。我們將設定新的伺服器。

1. 請輸入資料庫的 [名稱] (我們使用 **AdventureWorks**)。我們稍後再回頭討論其他資料庫設定。
2. 在 [伺服器] 下方，按一下 [設定必要的設定]，然後按一下 [建立新的伺服器]。

	![為您的資料庫命名](./media/sql-database-get-started/name-and-newserver.png)

3. 在 [新伺服器] 刀鋒視窗中，輸入整個 Azure 中唯一且容易記住的 [伺服器名稱]。稍後當您連接並使用資料庫時，將需要此名稱。
4. 輸入容易記住的 [伺服器管理員登入] (我們使用 **AdventureAdmin**)。接著輸入安全的 [密碼]，並在 [確認密碼] 中再次輸入。

	![新的資料庫伺服器設定](./media/sql-database-get-started/get-started-serversettings.png)

	 讓 [建立 V12 伺服器 (最新更新)] 維持設定為 [是]，以使用最新的功能。[位置] 會決定您建立伺服器的資料中心區域。

	>[AZURE.TIP]在接近應用程式的位置建立資料庫伺服器，其中應用程式將會使用該資料庫。如果您想要變更位置，只需按一下 [位置]，挑選其他位置，然後按一下 [確定] 即可。

5. 按一下 [確定] 回到 [SQL Database] 刀鋒視窗。

資料庫與伺服器尚未建立。此狀況將會在下一個步驟 (您會選擇從 AdventureWorks 範例中建立資料庫，然後確認設定) 完成後發生。

## 步驟 3：設定並建立 SQL Database
1. 在 [SQL Database] 刀鋒視窗中，按一下 [選取來源]，然後按一下 [範例]。 

	![從範例建立 SQL Database](./media/sql-database-get-started/new-sample-db.png)

2. 當您回到 [SQL Database] 刀鋒視窗後，[選取範例] 會立即顯示 **AdventureWorks LT [V12]**。按一下 [建立]，開始建立伺服器和資料庫。

	![建立範例 SQL Database](./media/sql-database-get-started/adworks_create.png)

	>[AZURE.NOTE]由於這些步驟為快速操作，因此我們並未變更 [定價層]、[定序] 和 [資源群組] 的設定。您可以隨時變更資料庫定價層，並向上調升和向下減少，無須停機。如需深入了解，請參閱 [SQL Database 價格](http://azure.microsoft.com/pricing/details/sql-database/)和 [SQL Database 定價層](sql-database-service-tiers.md)。完成資料庫定序設定後，您將無法進行變更。如需關於定序的詳細資料，請參閱[定序與 Unicode 支援](https://msdn.microsoft.com/library/ms143726.aspx)。如需關於 Azure 資源群組的詳細資料，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。

您會跳回「Azure 開始面板」，其中磚會顯示進度，直到資料庫建立完畢且已上線為止。您也可以按一下 [全部瀏覽]，然後按一下 [SQL Database] 確認資料庫是否已上線。
	
恭喜！ 您的雲端中目前已有執行的 SQL Database。您即將完成設定。最後還剩下一個重要步驟。您必須在資料庫中設定規則，以便連接至資料庫。

## 步驟 4：設定防火牆

您必須在伺服器中設定防火牆規則，允許從用戶端電腦 IP 位置進行連接，以便使用資料庫。如此不僅可確保能與資料庫連接，還是個可以查看區域的好方式，您可以在其中取得有關 Azure 中 SQL 伺服器的其他詳細資料。

1. 按一下 [全部瀏覽]，向下捲動並按一下 [SQL Server]，然後從 [SQL Server] 清單中按一下先前建立的伺服器名稱。

	![選取您的資料庫伺服器](./media/sql-database-get-started/browse_dbservers.png)

	
3. 於右邊顯示的資料庫屬性刀鋒視窗中，按一下 [設定]，然後從清單中按一下 [防火牆]。

	![開啟防火牆設定](./media/sql-database-get-started/db_settings.png)


	[防火牆設定] 會顯示您目前的 [用戶端 IP 位址]。

	![目前的 IP 位址](./media/sql-database-get-started/firewall_config_client_ip.png)

4. 按一下 [新增用戶端 IP]，讓 Azure 建立該 IP 位址的規則，然後按一下 [儲存]。

	![新增 IP 位址](./media/sql-database-get-started/firewall_config_new_rule.png)

	>[AZURE.IMPORTANT]您的用戶端 IP 位址可能會不時變動，且在您建立新的防火牆規則前，將可能無法存取伺服器。您可以使用 [Bing](http://www.bing.com/search?q=my%20ip%20address) 檢查 IP 位址，然後新增單一 IP 位址或某個範圍的 IP 位址。如需詳細資訊，請參閱[如何設定防火牆設定](sql-database-configure-firewall-settings.md)。

## 後續步驟
現在您已完成本 SQL Database 教學課程並建立有一些範例資料的資料庫，您可以準備開始使用慣用的工具進行探索。

- 如果您熟悉 TRANSACT-SQL 和 SQL Server Management Studio，請了解如何[使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)。

- 如果您熟悉 Excel，請了解如何[使用 Excel 連接至 SQL Database](sql-database-connect-excel.md)。

- 如果您已經準備開始撰寫程式碼，請參閱[使用 C# 連接和查詢 SQL Database](sql-database-connect-query.md) 和[從 .NET (C#) 使用 SQL Database](sql-database-develop-dotnet-simple.md)。如需 Node.js、Python、Ruby、Java、PHP 和 C++ 範例，以及 C# 以外的操作說明，請參閱 [SQL Database 的快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)。

- 如果您想要進一步了解如何將內部部署的 SQL Server 資料庫移動至 Azure，請參閱[將資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。

<!---HONumber=AcomDC_1203_2015-->