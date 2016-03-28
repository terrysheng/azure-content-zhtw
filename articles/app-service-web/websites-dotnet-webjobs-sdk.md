<properties 
	pageTitle="什麼是 Azure WebJobs SDK" 
	description="Azure WebJobs SDK 簡介。說明 SDK 是什麼、適用哪些典型案例，以及程式碼範例。" 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/14/2016" 
	ms.author="tdykstra"/>

# 什麼是 Azure WebJobs SDK

## <a id="overview"></a>概觀

本文說明 WebJobs SDK 是什麼、檢閱部分適用的典型案例，以及提供在程式碼中的使用方式概觀。

[WebJobs](websites-webjobs-resources.md) 是一項 Azure App Service 功能，可讓您在與 Web 應用程式、API 應用程式或行動應用程式相同的內容中執行程式或指令碼。[WebJobs SDK](websites-webjobs-resources.md) 的目的是為了簡化您為 WebJob 可執行的一般工作 (例如影像處理、佇列處理、RSS 彙總、檔案維護及傳送電子郵件) 撰寫的程式碼。WebJobs SDK 具有內建的功能，可與「Azure 儲存體」和「服務匯流排」搭配運作、用來排定工作和處理錯誤，以及處理許多其他常見的案例。此外，它的設計具有擴充性，而且有[擴充功能的開放原始碼儲存機制](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。

WebJobs SDK 包含下列元件：

* **NuGet 封裝**。您新增至 Visual Studio 主控台應用程式專案的 NuGet 封裝，會利用 WebJobs SDK 屬性修飾您的方法，提供一個供您程式碼使用的架構。
  
* **儀表板**。Azure App Service 中包含部分的 WebJobs SDK，該部份項目可針對使用 NuGet 封裝的程式提供豐富的監控和診斷功能。您無需撰寫程式碼就可以使用這些監視和診斷功能。

## <a id="scenarios"></a>案例

下列是 Azure WebJobs SDK 可協助您輕鬆處理的部分典型案例：

* 影像處理或其他需要大量 CPU 的工作。網站的一項常見功能是上傳影像或影片的能力。在許多時候，您想要在內容上傳後處理該內容，但又不想在您執行此作業時讓使用者等候。

* 佇列處理。Web 前端與後端服務的一個常見通訊方式是使用佇列。當網站需要完成工作時，它會將訊息推播至佇列。後端服務會從佇列提取訊息，並完成工作。您可以在影像處理中使用佇列：例如，在使用者上傳多個檔案之後，將檔案名稱放置於佇列訊息中，由後端挑選佇列訊息進行處理。或者您可以使用佇列來提升網站回應能力。例如，與其將目錄直接寫入 SQL Database，請寫入佇列並告知使用者已完成，然後由後端服務處理高延遲的關聯式資料庫工作。如需使用影像處理的佇列處理範例，請參閱 [WebJobs SDK 入門教學課程](websites-dotnet-webjobs-sdk-get-started.md)。

* RSS 彙總。如果您有維持 RSS 摘要清單的網站，您可以在背景處理序中提取摘要的所有文章。

* 檔案維護，例如彙總或清除記錄檔案。您可能擁有由數個網站在不同的時間範圍內所建立的記錄檔案，您想要結合這些檔案以便執行分析工作。或者您想要排程每週執行的工作，來將舊的記錄檔案清除。

* 輸入 Azure 資料表。您可能會有想要剖析的儲存檔案和 Blob，並想要將資料儲存在資料表中。輸入函數可能會寫入許多行 (在某些情況下可能有上百萬行)，而 WebJobs SDK 讓您可以輕易地實作此功能。SDK 還提供進度指標的即時監控，例如資料表中的寫入行數。

* 其他您想要在背景執行序中執行的長時間執行工作，例如[傳送電子郵件](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure)。

* 任何您想要依排程執行的工作，例如每晚執行備份作業。

在這些許多情況中，您可能會想要擴充 Web 應用程式以便在多個 VM 上執行，這會同時執行多個 WebJobs。在某些情況下，這可能會導致相同資料多次進行處理，但當您使用內建佇列、blob 和 WebJobs SDK 的服務匯流排觸發程序時，這不會造成問題。SDK 可確保您的函式針對每個訊息或 blob 僅會處理一次。

WebJobs SDK 也可讓您輕鬆地處理常見的錯誤處理案例。您可以設定警示以在函式失敗時傳送通知，還可以設定逾時，讓函式未在指定的時間限制內完成時自動取消。

## <a id="code"></a> 程式碼範例

使用 Azure 儲存體的處理傳統工作程式碼十分簡單。在您「主控台應用程式」的 `Main` 方法中，您會建立一個 `JobHost` 物件來協調對您所撰寫之方法的呼叫。WebJobs SDK 架構會根據您在方法中使用的 WebJobs SDK 屬性，知道何時要呼叫方法及要使用哪些參數值。SDK 提供可指定造成呼叫函式之條件的「觸發程序」，以及可指定如何將資訊傳入方法參數及從方法參數傳出的「繫結器」。

例如，[QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) 屬性會導致在佇列上收到訊息時呼叫函式，並且如果位元組陣列或自訂類型的訊息格式為 JSON，就會將該訊息自動還原序列化。每當 Azure 儲存體帳戶中有新的 Blob 建立時，[BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) 屬性就會觸發程序。

下列是個簡單程式，可用來輪詢佇列並為收到的每則佇列訊息建立 Blob：

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

`JobHost` 物件是一組背景功能的容器。`JobHost` 物件可監視功能、注意可觸發功能的事件，以及發生觸發事件時執行功能。您可以呼叫 `JobHost` 方法，指出您要在目前執行緒或背景執行緒上執行容器程序。在此範例中，`RunAndBlock` 方法會在目前執行緒上持續執行程序。

由於此範例中的 `ProcessQueueMessage` 方法具有 `QueueTrigger` 屬性，接收新的佇列訊息時便會觸發該功能。`JobHost` 物件會注意指定佇列 (在此範例中是 "webjobsqueue") 上的新佇列訊息，找到新佇列訊息時，此物件便會呼叫 `ProcessQueueMessage`。

`QueueTrigger` 屬性會將 `inputText` 參數繫結至佇列訊息的值。而 `Blob` 屬性則會將 `TextWriter` 物件繫結至名為 "containername" 容器中名為 "blobname" 的 Blob。

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
		    [Blob("containername/blobname")]TextWriter writer)

