<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# 建置 Azure Email Service 應用程式的背景工作角色 B (電子郵件寄件者) - 5/5

這是一系列教學課程的第五個教學課程 (共五個)，顯示如何建置和部署 Azure Email Service 範例應用程式。如需應用程式和教學課程系列的詳細資訊，請參閱[系列的第一個教學課程][系列的第一個教學課程]。

在本教學課程中，您將了解：

-   如何將背景工作角色新增至雲端服務專案。
-   如何輪詢佇列以及處理佇列中的工作項目。
-   如何使用 SendGrid 傳送電子郵件。
-   如何透過覆寫 `OnStop` 方法來處理計劃性關閉。
-   如何透過確定未傳送重複電子郵件來處理非計劃關閉。

## 本教學課程章節

-   [將背景工作角色 B 專案加入方案][將背景工作角色 B 專案加入方案]
-   [將參考加入 Web 專案][將參考加入 Web 專案]
-   [將 SendGrid NuGet 封裝加入專案][將 SendGrid NuGet 封裝加入專案]
-   [加入專案設定][加入專案設定]
-   [新增在背景工作角色啟動時所執行的程式碼][新增在背景工作角色啟動時所執行的程式碼]
-   [測試背景工作角色 B][測試背景工作角色 B]
-   [後續步驟][後續步驟]

## <a name="addworkerrole"></a><span class="short-header">新增背景工作角色 B</span>將背景工作角色 B 專案新增至方案

1.  在 [方案總管] 中，以滑鼠右鍵按一下雲端服務專案，然後選擇 [新的背景工作角色專案]。

    ![New worker role project menu][New worker role project menu]

