<properties 
	pageTitle="針對使用流量管理員的 Azure 網站設定自訂網域名稱" 
	description="" 
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
	ms.author="larryfr, jroth"/>

#針對使用流量管理員的 Azure 網站設定自訂網域名稱

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">網路解決方案</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name/" title="Websites">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">使用流量管理員的網站</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供對 Azure 網站 (使用流量管理員進行負載平衡的網站) 使用自訂網域名稱的一般指示。請檢查本文頂端的索引標籤，看看是否有列出您的網域註冊機構。如果有列出，請選取該索引標籤以取得註冊機構的特定步驟。

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [為您的網站啟用流量管理員](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>將網站設定為標準模式</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站建立關聯，必須在 DNS 表格中為您的自訂網域新增項目，方法是使用您向其購買網域名稱之網域註冊機構所提供的工具。使用下列步驟來尋找並使用 DNS 工具。

1. 在網域註冊機構登入您的帳戶，並尋找用於管理 DNS 記錄的頁面。在網站中尋找標示為 [Domain Name]**** (網域名稱)、[DNS]**** 或 [Name Server Management]**** (名稱伺服器管理) 的連結或區域。通常該頁面的連結可透過檢視您的帳戶資訊，然後尋找「我的網域」****之類的連結來找到。

4. 一旦找到網域名稱的管理頁面，請尋找可讓您編輯 DNS 記錄的連結。這可能會以 [Zone file]**** (區域檔案)、[DNS Records]**** (DNS 記錄) 或 [Advanced]**** (進階) 設定連結的形式列出。

	* 頁面上可能已建立一些記錄，例如將 '**@**' 或 '\*' 與 'domain parking'頁面建立關聯的項目。它也可能包含常見子網域 (例如 **www**) 的記錄。
	* 頁面上會提及「CNAME 記錄」****，或提供選取記錄類型的下拉式清單。它也可能提及其他記錄 (例如「A 記錄」****與「MX 記錄」****。在部分情況下，會以其他名稱來稱呼 CNAME 記錄，例如「別名記錄」****。
	* 頁面上也會有可讓您將「主機名稱」****或「網域名稱」 ******對應**到另一個網域名稱的欄位。

5. 由於每個註冊機構的特殊要求可能有所不同，一般來說，您會 *from*自訂網域名稱 (例如 **contoso.com**) 對應 *to*使用於 Azure 網站的流量管理員網域名稱 (**contoso.trafficmanager.net**)。

6. 在註冊機構處完成新增或修改 DNS 記錄的作業之後，請儲存變更。

<h2><a name="enabledomain"></a>啟用流量管理員網站</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
