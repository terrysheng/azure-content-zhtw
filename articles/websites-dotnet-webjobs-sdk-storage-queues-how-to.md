<properties linkid="" pageTitle="如何使用 WebJobs SDK 來使用 Azure 佇列儲存體 " metaKeywords="WebJobs SDK Azure 佇列儲存體 .NET C#" description="了解如何在 WebJobs SDK 中使用 Azure 佇列儲存體。建立及刪除佇列；插入、查看、取得及刪除佇列訊息等等。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# 如何使用 WebJobs SDK 來使用 Azure 佇列儲存體

本指南說明如何使用 Azure 佇列儲存體服務和 Azure WebJobs SDK 1.0.0 版，
在一般情況下撰寫 C# 程式碼。

本指南假設您已經知道[什麼是 Webjobs SDK](../websites-webjobs-sdk-storage-queues-how-to) 以及[如何執行基本工作](../websites-dotnet-webjobs-sdk-get-started/)，例如安裝 WebJobs SDK NuGet 套件、建立 Azure 儲存體帳戶，以及為指向您的儲存體帳戶的 WebJobs SDK 建立連線字串。

大部分程式碼片段都只會顯示函數，不會顯示建立 `JobHost` 物件的程式碼，如此範例所示：

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 目錄

-   [如何在收到佇列訊息時觸發函數](#trigger)
	- 字串佇列訊息
	- POCO 物件佇列訊息
	- 非同步函數
	- 輪詢演算法
	- 平行執行
	- 取得佇列或佇列訊息中繼資料
	- 順利關機
-   [如何在處理佇列訊息時建立佇列訊息](#createqueue)
	- 字串佇列訊息
	- POCO 物件佇列訊息
	- 建立多個訊息
	- 在函數主體中使用 Queue 屬性
-   [如何在處理佇列訊息時讀取及寫入 Blob](#blobs)
	- 字串佇列訊息
	- POCO 物件佇列訊息
	- 使用函數主體中的 Blob 屬性
-   [如何處理有害訊息](#poison)
	- 自動處理有害訊息
	- 手動處理有害訊息
-   [如何設定組態選項](#config)
	- 在程式碼中設定 SDK 連接字串
	- 設定 QueueTrigger 設定
	- 從組態取得佇列名稱
-   [如何手動觸發函數](#manual)
-   [如何寫入記錄檔](#logs)
-   [後續步驟](#nextsteps)

## <a id="trigger"></a> 如何在收到佇列訊息時觸發函數

若要編寫 SDK 在收到佇列訊息時呼叫的函數，請使用 `QueueTrigger` 屬性搭配字串或 POCO 參數 (視您預期訊息中有哪些內容而定)。屬性建構函式會使用指定要輪詢之佇列的名稱的字串參數。您也可以[動態設定佇列名稱](#config)。

如果您的網站是在多部 VM 上執行，WebJob 將會在每部機器上執行，且每部機器都將會等待觸發程序並嘗試執行函數。在某些案例中，這會導致部分函數處理相同的資料兩次，因此函數應是以等冪的方式 (寫入，因此使用相同輸入資料重複呼叫函數才不會產生重複的結果)。  

### 字串佇列訊息

在下面的範例中，佇列包含字串訊息，所以 `QueueTrigger` 會套用至名為 `logMessage` 且包含佇列訊息內容的字串參數。函數[會將記錄訊息寫入儀表板](#logs)。
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

除了 `string` 之外，參數也可以是一個位元組陣列、一個 `CloudQueueMessage` 物件，或一個您定義的 POCO 物件。

### POCO 物件佇列訊息

在下面的範例中，佇列訊息包含 `BlobInformation` 物件 (包括 `BlobName` 屬性) 的 JSON。SDK 會自動將物件還原序列化。

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

下列範例示範如何建立 POCO 佇列訊息而不使用 SDK 可以剖析的 WebJobs SDK。SDK 會使用 [Newtonsoft.Json NuGet 套件](http://www.nuget.org/packages/Newtonsoft.Json)來將訊息序列化及還原序列化。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### 非同步函數

下面的非同步函數[會將記錄檔寫入儀表板](#logs)。

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

非同步函數可能需要取消權杖，如下面複製 Blob 的範例中所示。(如需 `queueTrigger` 預留位置的說明，請參閱 [Blobs](#blobs) 一節。)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### 輪詢演算法

SDK 會實作隨機指數型倒退演算法，以降低閒置佇列輪詢對儲存體交易成本的影響。在找到訊息時，SDK 會等待兩秒鐘，然後檢查其他訊息。在沒有找到訊息時，SDK 則會等待約四秒鐘，然後再試一次。在後續嘗試取得佇列訊息失敗後，等待時間會繼續增加，直到達到等待時間上限為止，預設為 1 分鐘。[等待時間上限是可以設定的](#config)。

### 平行執行

如果您有多個函數在不同的佇列上接聽，SDK 將會在同時收到訊息時平行呼叫它們。 

收到單一佇列的多個訊息時也是如此。依照預設，SDK 一次可以取得一批佇列訊息 (16 個)，並以平行方式執行可處理它們的函數。[批次大小是可以設定的](#config)。當要處理的訊息數目減少到批次大小 (該批訊息數目) 的一半時，SDK 就會取得另一批訊息並開始處理那些訊息。因此，每個函數並行處理之訊息的上限數目為批次大小 (該批訊息數目) 的 1.5 倍。此限制會個別套用至具有 `QueueTrigger` 屬性的每個函數。如果您不想平行執行在單一佇列中收到的訊息，請將批次大小設定為 1。

### <a id="queuemetadata"></a>取得佇列或佇列訊息中繼資料

您可以透過新增參數至方法簽章來取得下列訊息屬性：

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (包含訊息文字)
* `string` id
* `string` popReceipt
* `int` dequeueCount

如果您想要直接使用 Azure 儲存體 API，您也可以新增 `CloudStorageAccount` 參數。

下面的範例會將此中繼資料全部寫入 INFO 應用程式記錄檔。在範例中，logMessage 和 queueTrigger 包含佇列訊息的內容。

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
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
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

以下是範例程式碼寫入的範例記錄檔：

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>順利關機

在連續 WebJob 中執行的函數可以接受 `CancellationToken` 參數，該參數可讓作業系統在 WebJob 即將終止時通知函數。您可以使用此通知來確保函數不會在讓資料維持不一致狀態的情況下意外終止。

下面的範例示範如何檢查函數中即將終止的 WebJob。

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

注意：****儀表板可能不會正確顯示已關閉之函數的狀態與輸出。
 
如需詳細資訊，請參閱 [WebJobs 正常關機](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)。   

## <a id="createqueue"></a> 如何在處理佇列訊息時建立佇列訊息

若要編寫會建立新佇列訊息的函數，請在輸出字串或 POCO 參數中使用 `Queue` 屬性。就像 `QueueTrigger` 一樣，您可以用字串的方式傳入佇列名稱，或者您可以[動態設定佇列名稱](#config)。

### 字串佇列訊息

下面的範例會在名稱為 "outputqueue" 的佇列中建立一個新的佇列訊息，其內容與名為 "inputqueue" 的佇列中收到的佇列訊息相同。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

如果輸出參數類型是下列類型中的任何一個，且如果在函數結束時物件不是空值，SDK 就會建立佇列訊息：

* `string` 
* `byte[]`
* 您定義的可序列化 POCO 類型
* `CloudQueueMessage`

若要建立多個訊息，請參閱本節中較後面的＜建立多個訊息＞****。

如果您想要手動傳送訊息，您也可以使用 `CloudQueue` 做為輸出參數類型。

### POCO 物件佇列訊息

若要建立包含 POCO 物件 (而非字串) 的佇列訊息，請將 POCO 做為輸出參數傳送至 `Queue` 屬性建構函式。 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK 會自動將物件序列化為 JSON。即使物件是空值，也一律會建立佇列訊息。

### 建立多個訊息

若要建立多個訊息，請將輸出佇列的參數類型設為 `ICollector<T>` 或 `IAsyncCollector`，如下面的範例所示。

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

每個佇列訊息都會在呼叫 `Add` 方法時立即建立。

### <a id="queuebody"></a>在函數主體中使用 Queue 屬性

如果您需要先在您的函數中執行部分工作，然後再使用 `Queue` 屬性建立佇列訊息，您可以使用 `IBinder` 介面取得可讓您直接使用佇列的 `CloudQueue` 物件。 

下面的範例會使用輸入佇列訊息，並在輸出佇列中建立含有相同內容的新訊息。輸出佇列名稱會由函數主體中的程式碼設定。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> 如何在處理佇列訊息時讀取及寫入 Blob 與表格

`Blob` 和 `Table` 屬性可讓您讀取及寫入 Blob 與表格。本節中的範例適用於 Blob。

可和 `Blob` 屬性搭配使用的部分類型包括 `Stream`、`TextReader`、`TextWriter` 及 `CloudBlockBlob`。屬性建構函式會使用指定容器與 Blob 名稱的 `blobPath` 參數，而且當屬性裝飾 `Stream` 物件時，另一個建構函式參數會將 `FileAccess` 模式指定為讀取、寫入或讀取/寫入。如果您需要先在函數中執行部分工作，然後再將 Blob 繫結至物件，您可以在函數主體中使用屬性，[如之前所示的佇列屬性](#queuebody)。

### 字串佇列訊息

對於包含字串的佇列訊息，您可以在 `Blob` 屬性的 `blobPath` 參數中使用 `queueTrigger` 預留位置，它包含了訊息的內容。 

下面的範例使用 `Stream` 物件來讀取及寫入 Blob。佇列訊息提供位於 textblobs 容器中之 Blob 的名稱。會在相同的容器中建立 Blob 的複本，並在其名稱中附加 "-new"。 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

下面的範例使用 `CloudBlockBlob` 物件來刪除 Blob。

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### POCO 物件佇列訊息

對於在佇列訊息中儲存為 JSON 的 POCO 物件，您可以在 `Queue` 屬性的 `blobPath` 參數中使用指定物件屬性的預留位置。您也可以使用[佇列中繼資料屬性名稱](#queuemetadata) 做為預留位置。 

下列範例使用 `BlobInformation` 物件的屬性指定輸入與輸出 Blob 的名稱，來將 Blob 複製到副檔名不同的新 Blob。 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

下列範例示範如何建立 POCO 佇列訊息而不使用 SDK 可以剖析的 WebJobs SDK。SDK 會使用 [Newtonsoft.Json NuGet 套件](http://www.nuget.org/packages/Newtonsoft.Json)來將訊息序列化及還原序列化。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> 如何處理有害訊息

訊息內容會導致函數失敗的訊息稱為「有害訊息」。當函數失敗時，佇列訊息不會被刪除且最後會再度被挑選，進而導致重複循環。SDK 可在有限的反覆次數之後自動中斷循環，或者您可以手動中斷循環。

### 自動處理有害訊息

SDK 將會呼叫函數最多 5 次以處理佇列訊息。如果第五次失敗，就會將訊息移到有害佇列。[重試次數上限是可以設定的](#config)。 

有害佇列的名稱為 *{originalqueuename}*-poison。您可以編寫函數來處理有害佇列中的訊息，方法是記錄它們或傳送需要注意的通知。 

在下面的範例中，`CopyBlob` 函數將會在佇列訊息包含不存在之 Blob 的名稱時失敗。發生失敗時，訊息會從 copyblobqueue 佇列移到 copyblobqueue-poison 佇列。然後 `ProcessPoisonMessage` 會記錄有害訊息。

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

下圖在顯示處理有害訊息時，這些函數的主控台輸出。

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### 手動處理有害訊息

您可以透過在函數中加入一個名為 `dequeueCount` 的 `int` 參數，來取得訊息已被挑選以供處理的次數。然後您可以檢查函數程式碼中的清除佇列計數，並在數目超出臨界值時自行執行有害訊息處理，如下面的範例所示。

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

若要讓此程式碼依照預期執行，您必須提高自動處理有害訊息的重試次數上限，否則此範例中的取消佇列計數將一律不會超過 5。

## <a id="config"></a> 如何設定組態選項

您可以使用 `JobHostConfiguration` 類型來設定下列組態選項：

* 在程式碼中設定 SDK 連接字串。
* 設定 `QueueTrigger` 設定，例如取消佇列計數上限。
* 從組態取得佇列名稱。

### <a id="setconnstr"></a>在程式碼中設定 SDK 連接字串

在程式碼中設定 SDK 連接字串可讓您在組態檔或環境變數中使用您自己的連接字串名稱，如下面範例所示。

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

### <a id="configqueue"></a>設定佇列設定

您可以設定下列套用至佇列訊息處理的設定：

- 被同時挑選以供平行執行的佇列訊息數目上限 (預設值為 16)。
- 將佇列訊息傳送至有害佇列前的重試次數上限 (預設值為 5)。
- 當佇列已空時再次輪詢之前的等待時間上限 (預設值為 1 分鐘)。

下面的範例示範如何設定這些設定：

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>從組態取得佇列名稱

`JobHostConfiguration` 類型可讓您將提供佇列名稱的 `NameResolver` 物件傳入 SDK，以供 `QueueTrigger` 與 `Queue` 屬性使用。

例如，假設您想要在測試環境中使用名為 logqueuetest 的佇列，以及在生產環境中使用名為 logqueueprod 的佇列。您不想使用硬式編碼的佇列名稱，而想要在 `appSettings` 集合中指定會擁有實際佇列名稱之項目的名稱。如果 `appSettings` 索引鍵為 logqueue，您的函數看起來可能像下面的範例。

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

您的 `NameResolver` 類別就會從 `appSettings` 取得佇列名稱，如下面的範例所示：

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

您可以將 `NameResolver` 類別傳入 `JobHost` 物件，如下面的範例所示。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>如何手動觸發函數

若要手動觸發函數，請在函數的 `JobHost` 物件與 `NoAutomaticTrigger` 屬性上使用 `Call` 或 `CallAsync` 方法，如下面的範例所示。 

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
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>如何寫入記錄檔

若要寫入在 WebJobs 儀表板頁面中顯示，並連結至特定函數叫用的記錄檔，請使用從您的方法簽章中的函數取得的 `TextWriter` 物件。

若要寫入[應用程式追蹤記錄](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)，請使用 `Console.Out` (會建立標記為 INFO 的記錄檔) 和 `Console.Error` (會建立標記為 ERROR 的記錄檔)。替代方法是使用 [Trace 或 TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)。

應用程式記錄檔會出現在網站記錄檔、Azure 表格或 Azure Blob 中，視您設定 Azure 網站的方式而定。如果程式正在 Azure WebJob 中執行，儀表板中也會顯示最近的 100 個應用程式記錄檔。(儀表板中不會顯示正在本機執行，或正在其他環境中執行之程式的應用程式記錄檔。)   

您可以[將儀表板連線字串設為空值]來停用記錄(#config)。

下面的範例示範寫入記錄檔的幾種方式：

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

在 WebJobs SDK 儀表板中，當您移到某個特定函數叫用的頁面並按一下 [切換輸出]**** 時，就會顯示來自 `TextWriter` 物件的輸出：

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

在 WebJobs SDK 儀表板中，當您移到 WebJob (而非函數叫用) 的頁面並按一下 [切換輸出]**** 時，則會顯示應用程式記錄檔最近的 100 行。
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

在連續 WebJob 中，應用程式記錄檔會顯示在網站檔案系統的 /data/jobs/continuous/*{webjobname}*/job_log.txt 中。

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

在 Azure Blob 中，應用程式記錄檔看起來像這樣：
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

而在 Azure 資料表中，`Console.Out` 與 `Console.Error` 記錄檔看起來像這樣：

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 後續步驟

本主題已經提供示範如何處理使用 Azure 佇列之一般情況的程式碼範例。如需如何使用 Azure WebJobs 與 WebJobs SDK 的相關詳細資訊，請參閱 [Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。

<!--HONumber=35.2-->
