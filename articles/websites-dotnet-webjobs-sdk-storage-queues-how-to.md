<properties linkid="" pageTitle="如何透過 WebJobs SDK 使用 Azure 佇列儲存體" metaKeywords="WebJobs SDK Azure 佇列儲存體 .NET C#" description="了解如何在 WebJobs SDK 使用 Azure 佇列儲存體建立和刪除查詢、插入、查看、取得和刪除佇列訊息等。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# 如何透過 WebJobs SDK 使用 Azure 佇列儲存體 (英文)

本指南說明如何使用 Azure 佇列儲存體服務及 
Azure WebJobs SDK 1.0.0 版為常見案例撰寫 C# 程式碼。

本指南假設您已經知道 [Webjobs SDK 的功能](../websites-webjobs-sdk-storage-queues-how-to)，以及[如何執行基本工作](../websites-dotnet-webjobs-sdk-get-started/)，例如安裝 WebJobs SDK NuGet 封裝、建立 Azure 儲存體帳戶，以及為指向您儲存體帳戶的 WebJobs SDK 建立連接字串。

大部分的程式碼片段只會顯示函數，不會顯示建立 'JobHost' 物件的程式碼，如此範例所示：

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 目錄

-   [如何在接收到佇列訊息時觸發函數](#trigger)
	- 字串佇列訊息
	- POCO 物件佇列訊息
	- Async 函數
	- 輪詢演算法
	- 平行執行
	- 取得佇列或佇列訊息中繼資料
	- 正常關機
-   [如何在處理佇列訊息時建立佇列訊息](#createqueue)
	- 字串佇列訊息
	- POCO 物件佇列訊息
	- 建立多個訊息
	- 在函數主體中使用佇列屬性
-   [如何在處理佇列訊息時讀取和寫入 Blob](#blobs)
	- 字串佇列訊息
	- POCO 物件佇列訊息
	- 在函數主體中使用 Blob 屬性
-   [如何處理有害訊息](#poison)
	- 自動處理有害訊息
	- 手動處理有害訊息
-   [如何設定組態選項](#config)
	- 在程式碼中設定 SDK 連接字串
	- 配置 QueueTrigger 設定
	- 從設定中取得佇列名稱
-   [如何手動觸發函數](#manual)
-   [如何寫入記錄檔](#logs)
-   [後續步驟](#nextsteps)

## <a id="trigger"></a> 如何在接收到佇列訊息時觸發函數

若要撰寫當接收到佇列訊息時 SDK 將呼叫的函數，請視您預期從訊息中取得的結果，搭配 'QueueTrigger' 屬性使用字串或 POCO 參數。屬性建構函式採用字串參數，來指定要輪詢的佇列名稱。您也可以[動態設定佇列名稱](#config)。

如果您的網站在多個 VM 上執行，則 WebJob 將在每部機器上執行，而且每部機器將會等待觸發程序並嘗試執行函數。在某些案例中，這會導致部分函數處理相同的資料兩次，因此函數應是以等冪的方式 (寫入，因此使用相同輸入資料重複呼叫函數才不會產生重複的結果)。  

### 字串佇列訊息

在下列範例中，佇列包含字串訊息，以便 `QueueTrigger` 套用至名為 `logMessage` 的字串參數，其中包含佇列訊息的內容。函數會[將記錄訊息寫入儀表板](#logs)。
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

除了 'String' 以外，參數可能是位元組陣列、'CloudQueueMessage' 物件或您定義的 POCO 物件。

### POCO 物件佇列訊息

在下列範例中，佇列訊息會包含 'BlobInformation' 物件的 JSON，其中包含 'BlobName' 屬性。SDK 會自動還原序列化物件。

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob:" + blobInfo.BlobName);
		}

下列範例示範如何建立 SDK 可剖析的 POCO 佇列訊息，而不使用 WebJobs SDK。SDK 會使用 [Newtonsoft.Json NuGet 封裝](http://www.nuget.org/packages/Newtonsoft.Json)來序列化和還原序列化訊息。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Async 函數

下列 async 函數[會將記錄寫入至儀表板](#logs)。

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Async 函數可能需要取消語彙基元，如下列範例所示 (會複製 Blob)。(如需 'queueTrigger' 預留位置的說明，請參閱 [Blob](#blobs) 區段)。

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### 輪詢演算法

SDK 會實作隨機指數型延緩演算法，以減少閒置佇列輪詢對於儲存體交易成本的影響。找到訊息時，SDK 會等待兩秒，然後檢查的另一個訊息；當找不到任何訊息時，它會等候大約四秒，然後再試一次。連續嘗試取得佇列訊息失敗後，等候時間會持續增加，直到它到達等待時間上限 (預設值為一分鐘)。[您可以設定等待時間上限](#config)。

### 平行執行

如果您有多個函數在不同的佇列上接聽，則同時接收到訊息時，SDK 會以平行方式呼叫它們。 

單一佇列接收到多個訊息時，也是如此。根據預設，SDK 會一次取得一批 (16 個) 佇列訊息，並執行以平行方式處理它們的函數。[您可以設定批次大小](#config)。當處理到批次大小數目的一半，SDK 會取得另一個批次，並開始處理那些訊息。因此每個函數可處理的並行處理訊息的最大數目是批次大小的一倍半。這項限制個別套用至具有 'QueueTrigger' 屬性的每個函數。如果您不想平行執行一個佇列上接收到的訊息，請將批次大小設定為 1。

### <a id="queuemetadata"></a>取得佇列或佇列訊息中繼資料

您可以將參數加入至方法簽章，以取得下列訊息屬性：

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contains message text)
* `string` id
* `string` popReceipt
* `int` dequeueCount

如果您想要直接使用 Azure 儲存體 API，您也可以加入 'CloudStorageAccount' 參數。

下列範例會將此中繼資料全部寫入至 INFO 應用程式記錄檔。在範例中，logMessage 和 queueTrigger 皆包含佇列訊息的內容。

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

以下是範例程式碼所寫入的範例記錄：

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>正常關機

連續 WebJob 中執行的函數可以接受 'CancellationToken' 參數，以啟用在 WebJob 即將終止時，讓作業系統通知函數。您可以使用此通知，以確定函數不會無預警地終止，而導致資料不一致的狀態。

下列範例示範如何透過函數檢查即將終止的 WebJob。

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**注意：** 若函數已停止，[儀表板] 可能無法正確地顯示其狀態及輸出。
 
如需詳細資訊，請參閱 [WebJobs 正常關機](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)。   

## <a id="createqueue"></a> 如何在處理佇列訊息時建立佇列訊息

若要撰寫會建立新的佇列訊息的函數，在輸出字串或 POCO 參數上使用 'Queue' 屬性。如同 'QueueTrigger'，您可透過字串傳入佇列名稱，或可以[動態設定的佇列名稱](#config)。

### 字串佇列訊息

下列範例會在名為 "inputqueue" 的佇列中接收到佇列訊息時，使用相同的內容在名為 "outputqueue" 的佇列中建立新的佇列訊息。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

如果輸出參數類型是任何下列類型，且函數結束時物件不是 null，則 SDK 會建立佇列訊息：

* `string` 
* `byte[]`
* 您定義的可序列化 POCO 類型
* `CloudQueueMessage`

若要建立多個訊息，請參閱本區段稍後介紹的**建立多個訊息**。

如果您想要以手動方式傳送訊息，也可以使用 'CloudQueue' 做為輸出參數類型。

### POCO 物件佇列訊息

若要建立包含 POCO 物件而不是字串的佇列訊息，將 POCO 類型做為輸出參數傳遞至 'Queue' 屬性建構函式。 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK 會自動將物件序列化為 JSON。一律會建立佇列訊息，即使物件是 null。

### 建立多個訊息

若要建立多個訊息，將 `ICollector<T>` 或 `IAsyncCollector` 設為輸出佇列的參數類型，如下列範例所示。

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

呼叫 'Add' 方法時，即會立即建立每個佇列訊息。

### <a id="queuebody"></a>在函數主體中使用佇列屬性

如果您需要在函數中執行一些工作，再使用 'Queue' 屬性來建立佇列訊息，您可以使用 'IBinder' 介面來取得 'CloudQueue' 物件，即可讓您直接使用佇列。 

下列範例需要輸入佇列訊息，並使用相同內容在輸出佇列中建立新的訊息。輸出佇列名稱是由函體主體中的程式碼設定。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> 如何在處理佇列訊息時讀取和寫入 Blob 和資料表

'Blob' 和 'Table' 屬性可讓您讀取和寫入 Blob 和資料表。此區段中的範例適用於 Blob。

可以搭配 `Blob` 屬性的部分類型，包括 `Stream`、`TextReader`、`TextWriter` 和 `CloudBlockBlob`。屬性建構函式需要 'blobPath' 參數來指定容器和 Blob 名稱；而當屬性修飾 'Stream' 物件，另一個建構函式參數指定做為讀取、寫入或讀取/寫入的 'FileAccess' 模式。如果您需要在函數中執行一些工作，再將 Blob 繫結至物件，您可以使用函數主體中的屬性，[如先前所示的 Queue 屬性](#queuebody)。

### 字串佇列訊息

對於包含字串的佇列訊息，'queueTrigger' 預留位置可在 'Blob' 屬性的 'blobPath' 參數中使用，其中包含訊息的內容。 

下列範例會使用 'Stream' 物件來讀取和寫入 Blob。佇列訊息提供位於 textblob 容器中的 Blob 名稱。在相同的容器中建立名稱中附加 "-new" 的 Blob 複本。 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

下列範例會使用 'CloudBlockBlob' 物件刪除 Blob。

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### POCO 物件佇列訊息

針對以 JSON 格式儲存在佇列訊息中的 POCO 物件，您可以使用 'Queue' 屬性之 'blobPath' 參數中物件的屬性名稱來做為預留位置。您也可以使用[佇列中繼資料屬性名稱](#queuemetadata)做為預留位置。 

下列範例會使用不同的副檔名將 Blob 複製到新的 Blob，使用 'BlobInformation' 物件的屬性以指定的輸入和輸出 Blob 的名稱。 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

下列範例示範如何建立 SDK 可剖析的 POCO 佇列訊息，而不使用 WebJobs SDK。SDK 會使用 [Newtonsoft.Json NuGet 封裝](http://www.nuget.org/packages/Newtonsoft.Json)來序列化和還原序列化訊息。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> 如何處理有害訊息

內容會導致函數失敗的訊息稱為*有害訊息*。當函數失敗時不會刪除佇列訊息，最後會再度挑選到該訊息，造成重複循環。SDK 可在限制的反覆運算次數後自動中斷循環，或您可以手動進行。

### 自動處理有害訊息

SDK 最多會呼叫函數 5 次來處理佇列訊息。如果第五次嘗試失敗，訊息便會移到有害佇列中。[您可以設定重試次數上限](#config)。 

有害佇列名為 * {originalqueuename}*-poison。您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。 

在下列範例中，當佇列訊息包含不存在的 Blob 名稱，'CopyBlob' 函數將會失敗。發生時，就會從 copyblobqueue 佇列將訊息移至 copyblobqueue-poison 佇列。接著，'ProcessPoisonMessage' 會記錄有害訊息。

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

下圖顯示這些函數處理有害訊息之後的主控台輸出。

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### 手動處理有害訊息

您可以將名為 'dequeueCount' 的 'int' 參數加入到函數中，來取得訊息已被挑選來處理的次數。接著，您可以檢查函數程式碼中的清除佇列計數，並在數目超過閾值時處理您自己有害訊息，如下列範例所示。

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

為了讓此程式碼如預期般運作，您必須增加自動處理有害訊息的重試次數上限，否則此範例中的清除佇列計數一律不會超過 5。

## <a id="config"></a> 如何設定組態選項

您可以使用 'JobHostConfiguration' 類型來設定下列組態選項：

* 在程式碼中設定 SDK 連接字串。
* 配置 'QueueTrigger' 設定，例如清除佇列計數上限。
* 從設定中取得佇列名稱。

### <a id="setconnstr"></a>在程式碼中設定 SDK 連接字串

在程式碼中設定 SDK 連接字串，可讓您在組態檔或環境變數中使用自己的連接字串名稱，如下列範例所示。

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>配置佇列設定

您可以配置會套用至佇列訊息處理的下列設定：

- 挑選以同時平行執行的佇列訊息數目上限 (預設值為 16)。
- 將佇列訊息傳送到有害佇列之前的重試次數上限 (預設值為 5)。
- 佇列為空的時，要再次輪詢前的等待時間上限 (預設值為 1 分鐘)。

下列範例示範如何配置這些設定：

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>從設定中取得佇列名稱

'JobHostConfiguration' 類型可讓您透過提供佇列名稱的 'NameResolver' 物件，傳遞給 SDK 以供 'QueueTrigger' 和 'Queue' 屬性使用。

例如，假設您想要在測試環境中使用名為 logqueuetest 的佇列，和在生產環境中使用名為 logqueueprod 的佇列。不用將佇列名稱寫入程式碼，您要在 'appSettings' 集合 (會有實際佇列名稱) 中指定項目名稱。如果 'appSettings' 索引鍵是 logqueue，您的函數看起來可能如下所示。

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

'NameResolver' 類別接著可以從 'appSettings' 取得佇列名稱，如下列範例所示：

		public class QueueNameResolver :INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

您可以將 'NameResolver' 類別傳遞至 'JobHost' 物件中，如下列範例所示。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>如何手動觸發函數

若要手動觸發函數，請在 'JobHost' 物件上使用 'Call' 或 'CallAsync' 方法，和在函數上使用 'NoAutomaticTrigger' 屬性，如下列範例所示。 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message:", message);
		    }
		}

## <a id="logs"></a>如何寫入記錄檔

若要寫入會顯示在 WebJobs 儀表板頁面 (連結至特定函數叫用) 中的記錄檔，使用在您方法簽章中參數取得的 'TextWriter' 物件。

若要寫入[應用程式追蹤記錄](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)，使用 'Console.Out' (建立標示為 INFO 的記錄檔) 和 'Console.Error' (建立標示為 ERROR 的記錄檔)。替代方法是使用 [Trace 或 TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)。

視您設定 Azure 網站的方式而定，應用程式記錄檔會出現在網站記錄檔、Azure 資料表或 Azure Blob 中。如果程式在 Azure WebJob 中執行，則 [儀表板] 中也會顯示最新的 100 個應用程式記錄檔。(針對在本機或某些其他環境中執行的程式，[儀表板] 中不會顯示任何應用程式記錄檔。)   

您可以[將儀表板連接字串設定為 null](#config) 來停用記錄。

下列範例示範寫入記錄檔的數種方式：

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

當您移至頁面以進行特定函數叫用，並按一下 [**切換輸出**] 時，WebJobs SDK 儀表板中會出現 'TextWriter' 物件的輸出：

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

當您移至頁面進行 WebJob (不是要叫用函數)，並按一下 [**切換輸出**] 時，WebJobs SDK 儀表板中會顯示應用程式記錄檔最新的 100 行。
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

在連續的 WebJob 中，應用程式記錄檔顯示在網站檔案系統中的 /data/jobs/continuous/*{webjobname}*/job_log.txt。

		[09/26/2014 21:01:13 > 491e54:INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54:ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54:INFO] Console.Out - Hello world!

在 Azure Blob 中，應用程式記錄檔看起來像這樣：
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

而在 Azure 資料表中，'Console.Out' 和 'Console.Error' 記錄檔看起來像這樣：

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 後續步驟

本主題提供了程式碼範例，示範如何處理使用 Azure 佇列的常見案例。如需 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
