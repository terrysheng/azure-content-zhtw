<properties urlDisplayName="How to manage" pageTitle="如何管理網站 - Microsoft Azure 服務管理" metaKeywords="Azure 入口網站管理" description="Microsoft Azure 的 Portal 網站管理頁面參考。對於各個網站管理頁面均提供詳細資料。" metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Websites" authors="cephalin"  solutions="" writer="mwasson" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

#<a name="howtomanage"></a>透過 Azure 管理入口網站管理網站

您可以使用 Azure 入口網站上一系列的網頁 (「索引標籤」) 管理您的網站。以下個別說明每個網站管理頁面。

## 快速入門 ##
[**快速入門**] 管理頁面包括以下幾個區段：

- **取得工具** - 提供連至[安裝 WebMatrix][mswebmatrix] 和 [Microsoft Azure SDK][azuresdk] 的連結。
- **發佈您的應用程式** - 提供的連結可以下載網站的發行設定檔、重設網站的部署認證、在非預備網站中增加預備發佈 (部署) 位置、了解預備發佈。
- **整合原始檔控制** - 從原始檔控制工具或網站，如 TFS、CodePlex、GitHub、Dropbox、Bitbucket 或本機 Git，設定及管理部署。

## 儀表板 ##
[**儀表板**] 管理頁面包括以下幾個區段：

一張以特定度量量值表示的網站使用量摘要圖表。

 - **CPU 時間** - 網站之 CPU 使用量的量值。
 - **資料輸入** - 網站接收到的用戶端資料量值。
 - **資料輸出** - 網站傳送給用戶端的資料量值。
 - **Http 伺服器錯誤** - 已傳送的 Http「5xx 伺服器錯誤」訊息數目。
 - **要求** - 網站所有用戶端要求的計數。

**注意：**您可以選擇 [**監視**] 管理頁面底部的 [**Add Metrics**]，在頁面中增加其他效能度量。如需詳細資訊，請參閱[如何監視網站][Monitor]。

**Web Endpoint Status** - 已設定要監視之 Web 端點的清單。如果尚未設定端點，請按一下 [**設定 Web 端點監視**] 並移至 [**設定**] 管理頁面的 [**監視**] 區段。如需詳細資訊，請參閱[如何監視網站][Monitor]。

**Autoscale Status** - 在標準模式中，您可以自動調整您的資源，這麼一來就只會消耗您所需要的資源。若要啟用自動調整，選擇 [**設定自動調整**] 以前往 [**調整**] 頁面。若您的網站為 [免費] 或 [共用] 模式，則必須先變更為 [標準] 模式 (在 [**調整**] 頁面上進行)，才能設定自動調整。[**自動調整作業記錄檔**] 將帶您前往 [**管理服務**] 入口網站，您可在此檢視網站的自動調整歷程記錄。預設的查詢是最近 24 小時，但您可修改查詢。

**使用量概觀** - 這個區段顯示網站的 CPU、檔案系統和記憶體使用量的統計資料。

**連結的資源** - 這個區段顯示已連線至網站的資源清單，如 SQL 或 MySQL 資料庫、Microsoft Azure 儲存體帳戶。按一下資源的名稱可管理資源。如果您有 MySQL 資料庫，按一下其名稱將帶您前往 ClearDB 管理頁面。您可在此查看效能度量，或前往 ClearDB 儀表板 (如有需要可在此處升級 MySQL 資料庫)。若沒有列出資源，請按一下 [**管理連結的資源**] 以移至 [**連結的資源**] 頁面，您可在此為網站增加資源的連結。

