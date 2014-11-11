<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# 使用流量管理員來設定 Windows Azure 網站的自訂網域名稱 (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-TW/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-TW/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name/" title="網站">網站</a> | <a href="/zh-TW/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="使用流量管理員的網站" class="current">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供使用 Azure 網站，從 [Dotster.com][Dotster.com] 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄][了解 DNS 記錄]
-   [將網站設定為標準模式][將網站設定為標準模式]
-   [新增自訂網域的 DNS 記錄][新增自訂網域的 DNS 記錄]
-   [啟用網站的流量管理員][啟用網站的流量管理員]

## <a name="understanding-records"></a>了解 DNS 記錄

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>將網站設定為標準模式

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## 新增自訂網域的 DNS 記錄

</p>
若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 Dotster 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 Dotster.com 中，使用下列步驟來尋找 DNS 工具

1.  在 Dotster.com 中登入您的帳戶。在 [網域] 功能表中，選取 [DomainCentral]。

    ![Domain Central Dotster Menu][Domain Central Dotster Menu]

2.  選取您的網域以帶出設定清單。然後選取 [Nameservers] 連結。

    ![Dotster Domain Configuration Options][Dotster Domain Configuration Options]

3.  選取 [Use different name servers]。為了要充分運用 Dotster 上的 DNS 服務，您必須指定下列名稱伺服器：ns1.nameresolve.com、ns2.nameresolve.com、ns3.nameresolve.com 和 ns4.nameresolve.com。

    ![Dotster Domain Configuration Options][1]

    > [WACOM.NOTE] 需要 24-48 小時的時間才能讓名稱伺服器變更生效。本文的其他步驟無法執行，直到變更生效為止。

4.  在 DomainCentral 中，選取您的網域，然後選取 [DNS]。在 [修改] 清單中，選取要新增的 DNS 記錄類型 ([CNAME 別名] 或 [A 記錄])。

    ![Dotster Domain Configuration Options][2]

5.  然後指定該記錄的 [主機] 和 [指向] 欄位。完成時，請按一下 [新增] 按鈕。

    ![Dotster Domain Configuration Options][3]

    -   新增 CNAME 記錄時，您必須將 [主機] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [指向] 欄位設定為您要在 Azure 網站中使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。

        > [WACOM.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。

## <a name="enabledomain"></a>啟用流量管理員網站

[WACOM.INCLUDE [modes][4]]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Dotster.com]: https://dotster.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [了解 DNS 記錄]: #understanding-records
  [將網站設定為標準模式]: #bkmk_configsharedmode
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [啟用網站的流量管理員]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Domain Central Dotster Menu]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Dotster Domain Configuration Options]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png
  [4]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
