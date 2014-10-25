<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# 建置 Azure 電子郵件服務應用程式的背景工作角色 A (電子郵件排程器) - 4 of 5。

這是一系列教學課程的第四個教學課程 (共五個)，說明如何建置和部署 Azure 電子郵件服務範例應用程式。如需應用程式和教學課程系列的詳細資訊，請參閱[系列的第一個教學課程][]。

在本教學課程中，您將了解：

-   如何查詢和更新 Azure 儲存體資料表。
-   如何將工作項目新增至佇列，以供另一個背景工作角色處理。
-   如何透過覆寫 `OnStop` 方法來處理計劃性關閉。
-   如何確定遇到非計劃性關機時不會漏傳電子郵件或多傳電子郵件。
-   如何透過伺服器總管來測試使用 Azure 儲存體資料表的背景工作角色。

您已在建立雲端服務專案時建立背景工作角色 A 專案。因此，您現在只需要對該背景工作角色進行程式設計。

## 本教學課程章節

-   [將參考加入 Web 專案][]
-   [加入 SendEmail 模型][]
-   [新增在背景工作角色啟動時所執行的程式碼][]
-   [測試背景工作角色 A][]
-   [後續步驟][]

## <a name="addref"></a><span class="short-header">新增專案參考</span>新增 Web 專案的參考

您需要 Web 專案參考，因為這是定義實體類別的位置。您將在背景工作角色 B 中使用相同的實體類別，以在應用程式所使用的 Azure 資料表中讀取和寫入資料。

**注意：**在生產應用程式中，建議不要從背景工作角色專案中設定 Web 專案的參考，因為如此會連背景工作角色中一些您不想要或不需要的相依組件也參考到。您通常會將共用模型類別保留在類別庫專案中，而 Web 與背景工作角色專案則會參考類別庫專案。為了簡化方案結構，本教學課程將模型類別儲存在 Web 專案中。

1.  以滑鼠右鍵按一下 WorkerRoleA 專案，選擇 [加入]，然後選擇 [參考]。

2.  在 [參考管理員] 中，將參考加入 MvcWebRole 專案，然後按一下 [確定]。

    ![Add reference to MvcWebRole][]

## <a name="addmodel"></a>加入 SendEmail 模型

背景工作角色 A 會在 `Message` 資料表中建立 `SendEmail` 資料列，而背景工作角色 B 會讀取這些資料列，以取得傳送電子郵件時所需的資訊。下圖顯示 `Message` 資料表中兩個 `Message` 資料列和三個 `SendEmail` 資料列的一小部分屬性。

![含有 sendmail 的訊息資料表][]

`Message` 資料表中的這些資料列有數種用途：

-   提供背景工作角色 B 傳送單一電子郵件所需的一切資訊。
-   追蹤是否已傳送電子郵件，以防止背景工作角色在因故障而重新啟動後傳送重複項目。
-   讓背景工作角色 A 可以在判斷已針對某訊息傳送所有電子郵件後，將該訊息標記為 `Complete`。

為了讀取和寫入 `SendEmail` 資料列，必須有個模型類別。因為該模型類別必須可供背景工作角色 A 和背景工作角色 B 存取，而且所有其他模型類別都是定義於 Web 專案中，所以將此模型類別同樣定義於 Web 專案中很合理。

1.  在 Web 專案的 *Models* 資料夾中，從所下載的專案加入 *SendEmail.cs* 檔。

這裡的程式碼類似其餘模型類別，差別在於不含 DataAnnotations 屬性，因為此模型沒有相關聯的 UI；它不是用在 MVC 控制器中。

        public class SendEmail : TableEntity
        {
            public long MessageRef { get; set; }
            public string EmailAddress { get; set; }
            public DateTime? ScheduledDate { get; set; }
            public String FromEmailAddress { get; set; }
            public string SubjectLine { get; set; }
            public bool? EmailSent { get; set; }
            public string SubscriberGUID { get; set; }
            public string ListName { get; set; }
        }

## <a name="addcode"></a><span class="short-header">新增背景工作角色程式碼</span>新增在背景工作角色啟動時執行的程式碼

在 WorkerRoleA 專案中，專案範本已使用下列程式碼建立 *WorkerRole.cs*：

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");

                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }

            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;

                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

                return base.OnStart();
            }
        }

這是背景工作角色的預設範本程式碼。在 `OnStart` 方法中，您可以放入只有在背景工作角色執行個體啟動時才執行的初始化程式碼，而 `Run` 方法會在 `OnStart` 方法完成之後呼叫。您會將這些程式碼取代為自己的初始化與執行程式碼。

1.  刪除 WorkerRoleA 專案中的 *WorkerRole.cs*，然後從所下載的專案加入 *WorkerRoleA.cs* 檔。

