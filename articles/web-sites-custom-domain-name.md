<properties  title="Learn how to configure an Azure web site to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# 設定 Azure 網站的自訂網域名稱

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name" title="自訂網域" class="current">自訂網域</a><a href="/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name" title="網路解決方案">網路解決方案</a><a href="/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/zh-tw/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/web-sites-custom-domain-name/" title="網站" class="current">網站</a> | <a href="/zh-tw/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="使用流量管理員的網站">使用流量管理員的網站</a></div>

 [WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供對 Azure 網站使用自訂網域名稱的一般指示。請檢查本文頂端的索引標籤，看看是否有列出您的網域註冊機構。如果有列出，請選取該索引標籤以取得註冊機構的特定步驟。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

* [了解 DNS 記錄](#understanding-records)
* [將網站設定為基本、共用或標準模式](#bkmk_configsharedmode)
* [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
* [在網站上啟用網域](#enabledomain)

<h2><a name="understanding-records"></a>了解 DNS 記錄</h2>


[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>將網站設定為基本、共用或標準模式</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a> 新增自訂網域的 DNS 記錄</h2>

若要將您的自訂網域與 Azure 網站建立關聯，必須在 DNS 資料表中為您的自訂網域新增項目，方法是使用您向其購買網域名稱之網域註冊機構所提供的工具。使用下列步驟來尋找並使用 DNS 工具。

1.  在網域註冊機構登入您的帳戶，並尋找用於管理 DNS 記錄的頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。通常該頁面的連結可透過檢視您的帳戶資訊，然後尋找**我的網域**之類的連結來找到。

2.  一旦找到網域名稱的管理頁面，請尋找可讓您編輯 DNS 記錄的連結。這可能會以 **Zone file**、**DNS Records** 或 **Advanced** 組態連結的形式列出。
    
    * 頁面上可能已建立一些記錄，例如將 '**@**' 或 '\*' 與 'domain parking' (網域停放) 頁面建立關聯的項目。它也可能包含常見子網域 (如 **www**) 的記錄。
    * 頁面上會提及 **A 記錄** 與 **CNAME 記錄**，或提供選取記錄類型的下拉式清單。它也可能提及其他記錄 (如 **MX 記錄**)。在部分情況下，會以其他名稱來稱呼這些記錄 (如 **IP 位址記錄**而非 A 記錄，或**別名記錄**而非 CNAME 記錄)。
    * 頁面上也會有可讓您將**主機名稱**或**網域名稱****對應**為 **IP 位址**或其他網域名稱的欄位。

3.  由於每個註冊機構的特殊要求可能不同，一般來說，您會*從*自訂網域名稱 (例如 **contoso.com**) 對應*至* Azure 網站網域名稱 (**contoso.azurewebsites.net**) 或 Azure 網站虛擬 IP 位址。
    
    * CNAME 記錄將一律對應至 Azure 網站網域 - **contoso.azurewebsites.net**。因此您將*從* **www** 之類的網域對應*至*您的**<yourwebsitename>.azurewebsites.net** 位址。


     
      > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
      > 
      > * *從* **www** 對應*至* 您的
      >   **<yourwebsitename>.azurewebsites.net**。
      > 
      > 或
      > 
      > * *從* **awverify.www** 對應*至*
      >   **awverify.<yourwebsitename>.azurewebsites.net**。
      > 
      > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域。
    
    * A 記錄將一律對應至 Azure 網站虛擬 IP 位址。因此您是*從* **www** 之類的網域對應*至*網站的虛擬 IP 位址。


     
      > [WACOM.NOTE] 若要將根網域 (例如 **contoso.com**) 對應至 Azure 網站，您通常是從
      > '**@**' 或空白項目對應至虛擬 IP 位址。若要建立將所有子網域對應至虛擬 IP 位址的萬用字元對應，您通常會從
      > '\*' 對應至虛擬 IP 位址。
      > 
      > 對應根網域或萬用字元的特殊要求，將因註冊機構而有所不同。請參閱註冊機構提供的文件，以取得更特定的指示。

4.  在註冊機構處將新增或修改 DNS 記錄的作業完成後，請儲存變更。

<h2><a name="enabledomain"></a>在網站上啟用網域名稱</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]