<properties title="Learn how to configure an Azure website to use a custom domain name" pageTitle="Configure a custom domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth, mwasson" />

# 設定 Azure 網站的自訂網域名稱

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="自訂網域" class="current">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name/" title="網站" class="current">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="使用流量管理員的網站">使用流量管理員的網站</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

當您建立網站時，Azure 會為該網站指派 azurewebsites.net 的子網域。例如，若網站名為 **contoso**，則 URL 為 **contoso.azurewebsites.net**。Azure 也會指派虛擬 IP 位址。

![contoso.azurewebsites.net 子網域][contoso.azurewebsites.net 子網域]

若是生產的網站，您可能會想讓使用者看到自訂網域名稱。本文說明如何以 Azure 網站設定自訂網域。(本文提供所有網域註冊機構的一般指示。本文最上方的索引標籤為特定註冊機構的部分文章連結。)

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [概觀][概觀]
-   [DNS 記錄類型][DNS 記錄類型]
-   [尋找虛擬 IP 位址][尋找虛擬 IP 位址]
-   [建立 DNS 記錄][建立 DNS 記錄]
-   [建立 “awverify” 記錄 (僅限 A 記錄)][建立 “awverify” 記錄 (僅限 A 記錄)]
-   [在網站上啟用網域名稱][在網站上啟用網域名稱]

## 概觀

以下是設定自訂網域名稱的一般步驟：

1.  保留您的網域名稱。本文不包含此流程。有許多網域註冊機構可供選擇。當您註冊時，這些網站會逐步引導您進行此流程。
2.  建立 DNS 記錄，將網域對應至您的 Azure 網站。
3.  將網域名稱加入 Azure 管理入口網站中。

在這基本大綱中，有一些需要注意的特定情況：

-   對應根網域。根網域是您透過網域註冊機構保留的網域。例如 **contoso.com**。
-   對應子網域。例如 **blogs.contoso.com**。您可以將不同的子網域對應至不同的網站。
-   對應萬用字元。例如 \***.contoso.com**。萬用字元項目會套用至網域的所有子網域。

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## DNS 記錄類型

網域名稱系統 (DNS) 使用資料記錄，將網域名稱對應至 IP 位址。有數種 DNS 記錄。您要為網站建立 *A* 記錄或 *CNAME* 記錄。

-   A **(位址)** 記錄會將網域名稱對應至 IP 位址。
-   **CNAME (正式名稱)** 記錄會將網域名稱對應至其他網域名稱。DNS 會使用第二個名稱查詢位址。使用者仍然可以在瀏覽器中看到第一個網域名稱。例如，您可以將 contoso.com 對應至 *\<yoursite\>*.azurewebsites.net。

若 IP 位址變更，CNAME 項目仍會有效，但 A 記錄就必須更新。不過，有部分網域註冊機構不允許 CNAME 記錄使用根網域或萬用字元網域。若有該情況，則必須使用 A 記錄。

> [WACOM.NOTE] 若您刪除網站後再重新建立，或是將網站模式變更回免費，則 IP 位址可能會變更。

## 尋找虛擬 IP 位址

若您要建立 CNAME 記錄，可略過此步驟。若要建立 A 記錄，您需要網站的虛擬 IP 位址。取得 IP 位址：

1.  在瀏覽器中開啟 [Azure 管理入口網站][Azure 管理入口網站]。
2.  在 [網站] 索引標籤中，按一下網站名稱，並選取 [儀表板]。
3.  選取頁面底部的 [管理網域]。(若此選項為停用，請確認您使用的模式是否為共用、基本或標準模式。如需詳細資訊，請參閱[如何調整網站][如何調整網站]。)

    ![][0]

4.  IP 位址列於接近對話方塊底部的地方。

    ![][1]

## 建立 DNS 記錄

登入網域註冊機構，並使用它們的工具加入 A 記錄或 CNAME 記錄。每個註冊機構的網站都會稍有不同，但以下是部分一般方針。

1.  尋找管理 DNS 記錄的頁面。在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。通常可透過檢視您的帳戶資訊，然後尋找**我的網域**之類的連結來找到此連結。
2.  當您找到管理頁面時，請尋找可讓您加入或編輯 DNS 記錄的連結。這可能會以 **Zone file**、**DNS Records** 或 **Advanced** 組態連結的形式列出。

頁面可能會分別列出 A 記錄與 CNAME 記錄，或提供選取記錄類型的下拉式清單。此外，頁面也可能會使用其他名稱做為記錄類型，例如 **IP 位址記錄**而不是 A 記錄，或使用**別名記錄**而不是 CNAME 記錄。註冊機構通常會為您建立一些記錄，所以可能已經有根網域或一般子網域的記錄，例如 **www**。

當您建立或編輯記錄時，可使用欄位將網域名稱對應至 IP 位址 (針對 A 記錄)，或對應至其他網域 (針對 CNAME 記錄)。若為 CNAME 記錄，您要「從」自訂網域對應「到」azurewebsites.net 子網域。

在許多註冊機構的工具中，您只要輸入網域的子網域部分即可，不必輸入整個網域名稱。此外，許多工具都會使用‘@’表示根網域。例如：

<table cellspacing="0" border="1">
<tr>
<th>主機</th>
<th>記錄類型</th>
<th>IP 位址或 URL</th>
</tr>
<tr>
    <td>@</td>
<td>A (位址)</td>
    <td>127.0.0.1</td>
</tr>
<tr>
<td>www</td>
<td>CNAME (別名)</td>
<td>contoso.azurewebsites.net</td>
</tr>
</table>

假設自訂網域名稱為 'contoso.com'，則會建立以下記錄：

-   對應至 127.0.0.1 的 **contoso.com**。
-   對應至 **contoso.azurewebsites.net** 的 **www.contoso.com**。

## 建立 “awverify” 記錄 (僅限 A 記錄)

若您建立 A 記錄，Azure 網站也會需要特殊的 CNAME 記錄，用於確認您是否擁有嘗試要使用的網域。此 CNAME 記錄必須具有下列形式。

-   *若 A 記錄對應至根網域或萬用字元網域：*請建立從 **awverify.\<yourdomain\>** 對應至 **awverify.\<yourwebsitename\>.azurewebsites.net** 的 CNAME 記錄。例如，針對 **contoso.com** 的 A 記錄，建立 **awverify.contoso.com** 的 CNAME 記錄。
-   *若 A 記錄對應至特定子網域：*請建立從 **awverify.\<subdomain\>** 對應至 **awverify.\<yourwebsitename\>.azurewebsites.net** 的 CNAME 記錄。例如，針對 **blogs.contoso.com** 的 A 記錄，建立 **awverify.blogs.contoso.com** 的 CNAME 記錄。

您網站的訪客不會看到 awverify 子網域；這只是供 Azure 用於驗證網域。

## 在網站上啟用網域名稱

[WACOM.INCLUDE [modes][2]]

<!-- Anchors. --> 
<!-- Images -->

  [網站]: /zh-tw/documentation/articles/web-sites-custom-domain-name/ "網站"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [contoso.azurewebsites.net 子網域]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [概觀]: #overview
  [DNS 記錄類型]: #dns-record-types
  [尋找虛擬 IP 位址]: #find-the-virtual-ip-address
  [建立 DNS 記錄]: #create-the-dns-records
  [在網站上啟用網域名稱]: #enable-the-domain-name-on-your-website
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [如何調整網站]: http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-scale/
  [0]: media/web-sites-custom-domain-name/dncmntask-cname-6.png
  [1]: media/web-sites-custom-domain-name/ipaddress.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
