<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# 設定 Azure 網站的自訂網域名稱 (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-TW/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-TW/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="網站" class="current">網站</a> | <a href="/zh-TW/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="使用流量管理員的網站">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

本文提供使用 Azure 網站，從 [DomainDiscover.com][] 購買的自訂網域名稱的使用指示。DomainDiscover.com 是 TierraNet 的一部分。

[WACOM.INCLUDE [introfooter][]]

本文內容：

-   [了解 DNS 記錄][]
-   [將網站設定為基本、共用或標準模式][]
-   [新增自訂網域的 DNS 記錄][]
-   [在網站上啟用網域][]

## <a name="understanding-records"></a>了解 DNS 記錄

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>將網站設定為基本、共用或標準模式

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## 新增自訂網域的 DNS 記錄

</p>
若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 DomainDiscover 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 DomainDiscover.com 中，使用下列步驟來尋找 DNS 工具

1.  在 DomainDiscover.com (TierraNet) 中登入您的帳戶，方法是從 [登入] 功能表中選取 [控制台]。

    ![DomainDiscover Login Menu][]

2.  在 [網域服務] 頁面上，選取要在您的 Azure 網站上使用的網域。

    ![Domain management page][]

3.  在 [網域] 設定的 [DNS 服務] 中，按一下 [編輯] 按鈕。

    ![DNS edit button][]

4.  在 [管理 DNS] 視窗中，選取要在 [新增記錄] 清單中新增的 DNS 記錄類型。然後按一下 [新增] 按鈕。

    ![DNS edit button][1]

5.  在下列頁面上，輸入 DNS 記錄值。然後按一下 [新增] 按鈕。

    ![DNS edit button][2]

    -   新增 CNAME 記錄時，您必須先在 [管理 DNS] 頁面上選取 [CNAME (別名)]。然後將 [主機] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [別名主機名稱] 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。然後提供 [存留時間 (TTL)] 值，例如 1800 秒。

    -   新增 A 記錄時，您必須先在 [管理 DNS] 頁面上選取 [A]。然後將 [主機] 欄位設定為 <**@>** (這代表根網域名稱，例如 **contoso.com**)，或您要使用的子網域 (例如 **www**)。您必須將 [IP 位址] 欄位設定為您 Azure 網站的 IP 位址。然後提供 [存留時間 (TTL)] 值，例如 1800 秒。

        > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
        >
        > -   **www** 的 [主機] 值加上 **\<yourwebsitename\>.azurewebsites.net** 的 [Alias Hostname] 值。
        >
        > 或
        >
        > -   **awverify.www** 的 [主機] 值加上 **awverify.\<yourwebsitename\>.azurewebsites.net** 的 [Alias Hostname] 值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

6.  如果您新增一筆 A 記錄，您可能會收到警告，指出網域的現有 A 記錄仍然有效。網域會使用最近變更的記錄，且 DomainDirect 的根網域名稱已有預設的 A 記錄。您可以仰賴此優先順序，或者您可以透過選取 [刪除] 按鈕來移除預設的 A 記錄。

## <a name="enabledomain"></a>在網站上啟用網域名稱

[WACOM.INCLUDE [modes][3]]

  [自訂網域]: /zh-TW/documentation/articles/web-sites-custom-domain-name "自訂網域"
  [GoDaddy]: /zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [網路解決方案]: /zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案"
  [Register.com]: /zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /zh-TW/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /zh-TW/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /zh-TW/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /zh-TW/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [網站]: /zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "網站"
  [使用流量管理員的網站]: /zh-TW/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "使用流量管理員的網站"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [DomainDiscover.com]: https://domaindiscover.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [了解 DNS 記錄]: #understanding-records
  [將網站設定為基本、共用或標準模式]: #bkmk_configsharedmode
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [在網站上啟用網域]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [DomainDiscover Login Menu]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Domain management page]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [DNS edit button]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png
  [3]: ../includes/custom-dns-web-site-enable-on-web-site.md
