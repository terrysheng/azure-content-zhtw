<properties 
	pageTitle="如何使用 .NET 的佇列儲存體 | Microsoft Azure" 
	description="了解如何使用 Microsoft Azure 佇列儲存體來建立和刪除佇列，以及插入、查看、取得和刪除佇列訊息。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>

# 如何使用 .NET 的佇列儲存體

本指南將示範如何使用 Azure 佇列儲存服務執行一般案例。這些範例是以 C\# 程式碼撰寫，並使用「適用於 .NET 的 Azure 儲存體用戶端」。所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。如需有關佇列的詳細資訊，請參閱[後續步驟][]一節。

> [AZURE.NOTE] 本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是「儲存體用戶端程式庫 4.x」，您可以透過 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) 或從 [Azure SDK for .NET](/zh-tw/downloads/) 中取得。請參閱下面的[做法：以程式設計方式存取佇列儲存體][]，以詳細了解如何取得「儲存體用戶端程式庫」。

<h2>目錄</h2>

-   [什麼是佇列儲存體][]
-   [概念][]
-   [建立 Azure 儲存體帳戶][]
-   [設定 Azure 儲存體連接字串][]
-   [做法：以程式設計方式存取佇列儲存體][]
-   [做法：建立佇列][]
-   [做法：將訊息插入佇列][]
-   [做法：查看下一個訊息][]
-   [做法：變更佇列訊息的內容][]
-   [做法：清除下一個佇列訊息][]
-   [做法：運用清除佇列訊息的其他選項][]
-   [做法：取得佇列長度][]
-   [做法：刪除佇列][]
-   [後續步驟][]

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>做法：以程式設計方式存取佇列儲存體

<h3>取得組件</h3>
您可以使用 NuGet 來取得  `Microsoft.WindowsAzure.Storage.dll` 組件。請在 [方案總管] 中您的專案上按一下滑鼠右鍵，然後選擇 [管理 NuGet 封裝]。在線上搜尋 "WindowsAzure.Storage"，然後按一下 [安裝] 以安裝「Azure 儲存體」封裝與相依性。

`Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 <a href="http://azure.microsoft.com/develop/net/#">.NET 開發人員中心</a>下載)。此組件會安裝在 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` 目錄。

<h3>命名空間宣告</h3>
請將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取「Azure 儲存體」的所有 C\# 檔案內的頂端。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

確定已參照  `Microsoft.WindowsAzure.Storage.dll` 組件。

<h3>擷取連接字串</h3>
您可以使用 **CloudStorageAccount** 類型來代表「儲存體帳戶」資訊。如果您使用 Windows Azure 專案範本且 (或) 具有 Microsoft.WindowsAzure.CloudConfigurationManager 參照，便可使用 **CloudConfigurationManager** 類型，從 Azure 服務組態擷取儲存體連接字串和儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您建立的是不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib 相依性</h3>
Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。這些特定的 ODataLib 封裝是 [OData]、[Edm] 及 [Spatial]。

<h2><a name="create-queue"></a>做法：建立佇列</h2>

**CloudQueueClient** 物件可讓您取得佇列的參照物件。下列程式碼將建立 **CloudQueueClient** 物件。本指南的所有程式碼都使用 Azure 應用程式服務設定中所儲存的儲存體連接字串。還有其他方式可以建立 **CloudStorageAccount** 物件。如需詳細資料，請參閱 [CloudStorageAccount][] 文件。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

請使用 **queueClient** 物件來取得您想要使用之佇列的
參照。如果佇列不存在，您可以建立佇列。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a>做法：將訊息插入佇列</h2>

若要將訊息插入現有的佇列，請先建立新的 **CloudQueueMessage**。接著，呼叫 **AddMessage** 方法。您可以從字串 (採用 UTF-8 格式) 或「位元組」陣列建立 **CloudQueueMessage**。以下是建立佇列 (如果佇列不存在) 並插入  'Hello, World' 訊息的程式碼：

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

<h2><a name="peek-message"></a>做法：查看下一個訊息</h2>

透過呼叫 **PeekMessage** 方法，您便可以在佇列前面查看訊息，而無需將它從佇列中移除。

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

