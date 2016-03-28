
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. 登入位於 http://portal.azure.com/ 的 [Azure 入口網站](https://portal.azure.com/)。

2. 在左邊的橫幅中按一下 [瀏覽]。即會顯示 [瀏覽] 刀鋒視窗。

3. 捲動並按一下 [SQL Server]。即會顯示 [SQL Server] 刀鋒視窗。

	![在入口網站中尋找您的 Azure SQL Database 伺服器][b21-FindServerInPortal]

4. 按一下伺服器的資料列。即會顯示您伺服器的刀鋒視窗。

5. 在伺服器刀鋒視窗中，按一下 [設定]。即會顯示 [設定] 刀鋒視窗。

6. 按一下 [防火牆]。即會顯示 [防火牆設定] 刀鋒視窗。

	![按一下 [設定] > [防火牆]。][b31-SettingsFirewallNavig]

7. 按一下 [新增用戶端 IP]，新增用戶端電腦的 IP 位址。入口網站將會為您的規則建構一個名稱。或者，您可以輸入名稱，並為您要啟用的範圍提供 IP 位址的上下限值。將低值結尾設為 **.0**、高值結尾設為 **.255** 會比較方便。

	![將一組 IP 位址範圍加入為允許][b41-AddRange]

8. 按一下 [儲存]。



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_0316_2016-->