2.  在 [新增角色專案] 對話方塊中，依序選取 [C#]、[背景工作角色]，將專案命名為 WorkerRoleB，然後按一下 [新增]。

    ![New role project dialog box][New role project dialog box]

## <a name="addreference"></a>將參考加入 Web 專案

您需要 Web 專案參考，因為這是定義實體類別的位置。您在背景工作角色 B 中將使用實體類別，讀取和寫入 Azure 資料表中由應用程式使用的資料。

1.  以滑鼠右鍵按一下 WorkerRoleB 專案，然後選擇 [加入參考]。

2.  在 [參考管理員] 中，將參考加入 MvcWebRole 專案。

    ![Add reference to MvcWebRole][Add reference to MvcWebRole]

## <a name="addsendgrid"></a>將 SendGrid NuGet 封裝加入專案

若要使用 SendGrid 傳送電子郵件，您必須安裝 SendGrid NuGet 封裝。

1.  在 [方案總管] 中以滑鼠右鍵按一下 WorkerRoleB 專案，然後選擇 [管理 NuGet 封裝]。

    ![Manage NuGet Packages][Manage NuGet Packages]

2.  在 [管理 NuGet 封裝] 對話方塊中，選取 [線上] 索引標籤，在搜尋方塊中輸入 "sendgrid"，然後按 Enter 鍵。

3.  在 **SendGrid** 封裝上按一下 [安裝]。

    ![Install the Sendgrid package][Install the Sendgrid package]

4.  關閉對話方塊。

## <a name="addsettings"></a>加入專案設定

如同背景工作角色 A，背景工作角色 B 也必須要有儲存帳號認證，才能使用資料表、佇列和 Blob。此外，若要傳送電子郵件，背景工作角色必須將認證內嵌在對 SendGrid 服務的呼叫中。而若要建構要在傳送的電子郵件中加入的取消訂閱連結，背景工作角色必須得知應用程式的 URL。這些值均儲存在專案設定中。

儲存帳號認證的相關程序，與[第三個教學課程][第三個教學課程]所提供的程序相同。

1.  在 [方案總管] 中，於雲端專案中的 [角色] 下，以滑鼠右鍵按一下 **WorkerRoleB**，然後選擇 [屬性]。

2.  選取 [設定] 索引標籤。

3.  確定已選取 [服務組態] 下拉式清單中的 [所有組態]。

4.  選取 [設定] 索引標籤，然後按一下 [加入設定]。

5.  在 [名稱] 欄位中，輸入 "StorageConnectionString"。

6.  選取 [類型] 下拉式清單中的 [連接字串]。

7.  按一下該行右邊的省略符號 (**...**) 按鈕，以開啟 [儲存體帳戶連接字串] 對話方塊。

8.  在 [Create Storage Connection String] 對話方塊中，按一下 [Azure 儲存體模擬器] 選項按鈕，然後按一下 [確定]。

接著，您必須建立並設定背景工作角色 B 專用的三項新設定。

1.  在 [屬性] 視窗的 [設定] 索引標籤中，按一下 [新增設定]，然後新增 [字串] 類型的三項新設定：

    -   **名稱**：SendGridUserName、**值**：您在[第二個教學課程][第二個教學課程]中建立的 SendGrid 使用者名稱。

    -   **名稱**：SendGridPassword、**值**：SendGrid 密碼。

    -   **名稱**：AzureMailServiceURL、**值**：應用程式在您加以部署時所將具備的基礎 URL，例如：<http://sampleurl.cloudapp.net>。

    ![New settings in WorkerRoleB project][New settings in WorkerRoleB project]

## <a name="addcode"></a>加入在背景工作角色啟動時所執行的程式碼

1.  在 WorkerRoleB 專案中，刪除 WorkerRole.cs。

2.  在 WorkerRoleB 專案中，從所下載的專案加入 WorkerRoleB.cs 檔。

3.  建置方案。

    如果您收到建置錯誤，可能表示您有多個版本的 Azure 儲存體 NuGet 封裝。在這個情況下，請移至 NuGet 封裝管理員解決問題。請選取已安裝的封裝，然後向下捲動以查看是否有兩個 Azure 儲存體封裝執行個體。選取 Azure Storage 4.0.0 項目，並從安裝所在的專案中加以刪除。然後選取 Azure Storage 3.0.x 項目，並在遺漏的專案中加以安裝。關閉並重新啟動 Visual Studio，然後重新建置方案。

4.  確定雲端服務專案仍是方案的啟始專案。

### Onstart 方法

如同您在背景工作角色 A 中看到的，`OnStart` 方法會初始化使用 Azure 儲存體實體所需的內容類別。這個方法也可確定您在 `Run` 方法中所需的所有資料表、佇列和 Blob 容器確實存在。

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

與背景工作角色 A 的差別在於，背景工作角色 B 新增了 Blob 容器，且在資源間尚無訂閱佇列存在時必須加以建立。您將會使用 Blob 容器取得內含 HTML 和純文字郵件本文的檔案。訂閱佇列則會用來傳送訂閱確認電子郵件。

### Run 方法

`Run` 方法會處理來自兩個佇列的工作項目：傳送至電子郵件清單的訊息 (背景工作角色 A 所建立的工作項目) 所使用的佇列，以及用於訂閱確認電子郵件 (MvcWebRole 專案中的訂閱 API 方法所建立的工作項目) 的佇列。

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

此程式碼會在無限迴圈中執行，直到背景工作角色關閉為止。如果在主要佇列中發現工作項目，程式碼會加以處理，然後檢查訂閱佇列。

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

如果兩個佇列中都沒有等候的項目，程式碼會在睡眠 60 秒後繼續執行迴圈。

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

設置睡眠時間的目的，是要盡可能降低 Azure 儲存體交易成本，如[上一個教學課程][上一個教學課程]所說明。

當 [GetMessage][GetMessage] 方法從佇列中提取佇列項目時，所有存取該佇列的其他背景工作和 Web 角色將有 30 秒的時間看不見該佇列項目。這是為了確保只有一個背景工作角色執行個體可提取給定的佇列訊息，以進行處理。您可以將[可見度逾時][可見度逾時]參數傳遞至 `GetMessage` 方法，以明確設定這個「獨佔租用」時間 (看不見佇列項目的時間)。如果背景工作角色處理佇列訊息所需的時間可能超過 30 秒，您應增加獨佔租用時間，以防止其他角色執行個體處理相同訊息。

另一方面，獨佔租用時間也不應設為過大的值。例如，如果獨佔租用時間設為 48 小時，而您的背景工作角色在清除佇列中的訊息後非預期地關閉，則其他背景工作角色將有 48 小時無法處理訊息。獨佔租用上限為 7 天。

[GetMessages][GetMessages] 方法 (請留意名稱結尾加了 "s") 在一次呼叫中最多可從佇列中提取 32 個訊息。每次佇列存取都會產生少許交易成本，且無論傳回了 32 個訊息還是零個訊息，交易成本都是一樣的。下列程式碼會在一次呼叫中擷取最多 32 個訊息，並加以處理。

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

在使用 `GetMessages` 移除多個訊息時，請確定可見度逾時可讓您的應用程式有足夠的時間處理所有訊息。可見度逾時到期後，其他角色執行個體即可存取訊息，而一旦開始存取，第一個執行個體即無法在完成工作項目的處理後刪除訊息。

### ProcessQueueMessage 方法

`Run` 方法在主要佇列中發現工作項目時，會呼叫 `ProcessQueueMessage`：

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

有害訊息是指在進行處理時會導致應用程式擲回例外狀況的訊息。如果某個訊息從佇列中提取的次數超過五次，我們即假設該訊息無法處理，並且會從佇列中移除該訊息，而不再嘗試加以處理。生產應用程式應考慮將有害訊息移至「無效訊息」佇列進行分析，而不應刪除訊息。

下列程式碼會將佇列訊息剖析為擷取 SendEmail 資料列所需的分割索引鍵和資料列索引鍵，然後重新啟動旗標。

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

如果這個訊息在非預期的關閉後重新啟動，程式碼將會檢查 `messagearchive` 資料表，以判斷這封電子郵件是否已傳送。如果已傳送，則程式碼會刪除 `SendEmail` 資料列 (如果存在)，並刪除佇列訊息。

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

接下來，我們要取得 `message` 資料表中的 `SendEmail` 資料列。此資料列具有傳送電子郵件所需的所有資訊 (包含 HTML 和純文字電子郵件本文的 Blob 除外)。

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

接著，程式碼會傳送電子郵件並封存 `SendEmail` 資料列。

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

在交易中無法將資料列移至 messagearchive 資料表，因為這會對多個資料表造成影響。

最後，如果其他各項皆成功執行，則會刪除佇列訊息。

            sendEmailQueue.DeleteMessage(msg);

### SendEmailToList 方法

使用 SendGrid 傳送電子郵件的實際工作，可藉由 `SendEmailToList` 方法來完成。如果您要使用 SendGrid 以外的服務，您只需變更此方法中的程式碼即可。

**注意：**如果您在專案設定中的認證無效，您對 SendGrid 的呼叫將會失敗，但系統不會指出應用程式的失敗。如果您在生產應用程式中使用 SendGrid，請考慮為 Web API 設定個別的認證，以避免在系統管理員變更其 SendGrid 使用者帳號密碼時導致無訊息失敗。如需詳細資訊，請參閱 [SendGrid MultiAuth - 多個帳號認證][SendGrid MultiAuth - 多個帳號認證]。您可以在 [][]<https://sendgrid.com/credentials></a> 上設定認證。

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

在 `GetBlobText` 方法中，程式碼會取得 Blob 大小，然後使用該值初始化 `MemoryStream` 物件，以確保效能。如果您未提供大小，`MemoryStream` 將會配置 256 位元組，後續若下載項目超出這個值，則會再配置 512 位元組，依此類推，每次配置的數量都倍增。就大型 Blob 而言，相較於在下載之初即配置正確數量，前述程序較無效率。

### ProcessSubscribeQueueMessage 方法

`Run` 方法在訂閱佇列中發現工作項目時，會呼叫 `ProcessSubscribeQueueMessage`：

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

此方法會執行下列工作：

-   如果訊息是「有害」訊息，則加以記錄並刪除。
-   從佇列訊息中取得訂閱者 GUID。
-   使用 GUID 從 MailingList 資料表中取得訂閱者資訊。
-   將確認電子郵件傳送給新的訂閱者。
-   刪除佇列訊息。

如同傳送至清單的電子郵件，電子郵件的實際傳送也會以個別方法執行，以便您在需要時對不同的電子郵件服務進行變更。

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>測試背景工作角色 B

1.  按 F5 鍵執行應用程式。

2.  移至 [訊息] 頁面，以檢視您建立用以測試背景工作角色 A 的訊息。約一分鐘後，請重新整理網頁，您會發現資料列已從清單中消失，因為該資料列已封存。

3.  查看您預期會收到電子郵件的電子郵件收件匣。請注意，使用 SendGrid 的電子郵件傳送或對您電子郵件用戶端的傳遞可能會有所延遲，因此可能要稍候片刻才能看到電子郵件。您也可能需要查看垃圾郵件資料夾。

## <a name="nextsteps"></a>後續步驟

現在您已從頭建置 Azure Email Service 應用程式，而其結果與下載已完成的專案相同。若要部署至雲端、在雲端中測試，並升級至生產環境，您可以使用[第二個教學課程][第二個教學課程]中的相同程序。

如需示範如何在 Azure 儲存體資料表服務佇列中使用 LINQ 的範例應用程式，請參閱 [PhluffyFotos][PhluffyFotos] (英文)。

若要深入了解 Azure 儲存體，請參閱下列資源：

-   [Windows Azure 儲存體須知][Windows Azure 儲存體須知] (Bruno Terkaly 的部落格)

若要深入了解 Azure 資料表服務，請參閱下列資源：

-   [Azure 資料表儲存體須知][Azure 資料表儲存體須知] (Bruno Terkaly 的部落格) (英文)
-   [如何充分發揮 Windows Azure 資料表的效益][如何充分發揮 Windows Azure 資料表的效益] (Azure 儲存體團隊部落格) (英文)
-   [如何在 .NET 中使用資料表儲存體服務][如何在 .NET 中使用資料表儲存體服務]
-   [Windows Azure 儲存體用戶端程式庫 2.0 資料表深入探討][Windows Azure 儲存體用戶端程式庫 2.0 資料表深入探討] (Azure 儲存體團隊部落格) (英文)
-   [Real World:設計 Azure 資料表儲存體的可調整分割策略][Real World:設計 Azure 資料表儲存體的可調整分割策略] (英文)

若要深入了解 Azure 佇列服務和 Azure 服務匯流排佇列，請參閱下列資源：

-   [以佇列為中心的工作模式 (運用 Windows Azure 建構真實的雲端應用程式)][以佇列為中心的工作模式 (運用 Windows Azure 建構真實的雲端應用程式)]
-   [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對照][Azure 佇列和 Azure 服務匯流排佇列 - 比較和對照]
-   [如何在 .NET 中使用佇列儲存體服務][如何在 .NET 中使用佇列儲存體服務]

若要深入了解 Azure Blob 服務，請參閱下列資源：

-   [非結構化 Blob 儲存體 (運用 Windows Azure 建構真實的雲端應用程式)][非結構化 Blob 儲存體 (運用 Windows Azure 建構真實的雲端應用程式)]
-   [如何在 .NET 中使用 Azure Blob 儲存體服務][如何在 .NET 中使用 Azure Blob 儲存體服務]

若要深入了解 Azure 雲端服務角色的自動調整，請參閱下列資源：

-   [如何使用自動調整應用程式區塊][如何使用自動調整應用程式區塊]
-   [自動調整與 Azure][自動調整與 Azure]
-   [使用 Azure 建置彈性、可自動調整的方案][使用 Azure 建置彈性、可自動調整的方案] (MSDN 第 9 頻道視訊)

## <a name="Acknowledgments"></a><span class="short-header">答謝</span>答謝

這些教學課程和範例應用程式由 [Rick Anderson][Rick Anderson] 和 Tom Dykstra 所撰寫。在此答謝下列人員的協助：

-   Barry Dorrans (Twitter [@blowdart][@blowdart])
-   [Cory Fowler][Cory Fowler] (Twitter [@SyntaxC4][@SyntaxC4] )
-   [Joe Giardino][Joe Giardino]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][Scott Hunter] (Twitter：[@coolcsh][@coolcsh])
-   [Brian Swan][Brian Swan]
-   [Daniel Wang][Daniel Wang]
-   提供意見反應的 Developer Advisory Council 成員：
   * Damir Arh
   * Jean-Luc Boucho
   * Carlos dos Santos
   * Mike Douglas
   * Robert Fite
   * Gianni Rosa Gallina
   * Fabien Lavocat
   * Karl Ots
   * Carlos-Alejandro Perez
   * Sunao Tomita
   * Perez Jones Tsisah
   * Michiel van Otegem

  [系列的第一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/1-overview/
  [將背景工作角色 B 專案加入方案]: #addworkerrole
  [將參考加入 Web 專案]: #addreference
  [將 SendGrid NuGet 封裝加入專案]: #addsendgrid
  [加入專案設定]: #addsettings
  [新增在背景工作角色啟動時所執行的程式碼]: #addcode
  [測試背景工作角色 B]: #testing
  [後續步驟]: #nextsteps
  [New worker role project menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [New role project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Add reference to MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [Manage NuGet Packages]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Install the Sendgrid package]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [第三個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [第二個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [New settings in WorkerRoleB project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [上一個教學課程]: /zh-tw/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee741827.aspx
  [可見度逾時]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - 多個帳號認證]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Windows Azure 儲存體須知]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Azure 資料表儲存體須知]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [如何充分發揮 Windows Azure 資料表的效益]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [如何在 .NET 中使用資料表儲存體服務]: http://www.windowsazure.com/zh-tw/develop/net/how-to-guides/table-services/
  [Windows Azure 儲存體用戶端程式庫 2.0 資料表深入探討]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Real World:設計 Azure 資料表儲存體的可調整分割策略]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh508997.aspx
  [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對照]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh767287.aspx
  [如何在 .NET 中使用佇列儲存體服務]: /zh-tw/develop/net/how-to-guides/queue-service/
  [如何在 .NET 中使用 Azure Blob 儲存體服務]: /zh-tw/develop/net/how-to-guides/blob-storage/
  [如何使用自動調整應用程式區塊]: /zh-tw/develop/net/how-to-guides/autoscaling/
  [自動調整與 Azure]: http://msdn.microsoft.com/zh-tw/library/hh680945(v=PandP.50).aspx
  [使用 Azure 建置彈性、可自動調整的方案]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
