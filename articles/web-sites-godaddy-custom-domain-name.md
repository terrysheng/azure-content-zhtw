<properties 
	pageTitle="設定 Azure 網站的 GoDaddy 網域名稱" 
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

#設定 Azure 網站的自訂網域名稱 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">網路解決方案</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">使用流量管理員的網站</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

此文章提供有關使用從 [GoDaddy](https://godaddy.com) 購買的自訂網域名稱搭配 Azure 網站的指示。

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [在網站上啟用網域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 GoDaddy 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 GoDaddy.com 中，使用下列步驟來尋找 DNS 工具

1. 在 GoDaddy.com 中登入您的帳戶，並依序選取 [我的帳戶]****、[管理我的網域]****。最後，在下拉式功能表中選取您要與 Azure 網站搭配使用的網域名稱，然後選取 [管理 DNS]****。

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. 在 [網域詳細資料]**** 頁面中，捲動至 [DNS 區域檔案]**** 索引標籤。此區段可用來新增與修改網域名稱的 DNS 記錄。 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	選取 [新增記錄]**** 以新增現有記錄。

	若要 [編輯]**** 現有記錄，請選取該記錄旁的 [紙筆] 圖示。

	> [AZURE.NOTE] 新增記錄之前，請注意，GoDaddy 已為熱門子網域 (在編輯器中稱為 [主機]****) 建立 DNS 記錄，例如 [電子郵件]****、[檔案]****、郵件****及其他。如果您要使用的名稱已存在，請修改現有記錄，而非建立新記錄。

4. 新增記錄時，您必須先選取記錄類型。

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	接下來，您必須提供 [主機]**** (自訂網域或子網域) 及其 [指向]**** 位置。

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* 新增 [A (主機) 記錄]**** 時，您必須將 [主機]**** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)、* (符合多個子網域的萬用字元)，或您要使用的子網域 (例如 **www**)。您必須將 [points to]**** (指向) 欄位設定為您 Azure 網站的 IP 位址。
	
		> [AZURE.NOTE] 用 [A (主機) 記錄] 時，您也必須透過下列設定新增 CNAME 記錄：
		> 
		> * **awverify** 的 [主機]**** 值，該 awverify 會 [Points to]**** (指向) **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** 的值。
		> 
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

	* 新增 [CNAME (別名) 記錄]**** - 您必須將 [主機]**** 欄位設定為您要使用的子網域。例如，**www**。您必須將 Azure 網站的 **.azurewebsites.net** 網域名稱設定為 [Points to]**** (指向) 值。例如，**contoso.azurwebsites.net**。


5. 當您完成新增或修改記錄時，請按一下 [完成]**** 以儲存變更。

<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>

[AZURE.INCLUDE [模式](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->