[**快速概覽**] 區段包含以下摘要資訊和連結 (視您的設定而異，以下列出的部分選項可能不會出現)：

 - **檢視適用的附加元件** - 會開啟 [**從市集購買**] 對話方塊，您可在其中選擇要購買的附加元件，為網站提供額外的功能。部分附加元件在您的區域或環境中可能不提供。
 - **檢視連接字串** - 檢視您的網站的資料庫連接字串。
 - **下載發行設定檔** - 按一下此連結可為您的網站下載發行設定檔。發行設定檔包含您的認證 (使用者名稱和密碼) 和 URL，用於以 FTP 和 Git 上傳內容至您的網站。設定檔為 XML 格式，可在文字編輯器中檢視。
 - **設定部署認證** - 按一下此選項建立使用者名稱和密碼，用於以 FTP 和 Git 上傳內容至您的網站。您可以使用這些認證將內容推送至訂閱中的任何網站。(請參閱 [FTP 認證]。)**注意**：不支援使用 Microsoft 帳戶 (Live ID) 認證對 FTP 主機或 Git 儲存機制進行驗證。
 - **重設您的發行設定檔認證** - 重設您的網站的發行設定檔。先前下載的發行設定檔將變無效。
 - **Set up deployment from source control** - 會顯示對話方塊，您可在其中設定從 Team Foundation Service、CodePlex、GitHub、Dropbox、Bitbucket 或本機 Git 進行連續發佈。
 - **Add a new deployment slot** - 標準模式的網站可使用此功能建立網站的預備位置。預備位置 (預備網站) 可讓您先驗證網站的內容和組態，再切換到實際執行環境。您也可以使用網站的預備版本逐漸增加內容更新，然後在預備位置上完成更新後，將網站切換至實際執行環境。(您無法為預備中的網站增加預備位置。)
 - **在 Visual Studio Online 中編輯** - 按一下此連結可以從 Microsoft Azure 入口網站使用 Visual Studio Online 直接線上編輯您的網站。若沒有在 [**設定**] 頁面上啟用此選項，此選項將不會出現。
 - **Disconnect from Dropbox** - 若您已為了部署目的設定 Dropbox 連接，此連結可讓您中斷連接。
 - **Delete Git repository** - 若您已設定 Git 儲存機制，此連結可加以刪除。
 - **狀態** - 指出網站是否正在執行。
 - **管理服務** - 按一下 [**作業記錄檔**] 連結可從 Microsoft Azure 管理服務入口網站檢視網站的作業記錄檔。
 - **虛擬 IP 位址** - 若您已經在 [**設定**] 索引標籤的 [**SSL 繫結**] 區段中為網站設定 IP 型 SSL 繫結，則會顯示網站的虛擬 IP 位址。 
 - **網站 URL** - 指定網站在網際網路上的公開存取位址。
 - **運算模式** - 指定網站是以免費、共用、基本或標準模式執行。如需 Web 規模群組模式的詳細資訊，請參閱[如何調整網站][Scale] (英文)。
 - **FTP 主機名稱** - 指定透過 FTP 發佈至網站時所要使用的 URL (請參閱 [FTP 認證])。
 - **FTPS 主機名稱** - 指定透過 FTPS 發佈至網站時所要使用的 URL (請參閱 [FTP 認證])。 
 - **部署使用者/FTP 使用者** - 指出透過 FTP 或 Git 將網站部署至 Microsoft Azure 時所使用的帳戶 (請參閱 [FTP 認證])。
 - **FTP 診斷記錄** - 若已在 [**設定**] 管理頁面上啟用診斷記錄，指定網站診斷記錄的 FTP 位置。
 - **FTPS 診斷記錄** - 若已在 [**設定**] 管理頁面上啟用診斷記錄，指定網站診斷記錄的 FTPS 位置。
 - **位置** - 指定代管網站的資料中心所在區域。
 - **訂閱名稱** - 指定與網站相關聯的訂閱名稱。
 - **訂閱識別碼** - 指定與網站相關聯之訂閱的唯一訂閱識別碼 (GUID)。


##部署##
當您已從原始檔控制設定部署時，才會出現此索引標籤。[**部署**] 管理頁面中提供的摘要資訊，與使用您選擇的發佈方法對網站進行的所有部署有關。如果將網站設為 Git 發佈，但尚未進行任何部署，[**部署**] 管理頁面會提供如何使用 GIT 將您的 Web 應用程式部署至網站的說明資訊。

##監視##
[**監視**] 管理頁面以圖表顯示網站的使用情況資訊。依預設，此圖表會顯示和 [**儀表板**] 頁面上的圖表相同的度量，如前文的＜儀表板＞一節中所述。此圖表也可設定為顯示「HTTP 成功」、「HTTP 重新導向」、「HTTP 401 錯誤」、「HTTP 403 錯誤」、「HTTP 404 錯誤」和「HTTP 406 錯誤」等度量。如需這些度量的詳細資訊，請參閱[如何監視網站][Monitor]。

##WebJobs##
WebJobs 管理頁面可讓您為網站建立隨需、排程或持續執行的工作。如需詳細資訊，請參閱[如何在 Microsoft Azure 網站中使用 WebJobs 功能](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-create-web-jobs/) (英文)。

##設定##
[**設定**] 管理頁面可用於設定應用程式專用的設定。

如需詳細資訊，請參閱[如何設定網站][Configure]。


##調整##
在 [**調整**] 管理頁面上，您可以指定 Web 規模群組模式 (**免費**、**共用**、**基本**或**標準**)。[**共用**]、[**基本**] 和 [**標準**] 模式提供較好的輸送量和效能。[**共用**]、[**基本**] 和 [**標準**] 模式可讓您增加 [**執行個體計數**]，此計數是您的網站及同一 Web 規模群組中其他網站所使用的虛擬機器數目。
 
在 [**標準**] 模式中，您也可以變更 [**執行個體大小**]，增加每個執行個體的核心計數和記憶體容量。為了獲得更好的成本效益，您可以選擇 [**自動調整**] 選項，讓 Microsoft Azure 為網站動態配置資源。 

