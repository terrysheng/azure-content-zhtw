
網域名稱系統 (DNS) 是用來尋找網際網路上的資訊。例如，當您在瀏覽器中輸入位址，或按一下網頁上的連結時，它會使用 DNS 將網域轉譯成 IP 位址。IP 位址如同街道地址，但它不是很容易使用。例如，記憶像 **contoso.com** 的 DNS 名稱，比記憶如 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 之類的 IP 位址容易得多。

DNS 系統是根據*記錄*。記錄會將特定的*名稱* (如 **contoso.com**)，與 IP 位址或其他 DNS 名稱相關聯。當應用程式 (如網頁瀏覽器) 查詢 DNS 中的名稱時，它會尋找記錄，並使用它指向為位址的任何值。如果指向的值為 IP 位址，瀏覽器將使用該值。如果指向其他 DNS 名稱，則應用程式必須重新解析。所有名稱解析最終會導出一個 IP 位址。

建立 Azure 網站時，會將 DNS 名稱自動指派給網站。此名稱將採用格式 **&lt;yoursitename\>.azurewebsites.net**。也可能有虛擬 IP 位址可用於建立 DNS 記錄，如此您即可建立指向 **.azurewebsites.net** 的記錄，或者指向 IP 位址。

> [WACOM.NOTE] 如果您刪除又重新建立網站，或在將網站模式設為基本、共用或標準後將其變回免費，則網站的 IP 位址會變更。

有多種記錄類型，每種記錄都有其本身的功能與限制，但對於網站只需要關注兩種記錄，即 *CNAME* 和 *A*。

### CNAME 或別名記錄

CNAME 記錄將*特定的*網域 (例如 **mail.contoso.com** 或 **www.contoso.com**) 對應到其他 (正式) 網域名稱。在 Azure 網站案例中，正式網域名稱為網站的 **&lt;myapp\>.azurewebsites.net** 網域名稱。CNAME 建立之後還會建立 **&lt;myapp\>.azurewebsites.net** 網域名稱的別名。CNAME 項目會自動解析 **&lt;myapp\>.azurewebsites.net** 網域名稱的 IP 位址，就算網站的 IP 位址變更，您也不需要採取任何動作。

> [WACOM.NOTE] 使用 CNAME 記錄時，某些網域註冊機構只允許您對應子網域 (如 **www.contoso.com**)，而不是根名稱 (如 **contoso.com**)。如需關於 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、[維基百科 CNAME 記錄條目](http://en.wikipedia.org/wiki/CNAME_record)，或 [IETF 網域名稱 - 實作與規格](http://tools.ietf.org/html/rfc1035)文件。

### A 記錄

A 記錄將網域 (例如 **contoso.com** 或 **www.contoso.com**) *或萬用字元網域* (例如 **\*.contoso.com**) 對應至 IP 位址。以 Azure 網站而言，就是指服務的虛擬 IP 或您為網站購買的特定 IP 位址。

相較於 CNAME 記錄，A 記錄的主要優點為：

-   您可以將根網域 (如 **contoso.com**) 對應至 IP 位址；許多註冊機報僅允許使用 A 記錄執行此動作

-   您可以擁有一個使用萬用字元的項目，例如 **\*.contoso.com**，即可處理多個子網域的要求，例如 **mail.contoso.com**、**login.contoso.com** 或 **www.contso.com**。

> [WACOM.NOTE] 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析網站 IP 位址的變更。您在設定網站的自訂網域名稱設定時會提供 A 記錄所使用的 IP 位址。不過，如果您刪除又重新建立網站，或將網站模式變回免費，此值就可能改變。

### Azure 網站 DNS 詳細規格

若要搭配使用 A 記錄與 Azure 網站，您必須先建立對應至下列任一項的 CNAME 記錄：

-   將 **www** 的 DNS 名稱對應至您的 **&lt;yourwebsitename\>.azurewebsites.net**。或
-   將 **awverify.www** 的 DNS 名稱對應至 **awverify.&lt;yourwebsitename\>.azurewebsites.net**。

此 CNAME 記錄可用來驗證您擁有正在嘗試使用的網域。這是建立指向網站之虛擬 IP 位址的 A 記錄以外的動作。

您可以執行下列步驟來尋找 IP 位址，以及您網站的 **awverify.www** 名稱和 **.azurewebsites.net** 名稱：

1.  在瀏覽器中開啟 [Azure 管理入口網站](https://manage.windowsazure.com)。

2.  在 **[網站]** 索引標籤中，按一下網站名稱，選取 **[儀表板]**，然後從頁面底部選取 **[管理網域]**。

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  在 **[管理自訂網域]** 對話方塊中，您會看到 **awverify** 資訊、目前指派的 **.azurewebsites.net** 網域名稱，以及虛擬 IP 位址。


