<properties 
	pageTitle="如何搭配使用 Azure 服務匯流排與 WebJobs SDK" 
	description="瞭解如何搭配使用 Azure 服務匯流排佇列和主題與 WebJobs SDK。" 
	services="web-sites, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# 如何搭配使用 Azure 服務匯流排與 WebJobs SDK

## 概觀

本指南提供 C# 程式碼範例，示範如何在建立或更新 Azure Blob 時觸發程序。此程式碼範例使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 1.x 版。

本指南假設您知道[如何使用指向您儲存體帳戶的連接字串，在 Visual Studio 中建立 WebJob 專案](websites-dotnet-webjobs-sdk-get-started.md)。

本範例的程式碼片段只會顯示函式，而非建立  `JobHost` 物件的程式碼：

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> 必要條件

若要使用服務匯流排，除了安裝其他 WebJobs SDK 封裝外，您還必須安裝 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet 封裝。 

除了儲存體連接字串外，您也必須設定 AzureWebJobsServiceBus 連接字串。

## <a id="trigger"></a> 如何在接收服務匯流排佇列訊息時觸發函式

若要撰寫接收佇列訊息時 WebJobs SDK 會呼叫的函式，請使用 `ServiceBusTrigger` 屬性。屬性建構函式會採用參數來指定要輪詢的佇列名稱。

### ServicebusTrigger 的運作方式

SDK 會在 `PeekLock` 模式中接收訊息，並在訊息上呼叫 `Complete` (如果成功完成函式) 或呼叫 `Abandon` (如果函式失敗)。如果函式執行時間超過 `PeekLock` 逾時，會自動更新鎖定。

服務匯流排沒有自己的有害佇列處理，如此一來既不受 WebJobs SDK 控制，也不會在 WebJobs SDK 中設定。 

### 字串佇列訊息

下列程式碼範例會讀取包含字串的佇列訊息，並將字串寫入 WebJobs SDK 儀表板。

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**注意：**如果您在沒有 WebJobs SDK 的應用程式中建立佇列訊息，請務必將 [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) 設為 "text/plain"。

### POCO 佇列訊息

SDK 會自動還原序列化為包含 POCO 之 JSON 的佇列訊息[(純舊 CLR 物件](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 類型。下列程式碼範例會讀取包含 `BlobInformation` 物件的佇列訊息，該物件具有 `BlobName` 屬性：

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

如需示範如何使用 POCO 屬性來處理相同函式中 Blob 和資料表的程式碼範例，請參閱[這篇文章的儲存體佇列版本](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs)。

### ServiceBusTrigger 使用的類型

除了 `string` 和 POCO  類型，您可以搭配使用 `ServiceBusTrigger` 屬性與位元組陣列或 `BrokeredMessage` 物件。

## <a id="create"></a> 如何建立服務匯流排佇列的訊息

若要撰寫可建立新佇列訊息的函式，請使用 `ServiceBus` 屬性，並將佇列名稱傳至屬性建構函式。 


### 在非同步函式中建立單一佇列訊息

下列程式碼範例會使用輸出參數，在名為 "outputqueue" 的佇列中建立新訊息，該訊息則與名為 "inputqueue" 的佇列中所接收的訊息具有相同的內容。

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

建立單一佇列訊息的輸出參數可以是下列任何類型：

* `string`
* `byte[]`
* `BrokeredMessage`
* 您定義的可序列化 POCO 類型。自動序列化為 JSON。

若為 POCO 類型參數，一定會在函式結束時建立佇列訊息；如果參數是 null，SDK 會建立在接收和還原序列化訊息將傳回 null 的佇列訊息。對於其他類型，如果參數是 null 則不會建立任何佇列訊息。

### 建立多個佇列訊息或非同步函式

若要建立多個訊息，請使用 `ServiceBus` 屬性與 `ICollector<T>` 或 `IAsyncCollector<T>`，如下列程式碼範例所示：

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

呼叫 `Add` 方法時會立即建立每個佇列訊息。

## <a id="topics"></a>如何使用服務匯流排主題

若要撰寫 SDK 在服務匯流排主題上收到訊息時所呼叫的函式，請將 `ServiceBusTrigger` 屬性與取得主題名稱和訂閱名稱的建構函式搭配使用，如下列程式碼範例所示：

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

若要在主題上建立訊息，請使用 `ServiceBus` 屬性與主題名稱，搭配的方式與該屬性與佇列名稱的使用方式相同。

## <a id="queues"></a>儲存體佇列作法文章涵蓋相關的主題

如需與特定服務匯流排無關的 WebJobs SDK 案例的相關資訊，請參閱[如何使用 Azure 佇列儲存體與 WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。 

該文章涵蓋下列主題：

* 非同步函數
* 多個執行個體
* 順利關機
* 在函式主體中使用 WebJobs SDK 屬性
* 在程式碼中設定 SDK 連接字串
* 在程式碼中設定 WebJobs SDK 建構函式參數的值
* 手動觸發函式
* 寫入記錄檔

## <a id="nextsteps"></a> 後續步驟

本指南提供了程式碼範例，示範如何處理 Azure 服務匯流排的常見使用案例。如需如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226) (英文)。

<!--HONumber=45--> 
