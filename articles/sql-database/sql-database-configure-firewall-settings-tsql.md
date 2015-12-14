<properties
	pageTitle="作法：進行防火牆設定 | Microsoft Azure"
	description="了解如何設定存取 Azure SQL Database 之 IP 位址的防火牆。"
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="11/13/2015"
	ms.author="rickbyh"/>


# 如何：使用 TSQL 在 SQL Database 上進行防火牆設定


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database 使用防火牆規則以允許連接到您的伺服器和資料庫。您可以在 Azure SQL Database 伺服器中，針對主要或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。

> [AZURE.IMPORTANT]若要允許應用程式從 Azure 連接到您的資料庫伺服器，必須啟用 Azure 連接。如需關於防火牆規則及啟用來自 Azure 連接的詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。如果您是在 Azure 雲端界限內建立連接，您可能必須開啟一些其他 TCP 連接埠。如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的＜**SQL Database V12：內部與外部**＞一節


## 透過 Transact-SQL 來管理伺服器層級防火牆規則

1. 透過傳統入口網站或 SQL Server Management Studio 來啟動查詢視窗。
2. 確認您已連接到 master 資料庫。
3. 可從查詢視窗中選取、建立、更新或刪除伺服器層級防火牆規則。
4. 若要建立或更新伺服器層級防火牆規則，請執行 sp\_set\_firewall 規則預存程序。下列範例會在伺服器 Contoso 上啟用某個範圍的 IP 位址。<br/>您可以從檢閱現有的規則開始著手。

		SELECT * FROM sys.firewall_rules ORDER BY name;

	接著，加入防火牆規則。

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	若要刪除伺服器層級防火牆規則，請執行 sp\_delete\_firewall\_rule 預存程序。下列範例會刪除名為 ContosoFirewallRule 的規則。
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 
## 資料庫層級防火牆規則

1. 建立您的 IP 位址的伺服器層級防火牆之後，請透過傳統入口網站或 SQL Server Management Studio 啟動查詢視窗。
2. 連接到要建立資料庫層級防火牆規則的資料庫。

	若要建立新的或更新現有的資料庫層級防火牆規則，請執行 sp\_set\_database\_firewall\_rule 預存程序。下列範例會建立名為 ContosoFirewallRule 的新防火牆規則。
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	若要刪除現有的資料庫層級防火牆規則，請執行 sp\_delete\_database\_firewall\_rule 預存程序。下列範例會刪除名為 ContosoFirewallRule 的規則。
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## 後續步驟

如需建立資料庫的教學課程，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱[以程式設計方式連接到 Azure SQL Database 的方針](https://msdn.microsoft.com/library/azure/ee336282.aspx)。若要了解如何瀏覽至資料庫，請參閱[管理 Azure SQL Database 中的資料庫和登入](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

<!---HONumber=AcomDC_1203_2015-->