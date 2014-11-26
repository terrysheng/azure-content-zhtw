<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# 建置 Azure Email Service 應用程式的 Web 角色 - 3/5

這是一系列教學課程的第三個教學課程 (共五個)，顯示如何建置和部署 Azure Email Service 範例應用程式。如需應用程式和教學課程系列的詳細資訊，請參閱[系列的第一個教學課程][系列的第一個教學課程]。

在本教學課程中，您將了解：

-   如何使用 Web 角色和背景工作角色建立含有雲端服務專案的解決方案。
-   如何在 MVC 5 控制器和檢視中使用 Azure 資料表、Blob 和佇列。
-   如何處理使用 Azure 資料表時的並行衝突。

## 本教學課程章節

-   [建立 Visual Studio 方案][建立 Visual Studio 方案]
-   [更新儲存體用戶端程式庫 NuGet 封裝][更新儲存體用戶端程式庫 NuGet 封裝]
-   [設定專案以使用儲存體模擬器][設定專案以使用儲存體模擬器]
-   [設定追蹤及處理重新啟動][設定追蹤及處理重新啟動]
-   [新增程式碼，以在 Application\_Start 方法中建立資料表、佇列和 Blob 容器][新增程式碼，以在 Application\_Start 方法中建立資料表、佇列和 Blob 容器]
-   [建立及測試郵寄清單][建立及測試郵寄清單]
-   [建立及測試訂閱者控制器和檢視][建立及測試訂閱者控制器和檢視]
-   [建立及測試訊息控制器和檢視][建立及測試訊息控制器和檢視]
-   [建立及測試取消訂閱者控制器和檢視][建立及測試取消訂閱者控制器和檢視]
-   [後續步驟][後續步驟]

## <a name="cloudproject"></a>建立 Visual Studio 方案

起點是建立 Visual Studio 解決方案，內含 Web 前端的專案以及一個後端 Azure 背景工作角色的專案。您稍後將新增第二個背景工作角色。

### 使用 Web 角色和背景工作角色建立雲端服務專案

1.  啟動 Visual Studio。

2.  從 [檔案] 功能表中，選取 [新增專案]。

    ![New Project menu][New Project menu]

