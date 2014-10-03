網域名稱系統 (DNS) 主要用來尋找網際網路上的資源。例如，當您在瀏覽器中輸入網站位址，或按一下網頁上的連結時，它會使用 DNS 將網域轉譯成 IP 位址。IP 位址如同街道地址，但它不是很容易使用。例如，記憶像 **contoso.com** 的 DNS 名稱，比記憶如 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 之類的 IP 位址容易得多。

DNS 系統是根據*記錄*。記錄會將特定的*名稱* (如 **contoso.com**)，與 IP 位址或其他 DNS 名稱相關聯。當應用程式 (如網頁瀏覽器) 查詢 DNS 中的名稱時，它會尋找記錄，並使用它指向為位址的任何值。如果指向的值為 IP 位址，瀏覽器將使用該值。如果指向其他 DNS 名稱，則應用程式必須重新解析。所有名稱解析最終會導出一個 IP 位址。

建立 Azure 網站時，會將 DNS 名稱自動指派給網站。此名稱將採用格式 **\<yoursitename\>.azurewebsites.net**。建立 DNS 記錄時也可以使用虛擬 IP 位址，因此您可建立指向 **.azurewebsites.net**，或指向 IP 位址的記錄。

> [WACOM.NOTE] 如果您刪除又重新建立網站，或在將網站模式設為基本、共用或標準後將其變回免費，則網站的 IP 位址會變更。

有多種記錄類型，每種記錄都有其本身的功能與限制，但對於網站只需要關注兩種記錄，即 *A* 和 *CNAME*。

### 位址記錄 (A 記錄)

A 記錄將網域 (例如 **contoso.com** 或 **www.contoso.com**) *或萬用字元網域* (例如 **\*.contoso.com**) 對應至 IP 位址。以 Azure 網站而言，就是指服務的虛擬 IP 或您為網站購買的特定 IP 位址。

相較於 CNAME 記錄，A 記錄的主要優點為：

-   您可以將根網域 (如 **contoso.com**) 對應至 IP 位址；許多註冊機報僅允許使用 A 記錄執行此動作

-   您可以擁有一個使用萬用字元的項目，例如 **\*.contoso.com**，即可處理多個子網域的要求，例如 **mail.contoso.com**、**blogs.contoso.com** 或 **www.contso.com**。

> [WACOM.NOTE] 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析網站 IP 位址的變更。您在設定網站的自訂網域名稱設定時會提供 A 記錄所使用的 IP 位址。不過，如果您刪除又重新建立網站，或將網站模式變回免費，此值就可能改變。

### 別名記錄 (CNAME 記錄)

CNAME 記錄將*特定的*網域 (例如 **mail.contoso.com** 或 **www.contoso.com**) 對應到其他 (正式) 網域名稱。在 Azure 網站案例中，正式網域名稱就是網站的 **\<yoursitename\>.azurewebsites.net** 網域名稱。建立之後，CNAME 還會建立 **\<yoursitename\>.azurewebsites.net** 網域名稱的別名。CNAME 項目會自動解析 **\<yoursitename\>.azurewebsites.net** 網域名稱的 IP 位址，就算網站的 IP 位址變更，您也不需要採取任何動作。

> [WACOM.NOTE] 使用 CNAME 記錄時，某些網域註冊機構只允許您對應子網域 (如 **www.contoso.com**)，而不是根名稱 (如 **contoso.com**)。如需關於 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、[維基百科 CNAME 記錄條目][]，或 [IETF 網域名稱 - 實作與規格][]文件。

### Azure 網站 DNS 詳細規格

若要在 Azure 網站中使用 A 記錄，您必須先建立下列其中一筆 CNAME 記錄：

-   **若是根網域或萬用字元子網域** - 將 **awverify** 的 DNS 名稱對應至 **awverify.\<yourwebsitename\>.azurewebsites.net**。

-   **若是特定子網域** - 將 **awverify.\<sub-domain\>** 的 DNS 名稱對應至 **awverify.\<yourwebsitename\>.azurewebsites.net**。例如，**awverify.blogs** (如果 A 記錄要提供給 **blogs.contoso.com** 使用)。

此 CNAME 記錄可用來驗證您擁有正在嘗試使用的網域。這是建立指向網站虛擬 IP 位址的 A 記錄以外的動作。

您可以執行下列步驟來尋找您網站的 IP 位址，以及 **awverify** 名稱和 **.azurewebsites.net** 名稱。

1.  在瀏覽器中開啟 [Azure 管理入口網站][]。

2.  在 [網站] 索引標籤中，按一下網站名稱，選取 [儀表板]，然後從頁面底部選取 [管理網域]。

    ![][]

    > [WACOM.NOTE] 如果 [管理網域] 未啟用，這代表您使用的是免費網站。您無法在免費網站中使用自訂網域名稱，且必須升級到「共用」、「基本」或「標準」模式。如需網站模式的詳細資訊，包括如何變更網站的模式，請參閱[如何調整網站][] (英文)。

3.  在 [管理自訂網域] 對話方塊中，您會看到 **awverify** 資訊、目前指派的 **.azurewebsites.net** 網域名稱，以及虛擬 IP 位址。儲存此資訊，在建立 DNS 記錄時會用得到。

    ![][1]

  [維基百科 CNAME 記錄條目]: http://en.wikipedia.org/wiki/CNAME_record
  [IETF 網域名稱 - 實作與規格]: http://tools.ietf.org/html/rfc1035
  [Azure 管理入口網站]: https://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [如何調整網站]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/
  [1]: ./media/custom-dns-web-site/managecustomdomains.png
