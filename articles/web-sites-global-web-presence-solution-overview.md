<properties urlDisplayName="Create a Global Web Presence on Azure Websites" pageTitle="在 Azure 網站上建立全域網站空間" metaKeywords="" description="本指南針對如何在 Azure 網站上託管您的組織的 (.COM) 網站提供技術概觀。這包括部署、自訂網域、SSL 和監視。" metaCanonical="http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-global-web-presence-solution-overview/" services="" documentationCenter="" title="Create a Global Web Presence on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />





# 在 Azure 網站上建立全域網站空間

本指南針對如何在 Azure 上託管您的組織的 (.COM) 網站提供技術概觀。本案例亦同時稱為全域網站空間。本指南著重在 [Azure 網站][websitesoverview]，因為「網站」是 Azure 上最快速且最容易建立、移轉、擴充與管理的 Web 應用程式。不過，為滿足部分應用程式需求，最好移轉到執行 IIS 的 [Azure 雲端服務][csoverview]或 [Azure 虛擬機器][vmoverview]。託管 Web 應用程式還有其他很好的選擇。如果您正處於初始規劃階段，請檢閱以下文件：[Azure 網站、雲端服務和 VM：使用時機][chooseservice]。如果使用雲端服務或虛擬機器的需求消失，我們建議您使用網站來託管您的全域網站空間。本文件其他部分將就如何在此案例中使用網站提供相關指導。 

本指南涵蓋下列領域：

