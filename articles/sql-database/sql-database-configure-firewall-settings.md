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


# 如何：使用 Azure 傳統入口網站在 SQL Database 上進行防火牆設定


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Microsoft Azure SQL Database 使用防火牆規則以允許連接到您的伺服器和資料庫。您可以在 Azure SQL Database 伺服器中，針對主要或使用者資料庫定義伺服器層級和資料庫層級的防火牆設定，以選擇性地允許存取資料庫。

> [AZURE.IMPORTANT]若要允許應用程式從 Azure 連接到您的資料庫伺服器，必須啟用 Azure 連接。如需關於防火牆規則及啟用來自 Azure 連接的詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。如果您是在 Azure 雲端界限內建立連接，您可能必須開啟一些其他 TCP 連接埠。如需詳細資訊，請參閱[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)的＜**SQL Database V12：內部與外部**＞一節


### 透過新的 Azure 傳統入口網站來管理伺服器層級防火牆規則


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## 透過傳統入口網站來管理伺服器層級防火牆規則 

1. 在傳統入口網站中，按一下 [SQL Database]。這裡會列出所有資料庫及其對應的伺服器。
2. 按一下頁面頂端的 [伺服器]。
3. 按一下您要管理防火牆規則的伺服器旁邊的箭號。
4. 按一下頁面頂端的 [設定]。

	*  若要加入目前的電腦，請按一下 [新增至允許的 IP 位址]。
	*  若要新增其他 IP 位址，請輸入「規則名稱」、「起始 IP 位址」和「結束 IP 位址」。
	*  若要修改現有的規則，請按一下和修改規則中的任何欄位。
	*  若要刪除現有的規則，將滑鼠停留在規則上方，直到資料列結尾出現 X。按一下 X 移除規則。
5. 按一下頁面底部的 [儲存] 以儲存變更。


## 後續步驟

如需建立資料庫的教學課程，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。如需從開放原始碼或協力廠商應用程式連接到 Azure SQL Database 的說明，請參閱[以程式設計方式連接到 Azure SQL Database 的方針](https://msdn.microsoft.com/library/azure/ee336282.aspx)。若要了解如何瀏覽至資料庫，請參閱[管理 Azure SQL Database 中的資料庫和登入](https://msdn.microsoft.com/library/azure/ee336235.aspx)。

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=AcomDC_1203_2015-->