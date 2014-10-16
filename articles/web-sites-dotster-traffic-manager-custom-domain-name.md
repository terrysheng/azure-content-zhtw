<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

使用流量管理員來設定 Windows Azure 網站的自訂網域名稱 (Dotster)
===============================================================

[自訂網域](/zh-tw/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/zh-tw/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name/ "網站") | [使用流量管理員的網站](/zh-tw/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文針對透過 Azure 網站在 [Dotster.com](https://dotster.com) 中購買的自訂網域名稱，提供使用的相關指示。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [啟用網站的流量管理員](#enabledomain)

了解 DNS 記錄
-------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

將網站設定為標準模式
--------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## 新增自訂網域的 DNS 記錄 

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 Dotster 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 Dotster.com 中，使用下列步驟來尋找 DNS 工具

1.  在 Dotster.com 中登入您的帳戶。在 **[網域]** 功能表中，選取 **[DomainCentral]**。

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2.  選取您的網域以帶出設定清單。然後選取 **[Nameservers]** 連結。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3.  選取 **[Use different name servers]**。為了要充分運用 Dotster 上的 DNS 服務，您必須指定下列名稱伺服器：ns1.nameresolve.com、ns2.nameresolve.com、ns3.nameresolve.com 和 ns4.nameresolve.com。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] 需要 24-48 小時的時間才能讓名稱伺服器變更生效。本文的其他步驟無法執行，直到變更生效為止。

4.  在 DomainCentral 中，選取您的網域，然後選取 **[DNS]**。在 **[修改]** 清單中，選取要新增的 DNS 記錄類型 ([CNAME 別名] **** 或 **[A 記錄]**)。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5.  然後指定該記錄的 **[主機]** 和 **[指向]** 欄位。完成時，請按一下 **[新增]** 按鈕。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png)

    -   新增 CNAME 記錄時，您必須將 **[主機]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[指向]** 欄位設定為您要在 Azure 網站中使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。

        > [WACOM.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。

啟用流量管理員網站
------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

