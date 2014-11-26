<properties  linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use the Windows Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="tamram" />

# 如何使用 .NET 的佇列儲存體

本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。這些範例均以 C# 程式碼撰寫，並使用 Azure Storage Client for.NET。**所涵蓋的案例包括「插入**、**查看**、**取得**和**刪除**佇列訊息，以及**建立和刪除佇列**。如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

<h2>目錄</h2>


* [什麼是佇列儲存體](#what-is)
* [概念](#concepts)
* [建立 Azure 儲存體帳戶](#create-account)
* [設定 Azure 儲存體連接字串](#setup-connection-string)
* [作法：使用 .NET 以程式設計方式存取佇列](#access)
* [作法：建立佇列](#create-queue)
* [作法：將訊息插入佇列](#insert-message)
* [作法：查看下一個訊息](#peek-message)
* [作法：變更佇列訊息的內容](#change-contents)
* [作法：將下一個訊息清除佇列](#get-message)
* [作法：運用清除佇列訊息的其他選項](#advanced-get)
* [作法：取得佇列長度](#get-queue-length)
* [作法：刪除佇列](#delete-queue)
* [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">建立帳戶</span>建立 Azure 儲存體帳戶</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">設定連接字串</span>設定 Azure 儲存體連接字串</h2>


Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。您可以將儲存體連接字串置於設定檔中，而非硬式編碼至程式碼中：

* 使用 Azure 雲端服務時，建議您使用 Azure 服務組態系統 (`*.csdef` 和 `*.cscfg` 檔案) 來儲存連接字串。 * 使用 Azure 網站、Azure 虛擬機器，或建置要在 Azure 外部執行的 .NET 應用程式時，建議您使用 .NET 組態系統   (例如 `web.config` 或 `app.config` 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本指南稍後所示範。

### 在使用雲端服務時設定連接字串

服務組態機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務組態中設定連接字串：

1.  在 Visual Studio 的 **方案總管]，於 Azure 部署專案之 [角色** 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 **屬性**。
    
    ![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  按一下 **設定** 索引標籤，再按 **加入設定** 按鈕。
    
    ![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)
    
    設定清單中隨即顯示新的 **Setting1** 項目。

3.  在新 **Setting1** 項目的 **類型** 下拉式清單中，選擇 **連接字串**。
    
    ![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  按一下 **Setting1** 項目右側的 **...** 按鈕。**儲存體帳戶連接字串** 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Azure 儲存體)，還是雲端中的實際儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。如您想要在稍早於 Azure 上建立的儲存體帳戶中儲存佇列資料，請輸入在本教學課程的稍早步驟複製的 **主要存取金鑰** 值。
    
    ![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  將 **名稱** 項目從 **Setting1** 變為更易記的名稱，如 **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。
    
    ![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)

### 使用 .NET 設定來設定連接字串

如果您要撰寫的應用程式並非 Azure 雲端服務 (請參閱上節)，建議您使用 .NET 設定系統 (例如 `web.config` 或 `app.config`)。這類應用程式包括 Azure 網站或 Azure 虛擬機器，以及設計成在 Azure 外執行的應用程式。您可以使用如下所示的 `<a ppSettings>` 元素儲存連接字串：

	<configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 如需儲存體連接字串的詳細資訊，請閱讀[設定連接字串][1]。

 現在您已準備就緒，可以開始執行本指南的「作法」工作。

 <h2><a name="access"></a><span  class="short-header">以程式設計方式存取</span>作法：使用 .NET 以程式設計方式存取佇列</h2>


 <h3>取得組件</h3>


 您可以使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 **方案總管** 中以滑鼠右鍵按一下專案，然後選擇 **管理 NuGet 封裝**。在線上搜尋 "WindowsAzure.Storage"，再按一下 **安裝** 以安裝 Azure 儲存體封裝與相依性。

 `Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 [.NET Developer Center][2] 下載)。此組件會安裝至 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` 目錄中。

 <h3>命名空間宣告</h3>


 將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

 確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

 <h3>擷取連接字串</h3>


 您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 Windows Azure 專案範本且 (或) 具有 Microsoft.WindowsAzure.CloudConfigurationManager 參照，可以使用 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

 如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 <h3>ODataLib 相依性</h3>


 Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。這些 ODataLib 封裝具體來說是 [OData][3]、[Edm][4] 和 [Spatial][5]。

 <h2><a name="create-queue"></a><span  class="short-header">建立佇列</span>作法：建立佇列</h2>


 **CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。本指南中的所有程式碼使用儲存於 Azure 應用程式服務組態中的儲存體連接字串。還有其他方式可以建立 **CloudStorageAccount** 物件。請參閱 CloudStorageAccount][6] 文件以取得詳細資料。

    // 從連接字串擷取儲存體帳戶
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

 使用 **queueClient** 物件來取得想要使用佇列的參照。如果佇列不存在，您可以建立佇列。

    // 擷取佇列的參照
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 如果佇列不存在，則建立佇列
    queue.CreateIfNotExists();

 <h2><a name="insert-message"> </a><span  class="short-header">插入訊息</span>作法：將訊息插入佇列</h2>


 若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage**。接著，呼叫 **AddMessage** 方法。您可以從字串 (採用 UTF-8 格式) 或**位元組**陣列建立 **CloudQueueMessage**。以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼：

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 如果佇列不存在，則建立佇列。
    queue.CreateIfNotExists();
    
    // 建立訊息並將它新增至佇列。
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

 <h2><a name="peek-message"></a><span  class="short-header">查看下一個訊息</span>作法：查看下一個訊息</h2>


 透過呼叫 **PeekMessage** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

    // 從連接字串擷取儲存體帳戶
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 查看下一個訊息
    CloudQueueMessage peekedMessage = queue.PeekMessage();
    
    // 顯示訊息。
    Console.WriteLine(peekedMessage.AsString);

 <h2><a name="change-contents"></a><span  class="short-header">變更訊息內容</span>作法：變更佇列訊息的內容</h2>


 您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，如果訊息重試超過 *n* 次，您會將它刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 從佇列中取得訊息並更新訊息內容。
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
    TimeSpan.FromSeconds(0.0),  // 讓它立即可見。
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);

 <h2><a name="get-message"></a><span  class="short-header">將下一個訊息清除佇列</span>作法：將下一個訊息清除佇列</h2>


 您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。呼叫 **GetMessage** 時，您會取得佇列中的下一個訊息。從 **GetMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。依預設，此訊息會維持 30 秒的不可見狀態。若要完成從佇列中移除訊息，您還必須呼叫 **DeleteMessage**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **DeleteMessage**。

    // 從連接字串擷取儲存體帳戶
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 取得下一個訊息
    CloudQueueMessage retrievedMessage = queue.GetMessage();
    
    //在 30 秒內處理訊息，然後將訊息刪除
    queue.DeleteMessage(retrievedMessage);

 <h2><a name="advanced-get"></a><span  class="short-header">其他清除佇列選項</span>作法：運用清除佇列訊息的其他選項</h2>


 自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。下列程式碼範例將使用 **GetMessages** 方法，在一次呼叫中取得 20 個訊息。接著它會使用 **foreach** 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘。請注意，系統會針對所有訊息同時開始計時 5 分鐘，所以從呼叫 **GetMessages** 開始的 5 分鐘後，任何尚未刪除的訊息都會重新出現。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
    // 在 5 分鐘內處理所有訊息，處理完後刪除所有訊息。
    queue.DeleteMessage(message);
    }

 <h2><a name="get-queue-length"></a><span  class="short-header">取得佇列長度</span>作法：取得佇列長度</h2>


 您可以取得佇列中的估計訊息數目。**FetchAttributes** 方法會要求佇列服務擷取佇列屬性，其中包含訊息計數。**ApproximateMethodCount** 屬性會傳回 **FetchAttributes** 方法所擷取的最後一個值，而無需呼叫佇列服務。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 擷取佇列屬性。
    queue.FetchAttributes();
    
    // 擷取大約的已快取訊息計數。
    int? cachedMessageCount = queue.ApproximateMessageCount;
    
    // 顯示訊息的數目。
    Console.WriteLine("Number of messages in queue:" + cachedMessageCount);

 <h2><a name="delete-queue"></a><span  class="short-header">刪除佇列</span>作法：刪除佇列</h2>


 若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 **Delete** 方法。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // 建立佇列用戶端。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // 擷取佇列的參照。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // 刪除佇列。
    queue.Delete();

 <h2><a name="next-steps"></a>後續步驟</h2>


 了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

 *  如需可用 API 的完整詳細資訊，請檢視佇列服務參考文件：
  *  [Storage Client Library for .NET 參考資料][7]
  *  [REST API 參考資料][8]
  

*  請參閱[在 Azure 中儲存和存取資料][9]，深入了解可使用 Azure  儲存體執行的更多進階工作。
*  如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  *  使用[資料表儲存體](/zh-tw/develop/net/how-to-guides/table-services/)儲存結構化資料。
  *  使用 [Blog 儲存體](/zh-tw/develop/net/how-to-guides/blob-storage/)儲存非結構化資料。
  *  使用 [SQL Database](/zh-tw/develop/net/how-to-guides/sql-database/) 儲存關聯式資料。
  


 </appsettings>



[1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/zh-tw/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
[7]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx