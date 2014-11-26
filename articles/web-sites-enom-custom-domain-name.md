<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# 設定 Azure 網站的自訂網域名稱 (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/zh-TW/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-TW/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name/" title="網站" class="current">網站</a> | <a href="/zh-TW/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="使用流量管理員的網站">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [eNom][eNom] 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄][了解 DNS 記錄]
-   [將網站設定為基本、共用或標準模式][將網站設定為基本、共用或標準模式]
-   [新增自訂網域的 DNS 記錄][新增自訂網域的 DNS 記錄]
-   [在網站上啟用網域][在網站上啟用網域]

## <a name="understanding-records"></a>了解 DNS 記錄

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>將網站設定為基本、共用或標準模式

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## 新增自訂網域的 DNS 記錄

</p>
若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 eNom 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 enom.com 中，使用下列步驟來尋找 DNS 工具

1.  在 eNom 中登入您的帳戶，並依序選取 [網域]、[我的網域]。如此會顯示您的網域名稱。

2.  在 [我的網域] 頁面中，使用 [管理網域] 欄位來選取 [主機記錄]。如此會顯示主機記錄欄位。

    ![DNS Zone File tab][DNS Zone File tab]

3.  [主機記錄] 編輯器可讓您使用 [記錄類型] 欄位，來選取特定記錄類型。若是 Azure 網站，您只能使用 [CNAME (別名)] 或 [A (位址)] 選項。

    ![zone file editor][zone file editor]

    > [WACOM.NOTE] 在將項目新增至區域檔案之前，請注意，eNom 已為根網域建立 DNS 記錄 (<'@'>)，並為子網域建立萬用字元 ('\*')。如果您希望將根網域重新導向至網站，或使用萬用字元 A 記錄，則您應修改這些項目，而非建立新項目。

    -   新增 CNAME 記錄時，您必須將 [主機名稱] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [位址] 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

        > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
        >
        > -   [www] 的 [別名] 值，包含 [\<yourwebsitename\>.azurewebsites.net] 的 [其他主機] 值。
        >
        > 或
        >
        > -   [awverify.www] 的 [別名] 值，包含 [awverify.\<yourwebsitename\>.azurewebsites.net] 的 [其他主機] 值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域。

    -   新增 A 記錄時，您必須將 [主機名稱] 欄位設定為 <**@*>\* (這代表根網域名稱，例如 **contoso.com**)、\* (符合多個子網域的萬用字元)，或您要使用的特定子網域 (例如 **www**)。您必須將 [位址] 欄位設定為您 Azure 網站的 IP 位址。

        > [WACOM.NOTE] 新增 A 記錄時，您還必須新增使用 **awverify** 主機的 CNAME 記錄，並將 [指向] 設定為 \*\*awverify.\<yourwebsitename\>.azurewebsites.net。

4.  當您完成新增或修改記錄時，請按一下 [儲存] 以儲存變更。

## <a name="enabledomain"></a>在網站上啟用網域名稱

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [eNom]: https://enom.com
  [了解 DNS 記錄]: #understanding-records
  [將網站設定為基本、共用或標準模式]: #bkmk_configsharedmode
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [在網站上啟用網域]: #enabledomain
  [DNS Zone File tab]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [zone file editor]: ./media/web-sites-custom-domain-name/e-editrecords.png
