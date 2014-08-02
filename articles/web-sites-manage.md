<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="How to manage" pageTitle="How to manage web sites - Microsoft Azure service management" metaKeywords="Azure portal website management" description="A reference for the Portal web site management pages in Microsoft Azure. Details are provided for each web site management page." metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage Web Sites" authors="timamm" solutions="" writer="timamm" manager="" editor="" />

如何管理網站
============

您可以使用 Azure 入口網站上一系列的網頁 (「索引標籤」) 管理您的網站。以下個別說明每個網站管理頁面。

快速入門
--------

**[快速入門]** 管理頁面包括以下幾個區段：

-   **Get the tools** - 提供連至[安裝 WebMatrix](http://go.microsoft.com/fwlink/?LinkID=226244) (英文) 和 [Microsoft Azure SDK](http://go.microsoft.com/fwlink/?LinkId=246928) (英文) 的連結。
-   **發佈您的應用程式** - 提供的連結可以：下載網站的發行設定檔、重設網站的部署認證、在非預備網站中增加預備發佈 (部署) 位置、了解預備發佈。
-   **Integrate source control** - 從原始檔控制工具或網站，如 TFS、CodePlex、GitHub、Dropbox、Bitbucket 或本機 Git，設定及管理部署。

儀表板
------

**[儀表板]** 管理頁面包括以下區段：

-   一張以特定度量量值表示的網站使用量摘要圖表。

 -   **CPU 時間** - 網站之 CPU 使用率的量值。
 -   **Data In** - 網站接收到的用戶端資料量值。
 -   **Data Out** - 網站傳送給用戶端的資料量值。
 -   **Http Server Errors** - 已傳送的 Http「5xx 伺服器錯誤」訊息數目。
 -   **要求** - 網站所有用戶端要求的計數。
<br />**注意：**您可以選擇 **[監視]** 管理頁面底部的 **[Add Metrics]**，在頁面中增加其他效能度量。如需詳細資訊，請參閱[如何監視網站](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/) (英文)。

-   **Web Endpoint Status** - 已設定要監視之 Web 端點的清單。若尚未設定端點，按一下 **[Configure Web Endpoint Monitoring]**，然後移至 **[設定]** 管理頁面的 **[監視]** 區段。如需詳細資訊，請參閱[如何監視網站](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/) (英文)。

-   **Autoscale Status** - 在標準模式中，您可以自動調整您的資源，這麼一來就只會消耗您所需要的資源。若要啟用自動調整，選擇 **[Configure Autoscale]** 以前往 **[調整]** 頁面。若您的網站為免費或共用模式，則必須先變更為標準模式 (在 **[調整]** 頁面上進行)，才能設定自動調整。**[Autoscale Operation Logs]** 將帶您前往 **[管理服務]** 入口網站，您可在此檢視網站的自動調整歷程記錄。預設的查詢是最近 24 小時，但您可修改查詢。

-   **Usage Overview** - 這個區段顯示網站的 CPU、檔案系統和記憶體使用量的統計資料。
-   **Linked Resources** - 這個區段顯示已連接至網站的資源清單，如 SQL 或 MySQL 資料庫、Microsoft Azure 儲存體帳戶。按一下資源的名稱可管理資源。如果您有 MySQL 資料庫，按一下其名稱將帶您前往 ClearDB 管理頁面。您可在此查看效能度量，或前往 ClearDB 儀表板 (如有需要可在此處升級 MySQL 資料庫)。若沒有列出資源，按一下 **[Manage Linked Resources]** 前往 **[Linked Resources]** 頁面，您可在此為網站增加資源的連結。
-   **[Quick Glance]** 區段包含以下摘要資訊和連結 (視您的設定而異，以下列出的部分選項可能不會出現)：
 -   **View Applicable Add-ons** - 會開啟 **[Purchase from Store]** 對話方塊，您可在其中選擇要購買的附加元件，為網站提供額外的功能。部分附加元件在您的區域或環境中可能不提供。
 -   **View connection strings** - 檢視您的網站的資料庫連接字串。
 -   **Download the publish profile** - 按一下此連結可為您的網站下載發行設定檔。發行設定檔包含您的認證 (使用者名稱和密碼) 和 URL，用於以 FTP 和 Git 上傳內容至您的網站。設定檔為 XML 格式，可在文字編輯器中檢視。
 -   **Set up deployment credentials** - 按一下此選項建立使用者名稱和密碼，用於以 FTP 和 Git 上傳內容至您的網站。在您建立 FTP/Git 部署認證後，可使用它們以 FTP 或 Git 將內容推播至訂閱中的任何網站。建立認證後，若要檢視認證，按一下 **[Download the publish profile]**。您下載的發行設定檔是 XML 格式的文字檔案，可在文字編輯器中檢視。**注意**：不支援使用 Microsoft 帳戶 (Live ID) 認證對 FTP 主機或 Git 儲存機制進行驗證。
 -   **Reset your publish profile credentials** - 重設您的網站的發行設定檔。先前下載的發行設定檔將變無效。
 -   **Set up deployment from source control** - 會顯示對話方塊，您可在其中設定從 Team Foundation Service、CodePlex、GitHub、Dropbox、Bitbucket 或本機 Git 進行連續發佈。
 -   **Add a new deployment slot** - 標準模式的網站可使用此功能建立網站的預備位置。預備位置 (預備網站) 可讓您先驗證網站的內容和組態，再切換到實際執行環境。您也可以使用網站的預備版本逐漸增加內容更新，然後在預備位置上完成更新後，將網站切換至實際執行環境。(您無法為預備中的網站增加預備位置。)
 -   **Edit in Visual Studio Online** - 按一下此連結可以從 Microsoft Azure 入口網站使用 Visual Studio Online 直接線上編輯您的網站。若沒有在 **[設定]** 頁面上啟用此選項，此選項將不會出現。
 -   **Disconnect from Dropbox** - 若您已為了部署目的設定 Dropbox 連接，此連結可讓您中斷連接。
 -   **Delete Git repository** - 若您已設定 Git 儲存機制，此連結可加以刪除。
 -   **狀態** - 指出網站是否正在執行。
 -   **管理服務** - 按一下 **[Operation Logs]** 連結可從 Microsoft Azure 管理服務入口網站檢視網站的作業記錄。
 -   **虛擬 IP 位址** - 若您已經在 **[設定]** 索引標籤的 **[SSL 繫結]** 區段中為網站設定 IP 型 SSL 繫結，則會顯示網站的虛擬 IP 位址。
 -   **網站 URL** - 指定網站在網際網路上的公開存取位址。
 -   **Compute Mode** - 指定網站是以免費、共用、基本或標準模式執行。如需 Web 規模群組模式的詳細資訊，請參閱[如何調整網站](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites) (英文)。
 -   **FTP Hostname** - 指定透過 FTP 發佈至網站時所要使用的 URL。
 -   **FTPS Hostname** - 指定透過 FTPS 發佈至網站時所要使用的 URL。
 -   **Deployment User / FTP User** - 指出透過 FTP 或 Git 將網站部署至 Microsoft Azure 時所使用的帳戶。
 -   **FTP Diagnostic Logs** - 若已在 **[設定]** 管理頁面上啟用診斷記錄，指定網站診斷記錄的 FTP 位置。
 -   **FTPS Diagnostic Logs** - 若已在 **[設定]** 管理頁面上啟用診斷記錄，指定網站診斷記錄的 FTPS 位置。
 -   **位置** - 指定代管網站的資料中心所在區域。
 -   **訂閱名稱** - 指定與網站相關聯的訂閱名稱。
 -   **訂閱識別碼** - 指定與網站相關聯之訂閱的唯一訂閱識別碼 (GUID)。

部署
----

當您已從原始檔控制設定部署時，才會出現此索引標籤。**[部署]** 管理頁面中提供的摘要資訊，與使用您選擇的發佈方法對網站進行的所有部署有關。如果將網站設為 Git 發佈，但尚未進行任何部署，**[部署]** 管理頁面會提供如何使用 GIT 將您的 Web 應用程式部署至網站的說明資訊。

監視
----

**[監視]** 管理頁面以圖表顯示網站的使用情況資訊。依預設，此圖表會顯示和 **[儀表板]** 頁面上的圖表相同的度量，如前文的＜儀表板＞一節中所述。此圖表也可設定為顯示「HTTP 成功」、「HTTP 重新導向」、「HTTP 401 錯誤」、「HTTP 403 錯誤」、「HTTP 404 錯誤」和「HTTP 406 錯誤」等度量。如需這些度量的詳細資訊，請參閱[如何監視網站](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-monitor-websites/) (英文)。

WebJobs
-------

WebJobs 管理頁面可讓您為網站建立隨需、排程或持續執行的工作。如需詳細資訊，請參閱[如何在 Microsoft Azure 網站中使用 WebJobs 功能](http://www.windowsazure.com/en-us/documentation/articles/web-sites-create-web-jobs/) (英文)。

設定
----

**[設定]** 管理頁面可用於設定應用程式專用的設定，包括：

-   **一般** - 設定您的 Web 應用程式所需的 .NET Framework、PHP、Python 或 Java 的版本。標準或基本模式的網站，會有 64 位元平台的選項可供選擇。只有當您擁有只在舊版 IIS (預設值為 **[已整合]**) 上執行的舊版網站時，才可將 **[Managed 管線模式]** 設為 **[傳統]**。若要讓網站能夠使用即時要求模式應用程式 (如 Chat)，可以將 **[Web Sockets]** 設為 **[開啟]**。若要能夠直接線上編輯網站，請將 **[Edit in Visual Studio Online]** 設為 **[開啟]**。
-   **憑證** - 上傳自訂網域的 SSL 憑證。SSL 憑證只能在標準或基本模式中上傳。您上傳的憑證會列在此處，並可指派至您的訂閱和區域中的任何網站。支援萬用憑證 (含星號的憑證)。
-   **網域名稱** - 檢視或新增網站的其他自訂網域名稱。只有共用、基本或標準模式能使用自訂網域名稱。
-   **SSL 繫結** - 只有基本或標準模式能使用自訂網域的 SSL 繫結。為特定網域名稱選擇 SSL 模式 (**[SNI]**、**[IP]** 或 **[No SSL]**)。如果選擇 [SNI] 或 [IP]，您可以從已上傳的憑證中擇一指定為網域的憑證。
-   **部署** - 當您已從原始檔控制啟用部署時，才會出現這個區段。請使用這些設定以設定部署。
-   **應用程式診斷** - 設定相關選項，為支援記錄功能的 Web 應用程式收集診斷資訊。您可以選擇記錄到檔案系統或 Microsoft Azure 儲存體帳戶，並選擇記錄層級以指定收集的資訊量。
-   **Site Diagnostics** - 設定用於收集網站診斷資訊的記錄選項，或啟用 Visual Studio 2012 或 Visual Studio 2013 為網站進行最多 48 小時的遠端偵錯。
-   **監視** - 針對標準模式的網站，從不同地理位置測試 HTTP 或 HTTPS 端點的可用性。
-   **Developer Analytics** - 對 Web 應用程式效能進行分析監視。從 Microsoft Azure 市集中選擇分析附加元件，或選擇自訂分析提供者，如 New Relic。
-   **應用程式設定** - 指定 Web 應用程式啟動時載入的名稱/值組。如果是 .NET 網站，這些設定會在執行階段插入網站的 .NET 組態的 AppSettings 中，並覆蓋現有設定。如果是 PHP 和 Node 網站，您可以在執行階段將這些設定視為環境變數使用。
-   **連接字串** - 檢視連至連結資源的連接字串。如果是 .NET 網站，這些連接字串會在執行階段插入網站的 .NET 組態的 connectionStrings 設定中，並覆蓋索引鍵等於所連結資料庫名稱的現有項目。如果是 PHP 和 Node 網站，您可以在執行階段將這些設定視為環境變數使用。
-   **預設文件** - 如果網站的預設文件不在此清單中，可將其加入清單。如果網站包含清單中的多個檔案，請變更清單中檔案的順序，確定網站的預設文件是清單中的第一個。
-   **處理常式對應** - 新增自訂指令碼處理器，用以處理特定檔案類型 (如 \*.php) 的要求。
-   **Virtual Applications and Directories** - 設定與您的網站相關聯的虛擬應用程式和目錄。您也可以選擇將虛擬目錄標示為網站組態中的應用程式。

如需如何設定網站的詳細資訊，請參閱[如何設定網站](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites) (英文)。

調整
----

在 **[調整]** 管理頁面中，您可以指定 Web 規模群組模式 (**[免費]**、**[共用]**、**[基本]** 或 **[標準]**)。**[共用]**、**[基本]** 和 **[標準]** 模式提供較好的輸送量和效能。**[共用]**、**[基本]** 和 **[標準]** 模式可讓您增加 **[執行個體計數]**，此計數是您的網站及同一 Web 規模群組中其他網站所使用的虛擬機器數目。

在 **[標準]** 模式中，您也可以變更 **[執行個體大小]**，增加每個執行個體的核心計數和記憶體容量。為了獲得更好的成本效益，您可以選擇 **[自動調整]** 選項，讓 Microsoft Azure 為網站動態配置資源。

如需設定網站規模選項的詳細資訊，請參閱[如何調整網站](http://www.windowsazure.com/en-us/manage/services/web-sites/how-to-scale-websites) (英文)。

連結的資源
----------

**[Linked Resources]** 管理頁面提供網站所使用的 Microsoft Azure 資源清單，包括 SQL 資料庫、MySQL 資料庫、Azure 儲存體帳戶。按一下資源的名稱可管理資源。

備份
----

**[備份]** 管理頁面可讓您建立網站的自動或手動備份、還原網站至先前的狀態、依據其中一個備份建立新網站。如需詳細資訊，請參閱 [Microsoft Azure 網站備份](http://www.windowsazure.com/en-us/documentation/articles/web-sites-backup/) (英文) 和[還原 Microsoft Azure 網站](http://www.windowsazure.com/en-us/documentation/articles/web-sites-restore/) (英文)。

管理頁面圖示
------------

在網站每一個管理頁面的底部皆會顯示圖示。其中數個圖示會出現在多個頁面上，少數幾個則只會出現在特定頁面上。**[儀表板]** 管理頁面的底部會顯示以下圖示：

-   **瀏覽** - 開啟網站的預設頁面。
-   **停止** - 停止網站。
-   **重新啟動** - 重新啟動網站。
-   **管理網域** - 將網域對應至此網站。**[免費]** 規模模式的網站無此功能圖示。
-   **刪除** - 刪除網站。
-   **WebMatrix** - 在 WebMatrix 中開啟支援的網站，讓您變更網站，並將這些變更發佈到 Microsoft Azure 上的網站。

以下圖示不會出現在 **[儀表板]** 管理頁面的底部，但會出現在其他管理頁面底部用以完成特定工作：

-   **Add Metrics** - 位於 **[監視]** 管理頁面的底部，可讓您在 [監視] 管理頁面顯示的圖表中新增度量。
-   **連結** - 位於 **[Linked Resources]** 管理頁面的底部，可讓您建立其他 Microsoft
-   Azure 資源的管理連結。例如，若您的網站存取 SQL 資料庫，您可以按一下 **[連結]** 建立此資料庫資源的管理連結。

