<properties 
	pageTitle="設定 Azure 網站的 Directnic 網域名稱" 
	description="了解如何設定 Azure 網站使用向 Directnic 註冊的網域名稱" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr,jroth"/>

#設定 Azure 網站的自訂網域名稱 (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">網路解決方案</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites" class="current">網站</a> |  <a href="/zh-tw/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用流量管理員的網站</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]


[AZURE.INCLUDE [簡介](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [DirectNic.com](https://directnic.com) 購買的自訂網域名稱的使用指示。

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容:

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為基本、共用或標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [在網站上啟用網域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>將網站設定為基本、共用或標準模式</h2>

[AZURE.INCLUDE [模式](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 Directnic 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找 Directnic.com 的 DNS 工具

1. 在 Directnic.com 中登入您的帳戶，並依序選取 **[我的服務]**、**[網域]**。

    ![Directnic 服務功能表](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. 按一下您要在 Azure 網站中使用的網域名稱。

2. 在您的網域管理頁面上，在 **[服務]** 窗格的 **[DNS]** 中按一下 **[管理]** 按鈕。

    ![服務面板](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. 透過填入 **[類型]**、**[名稱]** 和 **[資料]** 等欄位來新增 DNS 記錄。完成時，請按一下 **[新增記錄]** 按鈕。   

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * 新增 CNAME 記錄時，您必須將 **[名稱]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[資料]** 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

    * 新增 A 記錄時，您必須將 **[名稱]** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)，或您要使用的子網域 (例如 **www**)。您必須將 **[資料]** 欄位設定為您 Azure 網站的 IP 位址。

		> [AZURE.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
		> 
		> * **www** 的 **[名稱]** 值加上 **&lt;yourwebsitename&gt;.azurewebsites.net** 的 **[資料]** 值。
		> 
		> 或
		> 
		> * **awverify.www** 的 **[名稱]** 值加上 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** 的 **[資料]** 值。
		> 
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域


<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>

[AZURE.INCLUDE [模式](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->