3.  展開 [C\#]，並選取 [已安裝的範本] 下的 [雲端]，然後選取 [Azure 雲端服務]。

4.  將應用程式命名為 **AzureEmailService**，然後按一下 [確定]。

    ![New Project dialog box][New Project dialog box]

5.  在 [New Azure Cloud Service] 對話方塊中，選取 [ASP.NET Web 角色]，然後按一下指向右側的箭號。

    ![New Azure Cloud Project dialog box][New Azure Cloud Project dialog box]

6.  在右側的欄位中，將滑鼠指標移至 [WebRole1] 上方，然後按一下鉛筆圖示以變更 Web 角色的名稱。

7.  輸入 MvcWebRole 做為新名稱，然後按 Enter 鍵。

    ![New Azure Cloud Project dialog box - renaming the web role][New Azure Cloud Project dialog box - renaming the web role]

8.  依照新增 [背景工作角色] 的相同程序進行，並將它命名為 WorkerRoleA，然後按一下 [確定]。

    ![New Azure Cloud Project dialog box - adding a worker role][New Azure Cloud Project dialog box - adding a worker role]

9.  在 [New ASP.NET Project] 對話方塊中，選取 [MVC] 範本，選取 [Web API] 核取方塊，然後按一下 [變更驗證]。

    ![New Project dialog box][1]

10. 在 [變更驗證] 對話方塊中，按一下 \[不需要驗證\]，然後按一下 [確定]。

    ![不需要驗證][不需要驗證]

11. 在 [New ASP.NET Project] 對話方塊中，按一下 [確定]。

### 設定頁首、功能表和頁尾

在本節中，您將更新系統管理員 Web UI 的每個頁面上顯示的頁首、頁尾和功能表項目。應用程式會有三組系統管理員網頁：一個是針對郵寄清單、一個是針對郵寄清單訂閱者，而一個是針對郵件。

1.  如果您尚未下載[已完成的方案][已完成的方案] (英文)，請先加以下載，再繼續執行下一個步驟。

    在後續的教學課程中，當您需要新增程式碼時，您會從已下載的專案將檔案複製到新專案中，而不會複製並貼上程式碼片段。本教學課程將示範並說明您要複製之程式碼的主要部分。

    若要從已下載的專案新增檔案，請以滑鼠右鍵按一下檔案所要新增到的專案或資料夾，然後從內容功能表中選擇 [新增 - 現有項目]。接著，請瀏覽到已完成的專案下載的位置，選取您要的檔案，然後按一下 [新增]。如果出現 [目的地檔案存在] 對話方塊，請按一下 [是]。

2.  在 MvcWebRole 專案中，從已下載的專案新增 *Views\\Shared\_Layout.cshtml* 檔案 (以滑鼠右鍵按一下 *Views* 下的 *Shared* 資料夾，以新增檔案)。

    這會新增 [郵寄清單]、[訊息] 和 [訂閱者] 頁面的頁首、頁尾和功能表項目：

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### 在本機執行應用程式

1.  按 CTRL+F5 執行應用程式。

    如果您經常啟動不是 Azure 雲端服務專案的 Web 專案，您會注意到首頁需要較長的時間才會顯示在瀏覽器中。

    ![home page][home page]

    之所以會延遲，是因為 Visual Studio 會啟動 Azure 計算模擬器和 Azure 儲存體模擬器。您可以在 Windows 系統匣中看到計算模擬器圖示：

    ![Compute emulator in system tray][Compute emulator in system tray]

2.  關閉瀏覽器。

## <a name="updatescl"></a>更新儲存體用戶端程式庫 NuGet 封裝

您用來使用 Azure 儲存體資料表、佇列和 Blob 的 API 架構就是儲存體用戶端程式庫 (SCL)。此 API 包含在雲端服務專案範本的 NuGet 封裝中。但 SCL 的更新經常是在專案範本建立後才發行的，因此您最好先查看您的 SCL NuGet 封裝是否有可用的更新。

1.  在 Visual Studio [工具] 功能表中，將滑鼠指標移至 [Library Package Manager] 上方，然後按一下 [Manage NuGet Packages for Solution]。

    ![Manage NuGet Packages for Solution in menu][Manage NuGet Packages for Solution in menu]

2.  在 [Manage NuGet Packages] 對話方塊的左窗格中，選取 [更新]，並向下捲動到 [Azure 儲存體] 封裝，然後按一下 [更新]。

    ![Azure Storage package in Manage NuGet Packages dialog box][Azure Storage package in Manage NuGet Packages dialog box]

3.  在 [選取專案] 對話方塊中，確定已選取兩個專案，然後按一下 [確定]。

    ![Selecting both projects in the Select Projects dialog box][Selecting both projects in the Select Projects dialog box]

4.  接受授權合約以完成封裝的安裝，然後關閉 [Manage NuGet Packages] 對話方塊。

## <a name="configurestorage"></a>設定專案以使用儲存體模擬器

您後續將新增的 Web 角色和背景工作角色程式碼，會使用名為 StorageConnectionString 的連接字串連接到 Azure 儲存體。在本節中您會為角色屬性新增設定，並將其設定成使用儲存體模擬器。系列中的第二個教學課程會說明如何設定連接字串以使用 Azure 儲存體帳戶。

1.  在 [方案總管] 中，於 **AzureEmailService** 雲端專案之 [角色] 的 **MvcWebRole** 上按一下滑鼠右鍵，然後選擇 [屬性]。

    ![Web role properties][Web role properties]

2.  確定已選取 [服務組態] 下拉式清單中的 [所有組態]。

3.  選取 [設定] 索引標籤，然後按一下 [加入設定]。

4.  在 [名稱] 欄位中，輸入 "StorageConnectionString"。

5.  選取 [類型] 下拉式清單中的 [連接字串]。

6.  按一下該行右邊的省略符號 (**...**) 按鈕，以開啟 [儲存體帳戶連接字串] 對話方塊。

    ![Right Click Properties][Right Click Properties]

7.  在 [Create Storage Connection String] 對話方塊中，按一下 [Azure 儲存體模擬器] 選項按鈕，然後按一下 [確定]。

    根據預設，`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 連接字串會設為儲存體模擬器，因此您無須加以變更。

8.  依照用於 MvcWebRole 的相同程序，新增 WorkerRoleA 角色的儲存體連接字串。

當您使用 [Add Settings] 按鈕新增設定時，即會將新設定新增至 *ServiceDefinition.csdf* 檔案以及兩個 *.cscfg* 組態檔中的 XML。Visual Studio 會在 *ServiceDefinition.csdf* 檔案中新增下列 XML。

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

在每個 *.cscfg* 組態檔中新增下列 XML。

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

您可以手動在 *ServiceDefinition.csdf* 檔案和兩個 *.cscfg* 組態檔中新增設定，但是使用屬性編輯器具有下列連接字串優點：

-   您只需要在一個位置新增設定，就可以將正確設定 XML 新增至全部三個檔案。
-   針對這三個設定檔案產生正確的 XML。*ServiceDefinition.csdf* 檔案定義每個 *.cscfg* 組態檔中必須要有的設定。如果 *ServiceDefinition.csdf* 檔案和兩個 *.cscfg* 組態檔設定不一致，則會從 Visual Studio 收到下列錯誤訊息：*目前服務模型不同步。請確定服務組態和定義檔案有效。*

    ![Invalid service configuration and definition files error][Invalid service configuration and definition files error]

如果出現此錯誤，除非您手動編輯檔案以解決不一致問題，否則屬性編輯器將無法運作。

## <a name="tracing"></a>設定追蹤及處理重新啟動

1.  在 MvcWebRole 專案中，從已下載的專案新增 *WebRole.cs* 檔案。

這會新增設定記錄的方法，並從在 Web 角色啟動時執行的`OnStart` 方法加以呼叫。新`ConfigureDiagnostics` 方法中的程式碼在[第二個教學課程][第二個教學課程]中有相關說明。

這也會新增在 Web 角色收到即將關閉通知時執行的程式碼。Azure 雲端服務應用程式每個月大約會重新啟動兩次進行作業系統更新。(如需作業系統更新的詳細資訊，請參閱[角色執行個體因作業系統升級而重新啟動][角色執行個體因作業系統升級而重新啟動] (英文)。)即將關閉 Web 應用程式時，會引發`OnStop` 事件。Visual Studio 所建立的 Web 角色樣板不會覆寫`OnStop` 方法，因此，應用程式在關閉之前只有幾秒的時間可以完成 HTTP 要求的處理。您可以新增程式碼來覆寫`OnStop` 方法，以確保能夠正常處理關閉。

您剛剛新增的檔案包含下列`OnStop` 方法覆寫。

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

`OnStop` 方法在應用程式關閉之前，最多有 5 分鐘的時間可以結束。您可以對`OnStop` 方法新增 5 分鐘的睡眠呼叫，讓您的應用程式有最多的時間可以處理目前要求，但是如果您的應用程式已正確調整，則應該可以在 5 分鐘內處理完其餘的要求。最好盡快停止這項作業，以最快的速度重新啟動應用程式，並繼續處理要求。

Azure 去除角色之後，負載平衡器會停止將要求傳送至角色執行個體，並在之後呼叫`OnStop` 方法。如果沒有您角色的另一個執行個體，則除非您的角色完成關閉並重新啟動 (通常需要數分鐘的時間)，否則不會處理要求。這是 Azure 服務等級協定需要每個角色至少有兩個執行個體才能利用執行時間保證的其中一個原因。

在針對`OnStop` 方法顯示的程式碼中，會為下列項目建立 ASP.NET 效能計數器：`Requests Current`. `Requests Current` 計數器值包含目前要求數目，包括放入佇列、目前執行或等待寫入用戶端的要求。系統會每秒檢查一次`Requests Current` 值，如果降為零，則會傳回`OnStop` 方法。一旦傳回`OnStop` ，角色即會關閉。

從`OnStop` 方法呼叫，而未執行[隨選傳輸][隨選傳輸]時，追蹤資料不會儲存。您可以即時檢視`OnStop` 追蹤資訊，方法是透過遠端桌面連線使用 [dbgview][dbgview] 公用程式。

## <a name="createifnotexists"></a>新增程式碼，以在 Application\_Start 方法中建立資料表、佇列和 Blob 容器

Web 應用程式會使用`MailingList` 資料表、`Message` 資料表`azuremailsubscribequeue` 佇列和`azuremailblobcontainer` Blob 容器。您可以使用 Azure 儲存體總管這類工具手動建立這些項目，但是，之後每次透過新的儲存體帳戶開始使用應用程式時，都需要手動執行這項作業。在本節中，您將新增程式碼，以在啟動應用程式時執行、確認必要資料表、佇列和 Blob 容器是否存在，以及在沒有上述項目時建立它們。

您可以將此單次啟動程式碼新增至`OnStart` 方法 (在 *WebRole.cs* 檔案中)，或新增至 *Global.asax* 檔案。在本教學課程中，您會在 *Global.asax* 檔案中初始化 Azure 儲存體。

1.  在 [方案總管] 中，以滑鼠右鍵按一下 MvcWebRole 專案，然後從已下載的專案新增 *Global.asax.cs* 檔案。

您已新增從下列方法呼叫的新方法：`Application_Start` method:

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

在下列各節中，您會建置 Web 應用程式的元件，而且可以使用開發儲存體或儲存體帳戶來測試它們，而不需要先手動建立資料表、佇列或 Blob 容器。

## <a name="mailinglist"></a>建立及測試郵寄清單控制器和檢視

系統管理員使用 [郵寄清單] Web UI 建立、編輯和顯示郵寄清單 (例如 "Contoso University History Department announcements" 和 "Fabrikam Engineering job postings")。

### 將 MailingList 實體類別新增至 Models 資料夾

在`MailingList` 資料表中包含清單相關資訊 (例如清單的說明以及傳送至清單之電子郵件的「寄件者」電子郵件地址) 的資料列中，會使用`MailingList` 實體類別。

1.  在`Models` 資料夾中 (位於 MVC 專案中)，從已下載的專案新增 *MailingList.cs* 檔案。

    您將會使用`MailingList` 實體類別來讀取及寫入 mailinglist 資料表中的郵寄清單資料列。

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    要使用 Azure 儲存體 API，資料表作業的實體類別必須衍生自 [TableEntity][TableEntity]。`TableEntity` 會定義`PartitionKey`, `RowKey`, `TimeStamp`和`ETag` 欄位。系統會使用`TimeStamp` and`ETag` 屬性。在教學課程後續的內容中，您將了解如何使用`ETag` 屬性進行並行處理。

    (當您想要使用資料表資料列做為鍵值組的 Dictionary 集合，而非使用預先定義的模型類別時，也會使用 [DynamicTableEntity][DynamicTableEntity] 類別。如需詳細資訊，請參閱 [Azure 儲存體用戶端程式庫 2.0 資料表深入探討][Azure 儲存體用戶端程式庫 2.0 資料表深入探討] (英文)。)

    `mailinglist` 資料表分割索引鍵是清單名稱。在此實體類別中，分割索引鍵值的存取方式是使用`PartitionKey` 屬性 (定義於`TableEntity` 類別中) 或`ListName` 屬性 (定義於`MailingList` 類別中)。 `ListName` 屬性使用`PartitionKey` 做為其支援變數。定義`ListName` 屬性，可讓您在程式碼中使用更具描述性的變數名稱，並簡化 Web UI 的程式設計，因為格式和驗證 DataAnnotations 屬性可以新增至`ListName` 屬性，但是無法直接新增至`PartitionKey` 屬性。

    `RegularExpression` 屬性 (屬於`ListName` 屬性) 可讓 MVC 驗證使用者輸入，確保輸入的清單名稱值只包含英數字元或底線。這項限制的實作是要保持簡單的清單名稱，以輕鬆地將清單名稱用於 URL 的查詢字串中。

    > [WACOM.NOTE] 如果您想要讓清單名稱格式的限制較少，在查詢字串中使用清單名稱時，您可以允許其他字元和 URL 編碼清單名稱。不過，在 Azure 資料表分割索引鍵或資料列索引鍵中不允許使用特定字元，因此您至少需要排除那些字元。如需分割索引鍵或資料列索引鍵欄位中不允許或導致問題之字元的詳細資訊，請參閱[了解表格服務資料模型][了解表格服務資料模型]和 [PartitionKey 或 RowKey 中的 % 字元][PartitionKey 或 RowKey 中的 % 字元] (英文)。

    `MailingList` 類別會定義將`RowKey` 設定為硬式編碼字串 "mailinglist" 的預設建構函式，因為此資料表中的所有郵寄清單資料列都會使用該值做為其資料列索引鍵。(如需資料表結構的說明，請參閱[系列的第一個教學課程][系列的第一個教學課程]。)基於此目的，可能已選擇任何常數值，只要它絕對不會與電子郵件地址相同 (即此資料表中訂閱者資料列的資料列索引鍵)。

    在建立新的`MailingList` 實體時一律必須輸入清單名稱和「寄件者」電子郵件地址，因此這兩個項目具有`Required` 屬性。

    `Display` 屬性會指定用於 MVC UI 中某個欄位的預設標題。

### 新增 MailingList MVC 控制器

1.  在 MVC 專案的 *Controllers* 資料夾中，從已下載的專案新增 *MailingListController.cs* 檔案。

    控制器的預設建構函式會建立`CloudTable` 物件，用以處理`mailinglist` 資料表。

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    此程式碼會從雲端服務專案設定檔案中取得您 Azure 儲存體帳戶的認證，以連線至儲存體帳戶。(您稍後會在本教學課程中進行這些設定，但是是在測試控制器之前。)

    接下來是`FindRowAsync` 方法，只要控制器需要查詢`MailingList` 資料表的特定郵寄清單項目就會予以呼叫 (例如編輯郵寄清單項目)。此程式碼會擷取單一`MailingList` 實體，方法是使用傳遞給它的分割索引鍵和資料列索引鍵值。此控制器所編輯的資料列就是資料列索引鍵為 "MailingList" 的資料列，因此可能已針對資料列索引鍵硬式編碼 "MailingList"，但是指定分割索引鍵和資料列索引鍵是用於所有控制器中`FindRow` 方法的一種模式。

    > [WACOM.NOTE] 對於 Web 角色中的 I/O 作業，應用程式會使用 ASP.NET 4.5 非同步程式碼，以有效使用伺服器資源。如需 Web 應用程式中的非同步程式碼的相關資訊，請參閱[使用 .NET 4.5 的非同步支援避免封鎖呼叫][使用 .NET 4.5 的非同步支援避免封鎖呼叫] (英文)。

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    此`FindRow` 方法中的程式碼會傳回郵寄清單資料列。對應`FindRow` 方法中的程式碼 (`Subscriber` 控制器) 會從相同的`mailinglist` 資料表傳回訂閱者資料列。這兩種方法中的程式碼是類似的，差別在於 [TableOperation.Retrieve][TableOperation.Retrieve] 方法所使用的模型類型。

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    `TableOperation` 物件由`TableOperation.Retrieve` 方法所傳回，會指定您要讓查詢傳回的一或多個資料列的結構描述 (屬性)。單一資料表的不同資料列中可能會有不同的結構描述。讀取已用來建立資料列的資料列時，您通常會指定相同的模型類型。

    [索引] 頁面會顯示所有的郵寄清單資料列，因此，`Index` 方法中的查詢會傳回以 "mailinglist" 做為資料列索引鍵的`MailingList` 實體 (資料表中的其他資料列會以電子郵件地址做為資料列索引鍵，其中包含訂閱者資訊)。

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    `ExecuteQuerySegmentedAsync` 方法會將大型結果集切分。它最多會傳回 1,000 個資料列。如果您執行的查詢會擷取 1,000 個以上的資料列，您將得到 1,000 個資料列和接續權杖。您可以使用接續權杖，執行從前一個查詢結束之處開始的另一個查詢。以下顯示範例應用程式的簡化程式碼：所有的部分都彙總在一份清單中。對於實際執行應用程式，您會實作分頁程式碼。如需大型結果集和接續權杖的詳細資訊，請參閱[如何最佳利用 Azure 資料表][如何最佳利用 Azure 資料表] (英文) 和 [Azure 資料表：極度預期接續權杖][Azure 資料表：極度預期接續權杖] (英文)。

    當您建立`OperationContext` 物件時，您可以設定`ClientID` 屬性值，以提供由 Azure 儲存體寫入的記錄所將包含的唯一識別碼。您可以使用此識別碼，經由儲存體作業記錄來追蹤導致儲存體服務活動的程式碼。如需 Azure 儲存體記錄的相關資訊，請參閱 [Azure 儲存體記錄：使用記錄追蹤儲存體要求][Azure 儲存體記錄：使用記錄追蹤儲存體要求] (英文)。

    透過 SCL 2.1 和更新版本的 API，您也可以使用 LINQ 進行資料表查詢。如需程式碼範例以了解如何使用 LINQ，請參閱 [PhluffyFotos][PhluffyFotos]。

    如果您未指定重試原則，API 會使用指數方式增加的逾時限制自動重試三次。如果是使用者等待頁面出現的 Web 介面，這可能會導致無法接受的冗長等待時間。因此，此程式碼指定線性重試 (這樣一來，就不會每次都增加逾時限制) 以及使用者等待的合理逾時限制。重試原則指定於`webUIRetryPolicy` 物件中，而此物件會傳至`ExecuteQuerySegmentedAsync` 方法。 `webUIRetryPolicy` 物件定義於控制器建構函式中：

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    `Index` 方法包含用來處理逾時情況的 try-catch 區塊。

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    當使用者按一下 [建立] 頁面上的 [建立] 按鈕時，MVC 模型繫結器會從檢視中的輸入建立`MailingList` 實體，且`HttpPost Create` 方法會將此實體新增至資料表。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    對於 [編輯] 頁面，`HttpPost Edit` 方法會更新資料列。

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    在`HttpPost Edit` 方法中，catch 區塊會處理並行錯誤。

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    當使用者選取某個郵寄清單進行編輯時，如果在瀏覽器中顯示 [編輯] 頁面時，有另一位使用者編輯相同的郵寄清單，則會引發並行例外狀況。發生此情況時，程式碼會顯示警告訊息，並指出另一位使用者已變更的欄位。TSL API 會使用`ETag` 檢查有無並行衝突。每次更新資料表資料列時，`ETag` 值都會變更。當您編輯資料列時，您會儲存`ETag` 值，而當您執行更新或刪除作業時，您會傳入已儲存的`ETag` 值。(`Edit` 檢視具有 ETag 值的隱藏欄位。)如果更新作業發現所更新記錄上的`ETag` 值與傳入更新作業的`ETag` 值不同，則會引發並行例外狀況。如果您不在意並行衝突，則可以將實體中傳入更新作業的 ETag 欄位設定為星號 ("\*")，這樣會略過衝突。

    注意：HTTP 412 錯誤不是並行錯誤獨有的。SCL API 也可能針對其他錯誤而引發此錯誤。

    對於 [刪除] 頁面，`HttpPost Delete` 方法會刪除`MailingList` 資料列，以及任何與該資料列相關聯的`Subscriber` 資料列 (在`MailingList` 資料表中)。

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    如果需要刪除大量訂閱者，則此程式碼會以批次形式刪除記錄。刪除一個資料列的交易成本，與以批次形式刪除 100 個資料列的交易成本相同。可在一個批次中執行的作業數目上限是 100。

    雖然迴圈會同時處理`MailingList` 資料列和`Subscriber` 資料列，但它會將其全部讀取到`MailingList` 實體類別中，因為`Delete` 作業的必要欄位只有`PartitionKey`, `RowKey`和`ETag` 欄位。

### 新增 MailingList MVC 檢視

1.  在 MVC 專案的 *Views* 資料夾中建立新的資料夾，並將其命名為 *MailingList*。

2.  在新的 *Views\\MailingList* 資料夾中，從已下載的專案新增四個 *.cshtml* 檔案。

在 *Edit.cshtml* 檔案中請留意到，內含的隱藏欄位會保留用來處理並行衝突的`ETag` 值。

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

同時也請留意，`ListName` 欄位具有`DisplayFor` 協助程式，而不是`EditorFor` 協助程式。

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

我們未讓 [編輯] 頁面變更清單名稱，因為這樣在控制器中需要複雜的程式碼：the`HttpPost Edit` 方法需要刪除現有郵寄清單資料列和所有相關聯的訂閱者資料列，以及使用新的索引鍵值重新插入它們。在實際執行應用程式中，您可能會決定複雜一點是值得的。如您稍後所見，`Subscriber` 控制器會允許清單名稱變更，因為一次只會影響一個資料列。

在 *Index.cshtml* 檔案中，[編輯] 和 [刪除] 超連結會指定分割索引鍵和資料列索引鍵查詢字串參數，以識別特定資料列。因為資料列索引鍵一律為 "MailingList"，所以`MailingList` 實體實際上只需要分割索引鍵，但是會保留兩者，讓 MVC 檢視程式碼在所有控制器和檢視中都保持一致。

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### 將 MailingList 設為預設控制器

1.  開啟 *App\_Start* 資料夾中的 *Route.config.cs*。

2.  在指定預設值的行中，將預設控制器從 "Home" 變更為 "MailingList"。

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### 測試應用程式

1.  按 CTRL+F5，以執行專案。

    ![Empty MailingList Index page][Empty MailingList Index page]

2.  使用 **Create** 函數新增一些郵寄清單，並嘗試 **Edit** 和 **Delete** 函數確定其運作。

    ![MailingList Index page with rows][MailingList Index page with rows]

## <a name="subscriber"></a><span class="short-header">訂閱者</span>建立與測試訂閱者控制器和檢視

系統管理員使用 [訂閱者] Web UI 新增郵寄清單的訂閱者，以及編輯、顯示和刪除現有訂閱者。

### 將 Subscriber 實體類別新增至 Models 資料夾

`Subscriber` 實體類別用於`MailingList` 資料表中含有排程傳送至清單訂閱者相關資訊的資料列。這些資料列包含該人員電子郵件地址以及是否驗證地址這類資訊。

1.  在 MVC 專案的 *Models* 資料夾中，從已下載的專案新增 *Subscriber.cs* 檔案。

如同`MailingList` 實體類別，`Subscriber` 實體類別可用來讀取及寫入`mailinglist` 資料表中的資料列。

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

`Subscriber` 資料列使用電子郵件地址 (而非常數 "mailinglist") 做為資料列索引鍵。(如需資料表結構的說明，請參閱[系列的第一個教學課程][系列的第一個教學課程]。)因此我們定義了`EmailAddress` 屬性，使用`RowKey` 屬性做為其支援欄位，正如同`ListName` 使用`PartitionKey` 做為其支援欄位。如稍早的說明，這可讓您在屬性 (Property) 上放置格式和驗證 DataAnnotations 屬性 (Attribute)。

`SubscriberGUID` 值會在`Subscriber` 實體建立時產生。它用於訂閱和取消訂閱連結，協助確保只有授權人員才能訂閱或取消訂閱電子郵件地址。

一開始為新的訂閱者建立資料列時，`Verified` 值為`false`. `Verified` 值只有在下列情況才會變更為`true` ：新的訂閱者按一下歡迎使用電子郵件中的 [確認] 超連結之後。如果訊息在訂閱者的`Verified` = `false`時傳送至清單，則不會有電子郵件傳送給該訂閱者。

`Verified` 屬性 (屬於`Subscriber` 實體) 會定義為可為 Null。當您指定查詢應傳回`Subscriber` 實體時，某些已擷取的資料列可能不會有`Verified` 屬性。因此，如果查詢傳回沒有 *Verified* 屬性的資料表資料列，則`Subscriber` 實體會將其`Verified` 屬性定義為可為 Null，以更精確地反映資料列的實際內容。您可能會習慣使用 SQL Server 資料表，其中，資料表的每個資料列都有相同的結構描述。在 Azure 儲存體資料表中，每個資料列都只是屬性的集合，而且每個資料列都可以有一組不同的屬性。例如，在 Azure Email Service 範例應用程式中，資料列索引鍵為 "MailingList" 的資料列沒有`Verified` 屬性。如果查詢傳回沒有`Verified` 屬性的資料表資料列，則具現化`Subscriber` 實體類別時，實體物件中的`Verified` 屬性將會是 Null。如果此屬性不是可為 Null 的，則下列資料列將同樣會有`false` 值：`Verified` = `false` 的資料列和完全沒有`Verified` 屬性的資料列。因此，使用 Azure 資料表的最佳作法是將實體類別的每個屬性設為可為 Null，以精確地讀取使用不同實體類別或不同版本的目前實體類別所建立的資料列。

### 新增 Subscriber MVC 控制器

1.  在 [方案總管] 中，於 MVC 專案的 *Controllers* 資料夾上按一下滑鼠右鍵，然後選擇 [加入現有項目]。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 *Controllers* 資料夾中選取 *SubscriberController.cs* 檔案，然後按一下 [新增]。(請確定您是取得 *Subscriber.cs*，而非 *Subscribe.cs*；您稍後會新增 *Subscribe.cs*。)

此控制器中的大部分程式碼類似您在`MailingList` 控制器中看到的程式碼。甚至資料表名稱都會相同，因為訂閱者資訊保留在`MailingList` 資料表中。

除了`FindRowAsync` 方法以外還有`FindRow` 方法，因此有必要從 catch 區塊加以呼叫，而您無法從 catch 區塊呼叫非同步方法。

在`FindRow` 之後，您會看見`GetListNamesAsync` 方法。此方法取得 [建立] 和 [編輯] 頁面上下拉式清單的資料，您可以從中選取要訂閱電子郵件地址的郵寄清單。

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

這是您在`MailingList` 控制器中看到的相同查詢。針對下拉式清單，您想要具有郵寄清單相關資訊的資料列，因此您只選取 RowKey = "mailinglist" 的資料列。

針對擷取 [索引] 頁面之資料的方法，您想要具有訂閱者資訊的資料列，因此您選取沒有 RowKey = "MailingList" 的所有資料列。

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

請注意，查詢會指定資料將讀取到`Subscriber` 物件中 (藉由指定`<Subscriber>`)，但資料將會從`mailinglist` 資料表中讀取。

訂閱者數目可能會成長到太大而無法在單一查詢中使用此方式來處理。如前所述，在實際執行應用程式中，您會使用組態權杖實作分頁。

在`HttpGet Create` 方法中，您可以設定拉式清單的資料；而在`HttpPost` 方法中，您可以在儲存新實體之前設定預設值。

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

`HttpPost Edit` 頁面比您在`MailingList` 控制器中看見的頁面複雜得多，因為`Subscriber` 頁面可讓您變更清單名稱或電子郵件地址，兩者都是索引鍵欄位。如果使用者變更其中一個欄位，則您需要刪除現有記錄並且新增記錄，而非更新現有記錄。下列程式碼顯示編輯方法的一部分，而此編輯方法處理重要與不重要變更的不同程序：

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

MVC 模型繫結器傳至`Edit` 方法的參數，包含原始清單名稱和電子郵件地址值 (在`partitionKey` and`rowKey` 參數中)，以及使用者輸入的值 (在`listName` and`emailAddress` 參數中)：

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

傳至`UpdateModel` 方法的參數，會將`PartitionKey` and`RowKey` 屬性排除在模型繫結以外：

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

這是因為，`ListName` and`EmailAddress` 屬性使用`PartitionKey` and`RowKey` 做為其支援屬性，而使用者可能變更了其中一個值。當模型繫結器更新模型時 (藉由設定`ListName` 屬性)，`PartitionKey` 屬性會自動更新。如果模型繫結器要使用屬性的原始值更新`PartitionKey` 屬性 (在更新`ListName` 屬性之後)，它會覆寫`ListName` 屬性所設定的新值。 `EmailAddress` 屬性會以相同的方式自動更新`RowKey` 屬性。

在更新`editedSubscriber` 模型物件之後，程式碼接著會判斷分割索引鍵或資料列索引鍵是否已變更。如果任一個索引鍵值已變更，則必須刪除現有訂閱者資料列，並插入新的訂閱者資料列。如果只變更資料列索引鍵，則可以使用不可部分完成的批次交易來完成刪除和插入。

請注意，此程式碼會建立傳入 Delete`Delete` 作業的新實體：

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

您使用批次傳入作業的實體必須是不同的實體。例如，您無法在建立`Subscriber` 實體並將其傳入`Delete` 作業後，又變更相同`Subscriber` 實體中的值並將其傳入`Insert` 作業。如果您這麼做，屬性變更之後的實體狀態將作用於 Delete 和 Insert 作業。

批次進行的作業必須都位於相同的資料分割中。因為清單名稱的變更會變更分割索引鍵，所以無法在某個交易中完成。

### 新增 Subscriber MVC 檢視

1.  在 [方案總管] 中，於 MVC 專案的 *Views* 資料夾下建立新的資料夾，並將它命名為 *Subscriber*。

2.  在新的 *Views\\Subscriber* 資料夾上按一下滑鼠右鍵，然後選擇 [加入現有項目]。

3.  瀏覽到已在其中下載範例應用程式的資料夾，並選取 *Views\\Subscriber* 資料夾中的所有五個 .cshtml 檔案，然後按一下 [新增]。

*Edit.cshtml* 檔案中會包含`SubscriberGUID` 值的隱藏欄位，由於該欄位並未顯示，因此不會自動提供在表單欄位中。

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### 測試應用程式

1.  按 CTRL+F5 執行專案，然後按一下 [訂閱者]。

    ![Empty Subscriber Index page][Empty Subscriber Index page]

2.  使用 **Create** 函數新增一些郵寄清單，並嘗試 **Edit** 和 **Delete** 函數確定其運作。

    ![Subscribers Index page with rows][Subscribers Index page with rows]

## <a name="message"></a>建立及測試訊息控制器和檢視

系統管理員使用 [郵件] Web UI 建立、編輯和顯示排程傳送至郵寄清單之郵件的相關資訊。

### 將 Message 實體類別新增至 Models 資料夾

1.  在 MVC 專案的 *Models* 資料夾中，從已下載的專案新增 *Message.cs* 檔案。

`Message` 實體類別用於`Message` 資料表中含有訊息 (已排程要傳送至清單) 相關資訊的資料列。這些資料列包括下列資訊：主旨行、將郵件傳送至其中的清單，以及傳送它的排程日期。

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

`Message` 類別會定義預設建構函式，以將`MessageRef` 屬性設為訊息的唯一值。由於此值是資料列索引鍵的一部分，因此`MessageRef` 屬性的 Setter 也會自動設定`RowKey` 屬性。 `MessageRef` 屬性 setter 會串連 "message" 常值與`MessageRef` 值，並將該值放入`RowKey` 屬性中。

建立`MessageRef` 值的方式，是從下列項目取得`Ticks` 值：`DateTime.Now`. 這確保在 Web UI 中顯示郵件時，依預設會針對指定排程日期 (`ScheduledDate` 是分割索引鍵) 以其建立順序來顯示它們。您可以使用 GUID 讓郵件資料列成為唯一的，但是預設擷取順序會是隨機的。

預設建構函式也會將新`message` 資料列的預設狀態設定為「擱置」。

如需`Message` 資料表結構的詳細資訊，請參閱[系列的第一個教學課程][系列的第一個教學課程]。

### 新增 Message MVC 控制器

1.  在 MVC 專案的 *Controllers* 資料夾中，從已下載的專案新增 *MessageController.cs* 檔案。

此控制器中的大部分程式碼類似您在`Subscriber` 控制器中看到的相同查詢。這裡的新內容是使用 Blob 的程式碼。針對每封郵件，會以 .htm 和 .txt 檔案形式上傳電子郵件的 HTML 和純文字內容，並將其儲存至 Blob 中。

Blob 儲存在 Blob 容器中。Azure Email Service 應用程式會在名稱為 "azuremailblobcontainer" 的單一 Blob 容器中儲存其所有 Blob，而且控制器建構函式中的程式碼會參考此 Blob 容器：

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

針對使用者選擇上傳的每個檔案，MVC 檢視提供含有檔案相關資訊的`HttpPostedFile` 物件。使用者建立新的郵件時，會使用`HttpPostedFile` 物件，將檔案儲存至 Blob。使用者編輯郵件時，可以選擇上傳取代檔案，或將 Blob 保持不變。

控制器包含一個方法，可經由`HttpPost Create` and`HttpPost Edit` 方法的呼叫而儲存 Blob：

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

`HttpPost Create` 方法會儲存兩個 Blob，然後新增`Message` 資料表資料列。Blob 的命名方式是以副檔名 ".htm" 或 ".txt" 串連`MessageRef` 值。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

`HttpGet Edit` 方法會驗證擷取的訊息處於`Pending` 狀態，因此，在背景工作角色 B 開始處理訊息之後，使用者就無法再變更訊息。 `HttpPost Edit` 方法和`Delete` and`DeleteConfirmed` 方法中也有類似的程式碼。

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

在`HttpPost Edit` 方法中，只有在使用者選擇上傳新的檔案時，此程式碼才會儲存新的 Blob。

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

如果排程日期變更，則會變更分割索引鍵，而且必須刪除和插入資料列。這無法在單一交易中完成，因為會影響多個資料分割。

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

`HttpPost Delete` 方法會在刪除資料表中的資料列時刪除 Blob：

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### 新增 Message MVC 檢視

1.  在 MVC 專案的 *Views* 資料夾下建立新的資料夾，並將其命名為 `Message`.

2.  在新的 *Views\\Message* 資料夾中，從已下載的專案新增五個 *.cshtml* 檔案。

`HttpPost Edit` 方法需要分割索引鍵和資料列索引鍵，因此，*Edit.cshtml* 檔案中的程式碼會在隱藏欄位中提供這些項目。

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

`Subscriber` 控制器中不需要隱藏欄位，因為 (a)`ListName` and`EmailAddress` 屬性 (屬於`Subscriber` 方法) 會更新`PartitionKey` and`RowKey` 屬性，且 (b)`ListName` and`EmailAddress` 屬性已連同`EditorFor` 協助程式包含在 [編輯] 檢視中。 `Subscriber` 模型中的 MVC 模型繫結器更新`ListName` 屬性時，會自動更新`PartitionKey` 屬性，而 MVC 模型繫結器更新`EmailAddress` 屬性時 (在`Subscriber` 模型中)，會自動更新`RowKey` 屬性。在`Message` 模型中，對應至分割索引鍵和資料列索引鍵的欄位是不可編輯的欄位，因此無法使用這種方式進行設定。

`MessageRef` 屬性也有隱藏欄位。這是與分割索引鍵相同的值，包括它的目的是要在`HttpPost Edit` 方法中讓程式碼更為清楚。包含`MessageRef` 隱藏欄位，可讓`HttpPost Edit` 方法中的程式碼在建構 Blob 的檔案名稱時，透過該名稱參照`MessageRef` 值。

*Index.cshtml* 檔案中的 [訊息索引] 檢視與其他 [索引] 檢視的不同之處在於，[編輯] 和 [刪除] 連結只會針對處於`Pending` 狀態的訊息而顯示：

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

這有助於在背景工作角色 A 已開始處理郵件之後，防止使用者變更郵件。

### 測試應用程式

1.  按 CTRL+F5 執行專案，然後按一下 [郵件]。

    ![Empty Message Index page][Empty Message Index page]

2.  使用 **Create** 函數新增一些郵寄清單，並嘗試 **Edit** 和 **Delete** 函數確定其運作。

    ![Subscribers Index page with rows][2]

## <a name="unsubscribe"></a>建立及測試取消訂閱者控制器和檢視

接下來，您將實作取消訂閱程序的 UI。

本教學課程僅說明如何建置取消訂閱程序 (而不是訂閱程序) 的控制器。如[第一個教學課程][系列的第一個教學課程]所說明，訂閱程序的 UI 和服務方法包含在下載中，但未包含在本教學課程指示中，因為範例應用程式未實作服務方法的安全性。基於測試之用，您可以使用 [訂閱者] 系統管理員頁面將電子郵件地址訂閱至清單。

### 將 Unsubscribe 檢視模型新增至 Models 資料夾

1.  在`Models` 資料夾中 (在 MVC 專案中)，從已下載的專案新增`UnsubscribeVM.cs` 檔案。

`UnsubscribeVM` 檢視模型可用來在`Unsubscribe` 控制器及其檢視之間傳遞資料。

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

取消訂閱連結包含`SubscriberGUID`. 該值可用來取得`MailingList` 資料表中的電子郵件地址、清單名稱和清單說明。此檢視顯示電子郵件地址以及要從中取消訂閱的清單說明，並顯示使用者必須按一下才能完成取消訂閱程序的 [確認] 按鈕。

### 新增 Unsubscribe 控制器

1.  在`Controllers` 資料夾中 (在 MVC 專案中)，從已下載的專案新增 *UnsubscribeController.cs* 檔案。

此控制器的`HttpGet Index` 方法會顯示初始取消訂閱頁面，而`HttpPost Index` 方法會處理 [確認] 或 [取消] 按鈕。

`HttpGet Index` 方法會在查詢字串中使用 GUID 和清單名稱，取得訂閱者的`MailingList` 資料表資料列。然後，它會將檢視所需的所有資訊放入檢視模型，並顯示 [取消訂閱] 頁面。它會將`Confirmed` 屬性設定為 Null，以告知檢視顯示 [取消訂閱] 頁面的初始版本。

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

注意：SubscriberGUID 不在分割索引鍵或資料列索引鍵中，因此此查詢的效能會隨著資料分割大小 (郵寄清單中的電子郵件地址數目) 的增加而降低。如需讓此查詢更具延展性之替代方式的相關資訊，請參閱[系列的第一個教學課程][系列的第一個教學課程]。

`HttpPost Index` 方法同樣會使用 GUID 和清單名稱取得訂閱者資訊，並填入檢視模型屬性。然後，如果已按一下 [確認] 按鈕，則會刪除`MailingList` 資料表中的訂閱者資料列。如果已按下 [確認] 按鈕，則也會將`Confirm` 屬性設為`true`，否則會將`Confirm` 屬性設為`false`. `Confirm` 屬性的值會告知此檢視顯示 [取消訂閱] 頁面的已確認或已取消版本。

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### 建立 MVC 檢視

1.  在 MVC 專案的 *Views* 資料夾中建立新的資料夾，並將其命名為 *Unsubscribe*。

2.  在新的 *Views\\Unsubscribe* 資料夾中，從已下載的專案新增 *Index.cshtml* 檔案。

在 *Index.cshtml* 檔案中，`Layout = null` 行指定不應使用 \_Layout.cshtml 檔案來顯示此頁面。[取消訂閱] 頁面會顯示簡單的 UI，其中未包含用於系統管理員頁面的頁首和頁尾。

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

在頁面的本文中，`Confirmed` 屬性會決定將顯示在頁面上的內容：[確認] 和 [取消] 按鈕 (如果此屬性為 Null)、取消訂閱已確認郵件 (如果此屬性為 true)、取消訂閱已取消郵件 (如果此屬性為 false)。

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### 測試應用程式

1.  按 CTRL+F5 執行專案，然後按一下 [訂閱者]。

2.  按一下 [建立]，然後建立您在先前測試時所建立之任何郵寄清單的新訂閱者。

    在 [訂閱者] [索引] 頁面上，保持瀏覽器視窗的開啟狀態。

3.  開啟 [伺服器總管]，然後選取 [Azure / 儲存體 / (開發)] 節點。

4.  展開 [資料表]、以滑鼠右鍵按一下 [MailingList] 資料表，然後按一下 [檢視資料表]。

5.  按兩下您已新增的訂閱者資料列。

6.  在 [編輯實體] 對話方塊中，選取並複製`SubscriberGUID` 值。

    ![Azure 儲存體總管][Azure 儲存體總管]

7.  切換回瀏覽器視窗。在瀏覽器的網址列中，將 URL 中的 "Subscriber" 變更為 "unsubscribe?ID=[guidvalue]&listName=[listname]"，其中 [guidvalue] 是您從 Azure 儲存體總管複製的 GUID，而 [listname] 是郵寄清單的名稱。例如：

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    顯示要求確認之 [取消訂閱] 頁面的版本：

    ![Unsubscribe page][Unsubscribe page]

8.  按一下 [確認]，您會看到已取消訂閱電子郵件地址的確認。

    ![Unsubscribe confirmed page][Unsubscribe confirmed page]

9.  回到 [訂閱者] [索引] 頁面，以驗證訂閱者資料列已不存在。

## <a name="nextsteps"></a>後續步驟

如[系列中的第一個教學課程][系列的第一個教學課程]所說明，本教學課程不會顯示訂閱程序的元件，因為 ASP.NET Web API 服務方法未實作共用密碼安全性。不過，您在第二個教學課程中設定的 IP 限制也會保護服務方法，而且您可以從已下載的專案中複製下列檔案，來新增訂閱功能。

針對 ASP.NET Web API 服務方法：

-   Controllers\\SubscribeAPI.cs

針對訂閱者在按一下電子郵件中的 [確認] 連結時所取得的網頁，而電子郵件是由服務方法所產生：

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

在[下一個教學課程][下一個教學課程]中，您將設定和程式設計背景工作角色 A (排程電子郵件的背景工作角色)。

如需使用 Azure 儲存體資料表、佇列和 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程][本系列的最後一個教學課程]。

<div><a href="/zh-tw/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">教學課程 4</a></div>

  [系列的第一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/1-overview/
  [建立 Visual Studio 方案]: #cloudproject
  [更新儲存體用戶端程式庫 NuGet 封裝]: #updatescl
  [設定專案以使用儲存體模擬器]: #configurestorage
  [設定追蹤及處理重新啟動]: #tracing
  [新增程式碼，以在 Application\_Start 方法中建立資料表、佇列和 Blob 容器]: #createifnotexists
  [建立及測試郵寄清單]: #mailinglist
  [建立及測試訂閱者控制器和檢視]: #subscriber
  [建立及測試訊息控制器和檢視]: #message
  [建立及測試取消訂閱者控制器和檢視]: #unsubscribe
  [後續步驟]: #nextsteps
  [New Project menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [New Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [New Azure Cloud Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [New Azure Cloud Project dialog box - renaming the web role]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [New Azure Cloud Project dialog box - adding a worker role]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [不需要驗證]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [已完成的方案]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [home page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Compute emulator in system tray]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Manage NuGet Packages for Solution in menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Azure Storage package in Manage NuGet Packages dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Selecting both projects in the Select Projects dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Web role properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Right Click Properties]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Invalid service configuration and definition files error]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [第二個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [角色執行個體因作業系統升級而重新啟動]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [隨選傳輸]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/zh-tw/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Azure 儲存體用戶端程式庫 2.0 資料表深入探討]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [了解表格服務資料模型]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179338.aspx
  [PartitionKey 或 RowKey 中的 % 字元]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [使用 .NET 4.5 的非同步支援避免封鎖呼叫]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [如何最佳利用 Azure 資料表]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Azure 資料表：極度預期接續權杖]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Azure 儲存體記錄：使用記錄追蹤儲存體要求]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Empty MailingList Index page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [MailingList Index page with rows]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Empty Subscriber Index page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Subscribers Index page with rows]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Empty Message Index page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Azure 儲存體總管]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Unsubscribe page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Unsubscribe confirmed page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [下一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [本系列的最後一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
