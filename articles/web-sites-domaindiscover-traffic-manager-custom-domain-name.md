<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

使用流量管理員來設定 Windows Azure 網站的自訂網域名稱 (DomainDiscover / TierraNet)
==================================================================================

[自訂網域](/en-us/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "網站") | [使用流量管理員的網站](/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文針對透過 Azure 網站在 [DomainDiscover.com](https://domaindiscover.com) 中購買的自訂網域名稱，提供使用的相關指示。DomainDiscover.com 是 [TierraNet](https://www.tierra.net/) 的一部分。

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

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 DomainDiscover 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 DomainDiscover.com 中，使用下列步驟來尋找 DNS 工具

1.  在 DomainDiscover.com (TierraNet) 中登入您的帳戶，方法是從 **[登入]** 功能表中選取 **[控制台]**。

    ![DomainDiscover Login Menu](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2.  在 **[網域服務]** 頁面上，選取要在您的 Azure 網站上使用的網域。

    ![Domain management page](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3.  在 [網域] 設定的 **[DNS 服務]** 中，按一下 **[編輯]** 按鈕。

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4.  在 **[管理 DNS]** 視窗中，選取要在 **[新增記錄]** 清單中新增的 DNS 記錄類型。然後按一下 **[新增]** 按鈕。

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5.  在下列頁面上，輸入 DNS 記錄值。然後按一下 **[新增]** 按鈕。

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    -   新增 CNAME 記錄時，您必須先在 **[管理 DNS]** 頁面上選取 **[CNAME (別名)]**。然後將 **[主機]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[Alias Hostname]** 欄位設定為您要在 Azure 網站中使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。然後提供 [存留時間 (TTL)] 值，例如 1800 秒。

        > [WACOM.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。

啟用流量管理員網站
------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

