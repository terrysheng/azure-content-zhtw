<properties urlDisplayName="Queue Service" pageTitle="如何使用 .NET 的佇列儲存體 | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# 如何使用 .NET 的佇列儲存體
 本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。這些範例均以 C# 程式碼撰寫，並使用 Azure Storage Client for .NET。涵蓋的案例包括**插入**、**查看**、**取得**和**刪除**佇列訊息，以及**建立和刪除佇列**。如需佇列的詳細資訊，請參閱[後續步驟][]一節。

> [WACOM.NOTE] 本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是透過 [NuGet] 提供(https://www.nuget.org/packages/WindowsAzure.Storage/) 或隨 [Azure SDK for .NET] 一起安裝的 Storage Client Library 4.x(/zh-tw/downloads/)。請參閱[作法：以程式設計方式存取佇列儲存體][]，以詳細了解如何取得儲存體用戶端程式庫。

<h2>目錄</h2>

-   [什麼是佇列儲存體][]
-   [概念][]
-   [建立 Azure 儲存體帳戶][]
-   [設定 Azure 儲存體連接字串][]
-   [作法：以程式設計方式存取佇列儲存體][]
-   [作法：建立佇列][]
-   [作法：將訊息插入佇列][]
-   [作法：查看下一個訊息][]
-   [作法：變更佇列訊息的內容][]
-   [作法：清除下一個佇列訊息][]
-   [作法：運用清除佇列訊息的其他選項][]
-   [作法：取得佇列長度][]
-   [作法：刪除佇列][]
-   [後續步驟][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a>建立 Azure 儲存體帳戶</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a>設定 Azure 儲存體連接字串</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>作法：以程式設計方式存取佇列儲存體

<h3>取得組件</h3>
您可以使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 [**方案總管**] 中以滑鼠右鍵按一下專案，然後選擇 [**管理 NuGet 封裝**]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [**安裝**] 以安裝 Azure 儲存體封裝與相依性。

`Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 <a href="http://www.windowsazure.com/zh-tw/develop/net/#">.NET Developer Center 下載)</a>。此組件會安裝至 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 目錄中。

<h3>命名空間宣告</h3>
將下列程式碼命名空間宣告加入至任何 C\# 檔案的頂端
(您想要在該檔案中以程式設計方式存取 Azure 儲存體)：

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
Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。特定 ODataLib 封裝是 [OData]、[Edm] 和 [Spatial]。

<h2><a name="create-queue"></a>作法：建立佇列</h2>

**CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。本指南的所有程式碼都使用 Azure
應用程式服務設定中所儲存的儲存體連接字串。也有其他方式可建立
 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [CloudStorageAccount][]
文件。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use the **queueClient** object to get a reference to the queue you want
to use. You can create the queue if it doesn't exist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a>作法：將訊息插入佇列</h2>

若要將訊息插入現有佇列，請先建立新的
**CloudQueueMessage**。接著，呼叫 **AddMessage** 方法。A
您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 **CloudQueueMessage**
****。以下是建立佇列 (如果
佇列不存在) 並插入訊息 'Hello, World' 的程式碼：

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a>作法：查看下一個訊息</h2>

您可以在佇列前面查看訊息，而無需將它
從佇列中移除，只要透過呼叫 **PeekMessage** 方法即可。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a>作法：變更佇列訊息的內容</h2>

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，如果訊息重試超過 *n* 次，您會將它刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a>作法：將下一個訊息清除佇列</h2>

您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。呼叫 **GetMessage** 時，您會取得佇列中的下一個訊息。對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 **GetMessage** 傳回的
訊息。依預設，此訊息會維持 30 秒的不可見狀態。若要完成從佇列中移除訊息，您還必須呼叫 **DeleteMessage**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **DeleteMessage**。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a>作法：運用清除佇列訊息的其他選項</h2>

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。下列程式碼範例將使用 **GetMessages** 方法，在一次呼叫中取得 20 個訊息。接著它會使用 **foreach**

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a>How to: Get the queue length</h2>

You can get an estimate of the number of messages in a queue. The **FetchAttributes** method asks the Queue service to retrieve the queue attributes, including the message count. The **ApproximateMethodCount** property returns the last value retrieved by the **FetchAttributes** method, without calling the Queue service.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

<h2><a name="delete-queue"></a>How to: Delete a queue</h2>

To delete a queue and all the messages contained in it, call the **Delete** method on the queue object.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>Next steps</h2>

Now that you've learned the basics of Queue storage, follow these links
to learn how to do more complex storage tasks.

<ul>
<li>View the Queue service reference documentation for complete details about available APIs:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET reference</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355">REST API reference</a></li>
  </ul>
</li>
<li>Learn about more advanced tasks you can perform with Azure Storage at <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx">Storing and Accessing Data in Azure</a>.</li>
<li>Learn how to work with Azure Storage in backend processes for Azure Websites at <a href="/zh-tw/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Get Started with the Azure WebJobs SDK</a>.</li>
<li>View more feature guides to learn about additional options for storing data in Azure.
  <ul>
    <li>Use <a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/">Table Storage</a> to store structured data.</li>
    <li>Use <a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/">Blob Storage</a> to store unstructured data.</li>
    <li>Use <a href="/zh-tw/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> to store relational data.</li>
  </ul>
</li>
</ul>



  [Next Steps]: #next-steps
  [What is Queue Storage]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Setup an Azure Storage Connection String]: #setup-connection-string
  [How to: Programmatically access Queue storage]: #configure-access
  [How To: Create a Queue]: #create-queue
  [How To: Insert a Message into a Queue]: #insert-message
  [How To: Peek at the Next Message]: #peek-message
  [How To: Change the Contents of a Queued Message]: #change-contents
  [How To: Dequeue the Next Message]: #get-message
  [How To: Leverage Additional Options for Dequeuing Messages]: #advanced-get
  [How To: Get the Queue Length]: #get-queue-length
  [How To: Delete a Queue]: #delete-queue
  [Download and install the Azure SDK for .NET]: /zh-tw/develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35_1-->
