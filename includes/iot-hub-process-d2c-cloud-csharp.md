## 處理裝置到雲端訊息

在本節中，您將建立 Windows 主控台應用程式，可處理來自 IoT 中樞的裝置對雲端訊息。IoT 中樞會公開與[事件中樞]相容的端點，以讓應用程式讀取裝置對雲端訊息。本教學課程使用 [EventProcessorHost] 類別來處理主控台應用程式中的這些訊息。如需如何處理來自事件中樞的訊息之詳細資訊，您可以參考[開始使用事件中樞]教學課程。

實作可靠的資料點訊息儲存體或互動式訊息轉送時，您所面對的主要挑戰在於事件中樞的事件處理是依靠訊息取用者檢查其進度。此外，為達到高輸送量，當您從事件中樞讀取時，您應在大型批次中建立檢查點。如果發生失敗且您回復至先前的檢查點，這可能會導致重複處理大量訊息。在本教學課程中，您將會了解如何將 Azure 儲存體寫入及服務匯流排重複資料刪除時間範圍與 **EventProcessorHost** 檢查點同步處理。

為了要可靠地將訊息寫入至 Azure 儲存體，範例會使用[區塊 Blob][Azure Block Blobs] 的個別區塊認可功能。事件處理器會累積記憶體中的訊息，直到達到執行檢查點的時間為止 (例如在訊息的累積緩衝區達到 4 Mb 的區塊大小上限之後，或在超過服務匯流排重複資料刪除時間範圍之後)。然後，在檢查之前，程式碼會認可新區塊至 Blob。

事件處理器會使用事件中樞訊息位移做為區塊識別碼。這可以讓其在認可新區塊至儲存體之前，先執行重複資料刪除檢查，查看認可的區塊和檢查點之間是否有損毀。

> [AZURE.NOTE] 本教學課程使用單一儲存體帳戶來寫入從 IoT 中心擷取的所有訊息。請參閱 [Azure 儲存體延展性指導方針]來決定方案中是否需要使用多個 Azure 儲存體帳戶。

應用程式會使用服務匯流排重複資料刪除功能，來於處理互動式訊息時避免重複項目。模擬的裝置會藉由使用唯一的 **MessageId** 為每個互動式訊息加上時間戳記，讓服務匯流排能夠確保在指定的重複資料刪除時間範圍中，不會有兩個具有相同 **MessageId** 的訊息傳遞給接收者。此重複資料刪除功能和服務匯流排佇列所提供的每一訊息完成語意，使其能夠很容易地實作可靠的互動訊息處理。

為確保不會在重複資料刪除時間範圍之外重新提交任何訊息，程式碼會同步 **EventProcessorHost** 檢查點機制與服務匯流排佇列重複資料刪除時間範圍。完成此作業的方法是在每次重複資料刪除時間範圍過去時 (在本教學課程中為 1 小時)，強制檢查點至少執行一次。

> [AZURE.NOTE] 本教學課程使用單一分割服務匯流排佇列來處理所有擷取自 IoT 中心的互動式訊息。請參閱[服務匯流排文件]，以取得如何使用服務匯流排佇列來滿足您解決方案之擴展性需求的詳細資訊。

### 佈建 Azure 儲存體帳戶和服務匯流排佇列
為使用 [EventProcessorHost] 類別，您必須擁有 Azure 儲存體帳戶才能讓 **EventProcessorHost** 記錄檢查點資訊。您可以使用現有的儲存體帳戶，或是依照[關於 Azure 儲存體]中的指示建立新的帳戶。請記下儲存體帳戶連接字串。

您也需要服務匯流排佇列以可靠地處理互動式訊息。您可以程式設計方式使用 1 小時重複資料刪除視窗建立佇列，如同[如何使用服務匯流排佇列][Service Bus Queue]中所述，或遵循下列步驟來使用 [Azure 傳統入口網站]：

1. 按一下左下角的 [新增]，然後依序按一下 [應用程式服務]、[服務匯流排]、[佇列]、[自訂建立]，接著輸入名稱 **d2ctutorial**、選取區域、使用現有命名空間或建立一個新的命名空間，然後在下一頁選取 [啟用重複偵測]，並將 [重複的偵測記錄期間] 設為一小時。然後按一下核取記號以儲存您的佇列組態。

    ![][30]

2. 在服務匯流排佇列清單中，按一下 **d2ctutorial**，然後按一下 [設定]。建立兩個共用存取原則，一個名為**傳送**，具備**傳送**權限，而另一個名為**接聽**，具備**接聽**權限。完成時請按一下底部的 [儲存]。

    ![][31]

3. 按一下頂端的 [儀表板]，然後按一下底部的 [連接資訊]，並記下這兩個連接字串。

    ![][32]

### 建立事件處理器