- [建立 Azure 網站](#createwebsite)
- [部署網站](#deploywebsite)
- [新增自訂網域](#customdomain)
- [用 SSL 保護網站安全](#ssl)
- [監視網站](#monitor)

> [WACOM.NOTE]
> 本指南呈現一些與公用端 .COM 網站開發整合的最常見領域和工作。不過，還有其他 Azure 網站功能可供您在特定的實作中使用。若要檢閱這些功能，另請參閱 <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/digital-marketing-campaign-solution-overview">數位行銷活動</a> 和 <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/business-application-solution-overview">商務應用程式</a>的其他指南。
> 
> 如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。不需要信用卡，沒有承諾。

##<a name="createwebsite"></a>建立 Azure 網站
透過 Azure 管理入口網站，您可以多種方式建立新的 Azure 網站。當您在入口網站下方按一下 [**新增**] 按鈕時，會顯示下列對話方塊：

![GlobalWebCreate][GlobalWebCreate]

建立新的網站共有三個選項：**快速建立**、**自訂建立**和 **從組件庫**。不管您選取哪個選項，您都應該選取符合大多數使用群習慣的 Azure 區域。

如果您是移轉現有網站，[**自訂建立**] 選項可讓您建立或關聯 SQL Database 或 MySQL 資料庫。此選項同時讓您得以指定用於部署的多項原始檔控制選項，例如 GitHub 或 Team Foundation Server (TFS)。如果您正使用原始檔控制機制來管理您的網站，此方法可讓您快速設定用於部署的 Azure 網站。

[**從組件庫**] 選項可讓您從多項架構中 (例如 Drupal 或 WordPress) 選取一項來設定新的網站。此方法有助您快速設定新的網站，以便您在選定的架構下接著進行自訂。

就像 Azure 中大多數服務一樣，您必須為新網站選取 Azure 區域。Azure 在全球擁有多個區域。一旦您將網站部署到其中一個區域，就能透過網際網路在全球各地存取。不過，多個區域提供更大的彈性。最明顯的好處之一，就是可以將網站部署在最接近使用者的區域。 

如需建立新網站的詳細步驟，請參閱[開始使用 Azure 網站和 ASP.NET][howtocreatewebsites]。

##<a name="deploywebsite"></a>部署網站
有多種方式可將您的網站部署到 Azure。如果您從組件庫中選取架構，則已經部署了一個入門網站。不過，為了有所進展，您還是需要設定一些編輯與部署步驟的類型。某些部署選項包含：

- 使用 FTP 用戶端。
- 從原始檔控制部署。
- 從 Visual Studio 發行。
- 從 [WebMatrix][webmatrix] 發行。

這些選項各自擁有不同的優點。從 FTP 用戶端發行的解決方案簡單易用，可以直接將新的檔案推送到您的網站，而且任何仰賴 FTP 的現有發行工具或程序都能在 Azure 網站上繼續運作。原始檔控制可對網站內容版本提供最佳的控管機制，因為所有變更都能獲得追蹤、發行並在必要時回復到先前的版本。從 Visual Studio 或 Web Matrix 直接發行的選項可方便使用任何一項工具的開發人員。在專案或原型建立早期階段，是使用此功能的最好時機之一。無論是哪個情況，頻繁發行與測試可能比在開發環境中發行更為方便。 

在此許多部署工作都必須使用到 Azure 管理入口網站內的資訊。移至您的網站並選取 [**儀表板**] 索引標籤，然後尋找 [**快速概覽**] 區段。以下螢幕擷取畫面顯示數個選項。

![GlobalWebQuickGlance][GlobalWebQuickGlance]

某些原始檔控制工具與 FTP 用戶端會要求具備使用者名稱/密碼存取權限。對新的網站而言，系統不會自動建立認證。不過，您可以按一下 [**重設您的部署認證**] 輕鬆完成這項工作。完成後，您可以使用這些認證加上同一個 [**儀表板**] 頁面上的 [**FTP 主機名稱**] 來部署您的網站。

![GlobalWebFTPSettings][GlobalWebFTPSettings]

請注意，部署/FTP 使用者名稱是網站名稱和您提供之使用者名稱的組合。因此如果您的網站是 "http://contoso.azurewebsite.net" 且您的使用者名稱是 "myuser"，則用於部署與 FTP 使用者名稱會是 "contoso\myuser"。

您也可以選擇透過原始檔控制管理服務 (如 GitHub 或 TFS Online) 來進行部署。按一下 [**設定從原始檔控制進行部署**] 選項。接著遵循您所選的原始檔控制系統或服務指示進行。如需從本機 Git 儲存機制發行的逐步說明，請參閱[從原始檔控制發行至 Azure 網站][publishingwithgit]。

如果您打算使用 Visual Studio 來建立與管理網站，可以選擇直接從 Visual Studio 發行。方法之一就是按一下 [**下載發行設定檔**] 選項。此舉可讓您儲存 publishsettings 檔案，以便匯入 Visual Studio 供 Web 發行之用。 

> [WACOM.NOTE]
> 請務必確保 <i>publishsettings</i> 檔案安全且不受到原始檔控制影響，因為此檔案包含了部署與任何連結之資料庫連接字串所需的使用者名稱與密碼。

您也可以直接將訂閱資訊匯入 Visual Studio。舉例來說，假設 Visual Studio 裡有一個本機 ASP.NET 專案。以滑鼠右鍵按一下該 Web 專案，然後選取 [**發行**]。[**發行 Web**] 對話方塊裡的 [**匯入**] 按鈕可讓您匯入內含 Azure 訂閱設定的檔案，或是您從網站儀表板中下載的 publishsettings 檔案。以下螢幕擷取畫面顯示這些選項。

![GlobalWebVSPublish][GlobalWebVSPublish]

如需從 Visual Studio 發行至 Azure 的詳細資訊，請參閱「將 ASP.NET Web 應用程式部署至 Azure 網站」。 

開發與部署作業同時還有一個可用選項，那就是從 Azure 管理入口網站使用 WebMatrix。

![GlobalWebWebMatrix][GlobalWebWebMatrix]

如需此選項的詳細資訊，請參閱[使用 Microsoft WebMatrix 開發與部署網站][aspnetgetstarted]。

儘管這些步驟提供您部署 .COM 網站所需，您還是應該建立管理持續進行的內容發行週期的計畫。這些選項包括導入自訂解決方案、定期重新部署偶爾改變的網站，乃至於功能完整的內容管理系統 (CMS)。如果您是建立新的網站，請注意組件庫裡有些選項可供您使用現有的 CMS 架構，例如 [Drupal][drupal] 或 [Umbraco][umbraco]。

##<a name="customdomain"></a>新增自訂網域
如果這是您的全域網站空間，則您會想要將註冊的網域名稱與網站進行關聯。目前許多第三方服務提供者都有提供網域註冊服務。這些提供者每個都支援建立不同類型的 DNS 記錄來管理您的網域。DNS 記錄可幫助您將使用者易用的 URL (如 "www.contoso.com") 對應至實際 URL 或是託管網站的 IP 位址。 

<div class="dev-callout">
<strong>注意</strong>
<p>在以下討論當中，我們將探討兩種 DNS 記錄。首先，CNAME 記錄可以從某個 URL 重新導向，例如從 "www.contoso.com" 重新導向至 "contoso.azurewebsites.net" 之類的其他 URL。再者，A 記錄可以將 URL (例如 "www.contoso.com") 對應至 172.16.48.1 之類的 IP 位址。</p>
</div>

對 Azure 網站而言，您必須先對 Azure 網站建立一筆 CNAME 記錄。您可以透過第三方註冊機構網站完成此設定。以下為 CNAME 記錄範例。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">類型</th>
   <th align="left" valign="top">主機</th>
   <th align="left" valign="top">Answer</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>CNAME</strong></td>
   <td valign="top">www.contoso.com</td>
   <td valign="top">contoso.azurewebsites.net</td>
   <td valign="top">8000</td>
</tr>
</table>

如果您的網域剛剛註冊，可能需要一天以上的時間來解析所有 DNS 伺服器，以便消化快取 DNS 項目內容。不過，如果您的網域已經存在，則只要一分鐘不到的時間就能完成 CNAME 變更。請注意，CNAME 記錄可將您的網域 (必須使用子網域別名，例如 "www" 才算是有效的網域) 對應至 Azure 網站 URL。CNAME 記錄不管哪一邊都不包含 "http://" 首碼。

在 Azure 管理入口網站的 [**調整**] 索引標籤上，確認您目前執行 ****[**共用] 或 [標準**] 模式 (**免費**網站不支援自訂網域)。接著前往 [**設定**] 索引標籤，並按一下 [**管理網域**] 按鈕。此舉可讓您將網站與自訂網域名稱產生關聯。 

![GlobalWebWebMatrix][GlobalWebWebMatrix]

在您將自訂網域放入清單之前，首先必須移至 DNS 提供者處並為您的自訂網域 (www.contoso.com) 建立 CNAME 記錄以便指向您的 Azure 網站 URL (contoso.azurewebsites.net)。等到此記錄散佈之後，您就可在上一個螢幕擷取畫面所示的對話方塊中輸入自訂網域。當 www.contoso.com 存在指向此網站之 CNAME 記錄時，可確保您具有使用此網站之網域名稱的權限。此時，您可以在對話方塊下方建立內含 IP 位址的 A 記錄。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">類型</th>
   <th align="left" valign="top">主機</th>
   <th align="left" valign="top">Answer</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>A</strong></td>
   <td valign="top">contoso.com</td>
   <td valign="top">172.16.48.1</td>
   <td valign="top">8000</td>
</tr>
</table>

如需詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱][customdns]。

##<a name="ssl"></a>用 SSL 保護網站安全
如果您的網站包含唯讀資訊，就不需要為網站提供安全存取機制。不過，如果您要收集使用者資訊、執行電子商務或是管理任何機密資料，就應該確保網站安全。安全性是個很大的課題，本文無法全盤涵蓋所有最佳實務與技巧。不過，重要的是反白顯示您的網站啟用安全通訊端層 (SSL) 的程序。SSL 可讓使用者使用加密的 HTTPS 位址 (而非 HTTP) 連線到您的網站。在 Azure 網站上使用 SSL 有特定步驟。 

Azure 網站會針對實際網站 URL 自動提供安全連線。例如，當您的網站是 http://contoso.azurewebsites.net，則您只需將 "http" 變更為 "https" (如 **https**://contoso.azurewebsites.net 所示)，透過 SSL 輕鬆地連線。

不過，如果您是使用自訂網域名稱，則您必須採取步驟上傳憑證，並透過 Azure 管理入口網站，為您的網站啟用 SSL。以下步驟提供此程序摘要，不過您可以透過以下主題找到更詳細的說明：[設定 Azure 網站的 SSL 憑證][ssl]。

首先，從憑證授權單位取得 SSL 憑證。如果您打算使用多個子網域來確保網域安全 (例如，www.contoso.com 與 staging.contoso.com)，則您需要取得萬用字元憑證 (*.contoso.com)。這類方式可能會比較昂貴，因此您必須判斷為了獲得此類型憑證所提供的彈性是否物有所值。

從憑證授權單位取得憑證後，您不能原封不動地直接上傳到 Azure。您必須使用 oenssl 命令，產生 .pfx 檔案。openssl 命令是 OpenSSL 專案的一部分。原始檔分佈在 [OpenSSL 網站][openssl]上，但一般而言，您也可以在網際網路找到此工具預先編譯的版本。在以下範例中，我們使用憑證 myserver.crt 與私有金鑰檔 myserver.key 來建立 .pfx 檔案。

	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

若要將憑證上傳至 Azure，請先移至 [**調整**] 索引標籤，然後確認您正在 [**標準**] 模式下執行。[**免費**] 或 [**共用**] 模式皆不支援在自訂網域上使用 SSL。在 [**設定**] 索引標籤上，按一下 [**上傳憑證**] 按鈕。

![GlobalWebUplodateCert][GlobalWebUplodateCert]

接著在 [**SSL 繫結**] 區段中，將憑證對應至它要保護的網域名稱。您可使用兩個選項來進行此對應作業：SNI SSL 與 IP SSL。

![GlobalWebSSLBindings][GlobalWebSSLBindings]

[**IP SSL**] 選項是將公用的專屬 IP 位址對應至網域名稱的傳統方式。所有瀏覽器皆適用此方式。[**SNI SSL**] 選項可讓多個網域共用相同的 IP 位址，而且針對每個網域具有不同的相關聯 SSL 憑證。SNI SSL 無法在某些舊版的瀏覽器上運作 (如需相容性詳細資訊，請參閱 [Wikipedia 上的 SNI SSL 項目][sni])。每個 SSL 憑證都必須按月繳交使用費用 (按小時比例分配)，而定價則是依據您選擇的是 IP SSL 或是 SNI SSL 而定。如需定價資訊，請參閱[網站定價詳細資料][sslpricing]。如需此程序詳細資訊，請參閱[設定 Azure 網站的 SSL 憑證][ssl]。

##<a name="monitor"></a>監視網站
當您的網站開始主動處理使用者要求時，請務必使用監視功能。例如，您可能想要瞭解使用者負載是否引發大量的 CPU 使用時間，這時可能需要擴充網站。或者，應用程式效率不彰導致反應時間增加或是產生錯誤。本節涵蓋 Azure 管理入口網站上一些內建的監視功能。

[**監視**] 索引標籤包含一些圖形化格式的網站重要指標。 

![GlobalWebMonitor1][GlobalWebMonitor1]

您可以使用 [Add Metrics] 按鈕自訂此圖表內的指標。

![GlobalWebMonitor2][GlobalWebMonitor2]

如果是執行 [**標準**] 模式的網站，您也可以啟用端點監視與警示功能。在 [**設定**] 索引標籤上，移至 [**監視**] 區段並設定端點。此端點會從您指定的一或多個位置執行，並定期嘗試存取您的網站。這時會一併收集時間與錯誤資訊。 

在 [**監視**] 索引標籤上，此端點會顯示反應時間。如果您選取端點指標，則您可以按一下 [**新增規則**] 圖示來新增警示規則。

![GlobalWebMonitor3][GlobalWebMonitor3]

當反應時間超出指定的臨界值時，此規則會寄發電子郵件給管理員或其他相關人員。

![GlobalWebMonitor4][GlobalWebMonitor4]

如果您發現網站需要擴充，可以在 [**擴充**] 索引標籤中手動完成，或是透過 [自動擴充] 預覽方式完成。[擴充] 索引標籤提供您向上擴充 (容量更大的專屬機器) 或是向外擴充 (增加額外的共用執行個體或是相同大小的專屬執行個體)。不過，[自動擴充] 預覽功能僅支援向外擴充。如需此選項的詳細資訊，請參閱「如需網站監視的詳細資訊，請參閱[數位行銷活動][scenariodigitalmarketing]案例中的＜隨使用者需求進行調整＞一節」。另請參閱[如何監視網站][howtomonitor]。

##<a name="summary"></a>摘要
若要為您的組織建立 .COM 網站，標準作業包括選擇開發架構、建立網站、部署、自訂網域指派與監視。對於必須確保使用者資料安全的網站，高度建議採用 SSL。本文同時提供使用 Azure 網站執行這類工作的概觀。如需詳細資訊，請參閱本文所參照的下列技術文章。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">領域</th>
   <th align="left" valign="top">資源</th>
</tr>
<tr>
   <td valign="middle"><strong>規劃</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/choose-web-app-service">Azure 網站、雲端服務和 VM：使用時機</a></td>
</tr>
<tr>
   <td valign="middle"><strong>建立</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-dotnet-get-started/">開始使用 Azure 網站和 ASP.NET</a></td>
</tr>
<tr>
   <td valign="middle"><strong>部署</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/develop/net/common-tasks/publishing-with-git/">從原始檔控制發佈至 Azure 網站</a><br/>- <a href="http://www.windowsazure.com/zh-tw/develop/net/tutorials/get-started/">將 ASP.NET Web 應用程式部署至 Azure 網站</a><br/>- <a href="http://www.windowsazure.com/zh-tw/develop/net/tutorials/website-with-webmatrix/">使用 Microsoft WebMatrix 來開發和部署網站</a></td>
</tr>
<tr>
   <td valign="middle"><strong>自訂網域</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/develop/net/common-tasks/custom-dns-web-site/">設定 Azure 網站的自訂網域名稱</a></td>
</tr>
<tr>
   <td valign="middle"><strong>SSL</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/develop/net/common-tasks/enable-ssl-web-site/">設定 Azure 網站的 SSL 憑證</a></td>
</tr>
<tr>
   <td valign="middle"><strong>監視</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-monitor-websites/">如何監視網站</a></td>
</tr>
</table>

  [websitesoverview]:/zh-tw/documentation/services/web-sites/
  [csoverview]:/zh-tw/documentation/services/cloud-services/
  [vmoverview]:/zh-tw/documentation/services/virtual-machines/
  [chooseservice]:/zh-tw/manage/services/web-sites/choose-web-app-service
  
  
  [scenariodigitalmarketing]:/zh-tw/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [howtocreatewebsites]:/zh-tw/documentation/articles/web-sites-dotnet-get-started
  [webmatrix]:http://www.microsoft.com/web/webmatrix/
  [publishingwithgit]:/zh-tw/develop/net/common-tasks/publishing-with-git/
  [aspnetgetstarted]:/zh-tw/develop/net/tutorials/get-started/
  [drupal]:https://drupal.org/
  [umbraco]:http://umbraco.com/
  [customdns]:/zh-tw/develop/net/common-tasks/custom-dns-web-site/
  [ssl]:/zh-tw/develop/net/common-tasks/enable-ssl-web-site/
  [openssl]:http://www.openssl.org/
  [sni]:http://en.wikipedia.org/wiki/Server_Name_Indication
  [sslpricing]:/zh-tw/pricing/details/web-sites/#service-ssl
  [howtomonitor]:/zh-tw/manage/services/web-sites/how-to-monitor-websites/
  
 [GlobalWebCreate]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Create.png
  [GlobalWebQuickGlance]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_QuickGlance.png
  [GlobalWebMonitor1]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor1.png
  [GlobalWebMonitor2]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor2.png
  [GlobalWebMonitor3]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor3.png
  [GlobalWebMonitor4]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor4.png
  [GlobalWebVSPublish]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_VS_Publish.png
  [GlobalWebSSLBindings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_SSL_Bindings.png
  [GlobalWebUplodateCert]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Uplodate_Cert.png
  [GlobalWebCustomDomain]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_CustomDomain.png
  [GlobalWebWebMatrix]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_WebMatrix.png
  [GlobalWebFTPSettings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_FTPSettings.png
  
  
  
  
  
  
  
  
  
  
  

<!--HONumber=35.1-->
