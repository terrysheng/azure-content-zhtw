<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service (2.0)" pageTitle="How to use the queue storage service | Microsoft Azure" metaKeywords="Get started Azure queue, Azure asynchronous processing, Azure queue, Azure queue storage, Azure queue .NET, Azure queue storage .NET, Azure queue C#, Azure queue storage C#" description="Learn how to use the Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

如何使用佇列儲存體服務
======================

[1.7 版](/zh-tw/develop/net/how-to-guides/queue-service-v17/ "1.7 版") [2.0 版](/zh-tw/develop/net/how-to-guides/queue-service/ "2.0 版")

本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。這些範例均以 C\# 程式碼撰寫，並使用 .NET API。所涵蓋的案例包括**「插入」**、**「查看」**、**「取得」**和**「刪除」**佇列訊息，以及**「建立和刪除佇列」**。如需佇列的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

-   [什麼是佇列儲存體](#what-is)
-   [概念](#concepts)
-   [建立 Azure 儲存體帳戶](#create-account)
-   [設定 Azure 儲存體連接字串](#setup-connection-string)
-   [作法：使用 .NET 以程式設計方式存取佇列](#access)
-   [作法：建立佇列](#create-queue)
-   [作法：將訊息插入佇列](#insert-message)
-   [作法：查看下一個訊息](#peek-message)
-   [作法：變更佇列訊息的內容](#change-contents)
-   [作法：將下一個訊息清除佇列](#get-message)
-   [作法：運用清除佇列訊息的其他選項](#advanced-get)
-   [作法：取得佇列長度](#get-queue-length)
-   [作法：刪除佇列](#delete-queue)
-   [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

建立帳戶建立 Azure 儲存體帳戶
-----------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

設定連接字串設定 Azure 儲存體連接字串
-------------------------------------

Azure .NET 儲存體 API 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。您可以將儲存體連接字串置於設定檔中，而非硬式編碼至程式碼中：

-   使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (`*.csdef` 和 `*.cscfg` 檔案) 來儲存連接字串。
-   使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config` 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本指南稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 [方案總管]，於 Azure 部署專案之 **[角色]** 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 **[屬性]**。

    ![Blob5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  按一下 **[設定]** 索引標籤，再按 **[加入設定]** 按鈕。

    ![Blob6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

    設定清單中隨即顯示新的 **[Setting1]** 項目。

3.  在新 **[Setting1]** 項目的 **[類型]** 下拉式清單中，選擇 **[連接字串]**。

    ![Blob7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  按一下 **[Setting1]** 項目右側的 **[...]** 按鈕。**[儲存體帳戶連接字串]** 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Azure 儲存體)，還是雲端中的實際儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。如您想要在稍早於 Azure 上建立的儲存體帳戶中儲存 Blob 資料，請輸入在本教學課程的稍早步驟複製的 **[主要存取金鑰]** 值。

    ![Blob8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  將 **[名稱]** 項目從 **[Setting1]** 變為更易記的名稱，如 **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。

    ![Blob9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)

### 使用網站或虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config`)。您可以使用 `<appSettings>` 元素來儲存連接字串：

    <configuration>
    <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

如需儲存體連接字串的詳細資訊，請閱讀[設定連接字串](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx)。

現在您已準備就緒，可以開始執行本指南的「作法」工作。

以程式設計方式存取作法：使用 .NET 以程式設計方式存取佇列
--------------------------------------------------------

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C\# 檔案內的頂端：

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

您可以使用 **CloudStorageAccount** 類型及 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

建立佇列作法：建立佇列
----------------------

**CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。本指南的所有程式碼都使用 Azure 應用程式服務設定中所儲存的儲存體連接字串。還有其他方式可以建立 **CloudStorageAccount** 物件。請參閱 [CloudStorageAccount](http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx) 文件以取得詳細資料。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

使用 **queueClient** 物件來取得想要使用佇列的參照。如果佇列不存在，您可以建立佇列。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

插入訊息作法：將訊息插入佇列
----------------------------

若要將訊息插入現有佇列，請先建立新的 **CloudQueueMessage**。接著，呼叫 **AddMessage** 方法。您可以從字串 (採用 UTF-8 格式) 或**位元組**陣列建立 **CloudQueueMessage**。以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

查看下一個訊息作法：查看下一個訊息
----------------------------------

透過呼叫 **PeekMessage** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

變更訊息內容作法：變更佇列訊息的內容
------------------------------------

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，如果訊息重試超過 *n* 次，您會將它刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible immediately
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

清除下一個佇列訊息作法：清除下一個佇列訊息
------------------------------------------

您的程式碼可以使用兩個步驟來清除佇列訊息。呼叫 **GetMessage** 時，您會取得佇列中的下一個訊息。從 **GetMessage** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。依預設，此訊息會維持 30 秒的不可見狀態。若要完成從佇列中移除訊息，您還必須呼叫 **DeleteMessage**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **DeleteMessage**。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // 取得下一個訊息
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //在 30 秒內處理訊息，然後將訊息刪除
    queue.DeleteMessage(retrievedMessage);

其他清除佇列選項作法：運用清除佇列訊息的其他選項
------------------------------------------------

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。下列程式碼範例將使用 **GetMessages** 方法，在一次呼叫中取得 20 個訊息。接著它會使用 **foreach** 迴圈處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘。請注意，系統會針對所有訊息同時開始計時 5 分鐘，所以從呼叫 **GetMessages** 開始的 5 分鐘後，任何尚未刪除的訊息都會重新出現。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Do processing for all messages in less than 5 minutes, deleting each message after processing
        queue.DeleteMessage(message);
    }

取得佇列長度作法：取得佇列長度
------------------------------

您可以取得佇列中的估計訊息數目。**RetrieveApproximateMessageCount** 方法會要求佇列服務計算佇列中的訊息數。由於佇列服務在回應您的要求之後可以新增或移除訊息，此計數僅為近似值。**ApproximateMethodCount** 屬性會傳回 **RetrieveApproximateMessageCount** 所擷取的最後一個值，而無需呼叫佇列服務。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Retrieve the approximate message count
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Retrieve the cached approximate message count
    int
     cachedMessageCount = queue.ApproximateMessageCount;

刪除佇列作法：刪除佇列
----------------------

若要刪除佇列及其內含的所有訊息，請在佇列物件上呼叫 **Delete** 方法。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue
    queue.Delete();

後續步驟
--------

了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   如需可用 API 的完整詳細資訊，請檢視佇列服務參考文件：
    -   [.NET 用戶端程式庫參考](http://msdn.microsoft.com/zh-tw/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST API 參考](http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355)
-   請參閱[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)，深入了解可使用 Azure 儲存體執行的更多進階工作。
-   如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
    -   使用[資料表儲存體](/zh-tw/develop/net/how-to-guides/table-services/)儲存結構化資料。
    -   使用 [Blog 儲存體](/zh-tw/develop/net/how-to-guides/blob-storage/)儲存非結構化資料。
    -   使用 [SQL Database](/zh-tw/develop/net/how-to-guides/sql-database/) 儲存關聯式資料。