1. 在目前的 Visual Studio 方案中，按一下 [檔案] -> [加入] -> [新增專案]，以使用 [主控台應用程式] 專案範本建立新的 Visual C# Windows 專案。將專案命名為 **ProcessDeviceToCloudMessages**。

    ![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ProcessDeviceToCloudMessages** 專案，然後按一下 [管理 NuGet 封裝]。就會顯示 [NuGet 封裝管理員] 對話方塊。

3. 搜尋 **WindowsAzure.ServiceBus**，按一下 [安裝] 並接受使用規定。這會下載、安裝並加入 [Azure 服務匯流排 NuGet 封裝](https://www.nuget.org/packages/WindowsAzure.ServiceBus)的參考與其所有相依性。

4. 搜尋 **Microsoft Azure 服務匯流排事件中樞 - EventProcessorHost**，並按一下 [安裝]，然後接受使用規定。這會下載、安裝並加入 [Azure 服務匯流排事件中樞 - EventProcessorHost NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)的參考與其所有相依性。

5. 以滑鼠右鍵按一下 **ProcessDeviceToCloudMessages** 專案、按一下 [新增]，然後按一下 [類別]。將新類別命名為 **StoreEventProcessor**，然後按一下 [確定] 以建立該類別。

6. 在 StoreEventProcessor.cs 檔案開頭處新增下列陳述式：

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. 將該類別的主體取代為下列程式碼：

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    **EventProcessorHost** 類別會呼叫此類別以處理從 IoT 中樞收到的裝置對雲端訊息。此類別中的程式碼會實作邏輯，以在 Blob 容器中可靠地儲存訊息，並將互動式訊息轉送至服務匯流排佇列。**OpenAsync** 方法會初始化 **currentBlockInitOffset** 變數，此變數可追蹤此事件處理器所讀取之第一個訊息的目前位移。請記住，每個處理器都會負責單一分割區。
    
    **ProcessEventsAsync** 方法可從 IoT 中樞接收一批訊息，並依照以下方式處理訊息：它會傳送互動式訊息至服務匯流排佇列，並將資料點訊息附加到名為 **toAppend** 的記憶體緩衝區。如果記憶體緩衝區達到 4 Mb 區塊限制，或者距離上一個檢查點的時間已超過服務匯流排重複資料刪除時間範圍 (在本教學課程中為 1 小時)，都會觸發檢查點。

    **AppendAndCheckpoint** 方法會先為要附加的區塊產生區塊識別碼。Azure 儲存體需要所有區塊識別碼都具有相同的長度，此方法才能以前置的零來填補位移 - `currentBlockInitOffset.ToString("0000000000000000000000000")`。如果具有此識別碼的區塊已在 Blob 中，此方法會使用緩衝區目前的內容將它覆寫。

    > [AZURE.NOTE] 若要簡化程式碼，本教學課程會使用每個分割的單一 blob 檔案來儲存訊息。實際的解決方案會實作檔案復原，使用的方法是在檔案到達特定大小時 (請注意，Azure 區塊 Blob 大小上限為 195 Gb)，或在某段時間之後建立其他檔案。

8. 在 **Program** 類別中，於頂端新增下列 **using** 陳述式：

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. 如下所示修改 **Program** 類別中的 **Main** 方法，使用名為 **d2ctutorial** 之佇列的 [傳送] 權限取代 IoT 中樞 **iothubowner** 連接字串 (來自 [開始使用 IoT 中樞] 教學課程)、儲存體連接字串，以及服務匯流排連接字串：

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```
    
    > [AZURE.NOTE] 為了簡單起見，本教學課程使用 [EventProcessorHost] 類別的單一執行個體。請參閱[事件中樞程式設計指南]了解詳細資訊。

## 接收互動式訊息
在本節中，您將撰寫 Windows 主控台應用程式，它會接收來自服務匯流排佇列的互動式訊息。如需有關如何使用服務匯流排架構解決方案的詳細資訊，請參考[使用服務匯流排建置多層式應用程式][]。

1. 在目前的 Visual Studio 方案中，使用 [主控台應用程式] 專案範本建立新的 Visual C# Windows 專案。將專案命名為 **ProcessD2CInteractiveMessages**。

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ProcessD2CInteractiveMessages** 專案，然後按一下 [管理 NuGet 封裝]。就會顯示 [NuGet 封裝管理員] 視窗。

3. 搜尋 **WindowsAzure.Service Bus**，按一下 [安裝] 並接受使用規定。這會下載及安裝 [Azure 服務匯流排](https://www.nuget.org/packages/WindowsAzure.ServiceBus)，並加入對它的參考和其所有相依性。

4. 在 **Program.cs** 檔案開頭處新增以下 **using** 陳述式：

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 最後，將下列幾行加入至 **Main** 方法，將連接字串取代為名為 **d2ctutorial** 之佇列的**接聽**權限：

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

<!-- Links -->
[關於 Azure 儲存體]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[開始使用事件中樞]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Azure 儲存體延展性指導方針]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[事件中樞]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[事件中樞程式設計指南]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[使用服務匯流排建置多層式應用程式]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[]: iot-hub-csharp-csharp-getstarted.md
[服務匯流排文件]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0309_2016-->