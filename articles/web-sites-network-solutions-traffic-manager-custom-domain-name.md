<properties 
	pageTitle="針對使用流量管理員的 Azure 網站設定 Network Solutions 網域名稱" 
	description="了解如何使用來自 GoDaddy 的網域名稱搭配 Azure 網站" 
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

#使用流量管理員設定 Azure 網站的自訂網域名稱 (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">使用流量管理員的網站</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

此文章提供有關使用從 [Network Solutions](https://www.networksolutions.com) 購買的自訂網域名稱搭配 Azure 網站的指示。

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

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 Network Solutions 所提供的工具，在 DNS 表格中為您的自訂網域新增項目。使用下列步驟來尋找並使用 DNS 工具。

1. 在 networksolutions.com 上登入您的帳戶，並選取右上角的 [My Account]**** (我的帳戶)。

3. 在 [My Products and Services]**** (我的產品和服務) 索引標籤中，選取 [Edit DNS]**** (編輯 DNS)。

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. 從 [Domain Names]**** (網域名稱頁面) 上的 [Manage <yourdomainname>]**** (管理 <您的網域名稱>) 區段，選取 [Edit Advanced DNS Records]**** (編輯進階 DNS 記錄)。

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. [Update Advanced DNS]**** (更新進階 DNS) 頁面包含每個記錄類型的區段，每個區段底下都有 [Edit]**** (編輯) 按鈕。對於 CNAME 記錄，請使用 [Host Alias (CNAME Records)]**** (主機別名 (CNAME 記錄)) 區段。

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5. 按一下 [Edit]**** (編輯) 按鈕時會出現表單，可讓您用來修改現有的記錄或新增記錄。 

	新增 CNAME 記錄時，您必須將 [Alias]**** (別名) 欄位設定為您要使用的子網域。例如 **www**。您必須選取 [Other host]**** (其他主機) 欄位旁的圓形欄位，並將 [Other host]**** (其他主機) 設定搭配您的 Azure 網站使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如，**contoso.trafficmanager.net**。將 [Refers to Host Name]**** (參照主機名稱) 保留為 [Select]**** (選取)，因為在建立與 Azure 網站搭配使用的 CNAME 記錄時不需要此欄位。
	
	![cname form](./media/web-sites-custom-domain-name/ns-cnametm.png)

5. 完成記錄新增或修改後，請按一下 [Continue]**** (繼續) 以檢閱變更。選取 [Save changes only]**** (僅儲存變更) 以儲存變更。

<h2><a name="enabledomain"></a>啟用流量管理員網站</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