如需設定網站規模選項的詳細資訊，請參閱[如何調整網站][Scale]。

##連結的資源##
[**連結的資源**] 管理頁面提供網站所使用的 Microsoft Azure 資源清單，包括 SQL 資料庫、MySQL 資料庫、Azure 儲存體帳戶。按一下資源的名稱可管理資源。

##備份##
[**備份**] 管理頁面可讓您建立網站的自動或手動備份、還原網站至先前的狀態、依據其中一個備份建立新網站。如需詳細資訊，請參閱 [Microsoft Azure 網站備份](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-backup/) (英文) 和[還原 Microsoft Azure 網站](http://www.windowsazure.com/zh-tw/documentation/articles/web-sites-restore/) (英文)。

##管理頁面圖示##
在網站每一個管理頁面的底部皆會顯示圖示。其中數個圖示會出現在多個頁面上，少數幾個則只會出現在特定頁面上。[**儀表板**] 管理頁面的底部會顯示以下圖示：

- **瀏覽** - 開啟網站的預設頁面。
- **停止** - 停止網站。
- **重新啟動** - 重新啟動網站。
- **管理網域** - 將網域對應至此網站。[**免費**] 規模模式的網站無此功能圖示。
- **刪除** - 刪除網站。
- **WebMatrix** - 在 WebMatrix 中開啟支援的網站，讓您變更網站，並將這些變更發佈到 Microsoft Azure 上的網站。

以下圖示不會出現在 [**儀表板**] 管理頁面的底部，但會出現在其他管理頁面底部用以完成特定工作：

- **Add Metrics** - 位於 [**監視**] 管理頁面的底部，可讓您在 [監視] 管理頁面顯示的圖表中新增度量。
- **連結** - 位於 [**連結的資源**] 管理頁面的底部，可讓您建立其他 Microsoft
-  Azure 資源的管理連結。例如，若您的網站存取 SQL 資料庫，您可以按一下 [**連結**] 建立此資料庫資源的管理連結。


## FTP 認證

您可以使用兩組 FTP 認證；*部署* 認證和 *發行設定檔* 認證。以下是兩者的主要差異：

**部署認證**

- 與 Microsoft 帳戶相關聯。 
- 可用於部署所有與帳戶相關聯訂閱中的任何網站。 
- 可自選使用者名稱/密碼
- 通常用於 Git 或 FTP 部署

	 
**發行設定檔認證**

- 與單一網站相關聯。 
- 無法自選使用者名稱或密碼。
- 通常用於 Web Deploy，但也可用於 FTP。


您可以使用這兩組認證之一。FTP 和 FTPS 主機名稱會列於 [**快速概覽**]。


### 使用部署認證

若要設定部署認證： 

1.	在管理入口網站中，移至您網站的 [**儀表板**]。
2.	按一下 [**設定部署認證**]。
3.	在此對話方塊中，輸入使用者名稱和密碼。

注意：在步驟 2 中，如果您已擁有部署認證，則入口網站會表示 [**重設部署認證**]。按一下此項目可設定新密碼或變更使用者名稱。

部署認證與 Microsoft 帳戶相關聯。如果您變更使用者名稱或密碼，這項變更會套用至與該帳戶相關聯的所有網站中。如果 Azure 訂閱有多名系統管理員，則每個人都有自己的認證。 
完整的 FTP 使用者名稱為 "website\username"。此項會在 [**快速概覽**] 下方的入口網站中列為 [**部署/FTP 使用者**]。


### 使用發行設定檔認證

每個網站都有自己的發行設定檔認證。檢視這些認證：

1.	在管理入口網站中，移至您網站的 [**儀表板**]。
2.	按一下 [**下載發行設定檔**]。

發行設定檔檔案為 XML 檔案。其中包含兩個設定檔，一個是針對 Web Deploy，另一個則是針對 FTP。

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.azurewebsites.net:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.azurewebsites.net"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.windows.net/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.azurewebsites.net" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

查詢含有 <code>publishMethod="FTP"</code> 的設定檔。使用者名稱列於 <code>userName</code> 下方，而密碼則列於 <code>userPWD</code> 下方。

若要重設密碼，請按一下 [**重設您的發行設定檔認證**]。若要取得新的認證，請再下載一次發行設定檔。發行設定檔認證會與該網站相關聯。每個網站都有自己的發行設定檔。



<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:http://go.microsoft.com/fwlink/?LinkId=246928

[設定]: http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-configure-websites

[監視]: http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-monitor-websites/

[調整]: http://www.windowsazure.com/zh-tw/manage/services/web-sites/how-to-scale-websites


<!-- Anchors. -->
[FTP 認證]: #ftp-credentials