<h2><a name="change-contents"></a>做法：變更佇列訊息的內容</h2>

您可以在佇列中就地變更訊息內容。如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。通常，您也會保留重試計數，並在訊息重試超過 *n* 次時，就將該訊息刪除。這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

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

<h2><a name="get-message"></a>做法：清除下一個佇列訊息</h2>

您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。呼叫 **GetMessage** 時，您會取得佇列中的下一個訊息。對於從此佇列讀取訊息的任何其他程式碼而言，從 **GetMessage** 傳回的訊息會變成不可見。依預設，此訊息會維持 30 秒的不可見狀態。若要完成將訊息從佇列中移除的動作，您還必須呼叫 **DeleteMessage**。這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。您的程式碼會在處理完訊息之後立即呼叫 **DeleteMessage**。

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

<h2><a name="advanced-get"></a>做法：運用清除佇列訊息的其他選項</h2>

自訂從佇列中擷取訊息的方法有兩種。首先，您可以取得一批訊息 (最多 32 個)。其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。下列程式碼範例將使用 **GetMessages** 方法，在一次呼叫中取得 20 個訊息。接著，它會使用 **foreach** 迴圈來處理每個訊息。它也會將可見度逾時設定為每個訊息五分鐘。請注意，系統會針對所有訊息同時開始計時 5 分鐘，所以從呼叫 **GetMessages** 算起的 5 分鐘後，任何尚未刪除的訊息都會重新出現。

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

<h2><a name="get-queue-length"></a>做法：取得佇列長度</h2>

您可以取得佇列中的估計訊息數目。**FetchAttributes** 方法會要求佇列服務擷取佇列屬性，包括訊息計數。**ApproximateMethodCount** 屬性會傳回 **FetchAttributes** 方法所擷取的最後一個值，而無需呼叫佇列服務。

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

<h2><a name="delete-queue"></a>做法：刪除佇列</h2>

若要刪除佇列及其內含的所有訊息，請呼叫佇列物件上的 **Delete** 方法。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>後續步驟</h2>

了解佇列儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

<ul>
<li>如需可用 API 的完整詳細資訊，請檢視佇列服務參考文件：
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">適用於 .NET 的儲存體用戶端程式庫參考</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/windowsazure/dd179355">REST API 參考</a></li>
  </ul>
</li>
<li>參閱<a href="http://msdn.microsoft.com/library/windowsazure/gg433040.aspx">在 Azure 中儲存和存取資料</a>，以深入了解可使用「Azure 儲存體」來執行的更多進階工作。</li>
<li>了解如何使用下列工具來簡化您所撰寫要與「Azure 儲存體」搭配使用的程式碼：<a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK。</li>
<li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  <ul>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/">資料表儲存體</a>來儲存結構化資料。</li>
    <li>使用 <a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/">Blog 儲存體</a>來儲存非結構化資料。</li>
    <li>使用 <a href="/zh-tw/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 來儲存關聯式資料。</li>
  </ul>
</li>
</ul>



  [後續步驟]: #next-steps
  [什麼是佇列儲存體]: #what-is
  [概念]: #concepts
  [建立 Azure 儲存體帳戶]: #create-account
  [設定 Azure 儲存體連接字串]: #setup-connection-string
  [做法：以程式設計方式存取佇列儲存體]: #configure-access
  [做法：建立佇列]: #create-queue
  [做法：將訊息插入佇列]: #insert-message
  [做法：查看下一個訊息]: #peek-message
  [做法：變更佇列訊息的內容]: #change-contents
  [做法：清除下一個佇列訊息]: #get-message
  [做法：運用清除佇列訊息的其他選項]: #advanced-get
  [做法：取得佇列長度]: #get-queue-length
  [做法：刪除佇列]: #delete-queue
  [下載並安裝 Azure SDK for .NET]: /zh-tw/develop/net/
  [.NET 用戶端程式庫參考]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [在 Visual Studio 中建立 Azure 專案]: http://msdn.microsoft.com/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [在 Azure 中儲存和存取資料]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [設定連接字串]: http://msdn.microsoft.com/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
