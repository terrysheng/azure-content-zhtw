<properties title="Learn how to configure an Azure website to use a domain name registered with Dotster" pageTitle="設定 Azure 網站的 Dotster 網域名稱" metaKeywords="Azure, Azure 網站, Dotster" description="Learn how to configure an Azure website to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#設定 Azure 網站的自訂網域名稱 (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites" class="current">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [Dotster.com](https://dotster.com) 購買的自訂網域名稱的使用指示。

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

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 Dotster 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 Dotster.com 中，使用下列步驟來尋找 DNS 工具

1. 在 Dotster.com 中登入您的帳戶。在 [**網域**] 功能表中，選取 [**DomainCentral**]。

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. 選取您的網域以帶出設定清單。然後選取 [**Nameservers**] 連結。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. 選取 [**Use different name servers**]。為了要充分運用 Dotster 上的 DNS 服務，您必須指定下列名稱伺服器：ns1.nameresolve.com、ns2.nameresolve.com、ns3.nameresolve.com 和 ns4.nameresolve.com。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] 需要 24-48 小時的時間才能讓名稱伺服器變更生效。本文的其他步驟無法執行，直到變更生效為止。

4. 在 DomainCentral 中，選取您的網域，然後選取 [**DNS**]。在 [**修改**] 清單中，選取要新增的 DNS 記錄類型 ([**CNAME 別名**] 或 [**A 記錄**])。 

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. 然後指定記錄的 [**主機**] 和 [**指向**] 欄位。完成時，請按一下 [**新增**] 按鈕。

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)
 
    * 新增 CNAME 記錄時，您必須將 [**主機**] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [**指向**] 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

    * 新增 A 記錄時，您必須將 [**主機**] 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)，或您要使用的子網域 (例如 **www**)。您必須將 [**指向**] 欄位設定為您 Azure 網站的 IP 位址。

		> [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
		> 
		> * [**主機**] 值為 **www**，[**指向**] 值為 **&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> 或
		> 
		> * [**主機**] 值為 **awverify.www**，[**指向**] 值為 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域


<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