此功能會接著使用這些參數來將佇列訊息的值寫入 Blob：

		writer.WriteLine(inputText);

WebJobs SDK 的觸發程序和繫結器功能可大幅簡化您所必須撰寫的程式碼。WebJobs SDK 架構會為您完成處理佇列、Blob 或檔案或是起始已排定的工作所需的低階程式碼。例如，此架構會建立尚未存在的佇列、開啟佇列、讀取佇列訊息、在處理完成後刪除佇列訊息、建立尚未存在的 Blob 容器、寫入 Blob 等。

下列程式碼範例以一個 WebJob 示範各種不同的觸發程序：`QueueTrigger`、`FileTrigger`、`WebHookTrigger` 及 `ErrorTrigger`。

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a> 排程

`TimerTrigger` 屬性可讓您依排程觸發函式執行。您可以透過 Azure 來進行 WebJob 的整體排程，或是使用 WebJobs SDK `TimerTrigger` 來排定 WebJob 的個別函式。以下是程式碼範例。

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

如需詳細範例程式碼，請參閱 GitHub.com 上 azure-webjobs-sdk-extensions 存放庫中的 [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs)。

## 擴充性

您並不受限於使用內建功能 -- WebJobs SDK 可讓您撰寫自訂的觸發程序和繫結器。例如，您可以撰寫用於快取事件和定期排程的觸發程序。[開放原始碼存放庫](https://github.com/Azure/azure-webjobs-sdk-extensions)包含[關於 WebJobs SDK 擴充性的詳細指南](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)和範例程式碼，可幫助您開始撰寫自己的觸發程序和繫結器。

## <a id="workerrole"></a>在 WebJobs 外部使用 WebJobs SDK

使用 WebJobs SDK 的程式是指可在任意位置執行的標準主控台應用程式 -- 它不一定要以 WebJob 的形式執行。您可以在開發電腦上本機測試程式，並在實際執行環境中，以雲端服務背景工作角色或 Windows 服務的身分執行此程式 (如果您慣用其中一個環境)。

不過，對於 Azure App Service Web 應用程式，儀表板只能做為延伸模組來使用。如果您想要在 WebJob 外部執行但仍然使用儀表板，則您可以設定 Web 應用程式使用 WebJobs SDK 儀表板連接字串所參考的相同儲存體帳戶，接著該 Web 應用程式的 WebJobs 儀表板便會顯示有關這個異地執行程式中的執行函數資料。您可以使用 URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions 來進入儀表板。如需詳細資訊，請參閱[使用 WebJobs SDK 來取得本機開發的儀表板](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (英文)，但請注意，此部落格文章顯示舊的連接字串名稱。

## <a id="nostorage"></a>儀表板功能

即使您不使用 WebJobs SDK 觸發程序或繫結器，WebJobs SDK 仍可提供數個好處：

* 您可以從儀表板叫用函數。
* 您可以從儀表板轉送函數。
* 您可以在儀表板中檢視記錄檔，連結到特定的 WebJob (使用 Console.Out、Console.Error、Trace 等編寫的應用程式記錄檔) 或連結到產生它們的特定函式引動過程 (使用 SDK 傳遞至函數做為參數的 `TextWriter` 物件編寫的記錄檔)。 

如需詳細資訊，請參閱[如何手動叫用函數](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)和[如何寫入記錄](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>接續步驟

如需 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。

如需有關 WebJobs SDK 最新增強功能的資訊，請參閱[版本資訊](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)。
 

<!---HONumber=AcomDC_0316_2016-->