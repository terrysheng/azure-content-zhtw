<properties 
	pageTitle="設定 Azure 網站的 Network Solutions 網域名稱" 
	description="了解如何使用來自 Network Solutions 的網域名稱搭配 Azure 網站" 
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

#設定 Azure 網站的自訂網域名稱 (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="網站" class="current">網站</a> | <a href="/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="使用流量管理員的網站">使用流量管理員的網站</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [簡介](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [Network Solutions](https://www.networksolutions.com) 購買的自訂網域名稱的使用指示。

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為基本、共用或標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [在網站上啟用網域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>將網站設定為基本、共用或標準模式</h2>

[AZURE.INCLUDE [模式](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 Network Solutions 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找並使用 networksolutions.com 的 DNS 工具

1. 在 networksolutions.com 上登入您的帳戶，並選取右上角的 [我的帳戶]****。

3. 在 [我的產品和服務]**** 索引標籤中，選取 [編輯 DNS]****。

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. 從 [網域名稱]**** 頁面的**<yourdomainname> [管理 ]** 區段中，選取 [編輯進階 DNS 記錄]****。

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. [更新進階 DNS]**** 頁面包含每個記錄類型的區段，每個區段底下都有 [編輯]**** 按鈕。
	
	* 若為 A 記錄，請使用 [IP Address (A Records)]**** 區段。
	* 若為 CNAME 記錄，請使用 [Host Alias (CNAME Records)]**** 區段。

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. 按一下 [編輯]**** 按鈕時會出現表單，可讓您用來修改現有的記錄或新增記錄。

	> [AZURE.NOTE]新增項目前，請注意 Network Solutions 已為某些項目 (如根網域 ('@')，和子網域的萬用字元記錄 ('*')) 建立一些預設 DNS 記錄。若要使用的記錄已存在，請修改記錄而非建立新記錄。

	* 新增 CNAME 記錄時，必須將 [別名]**** 欄位設為要使用的子網域。例如 **www**。您必須選取 [其他主機]**** 欄位旁的圓形欄位，並將 [其他主機]**** 設為 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。保留 [參照主機名稱]**** 為 [選取]****，因此在建立與 Azure 網站搭配使用的 CNAME 記錄時不需要此欄位。
	
		![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

		> [AZURE.NOTE]如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
		> 
		> * [www]**** 的 [別名]**** 值，包含 [&lt;yourwebsitename&gt;.azurewebsites.net]**** 的 [其他主機]**** 值。
		> 
		> 或
		> 
		> * [awverify.www]**** 的 [別名]**** 值，包含 [awverify.&lt;yourwebsitename&gt;.azurewebsites.net]**** 的 [其他主機]**** 值。
		> 
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域。

	* 新增 A 記錄時，您必須將 [主機]**** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)、* (符合多個子網域的萬用字元)，或您要使用的子網域 (例如 **www**)。 您必須將 [數值 IP]**** 欄位設為 Azure 網站的 IP 位址。

		![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

5. 完成記錄新增或修改後，按一下 [繼續]**** 檢閱變更。選取 [僅儲存變更]**** 以儲存變更。

<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>

[AZURE.INCLUDE [模式](../includes/custom-dns-web-site-enable-on-web-site.md)]

<!--HONumber=54-->