### OnStart 方法

`OnStart` 方法會初始化您在使用 Azure 儲存體實體時所需的內容物件。其也會確定您將在 `Run` 方法中使用的所有資料表、佇列與 Blob 容器皆存在。執行這些工作的程式碼類似您稍早在 MVC 控制器建構函式中看到的程式碼。您將設定此方法稍後會使用的連接字串。

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 

            return base.OnStart();
        }

您可能已在早期關於使用 Azure 儲存體的文件中，看到以迴圈來檢查傳輸錯誤的初始化程式碼。這已不再需要，因為 API 現在有個內建重試機制，可以額外嘗試最多 3 次來化解暫時性網路失敗。

`OnStart` 方法所呼叫的 `ConfigureDiagnostics` 方法會設定追蹤，讓您能夠查看 `Trace.Information` 和 `Trace.Error` 方法的輸出。[第二個教學課程][]中會說明此方法。

[ServicePointManager.DefaultConnectionLimit][] 設定指定可在 .NET 中開啟之並行 TCP 連線的最大數目。[ServicePointManager][] 類別的文件說明背景工作角色執行個體連線的每個唯一主機都是個別連線。例如，這個背景工作角色會有三個並行連線，分別用於資料表、Blob 和佇列。對於某些連線 (例如 SQL Server 連線)，用戶端軟體會進行連線共用，如此可減少 `ServicePointManager` 所管理的並行連線數目。`DefaultConnectionLimit` 的最佳數目部分取決於您連線的後端服務。對於某些服務，具有 500 個開啟的連線可能不會造成問題，但其他服務可能只有 5 個連線便會超過負荷。在許多情況下，每個處理器一般建議最多使用 12 個連線。

### OnStop 方法

`OnStop` 方法會將全域變數 `onStopCalled` 設定為 true，然後等待 `Run` 方法將全域變數 `returnedFromRunMethod` 設定為 true (表示已準備好執行正常關機)。

        public override void OnStop()
        {
            onStopCalled = true;
            while (returnedFromRunMethod == false)
            {
                System.Threading.Thread.Sleep(1000);
            }
        }

當背景工作角色因下列其中一個原因而要關閉時，會呼叫 `OnStop` 方法：

-   Azure 需要重新啟動虛擬機器 (Web 角色或背景工作角色執行個體) 或是虛擬機器所裝載於的實體電腦。
-   您已使用 Azure 管理入口網站上的 [停止] 按鈕來停止雲端服務。
-   您已將更新部署至雲端服務專案。

`Run` 方法會監視 `onStopCalled` 變數，並在該變數變更為 `true` 時停止提取任何新的工作項目進行處理。`OnStop` 與 `Run` 方法之間的這項協調可讓工作者處理序得以正常關閉。

Azure 會定期安裝作業系統更新，以確保平台安全、可靠且效能良好。這些更新通常需要將雲端服務所裝載於的電腦關機再重新啟動。如需詳細資訊，請參閱[角色執行個體由於作業系統升級而重新啟動][] (英文)。

### Run 方法

`Run` 方法會執行兩個功能：

-   掃描 `message` 資料表以尋找排定在今天 (含) 以前傳送，但尚未建立佇列工作項目的訊息。

-   掃描 `message` 資料表以尋找在狀態中指出已建立所有佇列工作項目，但尚未傳送所有電子郵件的訊息。如果找到，則會掃描該訊息的 `SendEmail` 資料列確認是否已傳送所有電子郵件，如果已傳送，則會將狀態更新為 `Completed` 並封存 `message` 資料列。

這個方法也會檢查全域變數 `onStopCalled`。當該變數為 `true` 時，這個方法會停止提取新的工作項目進行處理，並在已啟動的工作完成時傳回。

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }
                    }

                    // Sleep for one minute to minimize query costs. 
                    System.Threading.Thread.Sleep(1000 * 60);
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

請注意，所有工作都是以 `while` 區塊中的無限迴圈完成，而 `while` 區塊中的所有程式碼都包裝在 `try`-`catch` 區塊中，以防止發生無法處理的例外狀況。如果發生無法處理的例外狀況，則 Azure 會引發 [UnhandledException][] 事件、終止工作者處理序，並使角色離線。Azure 將會重新啟動背景工作角色，但是這需要數分鐘的時間。`try` 區塊會呼叫 `TraceError` 以記錄錯誤，然後睡眠 60 秒，因此，如果錯誤持續發生，錯誤訊息並不會重複出現太多次。在生產應用程式中，您可以在 `try` 區塊中傳送電子郵件給系統管理員。

