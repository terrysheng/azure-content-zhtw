<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="設定 Azure 網站的 Moniker 網域名稱" metaKeywords="Azure, Azure 網站, 網域名稱" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#設定 Azure 網站的自訂網域名稱 (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites" class="current">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [Moniker.com](https://moniker.com) 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為基本、共用或標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [在網站上啟用網域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>將網站設定為基本、共用或標準模式</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 Moniker 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 Moniker.com 中，使用下列步驟來尋找 DNS 工具

1. 在 Moniker.com 中登入您的帳戶，並選取 [**我的網域**]，然後按一下 [**管理範本**]。

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. 在 [**Zone Template Management**] 頁面上，選取 [**建立新範本**]。

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. 填入 [**範本名稱**]。 

5. 接著建立 DNS 記錄，方法是首先選取 [**記錄類型**]。然後填入 [**主機名稱**] 和 [**位址**]。 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    * 新增 CNAME 記錄時，您必須將 [**主機名稱**] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [**位址**] 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

    * 新增 A 記錄時，您必須將 [**主機名稱**] 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)，或您要使用的子網域 (例如 **www**)。您必須將 [**位址**] 欄位設定為您 Azure 網站的 IP 位址。

		> [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
		> 
		> ***www** 的 [**主機名稱**] 值加上 **&lt;yourwebsitename&gt;.azurewebsites.net** 的 [**位址**] 值。
		> 
		> 或
		> 
		> ***awverify.www** 的 [**主機名稱**] 值加上 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** 的 [**位址**] 值。
		> 
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

7. 按一下 [**新增**] 按鈕以新增項目。

8. 在新增所有項目之後，按一下 [**儲存**] 按鈕。

5. 選取 [**網域管理員**] 以返回您的 [網域] 清單。

6. 勾選目標網域的核取方塊，然後再按一下 [**管理範本**]。

7. 尋找您在前一個步驟中建立的新範本。然後按一下 [**place selected domains (1) into this Template**] 連結。

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
