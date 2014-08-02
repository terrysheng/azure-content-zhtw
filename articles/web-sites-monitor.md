<properties linkid="manage-services-how-to-monitor-websites" urlDisplayName="How to monitor" pageTitle="How to monitor web sites - Azure service management" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Learn how to monitor Azure web sites by using the Monitor page in the Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Web Sites" authors="" solutions="" manager="" editor="" />

如何監視網站
============

網站能透過 [監視] 管理頁面來提供監視功能。[監視] 管理頁面能提供網站效能統計資料，如下文所述。

目錄
----

-   [作法：新增網站度量](#websitemetrics)
-   [作法：接收來自網站度量的警示](#howtoreceivealerts)
-   [作法：檢視網站的使用量配額](#howtoviewusage)
-   [作法：減少資源使用量](#resourceusage)
-   [超過資源使用量配額時的後續動作](#exceeded)
-   [作法：設定診斷功能及下載網站記錄](#howtoconfigdiagnostics)
-   [作法：監視 Web 端點狀態](#webendpointstatus)

作法：新增網站度量
------------------

1.  在 [Azure 管理入口網站](http://manage.windowsazure.com/)中，從網站的 [管理] 頁面按一下 **[監視]** 索引標籤以顯示 **[監視]** 管理頁面。依預設，**[監視]** 頁面之圖表顯示的度量與 **[儀表板]** 頁面的圖表相同。

2.  若要檢視網站的其他度量，請按一下頁面底部的 **[Add Metrics]** 以顯示 **[Choose Metrics]** 對話方塊。

3.  按一下以選取要顯示於 **[監視]** 頁面中的其他度量。

4.  選取要新增至 **[監視]** 頁面中的度量後，請按一下 **[確定]**。

5.  在將度量新增至 **[監視]** 頁面後，請按一下以啟用 (停用) 每個度量旁的選項方塊，將度量新增至頁面頂端的圖表 (或將度量移除)。

6.  若要將 **[監視]** 頁面中的度量移除，請選取要移除的度量，然後按一下頁面底部的 **[Delete Metric]** 圖示。

以下清單說明可在 **[監視]** 頁面之圖表中檢視的度量：

-   **CPUTime** - 網站之 CPU 使用率的量值。
-   **要求** - 網站的用戶端要求計數。
-   **Data Out** - 網站傳送給用戶端的資料量值。
-   **Data In** - 網站接收到的用戶端資料量值。
-   **Http Client Errors** - 已傳送的 Http「4xx 用戶端錯誤」訊息數目。
-   **Http Server Errors** - 已傳送的 Http「5xx 伺服器錯誤」訊息數目。
-   **Http Successes** - 已傳送的 Http「2xx 成功」訊息數目。
-   **Http Redirects** - 已傳送的 Http「3xx 重新導向」訊息數目。
-   **Http 401 errors** - 已傳送的 Http「401 未授權」訊息數目。
-   **Http 403 errors** - 已傳送的 Http「403 禁止」訊息數目。
-   **Http 404 errors** - 已傳送的 Http「404 找不到」訊息數目。
-   **Http 406 errors** - 已傳送的 Http「406 無法接受」訊息數目。

作法：接收來自網站度量的警示
----------------------------

在 **[標準]** 網站模式下，您可以接收以網站監視度量為基礎的警示。您必須先設定監視所用的 Web 端點才能使用警示功能，您可以在 **[設定]** 頁面的 **[監視]** 區段中加已設定。在 Azure 管理入口網站的 **[設定]** 頁面中，您可以建立規則以在選擇之度量達到指定的值時觸發警示。您也可以選擇在警示觸發時傳送電子郵件。如需詳細資訊，請參閱[作法：在 Windows Azure 中接收警示通知及管理警示規則](http://go.microsoft.com/fwlink/?LinkId=309356)。

作法：檢視網站的使用量配額
--------------------------

在網站的 **[調整]** 管理頁面中，您可以將網站設定為在 **[共用]** 或 **[標準]** 網站模式下運作。在 **[共用]** 網站模式下，每個 Azure 訂閱都能存取針對執行每個區域最多 100 個網站而提供的資源集區。基於此目的而供應給每個網站訂閱的資源集區，將由相同地理區域中設定為在 **[共用]** 模式下執行的其他網站共用。由於這些資源需與其他網站共用，因此所有訂閱在使用這些資源時均會受到限制。套用在訂閱上的資源使用限制，係以列示於各網站 **[儀表板]** 管理頁面之 [usage overview] 區段下的使用量配額來表示。

**注意**

將網站設定為在 **[標準]** 模式下運作時，系統會配置等同於 [Windows Azure 的虛擬機器和雲端服務大小](http://go.microsoft.com/fwlink/?LinkID=309169)內表格之 **[小型]** (預設值)、**[中型]** 或 **[大型]** 虛擬機器大小的專屬資源。對於在 **[標準]** 模式下運作的網站，其訂閱可使用的資源並沒有限制。然而，每個區域容許建立的 **[標準]** 模式網站數目只有 500 個。

### 檢視設定為共用網站模式之網站的使用量配額

若要判斷網站影響資源使用量配額的程度，請遵循以下步驟：

1.  開啟網站的 **[儀表板]** 管理頁面。
2.  在 **[usage overview]** 區段下方，**[Data Out]**、**[CPU 時間]** 及 **[File System Storage]** 的使用量配額均顯示於此。每個資源顯示的綠色長條代表網站目前耗用訂閱之資源使用量配額的程度；每項資源顯示的灰色長條代表與您網站訂閱相關之其他所有共用模式網站目前耗用訂閱之資源使用量配額的程度。

資源使用量配額能預防超用以下資源：

-   **Data Out** - 在目前的配額間隔 (24 小時) 內，於 **[共用]** 模式下運作之網站傳送到用戶端的資料量量值。
-   **CPU 時間** - 在目前的配額間隔內，於 **[共用]** 模式下運作之網站使用的 CPU 時間數量。
-   **File System Storage** - 於 **[共用]** 模式下運作之網站使用的檔案系統儲存體數量。

當超過訂閱的使用量配額時，Azure 會採取動作來停止資源超用。此舉是為了避免訂閱者耗盡資源，損害其他訂閱者的權益。

作法：減少資源使用量
--------------------

由於 Azure 會在 24 小時的配額間隔內量測訂閱之共用模式網站所用的資源，藉此計算資源使用量配額，因此請將以下事項納入考量：

-   隨著設定為在共用模式下運作的網站數目增加，超出共用模式資源使用量配額的機率也會提高。在即將超出資源使用量配額的情況下，請考慮減少設定為在共用模式下運作的網站數目。
-   同樣地，隨著任何在共用模式下運作之網站的執行個體數目增加，超出共用模式資源使用量配額的機率也會提高。在即將超出資源使用量配額的情況下，請考慮縮減共用模式之網站的額外執行個體。

超過資源使用量配額時的後續動作
------------------------------

在配額間隔 (24 小時) 內超出訂閱的資源使用量配額時，Azure 會採取以下措施：

-   **Data Out** - 在超出此項配額時，Azure 會在目前配額間隔的剩餘時間內停止訂閱之所有已設定為在 **[共用]** 模式下運作的網站。Azure 會在下一個配額間隔開始時啟動網站。

-   **CPU 時間** - 在超出此項配額時，Azure 會在目前配額間隔的剩餘時間內停止訂閱之所有已設定為在 **[共用]** 模式下運作的網站。Azure 會在下一個配額間隔開始時啟動網站。

-   **File System Storage** - 當部署會導致 File System Storage 使用量配額超量時，Azure 會禁止部署訂閱之任何已設定為在共用模式下運作的網站。當 File System Storage 資源達到配額允許的上限時，讀取操作仍可存取檔案系統儲存體，不過所有寫入操作 (包括一般網站活動所需的寫入操作) 都將遭到封鎖。當這種情況發生時，您可以將一或多個在共用網站模式下運作的網站設定為在標準網站模式下運作，抑或是減少檔案系統儲存體的使用量，使其低於 File System Storage 使用量配額。

作法：設定診斷功能及下載網站記錄
--------------------------------

您可以在網站的 **[設定]** 管理頁面中啟用診斷功能。診斷功能分為兩種：**[應用程式診斷]** **[和網站診斷]**。

#### 應用程式診斷

**[設定]** 管理頁面的 **[應用程式診斷]** 區段能控制應用程式產生之資訊的記錄，在記錄發生於應用程式內部的事件時，此項功能便能派上用場。例如，當應用程式內部發生錯誤時，您也許會希望能向使用者呈現易於理解的錯誤，意即在記錄中寫入更詳細的錯誤資訊，以供日後分析之用。

您可以啟用或停用以下應用程式診斷：

-   **Application Logging (File System)** - 開啟應用程式產生之資訊的記錄功能。**[Logging Level]** 欄位決定要記錄 [錯誤]、[警告] 還是 [資訊] 層級的資訊。您也可以選取 [詳細資訊] 來記錄應用程式產生的所有資訊。

    此項設定所產生的記錄乃儲存於網站的檔案系統中，因此您可以使用下文**下載網站的記錄檔**小節所述的步驟予以下載。

-   **Application Logging (Table Storage)** - 開啟應用程式產生之資訊的記錄功能，與 [Application Logging (File System)] 選項相似。不過記錄資訊是以資料表形式儲存在 Azure 儲存體帳戶中。

    若要指定 Azure 儲存體帳戶和資料表，請選擇 **[開啟]**、選擇 **[Logging Level]**，然後再選擇 **[Manage Table Storage]**。指定要使用的儲存體帳戶和資料表，或建立新的資料表。

    您可以使用 Azure 儲存體用戶端來存取儲存於資料表中的記錄資訊。

-   **Application Logging (Blob Storage)** - 開啟應用程式產生之資訊的記錄功能，與 [Application Logging (Table Storage)] 選項相似。不過記錄資訊是以 Blob 形式儲存在 Azure 儲存體帳戶中。

    若要指定 Azure 儲存體帳戶和 Blob，請選擇 **[開啟]**、選擇 **[Logging Level]**，然後再選擇 **[Manage Blob Storage]**。指定要使用的儲存體帳戶、Blob 容器及 Blob 名稱，或建立新的容器和 Blob。

如需 Azure 儲存體帳戶的詳細資訊，請參閱[如何管理儲存體帳戶](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/)。

**注意**

只有 .NET 應用程式才支援資料表或 Blob 儲存體的應用程式記錄功能。

由於儲存體的應用程式記錄功能須使用儲存體用戶端來檢視記錄資料，因此當您計劃使用了解如何直接讀取及處理 Azure 資料表或 Blob 儲存體之資料的服務或應用程式時，這項功能最為實用。檔案系統的記錄功能會產生可透過 FTP 或其他公用程式下載到本機電腦的檔案，如本節後續內容所述。

**注意**

您可以同時啟用 **[Application diagnostics (file system)]**、**[Application diagnostics (table storage)]** 及 **[Application diagnostics (blob storage)]**，且它們可以擁有各自的記錄層級組態。例如，您也許會想要將錯誤和警告記錄在儲存體並做為長期的記錄解決方案，同時在檢測應用程式程式碼後啟用檔案系統記錄並搭配詳細資訊層級，以供疑難排解問題之用。

**注意**

您也可以在 Azure PowerShell 中使用 **Set-AzureWebsite** Cmdlet 來啟用診斷功能。

如果您尚未安裝 Azure PowerShell，或尚未將其設定為使用 Azure 訂閱，請參閱[如何使用 Azure PowerShell](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/) (英文)。

**注意**

應用程式記錄功能仰賴應用程式產生的記錄資訊。用來產生記錄資訊的方法和資訊的格式取決於應用程式的撰寫語言。如需有關使用應用程式記錄功能的語言特有資訊，請參閱以下文章：

-   **.NET** - [啟用 Azure 網站的診斷記錄功能](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
-   **Node.js** - [如何在 Azure 網站中偵錯 Node.js 應用程式](/en-us/develop/nodejs/how-to-guides/Debug-Website/)

只有 .NET 應用程式才支援資料表或 Blob 儲存體的應用程式記錄功能。

#### 網站診斷

**[設定]** 管理頁面中的 **[site diagnostics]** 區段能控制由 Web 伺服器執行的記錄功能，如 Web 要求、頁面提供失敗或頁面提供所需時間等項目的記錄。您可以啟用或停用以下選項：

-   **Web Server Logging** - 開啟 Web 伺服器記錄功能以使用 W3C 擴充記錄檔格式儲存網站記錄。Web 伺服器記錄功能會產生涵蓋所有傳入網站之要求的記錄，其中包括用戶端 IP 位址、要求的 URI、回應的 HTTP 狀態碼及用戶端的使用者代理程式字串等資訊。您可以將記錄儲存在 Azure 儲存體帳戶或檔案系統中。

   若要將 Web 伺服器記錄儲存在 Azure 儲存體帳戶，請選擇 **[儲存體]**，然後再選擇 **[管理儲存體]** 以指定儲存體帳戶和用來保存記錄的 Azure Blob 容器。如需 Azure 儲存體帳戶的詳細資訊，請參閱[如何管理儲存體帳戶](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/)。

   若要將 Web 伺服器記錄儲存在檔案系統中，請選擇 **[檔案系統]**。如此能啟用 **[配額]** 方塊以供您設定容納記錄檔的磁碟空間上限。大小的下限為 25MB，上限為 100MB。預設大小為 35MB。

   依預設，系統永遠不會刪除 Web 伺服器記錄。若要指定系統自動刪除記錄之前經過的時間，請選取 **[Set Retention]**，然後在 **[保留期間]** 方塊中輸入記錄的保存天數。此項設定適用於 Azure 儲存體和檔案系統選項。

-   **Detailed Error Messages** - 開啟詳細錯誤記錄功能可額外記錄有關 HTTP 錯誤 (大於 400 的狀態碼) 的資訊。

-   **失敗要求的追蹤** - 開啟失敗要求追蹤功能可擷取失敗的用戶端要求資訊，如 400 系列的 HTTP 狀態碼。失敗要求追蹤功能會產生 XML 文件，其中包括要求在 IIS 中經過哪些模組的追蹤、模組傳回的詳細資料及叫用模組的時間。這些資訊可用來隔離出發生失敗的元件。

啟用網站診斷後，按一下 **[設定]** 管理頁面底部的 **[儲存]** 圖示可套用您設定的選項。

**重要事項**

記錄和追蹤等功能會使網站的資源需求大增。建議您在重製欲疑難排解的問題後關閉記錄和追蹤功能。

### 進階組態

您可以在 **[設定]** 管理頁面的 **[應用程式設定]** 區段中新增索引鍵/值配對，進一步修改診斷功能。以下是可在 **[應用程式設定]** 中配置的設定：

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

-   儲存應用程式記錄的位置，相對於 Web 根目錄。

-   預設值：..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

-   擷取應用程式記錄時，使用的緩衝區大小上限。剛開始時，系統會先將資訊寫入緩衝區，然後再排清到檔案或儲存體。如果系統將新資訊寫入緩衝區，然後再予以排清，您便會遺失先前記錄的資訊。如果應用程式突然產生大量的記錄資訊，請考慮增加緩衝區的大小。

-   預設值：10MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

-   **Application** 資料夾的大小上限，其為寫入檔案之應用程式診斷的儲存位置。

-   預設值：1MB

### 下載網站的記錄檔

您可以使用 FTP、Azure PowerShell 或 Azure 命令列工具來下載記錄檔。

**FTP**

1.  開啟網站的 **[儀表板]** 管理頁面，接著記下 **[Diagnostics Logs]** 列示的 FTP 站台和 **[Deployment User]** 列示的帳戶。FTP 站台是記錄檔的所在位置，而列示於 [Deployment User] 下的帳戶可用來通過 FTP 站台的驗證。
2.  如果您尚未建立部署認證，列示於 **[Deployment User]** 下的帳戶將會是 **[未設定]**。在這種情況下，您必須建立部署認證 (如 [儀表板] 的 [Reset Deployment Credentials] 區段所述)，因為您必須使用這些認證來通過儲存記錄檔之 FTP 站台的驗證。Azure 不支援使用 Live ID 認證來通過此 FTP 站台的驗證。
3.  請考慮使用 FTP 用戶端 (如 [FileZilla](http://go.microsoft.com/fwlink/?LinkId=247914)) 來連接 FTP 站台。與瀏覽器普遍的功能相比，FTP 用戶端更能簡化認證的指定和 FTP 站台資料夾的檢視。
4.  請將 FTP 站台上的記錄檔複製到本機電腦。

**Azure PowerShell**

1.  從 **[開始畫面]** 或 **[開始功能表]** 搜尋 **Azure PowerShell**。在 **[Azure PowerShell]** 項目上按一下滑鼠右鍵，然後選取 **[以系統管理員身份執行]**。

    **注意**

    如果您尚未安裝 **Azure PowerShell**，請參閱 [Windows Azure Cmdlet 使用者入門](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx)以取得安裝和組態資訊。

2.  在 Azure PowerShell 提示字元中使用下列命令來下載記錄檔：

        Save-AzureWebSiteLog -Name websitename

    如此可下載 **websitename** 所指定之網站的記錄檔，並將其儲存到目前目錄中的 **log.zip** 檔案。

    您也可以使用以下命令來檢視記錄事件的即時資料流：

        Get-AzureWebSiteLog -Name websitename -Tail

    如此可讓記錄資訊在發生時顯示於 Azure PowerShell 提示字元中。

**Azure 命令列工具**

開啟新的命令提示字元、PowerShell、Bash 或終端機工作階段，然後使用以下命令來下載記錄檔：

    azure site log download websitename

如此可下載 **websitename** 所指定之網站的記錄檔，並將其儲存到目前目錄中的 **log.zip** 檔案。

您也可以使用以下命令來檢視記錄事件的即時資料流：

    azure site log tail websitename

如此可將記錄資訊顯示在執行命令的命令提示字元、PowerShell、Bash 或終端機工作階段中。

**注意**

如果您尚未安裝 **azure** 命令，請參閱[如何使用 Azure 命令列工具](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/) (英文) 以取得安裝和組態資訊。

### 讀取記錄檔

在啟用網站記錄和/或追蹤之後產生的記錄檔，會因為您在網站之 [設定] 管理頁面中所設定的記錄/追蹤層級而有所差異。以下是記錄檔的位置和記錄檔的分析方式：

**記錄檔類型：應用程式記錄**

-   位置：/LogFiles/Application/。此資料夾內含有一或多個文字檔案，這些檔案涵蓋應用程式記錄所產生的資訊。所記錄的資訊包括日期和時間、應用程式的處理序識別碼 (PID)，以及應用程式檢測所產生的值。

-   檔案讀取方法：了解應用程式產生之值的文字編輯器或剖析器。

**記錄檔類型：失敗要求的追蹤**

-   位置：/LogFiles/W3SVC########\#/。此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。請務必將 XSL 檔案下載到 XML 檔案所在的目錄，因為在 Internet Explorer 中檢視 XML 檔案時，XSL 檔案能提供內容格式化和篩選等功能。

-   檔案讀取方法：Internet Explorer

**記錄檔類型：詳細的錯誤記錄**

-   位置：/LogFiles/DetailedErrors/。/LogFiles/DetailedErrors/ 資料夾內含有一或多個 .htm 檔案，這些檔案能提供任何已發生之 HTTP 錯誤的廣泛資訊。

-   檔案讀取方法：Web 瀏覽器

.htm 檔案包括以下區段：

-   **詳細錯誤資訊：**包括錯誤的相關資訊，如<em>模組</em>、<em>處理常式</em>、<em>錯誤碼</em> 及<em>要求的 URL</em>。

-   **最可能的原因：**列出幾個可能引發錯誤的原因。

-   **可嘗試的動作：**列出可能可以解決錯誤回報之問題的解決方案。

-   **連結和詳細資訊**：提供有關錯誤的其他摘要資訊。此外，可能還含有其他資源 (如 Microsoft 知識庫文章) 的連結。

**記錄檔類型：Web 伺服器記錄**

-   位置：/LogFiles/http/RawLogs。儲存在檔案中的資訊是使用 [W3C 擴充記錄檔格式](http://go.microsoft.com/fwlink/?LinkID=90561) (英文) 加以格式化。Azure 網站未使用 s-computername、s-ip 及 cs-version 等欄位。

-   檔案讀取方法：記錄檔剖析器。用來剖析及查詢 IIS 記錄檔。Microsoft 下載中心 <http://go.microsoft.com/fwlink/?LinkId=246619> (英文) 備有 Log Parser 2.2 可供使用。

作法：監視 Web 端點狀態
-----------------------

此功能是 **[標準]** 模式下的功能，它能讓您從最多 3 個地理位置監視最多 2 個端點。

端點監視能讓您設定從不同地理位置執行，且用來測試 Web URL 之回應時間和運作時間的 Web 測試。這項測試會針對 Web URL 執行 HTTP get 操作，以便從每個位置判斷回應時間和運作時間。各個已設定的位置會每隔五分鐘執行一次測試。

運作時間是透過 HTTP 回應碼來加以監視，而回應時間的測量單位是毫秒。當回應時間少於 30 秒且 HTTP 狀態碼小於 400 時，運作時間可視為 100%。當回應時間大於 30 秒或 HTTP 狀態碼大於 400 時，運作時間則為 0%。

在設定端點監視後，您可以從每個測試位置向下鑽研各個端點，以便檢視監視間隔內詳細的回應時間和運作時間狀態。

**設定端點監視：**

1.  開啟 **[網站]**。按一下要設定的網站名稱。
2.  按一下 **[設定]** 索引標籤。
3.  移至 **[監視]** 區段以輸入端點設定。
4.  輸入端點的名稱。
5.  輸入要監視之服務的 URL。例如 <http://contoso.cloudapp.net>。
6.  在清單中選取一或多個地理位置。
7.  您也可以選擇重複先前的步驟來建立第二個端點。
8.  按一下 **[儲存]**。Web 端點監視資料可能需要經過一段時間才會出現在 **[儀表板]** 和 **[監視]** 索引標籤中。

如需網站端點監視的詳細資訊，請觀看以下影片：

-   [Scott Guthrie 介紹 Azure 網站及設定端點監視](http://www.windowsazure.com/en-us/documentation/videos/websites-and-endpoint-monitoring-scottgu/) (英文)

-   [讓 Azure 網站保持運作以及端點監視 - 對談來賓 Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) (英文)