`Run` 方法會處理查詢，來找出 `message` 資料表中排程日期為明天之前的 `message` 資料列：

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**注意：**將處理後的訊息資料列移到 `messagearchive` 資料表的優點之一，是這個查詢只需要指定 `PartitionKey` 和 `RowKey` 作為搜尋條件。如果我們未將處理過的資料列封存起來，則查詢還必須指定非索引鍵欄位 (`Status`)，而且需要搜尋更多資料列。資料表大小將會增加，而查詢將會需要較久時間並可能開始取得接續 Token。

如果訊息處於 `Pending` 狀態，則表示處理尚未開始；如果處於 `Queuing` 狀態，則表示稍早已開始處理，但是在尚未建立所有佇列訊息時就遭到中斷。在該情況下，背景工作角色 B 在即將傳送每封電子郵件時，必須執行一項額外檢查，以確定尚未傳送電子郵件。這就是 `restartFlag` 變數的用途。

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

接下來，這個程式碼會將處於 `Pending` 狀態的 `message` 資料列設為 `Queuing`。然後，它會針對這些資料列以及任何已處於 `Queuing` 狀態的資料列，呼叫 `ProcessMessage` 方法來建立佇列工作項目，以針對訊息傳送電子郵件。

                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }

這個程式碼在處理處於 `Queuing` 狀態的訊息之後，會將 `Message` 資料列狀態設定為 `Processing`。`message` 資料表中未處於 `Pending` 或 `Queuing` 狀態的資料列已處於 `Processing` 狀態，而這個程式碼會針對這些資料列呼叫一個方法，來檢查是否已針對訊息傳送所有電子郵件。如果已傳送所有電子郵件，則會封存 `message` 資料列。

此程式碼在處理查詢所擷取的所有記錄之後，會睡眠一分鐘。

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

每個 Azure 儲存體查詢即使未傳回任何資料，都還是會產生基本費用，因此連續重新掃描可能會使您的 Azure 費用無謂增加。在撰寫本教學課程時，每 100 萬筆交易的成本是 $0.10 美元 (一項查詢算一筆交易)，因此，睡眠時間可能會變成遠小於一分鐘，而在資料表中掃描是否有要傳送的訊息時所產生的成本還是很少。如需價格的詳細資訊，請參閱[第一個教學課程][系列的第一個教學課程]。

**執行緒和最佳 CPU 使用率的注意事項：**`Run` 方法中有兩項工作 (將電子郵件排入佇列以及檢查是否有已完成的訊息)，而這兩項工作會在單一執行緒中循序執行。小型虛擬機器 (VM) 有 1.75 GB 的 RAM 而且只有 1 顆 CPU，因此以單一執行緒循序執行這些工作或許沒問題。假設您的應用程式若要有效率地執行，所需要的記憶體比小型 VM 所提供的記憶體還要多。中型 VM 提供 3.5 GB 的 RAM 以及 2 顆 CPU，但是此應用程式只會使用 1 顆 CPU，因為它採用單一執行緒。若要利用所有 CPU，您需要為每個 CPU 分別建立一個背景工作執行緒。即使如此，單一 CPU 也不會完全被一個執行緒用掉。執行緒在進行網路或 I/O 呼叫時，必須等待 I/O 或網路呼叫完成，而在這段等待期間，它不會執行有用的工作。如果 `Run` 方法是以兩個執行緒來實作，則在其中一個執行緒等待網路或 I/O 作業完成時，另一個執行緒可以執行有用的工作。

### ProcessMessage 方法

`ProcessMessage` 方法會取得目的地電子郵件清單的所有電子郵件地址，並為每個電子郵件地址分別建立一個佇列工作項目。它在建立佇列工作項目時，也會在 `Message` 資料表中建立 `SendEmail` 資料列。這些資料列會將傳送電子郵件時所需的資訊提供給背景工作角色 B，而這些資訊包括一個 `EmailSent` 屬性來追蹤是否已傳送每封電子郵件。

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };

                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);
            }

            Trace.TraceInformation("ProcessMessage end PK: "
                + messageToProcess.PartitionKey);
        }

這個程式碼會先從目的地郵寄清單的 `mailinglist` 資料表中取得郵寄清單資料列。此列具有「寄件者」電子郵件地址，該電子郵件必須提供給背景工作角色 B 來傳送電子郵件。

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

然後，它會在 `mailinglist` 資料表中查詢目的地郵寄清單的所有訂閱者資料列。

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

在處理查詢結果的迴圈中，此程式碼會開始檢查訂閱者電子郵件地址是否已通過驗證，如果沒有，則不會將任何電子郵件排入佇列。

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

接下來，這個程式碼會在 `message` 資料表中建立 `SendEmail` 資料列。此列包含背景工作角色 B 將用來傳送電子郵件的資訊。所建立的資料列會具有設為 `false` 的 `EmailSent` 屬性。

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

