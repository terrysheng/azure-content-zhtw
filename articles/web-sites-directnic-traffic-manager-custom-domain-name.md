<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="使用流量管理員設定 Azure 網站的 Directnic 網域名稱 "metaKeywords ="Windows Azure, Windows Azure 網站, Directnic, 流量管理員" description="了解如何設定使用 Traffic Manager 的 Azure 網站使用向 Directnic 註冊的網域名稱" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#使用流量管理員來設定 Windows Azure 網站的自訂網域名稱 (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [簡介](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供使用 Azure 網站，從 [DirectNic.com](https://directnic.com) 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [啟用網站的流量管理員](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>將網站設定為標準模式</h2>

[WACOM.INCLUDE [模式](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 Directnic 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找 Directnic.com 的 DNS 工具

1. 在 Directnic.com 中登入您的帳戶，並依序選取 [**我的服務**]、[**網域**]。 

    ![Directnic Services Menu](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. 按一下您要在 Azure 網站中使用的網域名稱。

2. 在網域的管理頁面上，在 [**服務**] 窗格的 [**DNS**] 中，按一下 [**管理**] 按鈕。

    ![Services Panel](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. 透過填入 [**類型**]、[**名稱**] 和 [**資料**] 欄位新增 DNS 記錄。完成時，請按一下 [**新增記錄**] 按鈕。

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    * 新增 CNAME 記錄時，您必須將 [**名稱**] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [**資料**] 欄位設定為您要在 Azure 網站中使用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。

	    > [WACOM.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。

<h2><a name="enabledomain"></a>啟用流量管理員網站</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
