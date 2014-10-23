<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# 使用流量管理員設定 Azure 網站的自訂網域名稱 (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="網站">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="使用流量管理員的網站" class="current">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

本文提供使用 Azure 網站，從 [Register.com][1] 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [tmwebsitefooter][]]

[WACOM.INCLUDE [introfooter][]]

本文內容：

-   [了解 DNS 記錄][]
-   [將網站設定為標準模式][]
-   [新增自訂網域的 DNS 記錄][]
-   [啟用網站的流量管理員][]

## <a name="understanding-records"></a>了解 DNS 記錄

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>將網站設定為標準模式

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## 新增自訂網域的 DNS 記錄

</p>
若要將您的自訂網域與 Azure 網站建立關聯，必須使用 Register.com 提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找並使用 DNS 工具。

1.  在 register.com 上登入您的帳戶，並選取右上角的 [您的帳戶] 來檢視網域，然後選取您的自訂網域名稱。

    ![the my account page][]

2.  向下捲動頁面，直到出現 [進階技術設定]。本區段中的連結可讓您管理網域的記錄。若為 CNAME 記錄，請使用 [編輯網域別名記錄] 連結。

    ![Advanced technical settings][]

3.  按一下 [編輯] 按鈕時會出現表單，可讓您用來修改現有的記錄或新增記錄。CNAME 和 A 記錄有類似的格式。

    -   新增 CNAME 記錄時，必須將 [.mydomainname.com] 欄位設為要使用的子網域。例如 **www**。您必須將正在使用 Azure 網站之流量管理員設定檔的 [.trafficmanager.net] 網域名稱，選取為 [指向] 值。例如 **contoso.trafficmanager.net**。保留 [參照主機名稱] 為 [選取]，因此在建立與 Azure 網站搭配使用的 CNAME 記錄時不需要此欄位。

        ![cname form][]

4.  完成記錄新增或修改後，按一下 [繼續] 檢閱變更。再次選取 [繼續] 儲存變更。

## <a name="enabledomain"></a>啟用流量管理員網站

[WACOM.INCLUDE [modes][2]]

  [自訂網域]: /zh-tw/documentation/articles/web-sites-custom-domain-name "自訂網域"
  [GoDaddy]: /zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [網路解決方案]: /zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案"
  [Register.com]: /zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /zh-tw/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /zh-tw/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /zh-tw/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /zh-tw/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [網站]: /zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "網站"
  [使用流量管理員的網站]: /zh-tw/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "使用流量管理員的網站"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.register.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [了解 DNS 記錄]: #understanding-records
  [將網站設定為標準模式]: #bkmk_configsharedmode
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [啟用網站的流量管理員]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [the my account page]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Advanced technical settings]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png
  [cname form]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
