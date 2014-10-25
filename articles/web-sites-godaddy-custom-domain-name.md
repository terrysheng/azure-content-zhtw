<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# 設定 Azure 網站的自訂網域名稱 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/web-sites-custom-domain-name" title="自訂網域">自訂網域</a><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案" class="current">網路解決方案</a><a href="/zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/zh-TW/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/zh-TW/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/zh-TW/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/zh-TW/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name/" title="網站" class="current">網站</a> | <a href="/zh-TW/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="使用流量管理員的網站">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

本文針對透過 Azure 網站在 [Go Daddy][] 中購買的自訂網域名稱，提供使用的相關指示。

[WACOM.INCLUDE [introfooter][]]

本文內容：

-   [了解 DNS 記錄][]
-   [新增自訂網域的 DNS 記錄][]
-   [在網站上啟用網域][]

## <a name="understanding-records"></a>了解 DNS 記錄

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configurecname"></a>新增自訂網域的 DNS 記錄

若要將您的自訂網域與 Azure 網站相關聯，您必須使用由 GoDaddy 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 GoDaddy.com 中，使用下列步驟來尋找 DNS 工具

1.  在 GoDaddy.com 中登入您的帳戶，並依序選取 [我的帳戶]、[管理我的網域]。最後，在下拉式功能表中選取您要與 Azure 網站搭配使用的網域名稱，然後選取 [管理 DNS]。

    ![custom domain page for GoDaddy][]

2.  在 [網域詳細資料] 頁面中，捲動至 [DNS 區域檔案] 索引標籤。此區段可用來新增與修改網域名稱的 DNS 記錄。

    ![DNS Zone File tab][]

    選取 [新增記錄] 以新增現有記錄。

    若要 [編輯] 現有記錄，請選取該記錄旁的 [紙筆] 圖示。

    > [WACOM.NOTE] 新增記錄之前，請注意，GoDaddy 已為熱門子網域 (在編輯器中稱為 [主機]) 建立 DNS 記錄，例如**電子郵件**、**檔案**、**郵件**及其他。如果您要使用的名稱已存在，請修改現有記錄，而非建立新記錄。

3.  新增記錄時，您必須先選取記錄類型。

    ![選取記錄類型][]

    接下來，您必須提供 [主機] (自訂網域或子網域) 及其 [指向] 位置。

    ![新增區域記錄][]

    -   新增 [A (主機) 記錄] 時，您必須將 [主機] 欄位設定為 <**@**> (這代表根網域名稱，例如 **contoso.com**)、\* (符合多個子網域的萬用字元)，或您要使用的子網域 (例如 **www**)。您必須將 [指向] 欄位設定為您 Azure 網站的 IP 位址。

        > [WACOM.NOTE] 使用 [A (主機) 記錄] 時，您也必須透過下列設定新增 CNAME 記錄：
        >
        > -   **awverify** 的 [主機] 值，該 awverify 會 [指向] **awverify.\<yourwebsitename\>.azurewebsites.net** 的值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

    -   新增 [CNAME (別名) 記錄] 時，您必須將 [主機] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [指向] 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

4.  當您完成新增或修改記錄時，請按一下 [完成] 以儲存變更。

## <a name="enabledomain"></a>在網站上啟用網域名稱

[WACOM.INCLUDE [modes][]]

  [自訂網域]: /zh-TW/documentation/articles/web-sites-custom-domain-name "自訂網域"
  [GoDaddy]: /zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [網路解決方案]: /zh-TW/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案"
  [Register.com]: /zh-TW/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /zh-TW/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /zh-TW/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /zh-TW/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /zh-TW/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /zh-TW/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [網站]: /zh-TW/documentation/articles/web-sites-godaddy-custom-domain-name/ "網站"
  [使用流量管理員的網站]: /zh-TW/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "使用流量管理員的網站"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Go Daddy]: https://godaddy.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [了解 DNS 記錄]: #understanding-records
  [新增自訂網域的 DNS 記錄]: #bkmk_configurecname
  [在網站上啟用網域]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [custom domain page for GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [DNS Zone File tab]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [選取記錄類型]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [新增區域記錄]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
  [modes]: ../includes/custom-dns-web-site-enable-on-web-site.md
