<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# 使用流量管理員設定 Azure 網站的自訂網域名稱 (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/zh-TW/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-TW/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name/" title="網站">網站</a> | <a href="/zh-TW/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="使用流量管理員的網站" class="current">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供使用 Azure 網站，從 [eNom][eNom] 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄][了解 DNS 記錄]
-   [將網站設定為標準模式][將網站設定為標準模式]
-   [新增自訂網域的 DNS 記錄][新增自訂網域的 DNS 記錄]
-   [啟用網站的流量管理員][啟用網站的流量管理員]

## <a name="understanding-records"></a>了解 DNS 記錄

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>將網站設定為標準模式

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## 新增自訂網域的 DNS 記錄

</p>
若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 eNom 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 enom.com 中，使用下列步驟來尋找 DNS 工具

1.  在 eNom 中登入您的帳戶，並依序選取 [網域]、[我的網域]。如此會顯示您的網域名稱。

2.  在 [我的網域] 頁面中，使用 [管理網域] 欄位來選取 [主機記錄]。如此會顯示主機記錄欄位。

    ![DNS Zone File tab][DNS Zone File tab]

3.  [主機記錄] 編輯器可讓您使用 [記錄類型] 欄位，來選取特定記錄類型。若是使用流量管理員的 Azure 網站，您只能使用 [CNAME (別名)] 選項，因為流量管理員僅適用於 CNAME 記錄。

    ![zone file editor][zone file editor]

    -   新增 CNAME 記錄時，您必須將 [主機名稱] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [位址] 欄位設定為 Azure 網站所用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。

4.  當您完成新增或修改記錄時，請按一下 [儲存] 以儲存變更。

## <a name="enabledomain"></a>啟用流量管理員網站

[WACOM.INCLUDE [modes][1]]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [eNom]: https://enom.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [了解 DNS 記錄]: #understanding-records
  [將網站設定為標準模式]: #bkmk_configsharedmode
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [啟用網站的流量管理員]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [DNS Zone File tab]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [zone file editor]: ./media/web-sites-custom-domain-name/e-editrecordstm.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
