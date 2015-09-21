<properties
	pageTitle="如何：進行防火牆設定 | Microsoft Azure"
	description="設定存取 Azure SQL 資料庫之 IP 位址的防火牆。"
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
	ms.date="09/04/2015"
	ms.author="rickbyh"/>


# 如何：在 SQL Database 上進行防火牆設定


Microsoft Azure SQL Database 使用防火牆規則以允許連接到您的伺服器和資料庫。您可以在 Azure SQL Database 伺服器中，針對主要或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。

**重要** 若要允許從 Azure 的應用程式連接到您的資料庫伺服器，必須啟用 Azure 連線。如需防火牆規則及啟用來自 Azure 連線的詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。


## 伺服器層級防火牆規則

伺服器層級防火牆規則可透過 Microsoft Azure 管理入口網站、Transact-SQL、Azure PowerShell 或 REST API 來建立及管理。

### 透過新的 Azure 入口網站來管理伺服器層級防火牆規則


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## 透過管理入口網站來管理伺服器層級防火牆規則 

1. 在管理入口網站中，按一下 [SQL Database]。這裡會列出所有資料庫及其對應的伺服器。
2. 按一下頁面頂端的 [伺服器]。
3. 按一下您要管理防火牆規則的伺服器旁邊的箭號。
4. 按一下頁面頂端的 [設定]。

	*  若要加入目前的電腦，請按一下 [新增至允許的 IP 位址]。
	*  若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。
	*  若要修改現有的規則，請按一下和修改規則中的任何欄位。
	*  若要刪除現有的規則，將滑鼠停留在規則上方，直到資料列結尾出現 X。按一下 X 移除規則。
5. 按一下頁面底部的 [儲存] 以儲存變更。

## 透過 Transact-SQL 來管理伺服器層級防火牆規則

1. 透過管理入口網站或 SQL Server Management Studio 來啟動查詢視窗。
2. 確認您已連接到 master 資料庫。
3. 可從查詢視窗中選取、建立、更新或刪除伺服器層級防火牆規則。
4. 若要建立或更新伺服器層級防火牆規則，請執行 sp\_set\_firewall 規則預存程序。下列範例會在伺服器 Contoso 上啟用某個範圍的 IP 位址。<br/>您可以從檢閱現有的規則開始著手。

		SELECT * FROM sys.database_firewall_rules ORDER BY name;

	接著，加入防火牆規則。

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	若要刪除伺服器層級防火牆規則，請執行 sp\_delete\_firewall\_rule 預存程序。下列範例會刪除名為 ContosoFirewallRule 的規則。
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## 透過 Azure PowerShell 來管理伺服器層級防火牆規則
1. 啟動 Azure PowerShell。
2. 可使用 Azure PowerShell 建立、更新及刪除伺服器層級防火牆規則。 

	若要建立新的伺服器層級防火牆規則，請執行 New-AzureSqlDatabaseServerFirewallRule Cmdlet。下列範例會在伺服器 Contoso 上啟用某個範圍的 IP 位址。
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	若要修改現有的伺服器層級防火牆規則，請執行 Set-AzureSqlDatabaseServerFirewallRule Cmdlet。下列範例會變更名為 ContosoFirewallRule 的規則可接受的 IP 位址範圍。
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	若要刪除現有的伺服器層級防火牆規則，請執行 Remove-AzureSqlDatabaseServerFirewallRule Cmdlet。下列範例會刪除名為 ContosoFirewallRule 的規則。

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## 透過 REST API 來管理伺服器層級防火牆規則
1. 透過 REST API 管理防火牆規則必須經過驗證。如需詳細資訊，請參閱「驗證服務管理要求」。
2. 伺服器層級規則可使用 REST API 建立、更新或刪除

	若要建立或更新伺服器層級防火牆規則，請使用下列命令執行 POST 方法：
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	要求本文

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	若要移除現有的伺服器層級防火牆規則，請使用下列命令執行 DELETE 方法：
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule
 
## 資料庫層級防火牆規則

1. 建立您的 IP 位址的伺服器層級防火牆之後，請透過管理入口網站或 SQL Server Management Studio 啟動查詢視窗。
2. 連接到要建立資料庫層級防火牆規則的資料庫。

	若要建立新的或更新現有的資料庫層級防火牆規則，請執行 sp\_set\_database\_firewall\_rule 預存程序。下列範例會建立名為 ContosoFirewallRule 的新防火牆規則。
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	若要刪除現有的資料庫層級防火牆規則，請執行 sp\_delete\_database\_firewall\_rule 預存程序。下列範例會刪除名為 ContosoFirewallRule 的規則。
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## 使用服務管理 REST API 管理防火牆規則

* [建立防火牆規則](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [刪除防火牆規則](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [取得防火牆規則](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [列出防火牆規則](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## 使用 PowerShell 管理防火牆規則

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## 後續步驟

如需建立資料庫的教學課程，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱[以程式設計方式連接至 Azure SQL Database 的方針](https://msdn.microsoft.com/library/azure/ee336282.aspx)。若要了解如何瀏覽至資料庫，請參閱[管理 Azure SQL Database 中的資料庫和登入](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Sept15_HO2-->