<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Step 5: Worker Role B" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 5: Worker role B" metaKeywords="Azure tutorial, adding working role cloud service, C# worker role" description="The fifth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role B (email sender) for the Azure Email Service application - 5 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

建置 Azure Email Service 應用程式的背景工作角色 B (電子郵件寄件者) - 5/5
========================================================================

這是一系列教學課程的第五個教學課程 (共五個)，顯示如何建置和部署 Azure Email Service 範例應用程式。如需應用程式和教學課程系列的詳細資訊，請參閱[系列的第一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。

在本教學課程中，您將了解：

-   如何將背景工作角色新增至雲端服務專案。
-   如何輪詢佇列以及處理佇列中的工作項目。
-   如何使用 SendGrid 傳送電子郵件。
-   如何透過覆寫 `OnStop` 方法來處理計劃關閉。
-   如何透過確定未傳送重複電子郵件來處理非計劃關閉。

新增背景工作角色 B將背景工作角色 B 專案新增至方案
-------------------------------------------------

1.  在 [方案總管] 中，以滑鼠右鍵按一下雲端服務專案，然後選擇 **[新的背景工作角色專案]**。

    ![New worker role project menu](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png)

2.  在 **[新增角色專案]** 對話方塊中，依序選取 **[C\#]**、**[背景工作角色]**，將專案命名為 WorkerRoleB，然後按一下 **[新增]**。

    ![New role project dialog box](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png)

新增參考新增 Web 專案參考
-------------------------

您需要 Web 專案參考，因為這是定義實體類別的位置。您在背景工作角色 B 中將使用實體類別，讀取和寫入 Azure 資料表中由應用程式使用的資料。

1.  在 WorkerRoleB 專案上按一下滑鼠右鍵，然後選擇 **[新增參考]**。

    ![Add reference in WorkerRoleB project](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png)

2.  在 **[參考管理員]** 中，新增 MvcWebRole 專案參考 (如果您是在 Azure 網站中執行 Web UI，則是 Web 應用程式專案參考)。

    ![Add reference to MvcWebRole](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png)

新增 SCL 2.0 封裝將儲存體用戶端程式庫 2.0 NuGet 封裝新增至專案
--------------------------------------------------------------

> [WACOM.NOTE] 對於 Visual Studio 2013，您可以略過本節，因為目前的 Azure 儲存體封裝會安裝在新的背景工作角色專案中。

當您新增專案時，專案並不會自動取得儲存體用戶端程式庫 NuGet 封裝的更新版本。它會取得舊有的 1.7 版封裝，因為這是專案範本中所包含的版本。現在，方案會提供兩種版本的 Azure 儲存體 NuGet 封裝：MvcWebRole 和 WorkerRoleA 專案中的 2.0 版，以及 WorkerRoleB 專案中的 1.7 版。在 WorkerRoleB 專案中，您必須解除安裝 1.7 版，並安裝 2.0 版。

1.  從 **[工具]** 功能表中選擇 **[Library Package Manager]**，然後選擇 **[Manage NuGet Packages for Solution]**。

2.  在左窗格中選取 **[Installed Packages]**，然後向下捲動至 Azure 儲存體封裝。

    您會看見該封裝列出兩次；1.7 版和 2.0 版各有一個。

3.  選取 1.7 版的封裝，然後按一下 **[管理]**。

    此時會清除 MvcWebRole 和 WorkerRoleB 的核取方塊，並選取 WorkerRoleB 的核取方塊。

4.  清除 WorkerRoleB 的核取方塊，然後按一下 **[確定]**。

5.  系統詢問您是否要解除安裝相依封裝時，請按一下 **[否]**。

    解除安裝完成後，您的 NuGet 對話方塊中只會有 2.0 版的封裝。

6.  對 2.0 版的封裝按一下 **[管理]**。

    此時會選取 MvcWebRole 和 WorkerRoleA 的核取方塊，並清除 WorkerRoleA 的核取方塊。

7.  選取 WorkerRoleA 的核取方塊，然後按一下 **[確定]**。

新增 SCL 1.7 參考新增 SCL 1.7 組件參考
--------------------------------------

> [WACOM.NOTE] 如果您已安裝最新的 SDK，並使用 Visual Studio 2013，請略過本節。

2.0 版的儲存體用戶端程式庫 (SCL) 並未具備診斷所需的所有項目，因此您必須新增其中一個 1.7 版組件的參考，如同您先前為其他兩個專案所做的一樣。

1.  在 WorkerRoleB 專案上按一下滑鼠右鍵，然後選擇 **[新增參考]**。

2.  按一下對話方塊底部的 **[瀏覽]** 按鈕。

3.  瀏覽到下列資料夾：

         C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  選取 *Microsoft.WindowsAzure.StorageClient.dll*，然後按一下 **[新增]**。

5.  在 **[參考管理員]** 對話方塊中，按一下 **[確定]**。

新增 SendGrid 封裝將 SendGrid NuGet 封裝新增至專案
--------------------------------------------------

若要使用 SendGrid 傳送電子郵件，您必須安裝 SendGrid NuGet 封裝。

1.  在 **[方案總管]** 中以滑鼠右鍵按一下 WorkerRoleB 專案，然後選擇 **[管理 NuGet 封裝]**。

    ![Manage NuGet Packages](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png)

2.  在 **[管理 NuGet 封裝]** 對話方塊中，選取 **[線上]** 索引標籤，在搜尋方塊中輸入 "sendgrid"，然後按 Enter 鍵。

3.  在 **SendGrid** 封裝上按一下 **[安裝]**。

    ![Install the Sendgrid package](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png)

4.  關閉對話方塊。

新增專案設定新增專案設定
------------------------

如同背景工作角色 A，背景工作角色 B 也必須要有儲存帳號認證，才能使用資料表、佇列和 Blob。此外，若要傳送電子郵件，背景工作角色必須將認證內嵌在對 SendGrid 服務的呼叫中。而若要建構要在傳送的電子郵件中加入的取消訂閱連結，背景工作角色必須得知應用程式的 URL。這些值均儲存在專案設定中。

儲存帳號認證的相關程序，與[第三個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage)所提供的程序相同。

1.  在 **[方案總管]** 中，於雲端專案中的 **[角色]** 下，以滑鼠右鍵按一下 **WorkerRoleB**，然後選擇 **[屬性]**。

2.  選取 **[設定]** 索引標籤。

3.  確定已選取 **[服務組態]** 下拉式清單中的 **[所有組態]**。

4.  選取 **[設定]** 索引標籤，然後按一下 **[加入設定]**。

5.  在 **[名稱]** 欄位中，輸入 "StorageConnectionString"。

6.  選取 **[類型]** 下拉式清單中的 **[連接字串]**。

7.  按一下該行右邊的省略符號 (**...**) 按鈕，以開啟 **[儲存體帳戶連接字串]** 對話方塊。

8.  在 **[Create Storage Connection String]** 對話方塊中，按一下 **[Your subscription]** 選項按鈕。

9.  選擇您為 Web 角色和背景工作角色 A 選擇的相同 **[訂閱]** 和 **[Account name]**。

10. 依照相同的程序，進行 **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** 連接字串的設定。

    接著，您必須建立並設定背景工作角色 B 專用的三項新設定。

11. 在 **[屬性]** 視窗的 **[設定]** 索引標籤中，按一下 **[新增設定]**，然後新增 **[字串]** 類型的三項新設定：

    -   **名稱**：SendGridUserName、**值**：您在[第二個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)中建立的 SendGrid 使用者名稱。

    -   **名稱**：SendGridPassword、**值**：SendGrid 密碼。

    -   **名稱**：AzureMailServiceURL、**值**：應用程式在您加以部署時所將具備的基礎 URL，例如：http://sampleurl.cloudapp.net。

    ![New settings in WorkerRoleB project](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png)

### 新增在背景工作角色啟動時所執行的程式碼

1.  在 WorkerRoleB 專案中，刪除 WorkerRole.cs。

2.  在 WorkerRoleB 專案上按一下滑鼠右鍵，然後選擇 **[新增現有項目]**。

    ![Add existing item to Worker Role B](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png)

3.  瀏覽到範例應用程式下載到的資料夾，並在 WorkerRoleB 專案中選取 WorkerRoleB.cs 檔案，然後按一下 **[新增]**。

> [WACOM.NOTE] 對於具有最新 SDK 和最新 SendGrid NuGet 封裝的 Visual Studio 2013，請開啟 *WorkerRoleB.cs*，並對程式碼進行下列變更：(1) 刪除 `SendGridMail.Transport` 的 `using` 陳述式。(2) 將 `SendGrid.GenerateInstance` 的兩個執行個體都變更為 `SendGrid.GetInstance`。(3) 將 `REST.GetInstance` 的兩個執行個體都變更為 `Web.GetInstance`。

1.  開啟 WorkerRoleB.cs，並檢查程式碼。

    如同您在背景工作角色 A 中看到的，`OnStart` 方法會初始化使用 Azure 儲存體實體所需的內容類別。此方法也可確定您在 `Run` 方法中所需的所有資料表、佇列和 Blob 容器確實存在。

    與背景工作角色 A 的差別在於，背景工作角色 B 新增了 Blob 容器，且在資源間尚無訂閱佇列存在時必須加以建立。您將會使用 Blob 容器取得內含 HTML 和純文字郵件本文的檔案。訂閱佇列則會用來傳送訂閱確認電子郵件。

         public override bool OnStart()
         {
        ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

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

        Trace.TraceInformation("WorkerB:Creating blob container, queue, tables, if they don't exist.");
        this.blobContainer.CreateIfNotExists();
        this.sendEmailQueue.CreateIfNotExists();
        this.subscribeQueue.CreateIfNotExists();
        var messageTable = tableClient.GetTableReference("Message");
        messageTable.CreateIfNotExists();
        var mailingListTable = tableClient.GetTableReference("MailingList");
        mailingListTable.CreateIfNotExists();

        return base.OnStart();
         }

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
        System.Threading.Thread.Sleep(1000*60);
                     }
                 }
        catch (Exception ex)
                 {
        string err = ex.Message;
        if (ex.InnerException != null)
                     {
        err += " Inner Exception:" + ex.InnerException.Message;
                     }
        if (msg != null)
                     {
        err += " Last queue message retrieved:" + msg.AsString;
                     }
        Trace.TraceError(err);
        // Don't fill up Trace storage if we have a bug in either process loop.
        System.Threading.Thread.Sleep(1000*60);
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
        System.Threading.Thread.Sleep(1000*60);
                     }

    設置睡眠時間的目的，是要盡可能降低 Azure 儲存體交易成本，如[上一個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)所說明。

    當 [GetMessage](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee741827.aspx) 方法從佇列中提取佇列項目時，所有存取該佇列的其他背景工作和 Web 角色將有 30 秒的時間看不見該佇列項目。這是為了確保只有一個背景工作角色執行個體可提取給定的佇列訊息，以進行處理。您可以將[可見度逾時](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758454.aspx)參數傳至 `GetMessage` 方法，以明確設定此*獨佔租用*時間 (看不見佇列項目的時間)。如果背景工作角色處理佇列訊息所需的時間可能超過 30 秒，您應增加獨佔租用時間，以防止其他角色執行個體處理相同訊息。

    另一方面，獨佔租用時間也不應設為過大的值。例如，如果獨佔租用時間設為 48 小時，而您的背景工作角色在清除佇列中的訊息後非預期地關閉，則其他背景工作角色將有 48 小時無法處理訊息。獨佔租用上限為 7 天。

    [GetMessages](http://msdn.microsoft.com/zh-tw/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx) 方法 (請留意名稱結尾加了 "s") 在一次呼叫中最多可從佇列中提取 32 個訊息。每次佇列存取都會產生少許交易成本，且無論傳回了 32 個訊息還是零個訊息，交易成本都是一樣的。下列程式碼會在一次呼叫中擷取最多 32 個訊息，並加以處理。

    foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))

         {
        ProcessQueueMessage(msg);
        messageFound = true;
         }

    在使用 `GetMessages` 移除多個訊息時，請確定可見度逾時可讓您的應用程式有足夠的時間處理所有訊息。可見度逾時到期後，其他角色執行個體即可存取訊息，而一旦開始存取，第一個執行個體即無法在完成工作項目的處理後刪除訊息。

    `Run` 方法在主要佇列中發現工作項目時，會呼叫 `ProcessQueueMessage`：

         private void ProcessQueueMessage(CloudQueueMessage msg)
         {
        // Log and delete if this is a "poison" queue message (repeatedly processed
        // and always causes an error that prevents processing from completing).
        // Production applications should move the "poison" message to a "dead message"
        // queue for analysis rather than deleting the message.           
        if (msg.DequeueCount > 5)
             {
        Trace.TraceError("Deleting poison message:message {0} Role Instance {1}.",
        msg.ToString(), GetRoleInstance());
        sendEmailQueue.DeleteMessage(msg);
        return;
             }
        // Parse message retrieved from queue.
        // Example:2012-01-01,0123456789email@domain.com,0
        var messageParts = msg.AsString.Split(new char[] { ',' });
        var partitionKey = messageParts[0];
        var rowKey = messageParts[1];
        var restartFlag = messageParts[2];
        Trace.TraceInformation("ProcessQueueMessage start:partitionKey {0} rowKey {1} Role Instance {2}.",
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
        Trace.TraceInformation("Email already sent:partitionKey=" + partitionKey + " rowKey= " + rowKey);
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
        Trace.TraceError("SendEmail row not found:partitionKey {0} rowKey {1} Role Instance {2}.",
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

        Trace.TraceInformation("ProcessQueueMessage complete:partitionKey {0} rowKey {1} Role Instance {2}.",
        partitionKey, rowKey, GetRoleInstance());
         }

    有害訊息是指在進行處理時會導致應用程式擲回例外狀況的訊息。如果某個訊息從佇列中提取的次數超過五次，我們即假設該訊息無法處理，並且會從佇列中移除該訊息，而不再嘗試加以處理。生產應用程式應考慮將有害訊息移至「無效訊息」佇列進行分析，而不應刪除訊息。

    下列程式碼會將佇列訊息剖析為擷取 SendEmail 資料列所需的分割索引鍵和資料列索引鍵，然後重新啟動旗標。

             var messageParts = msg.AsString.Split(new char[] { ',' });
        var partitionKey = messageParts[0];
        var rowKey = messageParts[1];
        var restartFlag = messageParts[2];

    如果此訊息在非預期的關閉後重新啟動，程式碼將會檢查 `messagearchive` 資料表，以判斷此電子郵件是否已傳送。如果已傳送，則程式碼會刪除 `SendEmail` 資料列 (如果存在)，並刪除佇列訊息。

             if (restartFlag == "1")
             {
        var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
        var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
        var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
        if (messagearchiveRow != null)
                 {
        Trace.TraceInformation("Email already sent:partitionKey=" + partitionKey + " rowKey= " + rowKey);
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
        Trace.TraceError("SendEmail row not found:partitionKey {0} rowKey {1} Role Instance {2}.",
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

    使用 SendGrid 傳送電子郵件的實際工作，可藉由 `SendEmailToList` 方法來完成。如果您要使用 SendGrid 以外的服務，您只需變更此方法中的程式碼即可。

    **注意：**如果您在專案設定中的認證無效，您對 SendGrid 的呼叫將會失敗，但系統不會指出應用程式的失敗。如果您在生產應用程式中使用 SendGrid，請考慮為 Web API 設定個別的認證，以避免在系統管理員變更其 SendGrid 使用者帳號密碼時導致無訊息失敗。如需詳細資訊，請參閱 [SendGrid MultiAuth - 多個帳號認證](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials)。您可以在 <https://sendgrid.com/credentials> 上設定認證。

         private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
        string htmlMessageBodyRef, string textMessageBodyRef)
         {
        var email = SendGrid.GenerateInstance();
        email.From = new MailAddress(fromEmailAddress);
        email.AddTo(emailAddress);
        email.Html = GetBlobText(htmlMessageBodyRef);
        email.Text = GetBlobText(textMessageBodyRef);
        email.Subject = subjectLine;
        var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
        RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
        var transportREST = REST.GetInstance(credentials);
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

    在 `GetBlobText` 方法中，程式碼會取得 Blob 大小，然後使用該值初始化 `MemoryStream` 物件，以確保效能。如果您未提供此大小，`MemoryStream` 將會配置 256 位元組，後續若下載項目超出此值，則會再配置 512 位元組，依此類推，每次配置的數量都倍增。就大型 Blob 而言，相較於在下載之初即配置正確數量，前述程序較無效率。

    `Run` 方法在訂閱佇列中發現工作項目時，會呼叫 `ProcessSubscribeQueueMessage`：

         private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
         {
        // Log and delete if this is a "poison" queue message (repeatedly processed
        // and always causes an error that prevents processing from completing).
        // Production applications should move the "poison" message to a "dead message"
        // queue for analysis rather than deleting the message.  
        if (msg.DequeueCount > 5)
             {
        Trace.TraceError("Deleting poison subscribe message:message {0}.",
        msg.AsString, GetRoleInstance());
        subscribeQueue.DeleteMessage(msg);
        return;
             }
        // Parse message retrieved from queue.Message consists of
        // subscriber GUID and list name.
        // Example:57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
        var messageParts = msg.AsString.Split(new char[] { ',' });
        var subscriberGUID = messageParts[0];
        var listName = messageParts[1];
        Trace.TraceInformation("ProcessSubscribeQueueMessage start:subscriber GUID {0} listName {1} Role Instance {2}.",
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

        Trace.TraceInformation("ProcessSubscribeQueueMessage complete:subscriber GUID {0} Role Instance {1}.",
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
        var email = SendGrid.GenerateInstance();
        email.From = new MailAddress(mailingList.FromEmailAddress);
        email.AddTo(subscriber.EmailAddress);
        string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
        "/subscribe
         id=" + subscriberGUID + "&listName=" + subscriber.ListName;
        email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
        "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
        "<a href= private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
         {
        var email = SendGrid.GenerateInstance();
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
        var transportREST = REST.GetInstance(credentials);
        transportREST.Deliver(email);
         }
        quot;{1} private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
         {
        var email = SendGrid.GenerateInstance();
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
        var transportREST = REST.GetInstance(credentials);
        transportREST.Deliver(email);
         }
        quot;>Confirm Subscription</a>", mailingList.Description, subscribeURL);
        email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
        "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
        "{1}", mailingList.Description, subscribeURL);
        email.Subject = "Subscribe to " + mailingList.Description;
        var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
        var transportREST = REST.GetInstance(credentials);
        transportREST.Deliver(email);
         }

測試測試背景工作角色 B
----------------------

1.  按 F5 鍵執行應用程式。

2.  移至 **[訊息]** 頁面，以檢視您建立用以測試背景工作角色 A 的訊息。約一分鐘後，請重新整理網頁，您會發現資料列已從清單中消失，因為該資料列已封存。

3.  查看您預期會收到電子郵件的電子郵件收件匣。請注意，使用 SendGrid 的電子郵件傳送或對您電子郵件用戶端的傳遞可能會有所延遲，因此可能要稍候片刻才能看到電子郵件。您也可能需要查看垃圾郵件資料夾。

後續步驟後續步驟
----------------

現在您已從頭建置 Azure Email Service 應用程式，而其結果與下載已完成的專案相同。若要部署至雲端、在雲端中測試，並升級至生產環境，您可以使用[第二個教學課程](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)中的相同程序。如果您選擇建置替代架構，請參閱 [Azure 網站開始使用教學課程](/en-us/develop/net/tutorials/get-started)，以了解如何將 MVC 專案部署至 Azure 網站。

若要深入了解 Azure 儲存體，請參閱下列資源：

-   [Windows Azure 儲存體須知](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (Bruno Terkaly 的部落格)

若要深入了解 Azure 資料表服務，請參閱下列資源：

-   [Azure 資料表儲存體須知](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (Bruno Terkaly 的部落格) (英文)
-   [如何充分發揮 Windows Azure 資料表的效益](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (Azure 儲存體團隊部落格) (英文)
-   [如何在 .NET 中使用資料表儲存體服務](http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/)
-   [Windows Azure 儲存體用戶端程式庫 2.0 資料表深入探討](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (Azure 儲存體團隊部落格) (英文)
-   [Real World:設計 Azure 資料表儲存體的可調整分割策略](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh508997.aspx) (英文)

若要深入了解 Azure 佇列服務和 Azure 服務匯流排佇列，請參閱下列資源：

-   [以佇列為中心的工作模式 (運用 Windows Azure 建構真實的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
-   [Azure 佇列和 Azure 服務匯流排佇列 - 比較和對照](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh767287.aspx)
-   [如何在 .NET 中使用佇列儲存體服務](/en-us/develop/net/how-to-guides/queue-service/)

若要深入了解 Azure Blob 服務，請參閱下列資源：

-   [非結構化 Blob 儲存體 (運用 Windows Azure 建構真實的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
-   [如何在 .NET 中使用 Azure Blob 儲存體服務](/en-us/develop/net/how-to-guides/blob-storage/)

若要深入了解 Azure 雲端服務角色的自動調整，請參閱下列資源：

-   [如何使用自動調整應用程式區塊](/en-us/develop/net/how-to-guides/autoscaling/)
-   [自動調整與 Azure](http://msdn.microsoft.com/zh-tw/library/hh680945(v=PandP.50).aspx)
-   [使用 Azure 建置彈性、可自動調整的方案](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (MSDN 第 9 頻道視訊)

答謝答謝
--------

這些教學課程和範例應用程式由 [Rick Anderson](http://blogs.msdn.com/b/rickandy/) 和 Tom Dykstra 所撰寫。在此答謝下列人員的協助：

-   Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart))
-   [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) )
-   [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
-   Don Glover
-   Jai Haridas
-   [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter：[@coolcsh](http://twitter.com/coolcsh))
-   [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
-   [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
-   提供意見反應的 Developer Advisory Council 成員：
    -   Damir Arh
    -   Jean-Luc Boucho
    -   Carlos dos Santos
    -   Mike Douglas
    -   Robert Fite
    -   Gianni Rosa Gallina
    -   Fabien Lavocat
    -   Karl Ots
    -   Carlos-Alejandro Perez
    -   Sunao Tomita
    -   Perez Jones Tsisah
    -   Michiel van Otegem

