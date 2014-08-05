<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Step 3: Web Role" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 3: Web role" metaKeywords="Azure tutorial, Email Service application, ASP.NET MVC web role, MVC controllers, Web API controller, Cloud Service project" description="The third tutorial in a series that teaches how to create and deploy the Email Service app in an Azure cloud service." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building the web role for the Azure Email Service application - 3 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

建置 Azure Email Service 應用程式的 Web 角色 - 3/5
==================================================

這是一系列教學課程的第三個教學課程 (共五個)，顯示如何建置和部署 Azure Email Service 範例應用程式。如需應用程式和教學課程系列的詳細資訊，請參閱[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。

在本教學課程中，您將了解：

-   如何使用 Web 角色和背景工作角色建立含有雲端服務專案的解決方案。
-   如何在 MVC 4 控制器和檢視中使用 Azure 資料表、Blob 和佇列。
-   如何處理使用 Azure 資料表時的並行衝突。
-   如何設定 Web 角色或 Web 專案使用 Azure 儲存體帳戶。

建立解決方案建立 Visual Studio 解決方案
---------------------------------------

起點是建立 Visual Studio 解決方案，內含 Web 前端的專案以及一個後端 Azure 背景工作角色的專案。您稍後將新增第二個背景工作角色。

(如果您想要在 Azure 網站中執行 Web UI，而非 Azure 雲端服務，請參閱本教學課程稍後的[替代架構](#alternativearchitecture)小節，了解這些指示的變更。)

### 使用 Web 角色和背景工作角色建立雲端服務專案

1.  使用較高的權限啟動 Visual Studio。

    > [WACOM.NOTE] 在 Visual Studio 2013 中，您不需要使用較高的權限，因為新的專案預設會使用計算模擬器運算式。

2.  從 **[檔案]** 功能表中，選取 **[新增專案]**。

    ![New Project menu](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png)

3.  展開 **[C\#]**，並選取 **[已安裝的範本]** 下的 **[雲端]**，然後選取 **[Azure 雲端服務]**。

4.  將應用程式命名為 **AzureEmailService**，然後按一下 **[確定]**。

    ![[新增專案] 對話方塊](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png)

5.  在 **[New Azure Cloud Service]** 對話方塊中，選取 **[ASP.NET Web 角色]**，然後按一下指向右側的箭號。

    > [WACOM.NOTE] 下載且用於本教學課程的程式碼是 MVC 4，但是使用針對 Visual Studio 2012 所寫的指示，無法在 Visual Studio 2013 中建立 MVC 4 Web 角色。在 Visual Studio 2013 中，請執行下列動作：(1) 跳過這裡建立 Web 角色的步驟，並針對背景工作角色執行這些步驟。(2) 建立背景工作角色之後，請在 **[方案總管]** 中，於解決方案上按一下滑鼠右鍵，然後按一下 **[新增]** -- **[新增專案]**。在 **[加入新的專案]** 對話方塊的左窗格中，展開 **[Web]**，然後選取 **[Visual Studio 2012]**。(3) 選擇 **[ASP.NET MVC 4 Web 應用程式]**，並將專案命名為 **MvcWebRole**，然後按一下 **[確定]**。(4) 在 **[New ASP.NET Project]** 對話方塊中，選取 **[網際網路應用程式]** 範本。(5) 在 **[方案總管]** 的 **[AzureEmailService]** 下，於 **[角色]** 上按一下滑鼠右鍵，然後按一下 **[新增]** - **[方案中的 Web 角色專案]**。(6) 在 **[與角色專案關聯]** 方塊中，選取 **[MvcWebRole]** 專案，然後按一下 **[確定]**。

    ![New Azure Cloud Project dialog box](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png)

6.  在右側的欄位中，將滑鼠指標移至 **[MvcWebRole1]** 上方，然後按一下鉛筆圖示以變更 Web 角色的名稱。

7.  輸入 MvcWebRole 做為新名稱，然後按 Enter 鍵。

    ![New Azure Cloud Project dialog box - renaming the web role](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png)

8.  依照新增 **[背景工作角色]** 的相同程序進行，並將它命名為 WorkerRoleA，然後按一下 **[確定]**。

    ![New Azure Cloud Project dialog box - adding a worker role](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png)

9.  在 **[New ASP.NET Project]** 對話方塊中，選取 **[網際網路應用程式]** 範本。

10. 在 **[檢視引擎]** 下拉式清單中，確定已選取 **[Razor]**，然後按一下 **[確定]**。

    ![[新增專案] 對話方塊](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png)

### 設定頁首、功能表和頁尾

在本節中，您將更新系統管理員 Web UI 的每個頁面上顯示的頁首、頁尾和功能表項目。應用程式會有三組系統管理員網頁：一個是針對郵寄清單、一個是針對郵寄清單訂閱者，而一個是針對郵件。

1.  在 **[方案總管]** 中，展開 Views\\Shared 資料夾，然後開啟 \_Layout.cshtml 檔案。

    ![方案總管中的 \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png)

2.  在 **[&lt;title\>]** 元素中，將 "My ASP.NET MVC Application" 變更為 "Azure Email Service"。

3.  在類別為 "site-title" 的 **[&lt;p\>]** 元素中，將 "your logo here" 變更為 "Azure Email Service"，然後將 "Home" 變更為 "MailingList"。

    ![title and header in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png)

4.  刪除功能表區段：

    ![menu in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png)

5.  在舊功能表區段所在處插入新的功能表區段：

         <ul id="menu">
        <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
        <li>@Html.ActionLink("Messages", "Index", "Message")</li>
        <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

6.  在 **[&lt;footer\>]** 元素中，將 "My ASP.NET MVC Application" 變更為 "Azure Email Service"。

    ![footer in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png)

### 在本機執行應用程式

1.  按 CTRL+F5 執行應用程式。

    應用程式首頁隨即出現在預設瀏覽器中。

    ![home page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png)

    應用程式在 Azure 計算模擬器中執行。您可以在 Windows 系統匣中看到計算模擬器圖示：

    ![Compute emulator in system tray](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png)

設定追蹤設定追蹤
----------------

若要儲存追蹤資料，請開啟 *WebRole.cs* 檔案，然後新增下列 `ConfigureDiagnostics` 方法。請新增程式碼，以在 `OnStart` 方法中呼叫新方法。

> [WACOM.NOTE] 在 Visual Studio 2013 中，在 MvcWebRole 專案上按一下滑鼠右鍵，按一下 **[加入現有項目]**，然後新增已下載專案中的 *WebRole.cs* 檔案，而不需要進行下列手動變更 *WebRole.cs* 中程式碼的步驟。

    private void ConfigureDiagnostics()
    {
    DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
    config.Logs.BufferQuotaInMB = 500;
    config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
    config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

    DiagnosticMonitor.Start(
    "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
    config);
    }

    public override bool OnStart()
    {
    ConfigureDiagnostics();
    return base.OnStart();
    }

[第二個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)中會說明 `ConfigureDiagnostics` 方法。

重新啟動新增程式碼以有效率地處理重新啟動。
------------------------------------------

Azure 雲端服務應用程式每個月大約會重新啟動兩次進行作業系統更新。(如需作業系統更新的詳細資訊，請參閱[角色執行個體因作業系統升級而重新啟動](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (英文)。)即將關閉 Web 應用程式時，會引發 `OnStop` 事件。Visual Studio 所建立的 Web 角色樣板不會覆寫 `OnStop` 方法，因此，應用程式在關閉之前只有幾秒的時間可以完成 HTTP 要求的處理。您可以新增程式碼來覆寫 `OnStop` 方法，以確保正常地處理關閉。

若要處理關閉和重新啟動，請開啟 *WebRole.cs* 檔案，並新增下列 `OnStop` 方法覆寫。

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

此程式碼需要其他 `using` 陳述式：

      using System.Diagnostics;

關閉應用程式之前，最多有 5 分鐘的時間可以結束 `OnStop` 方法。您可以對 `OnStop` 方法新增 5 分鐘的睡眠呼叫，讓您的應用程式有最多的時間可以處理目前要求，但是如果已正確地調整您的應用程式，則應該可以在 5 分鐘內處理完其餘的要求。最好盡快停止這項作業，以最快的速度重新啟動應用程式，並繼續處理要求。

Azure 去除角色之後，負載平衡器會停止將要求傳送至角色執行個體，並在之後呼叫 `OnStop` 方法。如果沒有您角色的另一個執行個體，則除非您的角色完成關閉並重新啟動 (通常需要數分鐘的時間)，否則不會處理要求。這是 Azure 服務等級協定需要每個角色至少有兩個執行個體才能利用執行時間保證的其中一個原因。

在針對 `OnStop` 方法顯示的程式碼中，會建立 `Requests Current` 的 ASP.NET 效能計數器。`Requests Current` 計數器值包含目前要求數目 (包括放入佇列、目前執行或等待寫入用戶端的要求)。每秒會檢查一次 `Requests Current` 值，並在落到零時傳回 `OnStop` 方法。傳回 `OnStop` 之後，即會關閉角色。

如果透過 `OnStop` 方法呼叫，而未[執行隨選傳輸](http://msdn.microsoft.com/en-us/library/windowsazure/gg433075.aspx)，則不會儲存追蹤資料。您可以使用 [dbgview](http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx) 公用程式，透過遠端桌面連線即時檢視 `OnStop` 追蹤資訊。

更新儲存體用戶端程式庫更新儲存體用戶端程式庫 NuGet 封裝
-------------------------------------------------------

> [WACOM.NOTE] 這可能不是必要步驟。如果 [更新] 清單中未顯示 Azure 儲存體 NuGet 封裝，則安裝的版本是最新版本。

您用來使用 Azure 儲存體資料表、佇列和 Blob 的 API 架構就是儲存體用戶端程式庫 (SCL)。此 API 包含在雲端服務專案範本的 NuGet 封裝中。不過，在撰寫本教學課程時，專案範本包括的是 1.7 版的 SCL，而不是最新的 2.0 版。因此，開始撰寫程式碼之前，將會更新 NuGet 封裝。

1.  在 Visual Studio **[工具]** 功能表中，將滑鼠指標移至 **[Library Package Manager]** 上方，然後按一下 **[Manage NuGet Packages for Solution]**。

    ![Manage NuGet Packages for Solution in menu](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png)

2.  在 **[Manage NuGet Packages]** 對話方塊的左窗格中，選取 **[更新]**，並向下捲動到 **[Azure 儲存體]** 封裝，然後按一下 **[更新]**。

    ![Azure Storage package in Manage NuGet Packages dialog box](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png)

3.  在 **[選取專案]** 對話方塊中，確定已選取兩個專案，然後按一下 **[確定]**。

    ![Selecting both projects in the Select Projects dialog box](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png)

4.  接受授權合約以完成封裝的安裝，然後關閉 **[Manage NuGet Packages]** 對話方塊。

5.  在 WorkerRoleA 專案的 *WorkerRole.cs* 中，如果有下列 `using` 陳述式，請刪除它，因為已不再需要它：

         using Microsoft.WindowsAzure.StorageClient;

1.7 版的 SCL 包括 LINQ 提供者，可以簡化資料表查詢的程式碼編寫。在撰寫本教學課程時，2.0 資料表服務層 (TSL) 還沒有 LINQ 提供者。如果您想要使用 LINQ，還是可以在 [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.table.dataservices.aspx) (英文) 命名空間中存取 SCL 1.7 LINQ 提供者。2.0 TSL 的設計目的是改善效能，而 1.7 LINQ 提供者並未從這些改善中獲益。範例應用程式使用 2.0 TSL，因此不會使用 LINQ 進行查詢。如需 SCL 和 TSL 2.0 的詳細資訊，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)結尾的資源。

> [WACOM.NOTE] 儲存體用戶端程式庫 2.1 已重新新增 LINQ 支援，但是本教學課程不會使用 LINQ 進行儲存體資料表查詢。目前 SCL 也支援非同步程式設計，但是本教學課程中未顯示非同步程式碼。如需非同步程式設計以及將它與 Azure SCL 搭配使用之範例程式碼的詳細資訊，請參閱下列電子書章節和其隨附的可下載專案：[使用 .NET 4.5 的非同步支援避免封鎖呼叫](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async) (英文)。

新增 SCL 1.7 參考新增 SCL 1.7 組件參考
--------------------------------------

> [WACOM.NOTE] 如果您是使用 SCL 2.1 或更新版本 (含最新 SDK 的 Visual Studio 2013)，請跳過本節。

2.0 版的儲存體用戶端程式庫 (SCL) 2.0 沒有進行診斷所需的任何項目，因此您需要新增 1.7 組件參考。

1.  在 MvcWebRole 專案上按一下滑鼠右鍵，然後選擇 **[加入參考]**。

2.  按一下對話方塊底部的 **[瀏覽...]** 按鈕。

3.  瀏覽到下列資料夾：

         C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  選取 *Microsoft.WindowsAzure.StorageClient.dll*，然後按一下 **[新增]**。

5.  在 **[參考管理員]** 對話方塊中，按一下 **[確定]**。

6.  請針對 WorkerRoleA 專案，重複此程序。

App\_Start 程式碼新增程式碼，以在 Application\_Start 方法中建立資料表、佇列和 Blob 容器
---------------------------------------------------------------------------------------

Web 應用程式將使用 `MailingList` 資料表、`Message` 資料表、`azuremailsubscribequeue` 佇列和 `azuremailblobcontainer` Blob 容器。您可以使用 Azure 儲存體總管這類工具手動建立這些項目，但是，之後每次透過新的儲存體帳戶開始使用應用程式時，都需要手動執行這項作業。在本節中，您將新增程式碼，以在啟動應用程式時執行、確認必要資料表、佇列和 Blob 容器是否存在，以及在沒有上述項目時建立它們。

您可以在 *WebRole.cs* 檔案的 `OnStart` 方法中或 *Global.asax* 檔案中新增這個單次啟動程式碼。在本教學課程中，您將在 *Global.asax* 檔案中初始化 Azure 儲存體，因為這適用於 Azure 網站和 Azure 雲端服務 Web 角色。

1.  在 **[方案總管]** 中，展開 *Global.asax*，然後開啟 *Global.asax.cs*。

2.  在 `Application_Start` 方法後面新增 `CreateTablesQueuesBlobContainers` 方法，然後從 `Application_Start` 方法呼叫新方法 (如下列範例所示)：

         protected void Application_Start()
         {
        AreaRegistration.RegisterAllAreas();
        WebApiConfig.Register(GlobalConfiguration.Configuration);
        FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        AuthConfig.RegisterAuth();
        // Verify that all of the tables, queues, and blob containers used in this application
        // exist, and create any that don't already exist.
        CreateTablesQueuesBlobContainers();
         }

        private static void CreateTablesQueuesBlobContainers()
         {
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
        // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
        //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
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

3.  在 `RoleEnvironment` 下的藍色波浪狀線條上按一下滑鼠右鍵，並選取 **[解析]**，然後選取 **[using Microsoft.WindowsAzure.ServiceRuntime]**。

    ![rightClick](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png)

4.  在 `CloudStorageAccount` 下的藍色波浪狀線條上按一下滑鼠右鍵，並選取 **[解析]**，然後選取 **[using Microsoft.WindowsAzure.Storage]**。

5.  或者，您可以手動新增下列 using 陳述式：

        using Microsoft.WindowsAzure.ServiceRuntime;
        using Microsoft.WindowsAzure.Storage;

6.  建置應用程式以儲存檔案，並確認沒有任何編譯錯誤。

在下列各節中，您會建置 Web 應用程式的元件，而且可以使用開發儲存體或儲存體帳戶來測試它們，而不需要先手動建立資料表、佇列或 Blob 容器。

郵寄清單建立與測試郵寄清單控制器和檢視
--------------------------------------

系統管理員使用 **[郵寄清單]** Web UI 建立、編輯和顯示郵寄清單 (例如 "Contoso University History Department announcements" 和 "Fabrikam Engineering job postings")。

### 將 MailingList 實體類別新增至 Models 資料夾

在 `MailingList` 資料表中含有清單資訊 (例如其說明以及傳送至清單之電子郵件的「寄件者」電子郵件地址) 的資料列中，會使用 `MailingList` 實體類別。

1.  在 **[方案總管]** 中，於 MVC 專案的 `Models` 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

    ![Add existing item to Models folder](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png)

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 `Models` 資料夾中選取 *MailingList.cs* 檔案，然後按一下 **[新增]**。

3.  開啟 *MailingList.cs*，並檢查程式碼。

         public class MailingList :TableEntity
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

    Azure 儲存體 TSL 2.0 API 需要您用於資料表作業的實體類別衍生自 [TableEntity](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx)。此類別定義 `PartitionKey`、`RowKey`、`TimeStamp` 和 `ETag` 欄位。系統會使用 `TimeStamp` 和 `ETag` 屬性。您稍後將會在教學課程看到如何使用 `ETag` 屬性進行並行處理。

    (當您想要使用資料表資料列做為鍵值組的 Dictionary 集合，而非使用預先定義的模型類別時，也會使用 [DynamicTableEntity](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) 類別。如需詳細資訊，請參閱 [Azure 儲存體用戶端程式庫 2.0 資料表深入探討](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (英文)。)

    `mailinglist` 資料表分割索引鍵是清單名稱。在此實體類別中，分割索引鍵值的存取方式是使用 `PartitionKey` 屬性 (定義於 `TableEntity` 類別中) 或 `ListName` 屬性 (定義於 `MailingList` 類別中)。`ListName` 屬性使用 `PartitionKey` 做為其支援變數。定義 `ListName` 屬性可讓您在程式碼中使用更具描述性的變數名稱，並簡化 Web UI 的程式設計，因為格式和驗證 DataAnnotations 屬性可以新增至 `ListName` 屬性，但是無法直接新增至 `PartitionKey` 屬性。

    `ListName` 屬性 (Property) 上的 `RegularExpression` 屬性 (Attribute) 可讓 MVC 驗證使用者輸入，確保輸入的清單名稱值只包含英數字元或底線。這項限制的實作是要保持簡單的清單名稱，以輕鬆地將清單名稱用於 URL 的查詢字串中。

    **注意：**如果您想要讓清單名稱格式的限制較少，則可以在查詢字串中使用清單名稱時允許其他字元和 URL 編碼清單名稱。不過，在 Azure 資料表分割索引鍵或資料列索引鍵中不允許使用特定字元，因此您至少需要排除那些字元。如需分割索引鍵或資料列索引鍵欄位中不允許或導致問題之字元的詳細資訊，請參閱[了解表格服務資料模型](http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx)和 [PartitionKey 或 RowKey 中的 % 字元](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx) (英文)。

    `MailingList` 類別定義將 `RowKey` 設定為硬式編碼字串 "mailinglist" 的預設建構函式，因為此資料表中的所有郵寄清單資料列都會使用該值做為其資料列索引鍵。(如需資料表結構的說明，請參閱[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。)基於此目的，可能已選擇任何常數值，只要它絕對不會與電子郵件地址相同 (即此資料表中訂閱者資料列的資料列索引鍵)。

    建立新的 `MailingList` 實體時，一律必須輸入清單名稱和「寄件者」電子郵件地址，這樣一來，即會有 `Required` 屬性。

    `Display` 屬性指定用於 MVC UI 中某個欄位的預設標題。

### 新增 MailingList MVC 控制器

1.  在 **[方案總管]** 中，於 MVC 專案的 Controllers 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

    ![Add existing item to Controllers folder](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png)

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 `Controllers` 資料夾中選取 *MailingListController.cs* 檔案，然後按一下 **[新增]**。

3.  開啟 *MailingListController.cs*，並檢查程式碼。

    預設建構函式會建立 `CloudTable` 物件以用於與 `mailinglist` 資料表搭配使用。

         public class MailingListController :Controller
         {
        private CloudTable mailingListTable;

        public MailingListController()
             {
        var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
        // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
        //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

        var tableClient = storageAccount.CreateCloudTableClient();
        mailingListTable = tableClient.GetTableReference("mailinglist");
             }

    此程式碼會從雲端服務專案設定檔案中取得您 Azure 儲存體帳戶的認證，以連線至儲存體帳戶。(您稍後會在本教學課程中進行這些設定，但是是在測試控制器之前。)如果您即將在 Azure 網站中執行 MVC 專案，則可以改為從 Web.config 檔案取得連接字串。

    接下來是 `FindRow` 方法，只要控制器需要查詢 `MailingList` 資料表的特定郵寄清單項目就會予以呼叫 (例如編輯郵寄清單項目)。此程式碼會擷取單一 `MailingList` 實體，方法是使用傳遞給它的分割索引鍵和資料列索引鍵值。此控制器所編輯的資料列就是資料列索引鍵為 "MailingList" 的資料列，因此可能已針對資料列索引鍵硬式編碼 "MailingList"，但是指定分割索引鍵和資料列索引鍵是用於所有控制器中 `FindRow` 方法的一種模式。

         private MailingList FindRow(string partitionKey, string rowKey)
         {
        var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
        var retrievedResult = mailingListTable.Execute(retrieveOperation);
        var mailingList = retrievedResult.Result as MailingList;
        if (mailingList == null)
             {
        throw new Exception("No mailing list found for:" + partitionKey);
             }

        return mailingList;
         }

    這指示比較下列兩者：`MailingList` 控制器中的 `FindRow` 方法 (傳回郵寄清單資料列) 與 `Subscriber` 控制器中的 `FindRow` 方法 (傳回相同 `mailinglist` 資料表中的訂閱者資料列)。

         private Subscriber FindRow(string partitionKey, string rowKey)
         {
        var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
        var retrievedResult = mailingListTable.Execute(retrieveOperation);
        var subscriber = retrievedResult.Result as Subscriber;
        if (subscriber == null)
             {
        throw new Exception("No subscriber found for:" + partitionKey + ", " + rowKey);
             }
        return subscriber;
         }

    這兩個查詢的唯一差異是傳遞給 [TableOperation.Retrieve](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) 方法的模型類型。模型類型指定您預期查詢傳回之一或多個資料列的結構描述 (屬性)。單一資料表的不同資料列中可能會有不同的結構描述。讀取已用來建立資料列的資料列時，您通常會指定相同的模型類型。

    **[索引]** 頁面會顯示所有郵寄清單資料列，因此，`Index` 方法中的查詢會傳回資料列索引鍵為 "mailinglist" 的所有 `MailingList` 實體 (資料表中其他資料列的資料列索引鍵為電子郵件地址，而且包含訂閱者資訊)。

                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
        lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

    `Index` 方法使用設計成處理逾時情況的程式碼來括住此查詢。

         public ActionResult Index()
         {
        TableRequestOptions reqOptions = new TableRequestOptions()
             {
        MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
        RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
             };
        List<MailingList> lists;
        try
             {
        var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
        lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
             }
        catch (StorageException se)
             {
        ViewBag.errorMessage = "Timeout error, try again. ";
        Trace.TraceError(se.Message);
        return View("Error");
             }

        return View(lists);
         }

    如果您未指定逾時參數，API 會使用指數方式增加的逾時限制來自動重試三次。如果是使用者等待頁面出現的 Web 介面，這可能會導致無法接受的冗長等待時間。因此，此程式碼指定線性重試 (這樣一來，就不會每次都增加逾時限制) 以及使用者等待的合理逾時限制。

    使用者按一下 **[建立]** 頁面上的 **[建立]** 按鈕時，MVC 模型繫結器會透過檢視中的輸入來建立 `MailingList` 實體，而 `HttpPost Create` 方法會將該實體新增至資料表。

         [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(MailingList mailingList)
         {
        if (ModelState.IsValid)
             {
        var insertOperation = TableOperation.Insert(mailingList);
        mailingListTable.Execute(insertOperation);
        return RedirectToAction("Index");
             }

        return View(mailingList);
         }

在 **[編輯]** 頁面中，`HttpGet Edit` 方法會查詢資料列，而 `HttpPost` 方法會更新資料列。

        [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
    if (ModelState.IsValid)
            {
    var mailingList = new MailingList();
    UpdateModel(mailingList);
    try
                {
    var replaceOperation = TableOperation.Replace(mailingList);
    mailingListTable.Execute(replaceOperation);
    return RedirectToAction("Index");
                }
    catch (StorageException ex)
                {
    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
    // Concurrency error
    var currentMailingList = FindRow(partitionKey, rowKey);
    if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                        {
    ModelState.AddModelError("FromEmailAddress", "Current value:" + currentMailingList.FromEmailAddress);
                        }
    if (currentMailingList.Description != editedMailingList.Description)
                        {
    ModelState.AddModelError("Description", "Current value:" + currentMailingList.Description);
                        }
    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
    + "was modified by another user after you got the original value.The "
    + "edit operation was canceled and the current values in the database "
    + "have been displayed.If you still want to edit this record, click "
    + "the Save button again.Otherwise click the Back to List hyperlink.");
    ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
                    }
    else
                    {
    throw; 
                    }
                }
            }
    return View(editedMailingList);
        }

  try-catch 區塊會處理並行錯誤。如果使用者選取某個郵寄清單進行編輯，同時，在瀏覽器中顯示 **Edit** 頁面時，有另一位使用者編輯相同的郵寄清單，則會引發並行例外狀況。發生此情況時，程式碼會顯示警告訊息，並指出另一位使用者已變更的欄位。TSL API 會使用 `ETag` 來檢查並行衝突。每次更新資料表資料列，`ETag` 值都會變更。取得要編輯的資料列時，即會儲存 `ETag` 值，而執行更新或刪除作業時，即會傳入所儲存的 `ETag` 值。(`Edit` 檢視具有 ETag 值的隱藏欄位。)如果更新作業發現所更新記錄上的 `ETag` 值與傳入更新作業的 `ETag` 值不同，則會引發並行例外狀況。如果您不在意並行衝突，則可以將實體中傳入更新作業的 ETag 欄位設定為星號 ("*")，這樣會略過衝突。 

  注意：HTTP 412 錯誤不是並行錯誤獨有的。SCL API 也可能針對其他錯誤而引發此錯誤。

  針對 **Delete** 頁面，`HttpGet Delete` 方法會查詢資料列來顯示其內容，而 `HttpPost` 方法會刪除 `MailingList` 資料表中的 `MailingList` 資料列以及任何與其相關聯的 `Subscriber` 資料列。

    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(string partitionKey)
        {
    // Delete all rows for this mailing list, that is, 
    // Subscriber rows as well as MailingList rows.
    // Therefore, no need to specify row key.
    var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
    var listRows = mailingListTable.ExecuteQuery(query).ToList();
    var batchOperation = new TableBatchOperation();
    int itemsInBatch = 0;
    foreach (MailingList listRow in listRows)
            {
    batchOperation.Delete(listRow);
    itemsInBatch++;
    if (itemsInBatch == 100)
                {
    mailingListTable.ExecuteBatch(batchOperation);
    itemsInBatch = 0;
    batchOperation = new TableBatchOperation();
                }
            }
    if (itemsInBatch > 0)
            {
    mailingListTable.ExecuteBatch(batchOperation);
            }
    return RedirectToAction("Index");
        }

  如果需要刪除大量訂閱者，則此程式碼會以批次形式刪除記錄。刪除一個資料列的交易成本，與以批次形式刪除 100 個資料列的交易成本相同。可在一個批次中執行的作業數目上限是 100。 

  雖然迴圈處理 `MailingList` 資料列和 `Subscriber` 資料列，但是會將它們都讀入 `MailingList` 實體類別，因為 `Delete` 作業只需要 `PartitionKey`、`RowKey` 和 `ETag` 欄位。

### 新增 MailingList MVC 檢視

1.  在 **[方案總管]** 中，於 MVC 專案的 *Views* 資料夾下建立新的資料夾，並將它命名為 *MailingList*。

2.  在新的 *Views\\MailingList* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

    ![Add existing item to Views folder](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png)

3.  瀏覽到已在其中下載範例應用程式的資料夾，並選取 *Views\\MailingList* 資料夾中的所有四個 .cshtml 檔案，然後按一下 **[新增]**。

4.  開啟 *Edit.cshtml* 檔案，並檢查程式碼。

         @model MvcWebRole.Models.MailingList
                 @{
        ViewBag.Title = "Edit Mailing List";
         }
        <h2>Edit Mailing List</h2>
        @using (Html.BeginForm()) {
        @Html.AntiForgeryToken()
        @Html.ValidationSummary(true)
        @Html.HiddenFor(model => model.ETag)
        <fieldset>
        <legend>MailingList</legend>
        <div class="editor-label">
        @Html.LabelFor(model => model.ListName)
        </div>
        <div class="editor-field">
        @Html.DisplayFor(model => model.ListName)
        </div>
        <div class="editor-label">
        @Html.LabelFor(model => model.Description)
        </div>
        <div class="editor-field">
        @Html.EditorFor(model => model.Description)
        @Html.ValidationMessageFor(model => model.Description)
        </div>
        <div class="editor-label">
        @Html.LabelFor(model => model.FromEmailAddress)
        </div>
        <div class="editor-field">
        @Html.EditorFor(model => model.FromEmailAddress)
        @Html.ValidationMessageFor(model => model.FromEmailAddress)
        </div>
        <p>
        <input type="submit" value="Save" />
        </p>
        </fieldset>
         }
        <div>
        @Html.ActionLink("Back to List", "Index")
        </div>        
        @section Scripts {
        @Scripts.Render("~/bundles/jqueryval")
         }

    此程式碼通常適用於 MVC 檢視。請注意隱藏欄位，而併入隱藏欄位的目的是要保留用於處理並行衝突的 `ETag` 值。也請注意 `ListName` 欄位具有 `DisplayFor` 協助程式，而非 `EditorFor` 協助程式。我們未讓 **[編輯]** 頁面變更清單名稱，因為這樣在控制器中需要複雜的程式碼：`HttpPost Edit` 方法需要刪除現有郵寄清單資料列和所有相關聯的訂閱者資料列，以及使用新的索引鍵值重新插入它們。在實際執行應用程式中，您可能會決定複雜一點是值得的。如您稍後所見，`Subscriber` 控制器確實允許清單名稱變更，因為一次只會影響一個資料列。

    *Create.cshtml* 和 *Delete.cshtml* 程式碼與 *Edit.cshtml* 類似。

5.  開啟 *Index.cshtml*，並檢查程式碼。

         @model IEnumerable<MvcWebRole.Models.MailingList>
         @{
        ViewBag.Title = "Mailing Lists";
         }
        <h2>Mailing Lists</h2>
        <p>
        @Html.ActionLink("Create New", "Create")
        </p>
        <table>
        <tr>
        <th>
        @Html.DisplayNameFor(model => model.ListName)
        </th>
        <th>
        @Html.DisplayNameFor(model => model.Description)
        </th>
        <th>
        @Html.DisplayNameFor(model => model.FromEmailAddress)
        </th>
        <th></th>
        </tr>
        @foreach (var item in Model) {
        <tr>
        <td>
        @Html.DisplayFor(modelItem => item.ListName)
        </td>
        <td>
        @Html.DisplayFor(modelItem => item.Description)
        </td>
        <td>
        @Html.DisplayFor(modelItem => item.FromEmailAddress)
        </td>
        <td>
        @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
        @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>
        </tr>
         }
        </table>

    此程式碼通常也適用於 MVC 檢視。**[編輯]** 和 **[刪除]** 超連結指定分割索引鍵和資料列索引鍵查詢字串參數，以識別特定資料列。因為資料列索引鍵一律為 "MailingList"，所以 `MailingList` 實體實際上只需要分割索引鍵，但是會保留兩者，讓 MVC 檢視程式碼在所有控制器和檢視中都保持一致。

### 將 MailingList 設為預設控制器

1.  開啟 *App\_Start* 資料夾中的 *Route.config.cs*。

2.  在指定預設值的行中，將預設控制器從 "Home" 變更為 "MailingList"。

          routes.MapRoute(
        name:"Default",
        url:"{controller}/{action}/{id}",
        defaults:new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

設定儲存體設定 Web 角色使用測試 Azure 儲存體帳戶
------------------------------------------------

您將輸入在本機執行專案時使用之測試儲存體帳戶的設定。若要新增設定，則必須針對雲端和本機新增，但是您稍後可以變更雲端值。您稍後將針對背景工作角色 A 新增相同的設定。

(如果您想要在 Azure 網站中執行 Web UI，而非 Azure 雲端服務，請參閱本教學課程稍後的[替代架構](#alternativearchitecture)小節，了解這些指示的變更。)

1.  在 **[方案總管]** 中，於 **AzureEmailService** 雲端專案之 **[角色]** 的 **MvcWebRole** 上按一下滑鼠右鍵，然後選擇 **[屬性]**。

    ![Web role properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png)

2.  確定已選取 **[服務組態]** 下拉式清單中的 **[所有組態]**。

3.  選取 **[設定]** 索引標籤，然後按一下 **[加入設定]**。

4.  在 **[名稱]** 欄位中，輸入 "StorageConnectionString"。

5.  選取 **[類型]** 下拉式清單中的 **[連接字串]**。

6.  按一下該行右邊的省略符號 (**...**) 按鈕，以開啟 **[儲存體帳戶連接字串]** 對話方塊。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png)

7.  在 **[Create Storage Connection String]** 對話方塊中，按一下 **[Your subscription]** 選項按鈕，然後按一下 **[Download Publish Settings]** 連結。

    > [WACOM.NOTE] 如果使用最新的 SDK，則不需要下載任何項目；您可以從下拉式清單的可用儲存體帳戶中進行選擇。

    **注意：**如果您已在教學課程 2 設定儲存體設定，而且將在相同的電腦上執行本教學課程，則不需要再次下載設定，只需要按一下 **[Your subscription]**，然後選擇正確的 **[訂閱]** 和 **[帳戶名稱]**。

    ![Right Click Properties](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png)

    當您按一下 **[Download Publish Settings]** 連結時，Visual Studio 會使用 Azure 管理入口網站下載發行設定頁面的 URL 來啟動您預設瀏覽器的新執行個體。如果您未登入入口網站，則系統會提示您登入。登入之後，瀏覽器會提示您儲存發佈設定。請記下設定的儲存位置。

    ![publish settings](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png)

8.  在 **[Create Storage Connection String]** 對話方塊中，按一下 **[匯入]**，然後瀏覽到前一個步驟中儲存的發佈設定檔案。

9.  選取想要使用的訂閱和儲存體帳戶，然後按一下 **[確定]**。

    ![select storage account](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png)

10. 依照用於 `StorageConnectionString` 連接字串的相同程序，設定 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 連接字串。

    您不需要重新下載發行設定檔案。當您按一下 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 連接字串的省略符號時，會發現 **[Create Storage Connection String]** 對話方塊記得您的訂閱資訊。當您按一下 **[Your subscription]** 選項按鈕時，只需要選取先前選取的相同 **[訂閱]** 和 **[帳戶名稱]**，然後按一下 **[確定]**。

11. 依照用於 MvcWebRole 角色的兩個連接字串的相同程序，設定 WorkerRoleA 角色的連接字串。

當您使用 **[Add Settings]** 按鈕新增設定時，即會將新設定新增至 *ServiceDefinition.csdf* 檔案以及兩個 *.cscfg* 組態檔中的 XML。Visual Studio 會在 *ServiceDefinition.csdf* 檔案中新增下列 XML。

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

在每個 *.cscfg* 組態檔中新增下列 XML。

      <Setting name="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;
      AccountName=azuremailstorage;
      AccountKey=[your account key]" />

您可以手動在 *ServiceDefinition.csdf* 檔案和兩個 *.cscfg* 組態檔中新增設定，但是使用屬性編輯器具有下列連接字串優點：

-   您只需要在一個位置新增設定，就可以將正確設定 XML 新增至全部三個檔案。
-   針對這三個設定檔案產生正確的 XML。*ServiceDefinition.csdf* 檔案定義每個 *.cscfg* 組態檔中必須要有的設定。如果 *ServiceDefinition.csdf* 檔案和兩個 *.cscfg* 組態檔設定不一致，則會從 Visual Studio 收到下列錯誤訊息：「目前服務模型不同步。請確定服務組態和定義檔案有效。」

    ![Invalid service configuration and definition files error](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png)

如果您收到此錯誤，則除非您解決不一致問題，否則屬性編輯器無法運作。

### 測試應用程式

1.  按 CTRL+F5，以執行專案。

    ![Empty MailingList Index page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png)

2.  使用 **Create** 函數新增一些郵寄清單，並嘗試 **Edit** 和 **Delete** 函數確定其運作。

    ![MailingList Index page with rows](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png)

訂閱者建立與測試訂閱者控制器和檢視
----------------------------------

系統管理員使用 **[訂閱者]** Web UI 新增郵寄清單的訂閱者，以及編輯、顯示和刪除現有訂閱者。

### 將 Subscriber 實體類別新增至 Models 資料夾

`Subscriber` 實體類別用於 `MailingList` 資料表中含有清單訂閱者相關資訊的資料列。這些資料列包含該人員電子郵件地址以及是否驗證地址這類資訊。

1.  在 **[方案總管]** 中，於 MVC 專案的 *Models* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 *Models* 資料夾中選取 *Subscriber.cs* 檔案，然後按一下 **[新增]**。

3.  開啟 *Subscriber.cs*，並檢查程式碼。

             public class Subscriber :TableEntity
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
            
        public bool
             Verified { get; set; }
             }

    與 `MailingList` 實體類別類似，`Subscriber` 實體類別是用來讀取和寫入 `mailinglist` 資料表中的資料列。`Subscriber` 資料列使用電子郵件地址 (而非常數 "mailinglist") 做為資料列索引鍵。(如需資料表結構的說明，請參閱[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。)因此，定義的 `EmailAddress` 屬性使用 `RowKey` 屬性做為其支援欄位，`ListName` 同樣地使用 `PartitionKey` 做為其支援欄位。如稍早的說明，這可讓您在屬性 (Property) 上放置格式和驗證 DataAnnotations 屬性 (Attribute)。

    建立 `Subscriber` 實體時，會產生 `SubscriberGUID` 值。它用於訂閱和取消訂閱連結，協助確保只有授權人員才能訂閱或取消訂閱電子郵件地址。一開始建立新訂閱者的資料列時，`Verified` 值為 `false`。只有在新的訂閱者按一下歡迎使用電子郵件中的 **[確認]** 超連結之後，`Verified` 值才會變更為 `true`。如果將郵件傳送至清單，而訂閱者的 `Verified` = `false`，則不會將電子郵件傳送給該訂閱者。

    `Subscriber` 實體中的 `Verified` 屬性定義為可為 Null。當您指定查詢應該傳回 `Subscriber` 實體時，有些擷取的資料列可能沒有 `Verified` 屬性。因此，如果查詢傳回沒有 *Verified* 屬性的資料表資料列，則 `Subscriber` 實體會將其 `Verified` 屬性定義為可為 Null，以更精確地反映資料列的實際內容。您可能會習慣使用 SQL Server 資料表，其中，資料表的每個資料列都有相同的結構描述。在 Azure 儲存體資料表中，每個資料列都只是屬性的集合，而且每個資料列都可以有一組不同的屬性。例如，在 Azure Email Service 範例應用程式中，資料列索引鍵為 "MailingList" 的資料列沒有 `Verified` 屬性。如果查詢傳回沒有 `Verified` 屬性的資料表資料列，則具現化 `Subscriber` 實體類別時，實體物件中的 `Verified` 屬性會是 Null。如果屬性不可為 Null，則 `Verified` = `false` 的資料列以及根本沒有 `Verified` 屬性的資料列都會具有相同值 `false`。因此，使用 Azure 資料表的最佳作法是將實體類別的每個屬性設為可為 Null，以精確地讀取使用不同實體類別或不同版本的目前實體類別所建立的資料列。

### 新增 Subscriber MVC 控制器

1.  在 **[方案總管]** 中，於 MVC 專案的 *Controllers* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 *Controllers* 資料夾中選取 *SubscriberController.cs* 檔案，然後按一下 **[新增]**。(請確定您是取得 *Subscriber.cs*，而非 *Subscribe.cs*；您稍後會新增 *Subscribe.cs*。)

3.  開啟 *SubscriberController.cs*，並檢查程式碼。

    此控制器中的大部分程式碼類似您在 `MailingList` 控制器中看到的程式碼。甚至資料表名稱都會相同，因為訂閱者資訊保留在 `MailingList` 資料表中。在 `FindRow` 方法後面，您會看到 `GetListNames` 方法。此方法取得 **[建立]** 和 **[編輯]** 頁面上下拉式清單的資料，您可以從中選取要訂閱電子郵件地址的郵寄清單。

         private List<MailingList> GetListNames()
         {
        var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
        var lists = mailingListTable.ExecuteQuery(query).ToList();
        return lists;
         }

    這是您在 `MailingList` 控制器中看到的相同查詢。針對下拉式清單，您想要具有郵寄清單相關資訊的資料列，因此您只選取 RowKey = "mailinglist" 的資料列。

    針對擷取 **[索引]** 頁面之資料的方法，您想要具有訂閱者資訊的資料列，因此您選取沒有 RowKey = "MailingList" 的所有資料列。

         public ActionResult Index()
         {
        var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
        var subscribers = mailingListTable.ExecuteQuery(query).ToList();
        return View(subscribers);
         }

    請注意，此查詢指定將資料讀入 `Subscriber` 物件 (指定 `<Subscriber>`)，但是會從 `mailinglist` 資料表中讀取資料。

    **注意：**訂閱者數目可能會成長到太大而無法在單一查詢中使用此方式來處理。在教學課程的未來版本中，我們希望實作分頁功能，以及顯示如何處理接續權杖。當您執行傳回 1,000 個以上資料列的查詢時，需要處理接續權杖：Azure 傳回 1,000 個資料列，以及一個接續權杖，用來執行從前一個查詢離開處開始的另一個查詢。(Azure 儲存體總管不會處理接續權杖；因此，其查詢將不會傳回 1,000 個以上的資料列。)如需大型結果集和接續權杖的詳細資訊，請參閱[如何最佳利用 Azure 資料表](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (英文) 和 [Azure 資料表：極度預期接續權杖](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously) (英文)。

    在 `HttpGet Create` 方法中，您設定下拉式清單的資料，並先在 `HttpPost` 方法中設定預設值，然後再儲存新的實體。

         public ActionResult Create()
         {
        var lists = GetListNames();
        ViewBag.ListName = new SelectList(lists, "ListName", "Description");
        var model = new Subscriber() { Verified = false };
        return View(model);
         }

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Subscriber subscriber)
         {
        if (ModelState.IsValid)
             {
        subscriber.SubscriberGUID = Guid.NewGuid().ToString();
        if (subscriber.Verified.HasValue == false)
                 {
        subscriber.Verified = false;
                 }

        var insertOperation = TableOperation.Insert(subscriber);
        mailingListTable.Execute(insertOperation);
        return RedirectToAction("Index");
             }

        var lists = GetListNames();
        ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

        return View(subscriber);
         }

    `HttpPost Edit` 頁面比在 `MailingList` 控制器中看到的內容更為複雜，因為 `Subscriber` 頁面可讓您變更清單名稱或電子郵件地址 (兩者都是索引鍵欄位)。如果使用者變更其中一個欄位，則您需要刪除現有記錄並且新增記錄，而非更新現有記錄。下列程式碼顯示編輯方法的一部分，而此編輯方法處理重要與不重要變更的不同程序：

             if (ModelState.IsValid)
             {
        try
                 {
        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                     {
        //Keys didn't change -- Update the row
        var replaceOperation = TableOperation.Replace(editedSubscriber);
        mailingListTable.Execute(replaceOperation);
                     }
        else
                     {
        // Keys changed, delete the old record and insert the new one.
        if (editedSubscriber.PartitionKey != partitionKey)
                         {
        // PartitionKey changed, can't do delete/insert in a batch.
        var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
        mailingListTable.Execute(deleteOperation);
        var insertOperation = TableOperation.Insert(editedSubscriber);
        mailingListTable.Execute(insertOperation);
                         }
        else
                         {
        // RowKey changed, do delete/insert in a batch.
        var batchOperation = new TableBatchOperation();
        batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
        batchOperation.Insert(editedSubscriber);
        mailingListTable.ExecuteBatch(batchOperation);
                         }
                     }
        return RedirectToAction("Index");

    MVC 模型繫結器傳遞給 `Edit` 方法的參數包括原始清單名稱和電子郵件地址值 (在 `partitionKey` 和 `rowKey` 參數中) 以及使用者所輸入的值 (在 `listName` 和 `emailAddress` 參數中)：

         public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

    傳遞給 `UpdateModel` 方法的參數會排除 `PartitionKey` 和 `RowKey` 屬性，不進行模型繫結：

             var excludeProperties = new string[] { "PartitionKey", "RowKey" };

    原因是 `ListName` 和 `EmailAddress` 屬性使用 `PartitionKey` 和 `RowKey` 做為其支援屬性，而且使用者可能已變更其中一個值。模型繫結器透過設定 `ListName` 屬性來更新模型時，會自動更新 `PartitionKey` 屬性。如果模型繫結器要在更新 `ListName` 屬性之後使用 `PartitionKey` 屬性的原始值來更新該屬性，則會覆寫 `ListName` 屬性所設定的新值。`EmailAddress` 屬性會使用相同的方式自動更新 `RowKey` 屬性。

    更新 `editedSubscriber` 模型物件之後，程式碼接著會判斷變更分割索引鍵還是資料列索引鍵。如果任一個索引鍵值已變更，則必須刪除現有訂閱者資料列，並插入新的訂閱者資料列。如果只變更資料列索引鍵，則可以使用不可部分完成的批次交易來完成刪除和插入。

    請注意，此程式碼會建立傳入 `Delete` 作業的新實體：

             // RowKey changed, do delete/insert in a batch.
        var batchOperation = new TableBatchOperation();
        batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
        batchOperation.Insert(editedSubscriber);
        mailingListTable.ExecuteBatch(batchOperation);

    您使用批次傳入作業的實體必須是不同的實體。例如，您不可以建立 `Subscriber` 實體，並將它傳入 `Delete` 作業，然後變更相同 `Subscriber` 實體中的值，並將它傳入 `Insert` 作業。如果您這麼做，屬性變更之後的實體狀態將作用於 Delete 和 Insert 作業。

    **注意：**批次進行的作業必須都位於相同的資料分割中。因為清單名稱的變更會變更分割索引鍵，所以無法在某個交易中完成。

### 新增 Subscriber MVC 檢視

1.  在 **[方案總管]** 中，於 MVC 專案的 *Views* 資料夾下建立新的資料夾，並將它命名為 *Subscriber*。

2.  在新的 *Views\\Subscriber* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

3.  瀏覽到已在其中下載範例應用程式的資料夾，並選取 *Views\\Subscriber* 資料夾中的所有五個 .cshtml 檔案，然後按一下 **[新增]**。

4.  開啟 *Edit.cshtml* 檔案，並檢查程式碼。

         @model MvcWebRole.Models.Subscriber
            
         @{
        ViewBag.Title = "Edit Subscriber";
         }
            
        <h2>Edit Subscriber</h2>
            
        @using (Html.BeginForm()) {
        @Html.AntiForgeryToken()
        @Html.ValidationSummary(true)
        @Html.HiddenFor(model => model.SubscriberGUID)
        @Html.HiddenFor(model => model.ETag)
        <fieldset>
        <legend>Subscriber</legend>
        <div class="display-label">
        @Html.DisplayNameFor(model => model.ListName)
        </div>
        <div class="editor-field">
        @Html.DropDownList("ListName", String.Empty)
        @Html.ValidationMessageFor(model => model.ListName)
        </div>
        <div class="editor-label">
        @Html.LabelFor(model => model.EmailAddress)
        </div>
        <div class="editor-field">
        @Html.EditorFor(model => model.EmailAddress)
        @Html.ValidationMessageFor(model => model.EmailAddress)
        </div>
        <div class="editor-label">
        @Html.LabelFor(model => model.Verified)
        </div>
        <div class="display-field">
        @Html.EditorFor(model => model.Verified)
        </div>
        <p>
        <input type="submit" value="Save" />
        </p>
        </fieldset>
         }
            
        <div>
        @Html.ActionLink("Back to List", "Index")
        </div>
            
        @section Scripts {
        @Scripts.Render("~/bundles/jqueryval")
         }

    此程式碼類似您稍早看到之 `MailingList` **[編輯]** 檢視的程式碼。`SubscriberGUID` 值不會顯示，所以未在表單欄位中自動提供 `HttpPost` 控制器方法的值。因此，會包括隱藏欄位，以保留此值。

    其他檢視所含的程式碼類似您看到之 `MailingList` 控制器的程式碼。

### 測試應用程式

1.  按 CTRL+F5 執行專案，然後按一下 **[訂閱者]**。

    ![Empty Subscriber Index page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png)

2.  使用 **Create** 函數新增一些郵寄清單，並嘗試 **Edit** 和 **Delete** 函數確定其運作。

    ![Subscribers Index page with rows](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png)

郵件建立與測試郵件控制器和檢視
------------------------------

系統管理員使用 **[郵件]** Web UI 建立、編輯和顯示排程傳送至郵寄清單之郵件的相關資訊。

### 將 Message 實體類別新增至 Models 資料夾

`Message` 實體類別用於 `Message` 資料表中含有排程傳送至清單之郵件相關資訊的資料列。這些資料列包括下列資訊：主旨行、將郵件傳送至其中的清單，以及傳送它的排程日期。

1.  在 **[方案總管]** 中，於 MVC 專案的 *Models* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 Models 資料夾中選取 *Message.cs* 檔案，然後按一下 **[新增]**。

3.  開啟 *Message.cs*，並檢查程式碼。

         public class Message :TableEntity
         {
        private DateTime
         _scheduledDate;
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
        public DateTime
         ScheduledDate 
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

    `Message` 類別會定義預設建構函式，以將 `MessageRef` 屬性設定為郵件的唯一值。因為此值是資料列索引鍵的一部分，所以 `MessageRef` 屬性的 Setter 也會自動設定 `RowKey` 屬性。`MessageRef` 屬性 Setter 會串連 "message" 常值與 `MessageRef` 值，並將該值放入 `RowKey` 屬性中。

    `MessageRef` 值的建立方式是從 `DateTime.Now` 取得 `Ticks` 值。這確保在 Web UI 中顯示郵件時，預設會針對指定排程日期 (`ScheduledDate` 是分割索引鍵) 以建立它們的順序來顯示它們。您可以使用 GUID 讓郵件資料列成為唯一的，但是預設擷取順序會是隨機的。

    預設建構函式也會將新 `message` 資料列的預設狀態設定為「擱置」。

    如需 `Message` 資料表結構的詳細資訊，請參閱[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。

### 新增 Message MVC 控制器

1.  在 **[方案總管]** 中，於 MVC 專案的 Controllers 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 *Controllers* 資料夾中選取 *MessageController.cs* 檔案，然後按一下 **[新增]**。

3.  開啟 *MessageController.cs*，並檢查程式碼。

    此控制器中的大部分程式碼類似您在 `Subscriber` 控制器中看到的程式碼。這裡的新內容是使用 Blob 的程式碼。針對每封郵件，會以 .htm 和 .txt 檔案形式上傳電子郵件的 HTML 和純文字內容，並將其儲存至 Blob 中。

    Blob 儲存在 Blob 容器中。Azure Email Service 應用程式會在名稱為 "azuremailblobcontainer" 的單一 Blob 容器中儲存其所有 Blob，而且控制器建構函式中的程式碼會參考此 Blob 容器：

         public class MessageController :Controller
         {
        private TableServiceContext serviceContext;
        private static CloudBlobContainer blobContainer;

        public MessageController()
             {
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
        // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
        //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

        // Get context object for working with tables and a reference to the blob container.
        var tableClient = storageAccount.CreateCloudTableClient();
        serviceContext = tableClient.GetTableServiceContext();
        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
             }

    針對使用者選擇上傳的每個檔案，MVC 檢視提供含有檔案相關資訊的 `HttpPostedFile` 物件。使用者建立新的郵件時，會使用 `HttpPostedFile` 物件，將檔案儲存至 Blob。使用者編輯郵件時，可以選擇上傳取代檔案，或將 Blob 保持不變。

    控制器包括 `HttpPost Create` 和 `HttpPost Edit` 方法呼叫以儲存 Blob 的方法：

         private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
         {
        // Retrieve reference to a blob. 
        CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
        // Create the blob or overwrite the existing blob by uploading a local file.
        using (var fileStream = httpPostedFile.InputStream)
             {
        blob.UploadFromStream(fileStream);
             }
         }

    `HttpPost Create` 方法會儲存兩個 Blob，然後新增 `Message` 資料表資料列。Blob 的命名方式是串連副檔名為 ".htm" 或 ".txt" 的 `MessageRef` 值。

         [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
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
        SaveBlob(message.MessageRef + ".htm", file);
        SaveBlob(message.MessageRef + ".txt", txtFile);

        var insertOperation = TableOperation.Insert(message);
        messageTable.Execute(insertOperation);

        return RedirectToAction("Index");
             }

        var lists = GetListNames();
        ViewBag.ListName = new SelectList(lists, "ListName", "Description");
        return View(message);
         }

    `HttpGet Edit` 方法會驗證擷取的郵件處於 `Pending` 狀態，因此，在背景工作角色 B 開始處理郵件之後，使用者就無法再變更郵件。類似的程式碼位於 `HttpPost Edit` 方法以及 `Delete` 和 `DeleteConfirmed` 方法中。

         public ActionResult Edit(string partitionKey, string rowKey)
         {
        var message = FindRow(partitionKey, rowKey);
        if (message.Status != "Pending")
             {
        throw new Exception("Message can't be edited because it isn't in Pending status.");
             }

        var lists = GetListNames();
        ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
        return View(message);
         }

    在 `HttpPost Edit` 方法中，只有在使用者選擇上傳新的檔案時，此程式碼才會儲存新的 Blob。下列程式碼會省略方法的並行處理部分，這與您先前看到之 `MailingList` 控制器的程式碼相同。

         [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
        DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
         {
        if (ModelState.IsValid)
             {
        var excludePropLst = new List<string>();
        excludePropLst.Add("PartitionKey");
        excludePropLst.Add("RowKey");

        if (httpFile == null)
                 {
        // They didn't enter a path or navigate to a file, so don't update the file.
        excludePropLst.Add("HtmlPath");
                 }
        else
                 {
        // They DID enter a path or navigate to a file, assume it's changed.
        SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                 }

        if (txtFile == null)
                 {
        excludePropLst.Add("TextPath");
                 }
        else
                 {
        SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                 }

        string[] excludeProperties = excludePropLst.ToArray();

        try
                 {
        UpdateModel(editedMsg, string.Empty, null, excludeProperties);
        if (editedMsg.PartitionKey == partitionKey)
                     {
        // Keys didn't change -- update the row.
        var replaceOperation = TableOperation.Replace(editedMsg);
        messageTable.Execute(replaceOperation);
                     }
        else
                     {
        // Partition key changed -- delete and insert the row.
        // (Partition key has scheduled date which may be changed;
        // row key has MessageRef which does not change.)
        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        messageTable.Execute(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        messageTable.Execute(insertOperation);
                     }
        return RedirectToAction("Index");
                 }

    如果排程日期變更，則會變更分割索引鍵，而且必須刪除和插入資料列。這無法在交易中完成，因為會影響多個資料分割。

    `HttpPost Delete` 方法會在刪除資料表中的資料列時刪除 Blob：

         [HttpPost, ActionName("Delete")]
        public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
         {
        // Get the row again to make sure it's still in Pending status.
        var message = FindRow(partitionKey, rowKey);
        if (message.Status != "Pending")
             {
        throw new Exception("Message can't be deleted because it isn't in Pending status.");
             }

        DeleteBlob(message.MessageRef + ".htm");
        DeleteBlob(message.MessageRef + ".txt");
        var deleteOperation = TableOperation.Delete(message);
        messageTable.Execute(deleteOperation);
        return RedirectToAction("Index");
         }

        private void DeleteBlob(string blobName)
         {
        var blob = blobContainer.GetBlockBlobReference(blobName);
        blob.Delete();
         }

### 新增 Message MVC 檢視

1.  在 **[方案總管]** 中，於 MVC 專案的 *Views* 資料夾下建立新的資料夾，並將它命名為 `Message`。

2.  在新的 *Views\\Message* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

3.  瀏覽到已在其中下載範例應用程式的資料夾，並選取 *Views\\Message* 資料夾中的所有五個 .cshtml 檔案，然後按一下 **[新增]**。

4.  開啟 *Edit.cshtml* 檔案，並檢查程式碼。

         @model MvcWebRole.Models.Message
            
         @{
        ViewBag.Title = "Edit Message";
         }
            
        <h2>Edit Message</h2>
            
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
        <div class="editor-label">
        @Html.LabelFor(model => model.ListName, "MailingList")
        </div>
        <div class="editor-field">
        @Html.DropDownList("ListName", String.Empty)
        @Html.ValidationMessageFor(model => model.ListName)
        </div>
        <div class="editor-label">
        @Html.LabelFor(model => model.SubjectLine)
        </div>
        <div class="editor-field">
        @Html.EditorFor(model => model.SubjectLine)
        @Html.ValidationMessageFor(model => model.SubjectLine)
        </div>
        <div class="editor-label">
        HTML Path:Leave blank to keep current HTML File.
        </div>
        <div class="editor-field">
        <input type="file" name="file" />
        </div>
        <div class="editor-label">
        Text Path:Leave blank to keep current Text File.
        </div>
        <div class="editor-field">
        <input type="file" name="TxtFile" />
        </div>
        <div class="editor-label">
        @Html.LabelFor(model => model.ScheduledDate)
        </div>
        <div class="editor-field">
        @Html.EditorFor(model => model.ScheduledDate)
        @Html.ValidationMessageFor(model => model.ScheduledDate)
        </div>
        <div class="display-label">
        @Html.DisplayNameFor(model => model.Status)
        </div>
        <div class="editor-field">
        @Html.EditorFor(model => model.Status)
        </div>
        <p>
        <input type="submit" value="Save" />
        </p>
        </fieldset>
         }
            
        <div>
        @Html.ActionLink("Back to List", "Index")
        </div>
            
        @section Scripts {
        @Scripts.Render("~/bundles/jqueryval")
         }

    `HttpPost Edit` 方法需要分割索引鍵和資料列索引鍵，因此，此程式碼在隱藏欄位中提供這些項目。`Subscriber` 控制器中不需要隱藏欄位，因為 (a) `Subscriber` 模型中的 `ListName` 和 `EmailAddress` 屬性會更新 `PartitionKey` 和 `RowKey` 屬性，而且 (b) 在 [編輯] 檢視中，`EditorFor` 協助程式有 `ListName` 和 `EmailAddress` 屬性。`Subscriber` 模型中的 MVC 模型繫結器更新 `ListName` 屬性時，會自動更新 `PartitionKey` 屬性，而且 MVC 模型繫結器更新 `Subscriber` 模型中的 `EmailAddress` 屬性時，會自動更新 `RowKey` 屬性。在 `Message` 模型中，對應至分割索引鍵和資料列索引鍵的欄位是不可編輯的欄位，因此無法使用這種方式進行設定。

    也包括 `MessageRef` 屬性的隱藏欄位。這是與分割索引鍵相同的值，包括它的目的是要在 `HttpPost Edit` 方法中讓程式碼更為清楚。包括 `MessageRef` 隱藏欄位，可讓 `HttpPost Edit` 方法中的程式碼在建構 Blob 的檔案名稱時，透過該名稱參照 `MessageRef` 值。

5.  開啟 *Index.cshtml* 檔案，並檢查程式碼。

         @model IEnumerable<MvcWebRole.Models.Message>
            
         @{
        ViewBag.Title = "Messages";
         }
            
        <h2>Messages</h2>
            
        <p>
        @Html.ActionLink("Create New", "Create")
        </p>
        <table>
        <tr>
        <th>
        @Html.DisplayNameFor(model => model.ListName)
        </th>
        <th>
        @Html.DisplayNameFor(model => model.SubjectLine)
        </th>
        <th>
        @Html.DisplayNameFor(model => model.ScheduledDate)
        </th>
        <th>
        @Html.DisplayNameFor(model => model.Status)
        </th>
        <th></th>
        </tr>
        @foreach (var item in Model)
             {
        <tr>
        <td>
        @Html.DisplayFor(modelItem => item.ListName)
        </td>
        <td>
        @Html.DisplayFor(modelItem => item.SubjectLine)
        </td>
        <td>
        @Html.DisplayFor(modelItem => item.ScheduledDate)
        </td>
        <td>
        @item.Status
        </td>
        <td>
        @if (item.Status == "Pending")
                         {
        @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
        @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
                         }
        @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
        </tr>
             }
            
        </table>

    與其他 **[索引]** 檢視的差異，是只會顯示處於 `Pending` 狀態之郵件的 **[編輯]** 和 **[刪除]** 連結：

         @if (item.Status == "Pending")
         {
        @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
        @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
         }

    這有助於在背景工作角色 A 已開始處理郵件之後，防止使用者變更郵件。

    其他檢視所含的程式碼類似 **[編輯]** 檢視，或您看到之其他控制器的其他檢視。

### 測試應用程式

1.  按 CTRL+F5 執行專案，然後按一下 **[郵件]**。

    ![Empty Message Index page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png)

2.  使用 **Create** 函數新增一些郵寄清單，並嘗試 **Edit** 和 **Delete** 函數確定其運作。

    ![Subscribers Index page with rows](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png)

取消訂閱建立與測試取消訂閱控制器和檢視
--------------------------------------

接下來，您將實作取消訂閱程序的 UI。

**注意：**本教學課程只會建置取消訂閱程序的控制器，而不是訂閱程序。如[第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)中所說明，除非實作服務方法的適當安全性，否則會略過訂閱程序的 UI 和服務方法。屆時，您可以使用 **[訂閱者]** 系統管理員頁面將電子郵件地址訂閱至清單。

### 將 Unsubscribe 檢視模型新增至 Models 資料夾

`UnsubscribeVM` 檢視模型是用來在 `Unsubscribe` 控制器與其檢視之間傳遞資料。

1.  在 **[方案總管]** 中，於 MVC 專案的 `Models` 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 Models 資料夾中選取 `UnsubscribeVM.cs` 檔案，然後按一下 **[新增]**。

3.  開啟 `UnsubscribeVM.cs`，並檢查程式碼。

         public class UnsubscribeVM
         {
        public string EmailAddress { get; set; }
        public string ListName { get; set; }
        public string ListDescription { get; set; }
        public string SubscriberGUID { get; set; }
        public bool
         Confirmed { get; set; }
         }

    取消訂閱連結包含 `SubscriberGUID`。該值是用來取得 `MailingList` 資料表中的電子郵件地址、清單名稱和清單說明。此檢視顯示電子郵件地址以及要從中取消訂閱的清單說明，並顯示使用者必須按一下才能完成取消訂閱程序的 **[確認]** 按鈕。

### 新增 Unsubscribe 控制器

1.  在 **[方案總管]** 中，於 MVC 專案的 `Controllers` 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

2.  瀏覽到已在其中下載範例應用程式的資料夾，並在 *Controllers* 資料夾中選取 *UnsubscribeController.cs* 檔案，然後按一下 **[新增]**。

3.  開啟 *UnsubscribeController.cs*，並檢查程式碼。

    此控制器的 `HttpGet Index` 方法顯示初始取消訂閱頁面，而 `HttpPost Index` 方法處理 **[確認]** 或 **[取消]** 按鈕。

    `HttpGet Index` 方法會在查詢字串中使用 GUID 和清單名稱，以取得訂閱者的 `MailingList` 資料表資料列。然後，它會將檢視所需的所有資訊放入檢視模型，並顯示 **[取消訂閱]** 頁面。它將 `Confirmed` 屬性設定為 Null，以告知檢視顯示 **[取消訂閱]** 頁面的初始版本。

          public ActionResult Index(string id, string listName)
          {
        if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
              {
        ViewBag.errorMessage = "Empty subscriber ID or list name.";
        return View("Error");
              }
        string filter = TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
        var query = new TableQuery<Subscriber>().Where(filter);
        var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
        if (subscriber == null)
              {
        ViewBag.Message = "You are already unsubscribed";
        return View("Message");
              }
        var unsubscribeVM = new UnsubscribeVM();
        unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
        unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
        unsubscribeVM.SubscriberGUID = id;
        unsubscribeVM.Confirmed = null;
        return View(unsubscribeVM);
          }

    注意：SubscriberGUID 不在分割索引鍵或資料列索引鍵中，因此此查詢的效能會隨著資料分割大小 (郵寄清單中的電子郵件地址數目) 的增加而降低。如需讓此查詢更具延展性之替代方式的詳細資訊，請參閱[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。

    `HttpPost Index` 方法會再次使用 GUID 和清單名稱來取得訂閱者資訊，並填入檢視模型屬性。然後，如果已按一下 **[確認]** 按鈕，則會刪除 `MailingList` 資料表中的訂閱者資料列。如果已按 **[確認]** 按鈕，則也會將 `Confirm` 屬性設定為 `true`，否則會將 `Confirm` 屬性設定為 `false`。`Confirm` 屬性的值會告知此檢視顯示 **[取消訂閱]** 頁面的已確認或已取消版本。

         [HttpPost] 
        [ValidateAntiForgeryToken]
        public ActionResult Index(string subscriberGUID, string listName, string action)
         {
        string filter = TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
        var query = new TableQuery<Subscriber>().Where(filter);
        var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

        var unsubscribeVM = new UnsubscribeVM();
        unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
        unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
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

1.  在 **[方案總管]** 中，於 MVC 專案的 *Views* 資料夾下建立新的資料夾，並將它命名為 *Unsubscribe*。

2.  在新的 *Views\\Unsubscribe* 資料夾上按一下滑鼠右鍵，然後選擇 **[加入現有項目]**。

3.  瀏覽到已在其中下載範例應用程式的資料夾，並在 *Views\\Unsubscribe* 資料夾中選取 *Index.cshtml* 檔案，然後按一下 **[新增]**。

4.  開啟 *Index.cshtml* 檔案，並檢查程式碼。

         @model MvcWebRole.Models.UnsubscribeVM
            
         @{
        ViewBag.Title = "Unsubscribe";
        Layout = null;
         }
            
        <h2>Email List Subscription Service</h2>
            
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
        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:@Html.DisplayFor(model => model.ListDescription)
         
        </p>
        <br />
        <p>
        <input type="submit" value="Confirm" name="action"/> 
                            
        <input type="submit" value="Cancel" name="action"/>
        </p>
                 }
        @if (Model.Confirmed == false) {
        <p>
        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from:@Html.DisplayFor(model => model.ListDescription).
        </p>
                 }
        @if (Model.Confirmed == true) {
        <p>
        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:@Html.DisplayFor(model => model.ListDescription).
        </p>
                 }
        </fieldset>
         }
            
        @section Scripts {
        @Scripts.Render("~/bundles/jqueryval")
         }

    `Layout = null` 行指定不應該使用 \_Layout.cshtml 檔案來顯示此頁面。**[取消訂閱]** 頁面顯示非常簡單的 UI，其未含用於系統管理員頁面的頁首和頁尾。

    在頁面的本文中，`Confirmed` 屬性決定將顯示在頁面上的內容：**[確認]** 和 **[取消]** 按鈕 (如果此屬性為 Null)、取消訂閱已確認郵件 (如果此屬性為 true)、取消訂閱已取消郵件 (如果此屬性為 false)。

### 測試應用程式

1.  按 CTRL+F5 執行專案，然後按一下 **[訂閱者]**。

2.  按一下 **[建立]**，然後建立您在先前測試時所建立之任何郵寄清單的新訂閱者。

    在 **[訂閱者]** **[索引]** 頁面上，保持瀏覽器視窗的開啟狀態。

3.  開啟 [Azure 儲存體總管]，然後選取測試儲存體帳戶。

4.  按一下 **[儲存體類型]** 下的 **[資料表]**，並選取 **[MailingList]** 資料表，然後按一下 **[查詢]**。

5.  按兩下您已新增的訂閱者資料列。

    ![Azure Storage Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png)

6.  在 **[編輯實體]** 對話方塊中，選取和複製 `SubscriberGUID` 值。

    ![Azure Storage Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png)

7.  切換回瀏覽器視窗。在瀏覽器的網址列中，將 URL 中的 "Subscriber" 變更為 "unsubscribe?ID=[guidvalue]&listName=[listname]"，其中 [guidvalue] 是您從 Azure 儲存體總管複製的 GUID，而 [listname] 是郵寄清單的名稱。例如：

         http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    顯示要求確認之 **[取消訂閱]** 頁面的版本：

    ![Unsubscribe page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png)

8.  按一下 **[確認]**，您會看到已取消訂閱電子郵件地址的確認。

    ![Unsubscribe confirmed page](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png)

9.  回到 **[訂閱者]** **[索引]** 頁面，以驗證訂閱者資料列已不存在。

替代架構(選用) 建置替代架構
---------------------------

如果您想要建置替代架構，則指示的下列變更適用 -- 亦即，使用 Azure 網站 (而非 Azure 雲端服務 Web 角色) 執行 Web UI。

-   當您建立解決方案時，請先建立 **[ASP.NET MVC 4 Web 應用程式]** 專案，然後使用背景工作角色將 **[Azure 雲端服務]** 專案新增至解決方案。

-   將 Azure 儲存體連接字串儲存至 Web.config 檔案中，而非雲端服務設定檔案中。(這僅適用於 Azure 網站。如果您嘗試針對 Azure 雲端服務 Web 角色中的儲存體連接字串使用 Web.config 檔案，則會收到 HTTP 500 錯誤。)

將名稱為 `StorageConnectionString` 的新連接字串新增至 *Web.config* 檔案 (如下列範例所示)：

           <connectionStrings>
              <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
              <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
           </connectionStrings>

從 [Azure 管理入口網站](http://manage.windowsazure.com)取得連接字串的值：選取 **[儲存體]** 索引標籤和儲存體帳戶，然後按一下頁面底部的 **[管理金鑰]**。

-   只要在程式碼中看到 `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")`，就將它取代為 `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`。

後續步驟後續步驟
----------------

如[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)所說明，除非我們已實作共用密碼來保護 ASP.NET Web API 服務方法，否則不會在本教學課程中詳細顯示如何建置訂閱程序。不過，IP 限制也會保護服務方法，而且您可以從已下載的專案中複製下列檔案，來新增訂閱功能。

針對 ASP.NET Web API 服務方法：

-   Controllers\\SubscribeAPI.cs

針對訂閱者在按一下電子郵件中的 **[確認]** 連結時所取得的網頁，而電子郵件是由服務方法所產生：

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

在[下一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)中，您將設定和程式設計背景工作角色 A (排程電子郵件的背景工作角色)。

如需使用 Azure 儲存體資料表、佇列和 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)結尾。

[教學課程 4](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)

