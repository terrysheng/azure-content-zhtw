<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="使用流量管理員設定 Azure 網站的 GoDaddy 網域名稱" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#使用流量管理員設定 Azure 網站的自訂網域名稱 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [簡介](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供使用 Azure 網站，從 [Go Daddy](https://godaddy.com) 購買的自訂網域名稱的使用指示。

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

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 GoDaddy 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 GoDaddy.com 中，使用下列步驟來尋找 DNS 工具

1. 在 GoDaddy.com 中登入您的帳戶，並依序選取 [**我的帳戶**]、[**管理您的網域**]。最後，選取您要在 Azure 網站中使用的網域名稱。

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. 從 [**網域詳細資料**] 頁面中，選取 [**DNS 區域檔案**] 索引標籤。此區段可用來新增與修改網域名稱的 DNS 記錄。選取 [**編輯**] 按鈕，以顯示 [**Zone File Editor**]。

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. [**Zone File Editor**] 會依每個記錄類型被分為不同區段，從 A 記錄開始 (列為 **A (主機)**)，接著是 CNAME 記錄 (列為 **CNAME (別名)**)。若要新增項目，請使用對應區段下方的 [**快速新增**] 按鈕。若要編輯現有項目，請選取該項目並修改現有資訊。

	![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [WACOM.NOTE] 在區域檔案新增項目之前，請注意，GoDaddy 已為熱門子網域 (在編輯器中稱為**主機**) 建立 DNS 記錄，例如**電子郵件**、**檔案**、**郵件**及其他。如果您要使用的名稱已存在，請修改現有記錄，而非建立新記錄。

新增 CNAME 記錄時，您必須將 [**主機**] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [**指向**] 欄位設定為 Azure 網站所用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。

	> [WACOM.NOTE] 當將您的自訂網域名稱與使用流量管理員進行負載平衡的網站建立關聯時，您只能使用 CNAME 記錄。

5. 當您完成新增或修改記錄時，請按一下 [**Save Zone File**] 以儲存變更。

<h2><a name="enabledomain"></a>啟用流量管理員網站</h2>

[WACOM.INCLUDE [模式](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
