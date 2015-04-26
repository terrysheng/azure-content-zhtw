<properties 
	pageTitle="針對使用流量管理員的 Azure 網站設定 Moniker 網域名稱" 
	description="了解如何設定使用流量管理員的 Azure 網站，以使用向 Moniker 註冊的網域名稱" 
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

#針對使用流量管理員的 Azure 網站設定自訂網域名稱 (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">使用流量管理員的網站</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

此文章提供有關使用從 [Moniker](https://moniker.com) 購買的自訂網域名稱搭配 Azure 網站的指示。

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

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 Moniker 所提供的工具，在 DNS 表格中為您的自訂網域新增項目。在 Moniker.com 中，使用下列步驟來尋找 DNS 工具

1. 在 Moniker.com 中登入您的帳戶，並選取 [My Domains]**** (我的網域)，然後按一下 [Manage Templates]**** (管理範本)。

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. 在 [Zone Template Management]**** (區域範本管理) 頁面上，選取 [Create New Template]**** (建立新範本)。

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. 填入 [Template Name]**** (範本名稱)。 

5. 接著，建立 DNS 記錄，方法是先選取 [Record Type]**** (記錄類型)。然後填入 [Hostname]**** (主機名稱) 和 [Address]**** (位址)。 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * 新增 CNAME 記錄時，您必須將 [Hostname]**** (主機名稱) 欄位設定為您要使用的子網域。例如 **www**。您必須將 [Address]**** (位址) 欄位設定為您要搭配 Azure 網站使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如，**contoso.trafficmanager.net**。

	    > [AZURE.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。
	 
7. 按一下 [Add]**** (新增) 按鈕以新增項目。

8. 在新增所有項目之後，按一下 [Save]**** (儲存) 按鈕。

5. 選取 [Domain Manager]**** (網域管理員) 以返回您的 [Domains] (網域) 清單。

6. 選取您目標網域的核取方塊，然後按一下 [Manage Templates]**** (管理範本)。

7. 尋找您在前一個步驟中建立的新範本。然後按一下 [place selected domains (1) into this Template]**** (將選取的網域 (1) 放入此範本) 連結。

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>啟用流量管理員網站</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
