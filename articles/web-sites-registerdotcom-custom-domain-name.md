<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="設定 Azure 網站的 Register.com 網域名稱" metaKeywords="Azure, Azure 網站, 網域名稱" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#設定 Azure 網站的自訂網域名稱 (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [Register.com](https://www.register.com) 購買的自訂網域名稱的使用指示。

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

若要將您的自訂網域與 Azure 網站建立關聯，必須使用 Register.com 提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找並使用 DNS 工具。

1. 在 register.com 上登入您的帳戶，並選取右上角的 [**您的帳戶**] 來檢視網域，然後選取您的自訂網域名稱。

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. 向下捲動頁面，直到出現 [**進階技術設定**]。本區段中的連結可讓您管理網域的記錄。

	* 若為 A 記錄，請使用 [**編輯 IP 位址記錄**] 連結。
	* 若為 CNAME 記錄，請使用 [**編輯網域別名記錄**] 連結。

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. 按一下 [**編輯**] 按鈕時會出現表單，可讓您用來修改現有的記錄或新增記錄。CNAME 和 A 記錄有類似的格式。

	* 新增 CNAME 記錄時，您必須將 [**.mydomainname.com**] 欄位設定為您要使用的子網域。例如 **www**。您必須將 Azure 網站的 [**.azurewebsites.net**] 網域名稱選取為 [**指向**] 值。例如 **contoso.azurwebsites.net**。保留 [**參照主機名稱**] 為 [**選取**]，因此在建立與 Azure 網站搭配使用的 CNAME 記錄時不需要此欄位。
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
		> 
		> ***www** 的 [**別名**] 值加上 **&lt;yourwebsitename&gt;.azurewebsites.net** 的 [**其他主機**] 值。
		> 
		> 或
		> 
		> * **awverify.www** 的 [**別名**] 值加上 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** 的 [**其他主機**] 值。
		> 
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域。

	* 新增 A 記錄時，必須將 [**.mydomainname.com**] 欄位設為要使用的子網域 (例如，**www**)。留空欄位以設定根網域，或使用和 **\** 以建立萬用字元對應。您必須將 [**指向**] 欄位設定為您 Azure 網站的 IP 位址。

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. 完成記錄新增或修改後，按一下 [**繼續**] 檢閱變更。再次選取 [**繼續**] 儲存變更。

<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
