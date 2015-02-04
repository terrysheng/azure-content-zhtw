<properties urlDisplayName="How to monitor" pageTitle="如何監視網站 - Azure 服務管理" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="了解如何使用 Management Portal 中的監視器頁面監視 Azure 網站。" metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Websites" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />



#<a name="howtomonitor"></a>如何監視網站

網站能透過 [監視] 管理頁面來提供監視功能。[監視] 管理頁面能提供網站的效能統計資料，如下文所述。

## 目錄 ##
- [作法：新增網站度量](#websitemetrics)
- [作法：接收來自網站度量的警示](#howtoreceivealerts)
- [作法：檢視網站的使用量配額](#howtoviewusage)
- [作法：避免超出配額](#resourceusage)
- [作法：設定診斷功能及下載網站記錄](#howtoconfigdiagnostics)
- [作法：監視 Web 端點狀態](#webendpointstatus)

##<a name="websitemetrics"></a>作法：加入網站度量
1. 在 [Azure 管理入口網站](http://manage.windowsazure.com/)中，從網站的 [管理] 頁面，按一下 [**監視**] 索引標籤以顯示 [**監視**] 管理頁面。依預設，[**監視**] 頁面之圖表顯示的度量與 [**儀表板**] 頁面的圖表相同。 

2. 若要檢視網站的其他度量，請按一下頁面底部的 [**加入度量**] 以顯示 [**選擇度量**] 對話方塊。 

3. 按一下以選取要顯示於 [**監視**] 頁面中的其他度量。 

4. 選取您要加入至 [**監視**] 頁面的度量之後，按一下底部的核取記號。 

5. 在將度量新增至 [**監視**] 頁面後，請按一下以啟用 (停用) 每個度量旁的選項方塊，將度量新增至頁面頂端的圖表 (或將度量移除)。

6. 若要將 [**監視**] 頁面中的度量移除，請選取要移除的度量，然後按一下頁面底部的 [**刪除度量**] 圖示。

##<a name="howtoreceivealerts"></a>作法：接收來自網站度量的警示
在 [**標準**] 網站模式下，您可以接收以網站監視度量為基礎的警示。您必須先設定監視所用的 Web 端點才能使用警示功能，您可以在 [**設定**] 頁面的 [**監視**] 區段中加已設定。在 Azure 管理入口網站的 [**設定**] 頁面中，您可以建立規則以在選擇之度量達到指定的值時觸發警示。您也可以選擇在觸發警示時傳送電子郵件。如需詳細資訊，請參閱[作法：在 Azure 中接收警示通知及管理警示規則](http://go.microsoft.com/fwlink/?LinkId=309356)。  

##<a name="howtoviewusage"></a>作法：檢視網站的使用量配額

在網站的 [**調整**] 管理頁面中，您可以將網站設定為以 [**共用**] 或 [**標準**] 網站模式運作。利用 [**共用**] 網站模式，每個 Azure 訂用帳戶都能存取針對執行每個區域最多 100 個網站而提供的資源集區。基於此目的而供應給每個網站訂用帳戶的資源集區，將由相同地理區域中設定為以 [**共用**] 模式執行的其他網站共用。由於這些資源需與其他網站共用，因此所有訂用帳戶在使用這些資源時均會受到限制。套用在訂用帳戶上的資源使用限制，係以列示於各網站 [**儀表板**] 管理頁面之 [使用量概觀] 區段下的使用量配額來表示。

>[WACOM.NOTE] 若將網站設定為以 [**標準**] 模式執行，則該網站會被配置相當於下文表格中 [**小型**] (預設值)、[**中型**] 或 [**大型**] 虛擬機器大小的專用資源：[Azure 的虛擬機器和雲端服務大小][vmsizes]。對於以**標準**模式運作的網站，訂用帳戶可使用的資源並沒有限制。然而，每個區域容許建立的**標準**模式網站數目只有 500 個。
 
### 作法：檢視設定為共用網站模式之網站的使用量配額 ###
若要判斷網站影響資源使用量配額的程度，請遵循以下步驟：

1. 開啟網站的 [**儀表板**] 管理頁面。
2. 個別 Web 裝載計畫的使用量配額會顯示在 [**使用量概觀**] 區段之下，其屬於下列的一部分：
	-	**資料輸出**、**CPU 時間**和**記憶體** - 若超出配額，Azure 會在目前剩餘的配額間隔期間停止網站。Azure 會在下一個配額間隔開始時啟動網站。
	-	**檔案系統儲存體** - 若達到此配額，檔案系統儲存體仍可進行讀取作業，但是會封鎖所有的寫入作業，包括正常網站活動所需的作業。當您降低檔案使用量或將網站移到配額較高的 Web 裝載計畫時，則會繼續進行寫入作業。
	-	**已連結的資源** - 這裡也會顯示網站的任何連結資源 (如資料庫或儲存體) 的配額。   

	有些配額可以依據 Web 裝載計畫套用，但有些配額則可依據網站套用。如需各 Web 裝載計畫的使用量配額詳細資訊，請參閱[網站限制](http://azure.microsoft.com/zh-tw/documentation/articles/azure-subscription-service-limits/#websiteslimits)。


##<a name="resourceusage"></a>作法：避免超出配額

配額與效能或成本無關，但這是 Azure 管理在多租用戶環境中資源使用量的方法，以免租用戶過度使用共用資料。因為超過配額即表示您的網站停機或功能降低，如果您希望在即將達到配額時讓網站持續運作，請考慮下列各項：

- 將網站移到較高層級的 Web 裝載計畫，以便利用較大的配額。例如，[**基本**] 和 [**標準**] 計畫的唯一配額為檔案系統儲存體。 
- 隨著網站的執行個體數目增加，超過共用資源配額的可能性也會隨之提高。如果合適，請考慮在共用資源配額超出時調降網站的其他執行個體。


##<a name="howtoconfigdiagnostics"></a>作法：設定診斷功能及下載網站記錄

您可以在網站的 [**設定**] 管理頁面中啟用診斷功能。診斷功能可分為兩種：**應用程式診斷**和**網站診斷**。

####應用程式診斷####

[**設定**] 管理頁面的 [**應用程式診斷**] 區段能控制應用程式產生之資訊的記錄，在記錄發生於應用程式內部的事件時，此項功能便能派上用場。例如，當應用程式內部發生錯誤時，您也許會希望能向使用者呈現易於理解的錯誤，意即在記錄中寫入更詳細的錯誤資訊，以供日後分析之用。

您可以啟用或停用以下應用程式診斷：

- **Application Logging (File System)** - 啟應用程式產生之資訊的記錄功能。[**Logging Level**] 欄位決定要記錄 [錯誤]、[警告] 還是 [資訊] 層級的資訊。您也可以選取 [詳細資訊] 來記錄應用程式產生的所有資訊。

	此項設定所產生的記錄會儲存於網站的檔案系統中，因此您可以使用下面＜**下載網站的記錄檔**＞一節中所述的步驟予以下載。

- **Application Logging (Table Storage)** - 開啟應用程式產生之資訊的記錄功能，與 [Application Logging (File System)] 選項相似。不過記錄資訊是以資料表形式儲存在 Azure 儲存體帳戶中。

	若要指定 Azure 儲存體帳戶和資料表，請選擇 [**開啟**]、選取 [**Logging Level**]，然後再選擇 [**Manage Table Storage**]。指定要使用的儲存體帳戶和資料表，或建立新的資料表。

	您可以使用 Azure 儲存體用戶端來存取儲存於資料表中的記錄資訊。

- **Application Logging (Blob storage)** - 開啟應用程式產生之資訊的記錄功能，與 [Application Logging (Table Storage)] 選項相似。不過記錄資訊是以 Blob 形式儲存在 Azure 儲存體帳戶中。

	若要指定 Azure 儲存體帳戶和 Blob，請選擇 [**開啟**]、選取 [**Logging Level**]，然後再選擇 [**Manage Blob Storage**]。指定要使用的儲存體帳戶、Blob 容器及 Blob 名稱，或建立新的容器和 Blob。

如需 Azure 儲存體帳戶的詳細資訊，請參閱[如何管理儲存體帳戶](https://www.windowsazure.com/zh-tw/manage/services/storage/how-to-manage-a-storage-account/)。

<div class="dev-callout"on> 
<b>注意/b> 
<p>只有 .NET 應用程式才支援資料表或 Blob 儲存體的應用程式記錄功能。</p> </div>

由於儲存體的應用程式記錄功能須使用儲存體用戶端來檢視記錄資料，因此當您計劃使用了解如何直接讀取及處理 Azure 資料表或 Blob 儲存體之資料的服務或應用程式時，這項功能最為實用。 檔案系統的記錄功能會產生可透過 FTP 或其他公用程式下載到本機電腦的檔案，如本節後續內容所述。

<div class="dev-callout"> 
	<b>注意</b> 
	<p><b>應用程式診斷 (檔案系統)</b>、 <b>應用程式診斷 (資料表儲存體)</b>和 <b>應用程式診斷 (Blob 儲存體)</b> 可以同時啟用，並且有個別的記錄層級組態。例如，您也許會想要將錯誤和警告記錄在儲存體並做為長期的記錄解決方案，同時在檢測應用程式程式碼後啟用檔案系統記錄並搭配詳細資訊層級，以供疑難排解問題之用。</p> </div>

<div class="dev-callout"> 
	<b>注意</b> 
	<p>您也可以在 Azure PowerShell 中使用 <b>Set-AzureWebsite</b> Cmdlet 來啟用診斷功能。</p><p>如果您尚未安裝 Azure PowerShell，或尚未將其設定為使用 Azure 訂閱，請參閱 <a href="http://www.windowsazure.com/zh-tw/develop/nodejs/how-to-guides/powershell-cmdlets/">如何使用 Azure PowerShell</a>。</p></div>

<div class="dev-callout"> 
<b>注意</b> 
<p>應用程式記錄功能仰賴應用程式產生的記錄資訊。用來產生記錄資訊的方法和資訊的格式取決於應用程式的撰寫語言。如需使用應用程式記錄功能的語言特有資訊，請參閱以下文章：</p>
<ul>
<li><b>.NET</b> - <a href="/zh-tw/develop/net/common-tasks/diagnostics-logging-and-instrumentation/">對 Azure 網站啟用診斷記錄</a></li>
<li><b>Node.js</b> - <a href="/zh-tw/develop/nodejs/how-to-guides/Debug-Website/">如何在 Azure 網站中偵錯 Node.js 應用程式</a></li>
</ul>
<p>只有 .NET 應用程式才支援資料表或 Blob 儲存體的應用程式記錄功能。</p>
</div>

####網站診斷####

[**設定**] 管理頁面中的 [**網站診斷**] 區段能控制由 Web 伺服器執行的記錄功能，如 Web 要求、頁面提供失敗或頁面提供所需時間等項目的記錄。您可以啟用或停用以下選項：

- **Web 伺服器記錄功能** - 開啟 Web 伺服器記錄功能以使用 W3C 擴充記錄檔格式儲存網站記錄。Web 伺服器記錄功能會產生涵蓋所有傳入網站之要求的記錄，其中包括用戶端 IP 位址、要求的 URI、回應的 HTTP 狀態碼及用戶端的使用者代理程式字串等資訊。您可以將記錄儲存在 Azure 儲存體帳戶或檔案系統中。

 若要將 Web 伺服器記錄儲存在 Azure 儲存體帳戶，請選擇 [**儲存體**]，然後再選擇 [**管理儲存體**] 以指定儲存體帳戶和用來保存記錄的 Azure Blob 容器。如需 Azure 儲存體帳戶的詳細資訊，請參閱[如何管理儲存體帳戶](https://www.windowsazure.com/zh-tw/manage/services/storage/how-to-manage-a-storage-account/)。

   若要將 Web 伺服器記錄儲存在檔案系統中，請選擇 [**檔案系統**]。如此能啟用 [**配額**] 方塊以供您設定容納記錄檔的磁碟空間上限。大小的下限為 25MB，上限為 100MB。預設大小為 35MB。

 依預設，系統永遠不會刪除 Web 伺服器記錄。若要指定自動刪除記錄的時間，選取 [**設定保留**] 並在 [**保留週期**] 方塊內輸入記錄的保留天數。此項設定適用於 Azure 儲存體和檔案系統選項。

- **詳細錯誤訊息** - 開啟詳細錯誤記錄功能可額外記錄有關 HTTP 錯誤 (大於 400 的狀態碼) 的資訊。

- **失敗要求的追蹤** - 開啟失敗要求追蹤功能可擷取失敗的用戶端要求資訊，如 400 系列的 HTTP 狀態碼。失敗要求追蹤功能會產生 XML 文件，其中包括要求在 IIS 中經過哪些模組的追蹤、模組傳回的詳細資料及叫用模組的時間。這些資訊可用來隔離出發生失敗的元件。


啟用網站診斷後，按一下 [**設定**] 管理頁面底部的 [**儲存**] 圖示可套用您設定的選項。

<div class="dev-callout"> 
<b>重要事項</b> 
<p>記錄和追蹤等功能會使網站的資源需求大增。建議您在重製欲疑難排解的問題後關閉記錄和追蹤功能。</p> 
</div>

###進階組態###

您可以在 [**設定**] 管理頁面的 [**應用程式設定**] 區段中新增索引鍵/值配對，進一步修改診斷功能。以下是可在 [**應用程式設定**] 中配置的設定：

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- 儲存應用程式記錄的位置，相對於 Web 根目錄。

- 預設值：..\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- 擷取應用程式記錄時，使用的緩衝區大小上限。剛開始時，系統會先將資訊寫入緩衝區，然後再排清到檔案或儲存體。如果系統將新資訊寫入緩衝區，然後再予以排清，您便會遺失先前記錄的資訊。如果應用程式突然產生大量的記錄資訊，請考慮增加緩衝區的大小。

- 預設值：10MB

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- **Application** 資料夾的大小上限，其為寫入檔案之應用程式診斷的儲存位置。

- 預設值：1MB

###下載網站的記錄檔###

您可以使用 FTP、Azure PowerShell 或 Azure 命令列工具來下載記錄檔。

**FTP**

1. 開啟網站的 [**儀表板**] 管理頁面，接著記下 [**診斷記錄**] 列示的 FTP 站台和 [**部署使用者**] 列示的帳戶。FTP 站台是記錄檔的所在位置，而列示於 [Deployment User] 下的帳戶可用來通過 FTP 站台的驗證。
2. 如果您尚未建立部署認證，列示於 [**Deployment User**] 下的帳戶將會是 [**未設定**]。在這種情況下，您必須建立部署認證 (如 [儀表板] 的 [Reset Deployment Credentials] 區段所述)，因為您必須使用這些認證來通過儲存記錄檔之 FTP 站台的驗證。Azure 不支援使用 Live ID 認證來通過此 FTP 站台的驗證。
3. 請考慮使用 FTP 用戶端 (如 [FileZilla][fzilla]) 來連接 FTP 站台。與瀏覽器普遍的功能相比，FTP 用戶端更能簡化認證的指定和 FTP 站台資料夾的檢視。
4. 請將 FTP 站台上的記錄檔複製到本機電腦。

**Azure PowerShell**

1. 從 [**開始畫面**] 或 [**開始功能表**] 搜尋 **Azure PowerShell**。在 [**Azure PowerShell**] 項目上按一下滑鼠右鍵，然後選取 [**以系統管理員身份執行**]。

	<div class="dev-callout"> 
	<b>注意</b> 
	<p>如果 <b>Azure PowerShell</b> 尚未安裝，請參閱 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554332.aspx">開始使用 Azure PowerShell Cmdlet</a> 以取得安裝和設定資訊。</p> 
	</div>

2. 在 Azure PowerShell 提示字元中使用下列命令來下載記錄檔：

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

<div class="dev-callout"> 
<b>注意</b> 
<p>如果 <b>azure</b> 命令尚未安裝，請參閱 <a href="http://www.windowsazure.com/zh-tw/develop/nodejs/how-to-guides/command-line-tools/">如何使用 Azure 命令列介面</a> 以取得安裝和設定資訊。</p>
</div>

###讀取記錄檔###

在啟用網站記錄和/或追蹤之後產生的記錄檔，會因為您在網站之 [設定] 管理頁面中所設定的記錄/追蹤層級而有所差異。以下是記錄檔的位置和記錄檔的分析方式：

**記錄檔類型：應用程式記錄**

- 位置：/LogFiles/Application/。此資料夾內含有一或多個文字檔案，這些檔案涵蓋應用程式記錄所產生的資訊。所記錄的資訊包括日期和時間、應用程式的處理序識別碼 (PID)，以及應用程式檢測所產生的值。

- 檔案讀取方法：了解應用程式產生之值的文字編輯器或剖析器。

**記錄檔類型：失敗要求的追蹤**

- 位置：/LogFiles/W3SVC#########/。此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。請確保將 XSL 檔案下載至 XML 檔案所在的相同目錄，因為 XSL 檔案可提供格式化功能，讓您在 Internet Explorer 中檢視時能夠篩選 XML 檔案內容。 

- 檔案讀取方法：Internet Explorer

**記錄檔類型：詳細的錯誤記錄**

- 位置：/LogFiles/DetailedErrors/。/LogFiles/DetailedErrors/ 資料夾內含有一或多個 .htm 檔案，這些檔案能提供任何已發生之 HTTP 錯誤的廣泛資訊。 

- 檔案讀取方法：Web 瀏覽器

.htm 檔案包括以下區段：

- **詳細錯誤資訊：**包含錯誤相關資訊，例如 <em>模組</em>、 <em>處理常式</em>、 <em>錯誤碼</em>和 <em>要求的 URL</em>。

- **最可能的原因：**列出幾個可能引發錯誤的原因。

- **可嘗試的動作：**列出可能可以解決錯誤回報之問題的解決方案。

- **連結和詳細資訊**：提供有關錯誤的其他摘要資訊。此外，可能還含有其他資源 (如 Microsoft 知識庫文章) 的連結。

**記錄檔類型：Web 伺服器記錄**

- 位置：/LogFiles/http/RawLogs。儲存在檔案中的資訊是使用 [W3C 擴充記錄檔格式] (英文) 加以格式化(http://go.microsoft.com/fwlink/?LinkID=90561)。Azure 網站未使用 s-computername、s-ip 及 cs-version 等欄位。

- 檔案讀取方法：記錄檔剖析器。用來剖析及查詢 IIS 記錄檔。Log Parser 2.2 可在 Microsoft 下載中心取得： <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>。


##<a name="webendpointstatus"></a>作法：監視 Web 端點狀態

此功能是 [**標準**] 模式下的功能，它能讓您從最多 3 個地理位置監視最多 2 個端點。 

端點監視能讓您設定從不同地理位置執行，且用來測試 Web URL 之回應時間和運作時間的 Web 測試。這項測試會針對 Web URL 執行 HTTP get 操作，以便從每個位置判斷回應時間和運作時間。各個已設定的位置會每隔五分鐘執行一次測試。

運作時間是透過 HTTP 回應碼來加以監視，而回應時間的測量單位是毫秒。當回應時間少於 30 秒且 HTTP 狀態碼小於 400 時，運作時間可視為 100%。當回應時間大於 30 秒或 HTTP 狀態碼大於 400 時，運作時間則為 0%。

在設定端點監視後，您可以從每個測試位置向下鑽研各個端點，以便檢視監視間隔內詳細的回應時間和運作時間狀態。您也可以設定警示規則 (例如，當端點的回應時間太長時)。 

**若要設定端點監視：**

1.	開啟 [**網站**]。按一下要設定的網站名稱。
2.	按一下 [**設定**] 索引標籤。 
3.     移至 [**監視**] 區段以輸入端點設定。
4.	輸入端點的名稱。
5.	輸入要監視之網站部分的 URL。例如，[http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive)。 
6.	在清單中選取一或多個地理位置。
7.	您也可以選擇重複先前的步驟來建立第二個端點。
8.	按一下 [**儲存**]。Web 端點監視資料可能需要經過一段時間才會出現在 [**儀表板**] 和 [**監視**] 索引標籤中。

	若要建立電子郵件規則，請執行下列動作：

9.	在左邊的服務列中，按一下 [**管理服務**]。
10.	按一下底部的 [**新增規則**]。
11.	在 [**服務類型**] 中，選取 [**網站**]，然後選取您先前設定端點監視的網站。按 [**下一步**]。
12.	在 [**度量**]，您現在可以為您設定的端點選取其他度量。例如：**回應時間 (首頁/US：IL-Chicago)**。選取 [回應時間] 度量並在 [**臨界值**] 中輸入 3，以指定 3 秒的臨界值。
13.	選取 [**Send an email to the service administrator and co-administrators**]。按一下 [**完成**]。

	Azure 現在會主動監視端點，並且在端點的回覆時間超過 3 秒時傳送點子郵件通知。


如需網站端點監視的詳細資訊，請觀看以下影片：

- [Scott Guthrie 介紹 Azure 網站及設定端點監視](http://www.windowsazure.com/zh-tw/documentation/videos/websites-and-endpoint-monitoring-scottgu/) (英文)

- [讓 Azure 網站保持運作以及端點監視 - 對談來賓 Stefan Schackow](http://www.windowsazure.com/zh-tw/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) (英文)





[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169





<!--HONumber=35.1-->
