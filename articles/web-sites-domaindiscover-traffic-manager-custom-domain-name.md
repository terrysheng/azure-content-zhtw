<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# 使用流量管理員來設定 Windows Azure 網站的自訂網域名稱 (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-TW/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-TW/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="網站">網站</a> | <a href="/zh-TW/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="使用流量管理員的網站" class="current">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供使用 Azure 網站，從 [DomainDiscover.com][DomainDiscover.com] 購買的自訂網域名稱的使用指示。DomainDiscover.com 是 [TierraNet][TierraNet] 的一部分。

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
若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 DomainDiscover 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 DomainDiscover.com 中，使用下列步驟來尋找 DNS 工具

1.  在 DomainDiscover.com (TierraNet) 中登入您的帳戶，方法是從 [登入] 功能表中選取 [控制台]。

    ![DomainDiscover Login Menu][DomainDiscover Login Menu]

2.  在 [網域服務] 頁面上，選取要在您的 Azure 網站上使用的網域。

    ![Domain management page][Domain management page]

3.  在 [網域] 設定的 [DNS 服務] 中，按一下 [編輯] 按鈕。

    ![DNS edit button][DNS edit button]

4.  在 [管理 DNS] 視窗中，選取要在 [新增記錄] 清單中新增的 DNS 記錄類型。然後按一下 [新增] 按鈕。

    ![DNS edit button][1]

5.  在下列頁面上，輸入 DNS 記錄值。然後按一下 [新增] 按鈕。

    ![DNS edit button][2]

    -   新增 CNAME 記錄時，您必須先在 [管理 DNS] 頁面上選取 [CNAME (別名)]。然後將 [主機] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [別名主機名稱] 欄位設定為您要在 Azure 網站中使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。然後提供 [存留時間 (TTL)] 值，例如 1800 秒。

        > [WACOM.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。

## <a name="enabledomain"></a>啟用流量管理員網站

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [DomainDiscover.com]: https://domaindiscover.com
  [TierraNet]: https://www.tierra.net/
  [了解 DNS 記錄]: #understanding-records
  [將網站設定為標準模式]: #bkmk_configsharedmode
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [啟用網站的流量管理員]: #enabledomain
  [DomainDiscover Login Menu]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Domain management page]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [DNS edit button]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png