此程式碼使用 "upsert" 作業是因為如果背景工作角色 A 因故障而重新啟動，列可能已存在。

對於每個電子郵件地址，最後要執行的工作就是建立佇列工作項目，以觸發背景工作角色 B 來傳送電子郵件。這個佇列工作項目包含剛建立之 `SendEmail` 資料列的資料分割索引鍵與資料列索引鍵值，以及稍早設定的重新啟動旗標。`SendEmail` 資料列包含背景工作角色 B 傳送電子郵件時所需的一切資訊。

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### CheckAndUpdateStatusIfComplete 方法

`CheckAndUpdateStatusIfComplete` 方法會檢查處於 Processing 狀態的訊息，確認是否已傳送所有電子郵件。如果找不到未傳送的電子郵件，則會將列狀態更新為 `Completed` 並封存資料列。

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
            string pkrkFilter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
            var query = new TableQuery<SendEmail>().Where(pkrkFilter);
            var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

            if (emailToBeSent != null)
            {
                return;
            }

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>測試背景工作角色 A

1.  按 F5 鍵執行應用程式。

2.  使用各系統管理員網頁來建立郵寄清單以及建立郵寄清單的訂閱者。請將至少一個訂閱者的 `Verified` 屬性設定為 `true`，並將電子郵件地址設定為您可收到郵件的電子郵件地址。

    要等到您實作背景工作角色 B 後才會有電子郵件傳送，但是您將使用相同的測試資料來測試背景工作角色 B。

3.  建立要傳送到您所建立郵寄清單的訊息，並將排程日期設為今天或過去某個日期。

    ![New message in pending status][]

4.  在稍微超過一分鐘後 (因為 Run 方法有一分鐘的睡眠時間)，重新整理 [訊息] 網頁，您就會看到狀態變更為 Processing。(您可能會看到它先變更為 Queuing，但是從 Queuing 變到 Processing 的速度通常很快，讓您來不及看到 Queuing。)

    ![New message in processing status][]

5.  開啟 [伺服器總管]，然後開啟開發儲存體的節點。

6.  展開 [佇列]，以滑鼠右鍵按一下 **azuremailqueue**，然後按一下 [檢視佇列]。

    您會看見目的地電子郵件清單中的每個已驗證訂閱者都有一則佇列訊息。

    ![Queue message in ASE][]

7.  按兩下佇列訊息。

    您會看到佇列訊息的內容：資料分割索引鍵 (日期)、資料列索引鍵 (MessageRef 值加電子郵件地址) 和重新啟動旗標，彼此以逗號分隔。

    ![Queue message contents in ASE][]

8.  關閉 [檢視訊息] 對話方塊。

9.  展開 [資料表] 節點，以滑鼠右鍵按一下 [訊息] 資料表，然後按一下 [檢視資料表]。

    您會看到已排程的訊息 (資料列索引鍵中包含 "message")，後面接著一個資料列來代表每個已驗證的訂閱者 (資料列索引鍵中包含電子郵件地址)。

10. 按兩下資料列索引鍵中包含 "message" 的資料列，以查看 Web 角色所建立之資料列的內容。

    ![訊息資料列][]

11. 按兩下資料列索引鍵中包含電子郵件地址的資料列，以查看背景工作角色 A 所建立之 `SendEmail` 資料列的內容。

    ![SendEmail row in Message table][]

## <a name="nextsteps"></a>後續步驟

您現在已建置背景工作角色 A，並已確認它會建立背景工作角色 B 傳送電子郵件時所需的佇列訊息與資料表列。在[下一個教學課程][]中，您將建置和測試背景工作角色 B。

如需使用 Azure 儲存體資料表、佇列和 Blob 的其他資源連結，請參閱[本系列的最後一個教學課程][下一個教學課程]。

<div><a href="/zh-tw/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">教學課程 5</a></div>

  [系列的第一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/1-overview/
  [將參考加入 Web 專案]: #addref
  [加入 SendEmail 模型]: #addmodel
  [新增在背景工作角色啟動時所執行的程式碼]: #addcode
  [測試背景工作角色 A]: #testing
  [後續步驟]: #nextsteps
  [Add reference to MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [含有 sendmail 的訊息資料表]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [第二個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/zh-tw/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/zh-tw/library/system.net.servicepointmanager.aspx
  [角色執行個體由於作業系統升級而重新啟動]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [UnhandledException]: http://msdn.microsoft.com/zh-tw/library/system.appdomain.unhandledexception.aspx
  [New message in pending status]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [New message in processing status]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [Queue message in ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [Queue message contents in ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [訊息資料列]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [SendEmail row in Message table]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [下一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
