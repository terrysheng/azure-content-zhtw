
# 設定 Azure 網站的自訂網域名稱

建立網站時，Azure 會在 azurewebsites.net 網域上提供好記的子網域，讓使用者以類似 http://<mysite>.azurewebsites.net 的 URL 來存取您的網站。不過，如果您將網站設為共用或標準模式，則可以將網站對應到您自己的網域名稱。

您也可以選擇使用 Azure 流量管理員來負載平衡連入網站的流量。如需流量管理員如何與「網站」搭配使用的詳細資訊，請參閱[使用 Azure 流量管理員來控制 Azure 網站的流量](/zh-tw/documentation/articles/web-sites-traffic-manager/)。

> [WACOM.NOTE] 此工作中的程序適用於 Azure 網站。若為雲端服務，請參閱＜[在 Azure 中設定自訂網域名稱][1]。

> [WACOM.NOTE]
> 您需要將網站設定為共用或標準模式，才能執行此工作中的步驟，但這可能會變更您的訂閱費用。如需詳細資訊，請參閱[網站定價詳細資料][2]。

本文內容：

* [了解 CNAME 和 A 記錄](#understanding-records)
* [將網站設定為共用或標準模式](#bkmk_configsharedmode)
* [將網站新增至流量管理員](#trafficmanager)
* [新增自訂網域的 CNAME](#bkmk_configurecname)
* [新增自訂網域的 A 記錄](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>了解 CNAME 和 A 記錄</h2>


CNAME (或別名記錄) 和 A 記錄都可讓您將網域名稱和網站產生關聯，但運作方式不同。

### CNAME 或別名記錄

CNAME 記錄將*特定的*網域 (例如 **contoso.com** 或 **www.contoso.com**) 對應到正式網域名稱。在此案例中，正式網域名稱為 Azure 網站的 **<myapp>.azurewebsites.net** 網域名稱，或流量管理員設定檔的 **<myapp>.trafficmgr.com** 網域名稱。CNAME 建立之後會建立 **<myapp>.azurewebsites.net** 的別名或 **<myapp>.trafficmgr.com** 網域名稱。CNAME 項目會自動解析為 **<myapp>.azurewebsites.net** 或 **<myapp>.trafficmgr.com** 網域名稱的 IP 位址，就算網站的 IP 位址變更，您也不需要採取任何動作。

> [WACOM.NOTE] 使用 CNAME 記錄時，某些網域註冊機構只允許您對應子網域，例如
> www.contoso.com，而不是根名稱，例如 contoso.com。如需關於 CNAME
> 記錄的詳細資訊，請參閱註冊機構提供的文件、[維基百科 CNAME 記錄條目][3]，或
> [IETF 網域名稱 - 實作與規格][4]文件。

### A 記錄

A 記錄將網域 (例如 **contoso.com** 或 **www.contoso.com**) *或萬用字元網域* (例如 **\*.contoso.com**) 對應至 IP 位址。以 Azure 網站而言，就是指服務的虛擬 IP 或您為網站購買的特定 IP 位址。相較於 CNAME 記錄，A 記錄的主要優點在於只需要有一個項目使用萬用字元，例如 **\*.contoso.com**，即可處理多個子網域的要求，例如 **mail.contoso.com**、**login.contoso.com** 或 **www.contso.com**。

> [WACOM.NOTE] 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析網站 IP
> 位址的變更。您在設定網站的自訂網域名稱設定時會提供 A 記錄所使用的 IP
> 位址。不過，如果您刪除又重新建立網站，或將網站模式變回免費，此值就可能改變。

> [WACOM.NOTE] A 記錄無法用於流量管理員的負載平衡。如需詳細資訊，請參閱[使用 Azure 流量管理員來控制 Azure
> 網站的流量](/zh-tw/documentation/articles/web-sites-traffic-manager/)。

<a name="bkmk_configsharedmode"></a><h2>將網站設定為共用或標準模式</h2>

只有在 Azure 網站的共用和標準模式下，才能在網站上設定自訂網域名稱。將網站從免費網站模式切換到共用或標準網站模式之前，您必須先移除網站訂閱的支出上限。如需共用與標準模式定價的詳細資訊，請參閱[定價詳細資料][5]。

1.  在瀏覽器中開啟[管理入口網站][6]。 2.  在 **網站** 索引標籤中，按一下您網站的名稱。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-1.png)

3.  按一下 **調整** 索引標籤。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-2.png)

4.  在 **一般** 區段中，按一下 **共用** 來設定網站模式。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-3.png)


   
    > [WACOM.NOTE] 如果您將對此網站使用流量管理員，必須選取標準模式而非共用模式。

5.  按一下 **儲存**。 6.  系統會指出共用模式 (若選擇 **標準]，則為標準模式) 將增加成本，如果同意，請按一下 [是**。
    
    <!--![][standardmode4]-->
    
    **注意**  
     如果發生「設定網站 'web site name' 的大小失敗」錯誤，您可以利用詳細資料按鈕來取得其他資訊。

<a name="trafficmanager"></a><h2>(選用) 將網站新增至流量管理員</h2>

如果您要對網站使用流量管理員，請執行下列步驟。

1.  如果您還沒有流量管理員設定檔，請利用[使用快速建立來建立流量管理員設定檔][7]中的資訊，建立設定檔。請記下與您的流量管理員設定檔關聯的 **.trafficmgr.com** 網域名稱。稍後將會使用該資訊。

2.  請使用[新增或刪除端點][8]中的資訊，將網站新增為流量管理員設定檔中的端點。


   
    > [WACOM.NOTE]
    > 如果在新增端點時未列出您的網站，請確認模式是設為標準模式。您必須讓網站使用標準模式，才能與流量管理員搭配使用。

3.  登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。

4.  現在，找出可選取或輸入 CNAME 記錄的地方。您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。請尋找 **CNAME**、**Alias** 或 **Subdomains** 之類的字。

5.  您也必須為 CNAME 提供網域或子網域別名。例如，若要建立 **www.customdomain.com** 的別名，請提供 **www**。

6.  您也必須提供主機名稱，做為此 CNAME 別名的正式網域名稱。這是您網站的 **.trafficmgr.com** 名稱。

例如，下列 CNAME 記錄會將來自 **www.contoso.com** 的所有流量，轉送至 **contoso.trafficmgr.com** (網站的網域名稱)：

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>別名/主機名稱/子網域</strong>
</td>

<td><strong>正式網域</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.trafficmgr.com</td>

</tr>

</table>

**www.contoso.com** 的訪客絕對看不到真正的主機 (contoso.azurewebsite.net)，所以使用者不會察覺到轉送過程。

> [WACOM.NOTE] 如果您正對某個網站使用流量管理員，則不需遵循下列區段中的步驟：**新增自訂網域的 CNAME**
> 與**新增自訂網域的 A 記錄**。在前述步驟建立的 CNAME 記錄，會將連入的流量遞送至流量管理員，然後將流量遞送至網站端點。

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 CNAME</h2>

若要建立 CNAME 記錄，您必須使用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1.  請利用下列其中一種方法，尋找指派給您網站的 **.azurewebsite.net** 網域名稱。
    
    * 登入 **Azure 管理入口網站][6]，選取您的網站，再選取 [儀表板**，然後在 [Quick Glance]**** 區段中找出 **網站 URL** 項目。
    
    * 安裝並設定 [Azure Powershell][9]，然後使用下列命令：
      
            get-azurewebsite yoursitename | select hostnames
    
    * 安裝並設定 [Azure 跨平台命令列介面][10]，然後使用下列命令：
      
          	azure site domain list yoursitename
    
    下列步驟會用到 **.azurewebsite.net** 名稱，請儲存此名稱。

2.  登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。

3.  現在，找出可選取或輸入 CNAME 記錄的地方。您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。請尋找 **CNAME**、**Alias** 或 **Subdomains** 之類的字。

4.  您也必須為 CNAME 提供網域或子網域別名。例如，若要建立 **www.customdomain.com** 的別名，請提供 **www**。如果要建立根網域的別名，註冊機構的 DNS 工具中可能會以 '**@**' 符號列出此別名。

5.  您也必須提供主機名稱，做為此 CNAME 別名的正式網域名稱。這是您網站的 **.azurewebsite.net** 名稱。

例如，下列 CNAME 記錄會將來自 **www.contoso.com** 的所有流量，轉送至 **contoso.azurewebsite.net** (網站的網域名稱)：

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>別名/主機名稱/子網域</strong>
</td>

<td><strong>正式網域</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.azurewebsite.net</td>

</tr>

</table>

**www.contoso.com** 的訪客絕對看不到真正的主機 (contoso.azurewebsite.net)，所以使用者不會察覺到轉送過程。

> [WACOM.NOTE] 上述範例僅適用於 **www** 子網域的流量。因為 CNAME
> 記錄不能使用萬用字元，所以您必須為每一個網域/子網域建立一個 CNAME。如果要將來自子網域 (例如 \*.contoso.com)
> 的流量導向您的 azurewebsite.net 位址，您可以在 DNS 設定中設定 **URL 重新導向** 或 [URL
> 轉送]**** 項目，或建立一筆 A 記錄。

> [WACOM.NOTE] 需要一些時間，CNAME 才能傳播至整個 DNS 系統。必須等到 CNAME 傳播之後，您才能設定網站的
> CNAME。您可以使用 [http://www.digwebinterface.com/][11]{:
> } 之類的服務來驗證 CNAME 已生效。

### 將網域名稱新增至網站

當網域名稱的 CNAME 記錄傳播完成時，您必須將此記錄與網站產生關聯。您可以使用 Azure 跨平台命令列介面或 Azure 管理入口網站，將 CNAME 記錄所定義的自訂網域名稱新增至網站。

**使用命令列工具新增網域名稱**

安裝並設定 [Azure 跨平台命令列介面][10]，然後使用下列命令：

    azure site domain add customdomain yoursitename

例如，下列命令會將自訂網域名稱 **www.contoso.com** 新增至 **contoso.azurewebsite.net** 網站：

    azure site domain add www.contoso.com contoso

您可以使用下列命令來確認自訂網域名稱已新增至網站：

    azure site domain list yoursitename

此命令傳回的清單應該包含自訂網域名稱，以及預設的 **.azurewebsite.net** 項目。

**使用 Azure 管理入口網站新增網域名稱**

1.  在瀏覽器中開啟 [Azure 管理入口網站][6]。

2.  在 **網站** 索引標籤中，按一下網站名稱，選取 **儀表板**，然後從頁面底部選取 **管理網域**。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  在 **網域名稱** 文字方塊中，輸入您已設定的網域名稱。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  按一下核取記號以接受網域名稱。

完成設定之後，自訂網域名稱會列在網站 **設定** 頁面的 **網域名稱** 區段中。

<a name="bkmk_configurearecord"></a><h2>新增自訂網域的 A 記錄</h2>

若要建立 A 記錄，您必須先找出網站的 IP 位址。然後，利用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。各註冊機構指定 A 記錄的方法都很類似，只是稍微不同，但概念都一樣。除了建立 A 記錄，您還必須建立 CNAME 記錄，供 Azure 用來驗證 A 記錄。

1.  在瀏覽器中開啟 [Azure 管理入口網站][6]。

2.  在 **網站** 索引標籤中，按一下網站名稱，選取 **儀表板**，然後從畫面底部選取 **管理網域**。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  在 **Manage custom domains** 對話方塊中，找出 **The IP Address to use when configuring A records**。複製 IP 位址。建立 A 記錄時會使用此位址。

4.  在 **Manage custom domains** 對話方塊中，請注意對話方塊頂端的文字結尾的 awverify 網域名稱。應該是 **awverify.mysite.azurewebsites.net**，其中 **mysite** 是您的網站名稱。這是在建立驗證 CNAME 記錄時所使用的網域名稱，請複製下來。

5.  登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。

6.  找出可選取或輸入 A 和 CNAME 記錄的地方。您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。

7.  執行下列步驟來建立 A 記錄：
    
    1.  選取或輸入將使用 A 記錄的網域或子網域。例如，若要建立 **www.customdomain.com** 的別名，請選取 **www**。如果要為所有子網域建立萬用字元項目，請輸入 '**\***'。這將會涵蓋所有子網域，例如 **mail.customdomain.com**、**login.customdomain.com** 和 **www.customdomain.com**。
        
        如果要建立根網域的 A 記錄，註冊機構的 DNS 工具中可能會以 '**@**' 符號列出此記錄。
    
    2.  在提供的欄位中，輸入雲端服務的 IP 位址。這樣會將 A 記錄中使用的網域項目與雲端服務部署的 IP 位址產生關聯。
        
        例如，下列 A 記錄會將來自 **contoso.com** 的所有流量轉送至 **137.135.70.239** (已部署之應用程式的 IP 位址)：
        
        <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
         <tr>
        <td><strong>主機名稱/子網域</strong>
        </td>
        
        <td><strong>IP 位址</strong>
        </td>
        
         </tr>
        
         <tr>
         <td>@</td>
        
         <td>137.135.70.239</td>
        
         </tr>
        
         </table>
        
        此範例示範建立根網域的 A 記錄。如果想要建立萬用字元項目來涵蓋所有子網域，請輸入 '**\***' 做為子網域。

8.  接下來，建立具有別名 **awverify** 和您稍早取得之正式網域 **awverify.mysite.azurewebsites.net** 的 CNAME 記錄。


   
    > [WACOM.NOTE] 有些註冊機構接受別名 awverify，但其他註冊機構可能需要完整的別名網域名稱
    > awverify.www.customdomainname.com 或 awverify.customdomainname.com。
    
    例如，以下會建立一筆 CNAME 記錄，可供 Azure 用來驗證 A 記錄組態。
    
    <table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
     <tr>
    <td><strong>別名/主機名稱/子網域</strong>
    </td>
    
    <td><strong>正式網域</strong>
    </td>
    
     </tr>
    
     <tr>
    <td>awverify</td>
    
    <td>awverify.contoso.azurewebsites.net</td>
    
     </tr>
    
     </table>

> [WACOM.NOTE] 需要一些時間，awverify CNAME 才能傳播至整個 DNS 系統。必須等到 awverify
> CNAME 傳播之後，您才能為網站設定 A 記錄所定義的自訂網域名稱。您可以使用
> [http://www.digwebinterface.com/][11] 之類的服務來驗證
> CNAME 已生效。

### 將網域名稱新增至網站

當網域名稱的 **awverify** CNAME 記錄傳播完成時，您就可以將 A 記錄所定義的自訂網域與網站產生關聯。您可以使用 Azure 跨平台命令列介面或 Azure 管理入口網站，將 A 記錄所定義的自訂網域名稱新增至網站。

**使用命令列工具新增網域名稱**

安裝並設定 [Azure 跨平台命令列介面][10]，然後使用下列命令：

    azure site domain add customdomain yoursitename

例如，下列命令會將自訂網域名稱 **contoso.com** 新增至 **contoso.azurewebsite.net** 網站：

    azure site domain add contoso.com contoso

您可以使用下列命令來確認自訂網域名稱已新增至網站：

    azure site domain list yoursitename

此命令傳回的清單應該包含自訂網域名稱，以及預設的 **.azurewebsite.net** 項目。

**使用 Azure 管理入口網站新增網域名稱**

1.  在瀏覽器中開啟 [Azure 管理入口網站][6]。

2.  在 **網站** 索引標籤中，按一下網站名稱，選取 **儀表板**，然後從頁面底部選取 **管理網域**。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  在 **網域名稱** 文字方塊中，輸入您已設定的網域名稱。
    
    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  按一下核取記號以接受網域名稱。

完成設定之後，自訂網域名稱會列在網站 **設定** 頁面的 **網域名稱** 區段中。

> [WACOM.NOTE] 將 A 記錄所定義的自訂網域名稱新增至網站之後，您就可以使用註冊機構所提供的工具來移除 awverify
> CNAME 記錄。不過，如果未來想要新增另一筆 A 記錄，則必須重新建立 awverify 記錄，才能將新的 A
> 記錄所定義的新網域名稱與網站產生關聯。

## 後續步驟

* [如何管理網站][12]

* [設定網站的 SSL 憑證][13]

<!-- Bookmarks -->

<!-- Links -->

<!-- images -->

<!-- images -->



[1]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/custom-dns/
[2]: http://www.windowsazure.com/zh-tw/pricing/details/web-sites/
[3]: http://en.wikipedia.org/wiki/CNAME_record
[4]: http://tools.ietf.org/html/rfc1035
[5]: https://www.windowsazure.com/en-us/pricing/details/
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn339012.aspx
[8]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744839.aspx
[9]: http://www.windowsazure.com/zh-tw/manage/install-and-configure-windows-powershell/
[10]: http://www.windowsazure.com/zh-tw/manage/install-and-configure-cli/
[11]: http://www.digwebinterface.com/
[12]: http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-manage-websites/
[13]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/enable-ssl-web-site/