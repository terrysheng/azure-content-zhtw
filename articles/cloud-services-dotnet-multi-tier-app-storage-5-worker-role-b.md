<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Step 5: Worker Role B" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 5: Worker role B" metaKeywords="Azure tutorial, adding working role cloud service, C# worker role" description="The fifth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role B (email sender) for the Azure Email Service application - 5 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Azure 전자 메일 서비스 응용 프로그램에 대한 작업자 역할 B(전자 메일 보낸 사람) 구축 - 5/5.
==========================================================================================

Azure 전자 메일 서비스 샘플 응용 프로그램을 빌드 및 배포하는 방법을 보여 주는 5개 자습서 시리즈의 다섯 번째 자습서입니다. 이 응용 프로그램 및 자습서 시리즈에 대한 자세한 내용은 [시리즈의 첫 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)를 참조하십시오.

이 자습서에서는 다음에 대해 알아봅니다.

-   작업자 역할을 클라우드 서비스 프로젝트에 추가하는 방법
-   큐를 폴링하고 큐의 작업 항목을 처리하는 방법
-   SendGrid를 사용하여 전자 메일을 보내는 방법
-   `OnStop` 메서드를 재정의하여 계획된 종료를 처리하는 방법
-   중복 전자 메일이 전송되지 않았는지 확인하여 계획되지 않은 종료를 처리하는 방법

작업자 역할 B 추가솔루션에 작업자 역할 B 프로젝트 추가
------------------------------------------------------

1.  솔루션 탐색기에서 클라우드 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **새 작업자 역할 프로젝트**를 선택합니다.

    ![새 작업자 역할 프로젝트 메뉴](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png)

2.  **새 역할 프로젝트 추가** 대화 상자에서 **C\#**을 선택하고 **작업자 역할**을 선택하고 프로젝트 이름을 WorkerRoleB로 지정한 후 **추가**를 클릭합니다.

    ![새 역할 프로젝트 대화 상자](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png)

참조 추가웹 프로젝트에 참조 추가
--------------------------------

엔터티 클래스가 정의되어 있기 때문에 웹 프로젝트에 대한 참조가 필요합니다. 작업자 역할 B에서도 동일한 엔터티 클래스를 사용하여 응용 프로그램에서 사용되는 Azure 테이블의 데이터를 읽고 쓰게 됩니다.

1.  WorkerRoleB 프로젝트를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

    ![WorkerRoleB 프로젝트에 참조 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png)

2.  **참조 관리자**에서 MvcWebRole 프로젝트에(또는 Azure 웹 사이트에서 웹 UI를 실행 중인 경우 웹 응용 프로그램 프로젝트에) 참조를 추가합니다.

    ![MvcWebRole에 참조 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png)


SCL 2.0 패키지 추가프로젝트에 Storage Client Library 2.0 NuGet 패키지 추가
--------------------------------------------------------------------------

> [WACOM.NOTE] 현재 Azure 저장소 패키지가 새 작업자 역할 프로젝트에 설치되어 있으므로 Visual Studio 2013에서는 이 섹션을 건너뛸 수 있습니다.

프로젝트가 추가될 때 Storage Client Library NuGet 패키지의 업데이트 버전을 자동으로 가져오지 않습니다. 대신 패키지의 이전 1.7 버전이 프로젝트 템플릿에 포함되어 있으므로 이 버전을 가져왔습니다. 이제 솔루션에 두 가지 버전의 Azure 저장소 NuGet 패키지(MvcWebRole 및 WorkerRoleA 프로젝트의 2.0 버전과 WorkerRoleB 프로젝트의 1.7 버전)가 있습니다. WorkerRoleB 프로젝트에서 1.7 버전을 제거하고 2.0 버전을 설치해야 합니다.

1.  **도구** 메뉴에서 **라이브러리 패키지 관리자**, **솔루션의 NuGet 패키지 관리**를 차례로 선택합니다.

2.  왼쪽 창에서 선택된 **설치된 패키지**에서 Azure 저장소 패키지를 찾을 때까지 아래로 스크롤합니다.

    패키지가 1.7 버전용과 2.0 버전용으로 두 번 나열됩니다.

3.  1.7 버전의 패키지를 선택하고 **관리**를 클릭합니다.

    MvcWebRole 및 WorkerRoleB의 확인란은 선택이 취소되어 있고 WorkerRoleB의 확인란이 선택되어 있습니다.

4.  WorkerRoleB의 확인란 선택을 취소한 다음 **확인**을 클릭합니다.

5.  종속 패키지를 제거할지 묻는 메시지가 나타나면 **아니요**를 클릭합니다.

    제거가 완료되면 NuGet 대화 상자에 2.0 버전의 패키지만 남습니다.

6.  2.0 버전의 패키지에 대해 **관리**를 클릭합니다.

    MvcWebRole 및 WorkerRoleA의 확인란이 선택되어 있고, WorkerRoleA의 확인란은 선택이 취소되어 있습니다.

7.  WorkerRoleA의 확인란을 선택한 다음 **확인**을 클릭합니다.


SCL 1.7 참조 추가SCL 1.7 어셈블리에 대한 참조 추가
--------------------------------------------------

> [WACOM.NOTE] 최신 SDK를 설치했으며 Visual Studio 2013을 사용 중이면 이 섹션을 건너뛰십시오.

SCL(Storage Client Library) 2.0 버전에는 진단에 필요한 사항 중 일부만 있습니다. 따라서 다른 두 프로젝트에 대해 앞서 수행한 것처럼 1.7 어셈블리 중 하나에 대한 참조를 추가해야 합니다.

1.  WorkerRoleB 프로젝트를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

2.  대화 상자 아래쪽에 있는 **찾아보기...** 단추를 클릭합니다.

3.  다음 폴더로 이동합니다.

         C:\Program Files\Microsoft SDKs\Azure\.NET SDK012-10\ref

4.  *Microsoft.WindowsAzure.StorageClient.dll*을 선택한 후 **추가**를 클릭합니다.

5.  **참조 관리자** 대화 상자에서 **확인**을 클릭합니다.

SendGrid 패키지 추가프로젝트에 SendGrid NuGet 패키지 추가
---------------------------------------------------------

SendGrid를 사용하여 전자 메일을 보내려면 SendGrid NuGet 패키지를 설치해야 합니다.

1.  **솔루션 탐색기**에서 WorkerRoleB 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

    ![NuGet 패키지 관리](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png)

2.  **NuGet 패키지 관리** 대화 상자에서 **온라인** 탭을 선택하고 검색 상자에 "sendgrid"를 입력하고 Enter 키를 누릅니다.

3.  **SendGrid** 패키지에서 **설치**를 클릭합니다.

    ![Sendgrid 패키지를 설치합니다.](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png)

4.  대화 상자를 닫습니다.

프로젝트 설정 추가프로젝트 설정 추가
------------------------------------

작업자 역할 A와 마찬가지로 작업자 역할 B에 저장소 계정 자격 증명이 있어야 테이블, 큐, Blob 작업을 할 수 있습니다. 또한 전자 메일을 보내려면 작업자 역할에 SendGrid 서비스에 대한 호출에 포함할 자격 증명이 있어야 합니다. 보내는 전자 메일에 구독 취소 링크를 생성하려면 작업자 역할에서 응용 프로그램의 URL을 알아야 합니다. 이러한 값은 프로젝트 설정에 저장되어 있습니다.

저장소 계정 자격 증명 절차는 [세 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage)에서 보았던 절차와 동일합니다.

1.  **솔루션 탐색기**에서 클라우드 프로젝트의 **역할** 아래에서 **WorkerRoleB**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2.  **설정** 탭을 선택합니다.

3.  **서비스 구성** 드롭다운 목록에서 **모든 구성**이 선택되어 있는지 확인합니다.

4.  **설정** 탭을 선택한 후 **설정 추가**를 클릭합니다.

5.  **이름** 열에 "StorageConnectionString"을 입력합니다.

6.  **형식** 드롭다운 목록에서 **연결 문자열**을 선택합니다.

7.  줄의 오른쪽 끝에 있는 줄임표(**...**) 단추를 클릭하여 **저장소 계정 연결 문자열** 대화 상자를 엽니다.

8.  **저장소 연결 문자열 만들기** 대화 상자에서 **구독** 라디오 단추를 선택합니다.

9.  웹 역할과 작업자 역할 A에 대해 선택한 것과 동일한 **구독** 및 **계정 이름**을 선택합니다.

10. 동일한 절차에 따라 **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** 연결 문자열의 설정을 구성합니다.

    그런 다음 작업자 역할 B만 사용하는 새로운 설정 세 가지를 만들고 구성합니다.

11. **속성** 창의 **설정** 탭에서 **설정 추가**를 클릭한 다음 **문자열** 형식의 새 설정 세 가지를 추가합니다.

    -   **이름**: SendGridUserName, **값**: [두 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)에서 만든 SendGrid 사용자 이름입니다.

    -   **이름**: SendGridPassword, **값**: SendGrid 암호입니다.

    -   **이름**: AzureMailServiceURL, **값**: 응용 프로그램이 배포될 때 갖는 기준 URL입니다. 예: http://sampleurl.cloudapp.net.

    ![WorkerRoleB 프로젝트의 새 설정](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png)

### 작업자 역할 시작 시 실행되는 코드 추가

1.  WorkerRoleB 프로젝트에서 WorkerRole.cs를 삭제합니다.

2.  WorkerRoleB 프로젝트를 마우스 오른쪽 단추로 클릭하고 **기존 항목 추가**를 선택합니다.

    ![작업자 역할 B에 기존 항목 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png)

3.  샘플 응용 프로그램을 다운로드한 폴더로 이동하여 WorkerRoleB 프로젝트에서 WorkerRoleB.cs 파일을 선택한 후 **추가**를 클릭합니다.

> [WACOM.NOTE] 최신 SDK 및 SendGrid NuGet 패키지가 설치된 Visual Studio 2013의 경우 *WorkerRoleB.cs*를 열고 다음과 같이 코드를 변경합니다. (1) `SendGridMail.Transport`의 `using` 문을 삭제합니다. (2) `SendGrid.GenerateInstance`의 인스턴스를 둘 다 `SendGrid.GetInstance`로 변경합니다. (3) `REST.GetInstance`의 인스턴스를 둘 다 `Web.GetInstance`로 변경합니다.

1.  WorkerRoleB.cs를 열고 코드를 검토합니다.

    작업자 역할 A에서 본 것처럼 `OnStart` 메서드는 Azure 저장소 엔터티와 작동하기 위해 필요한 컨텍스트 클래스를 초기화합니다. 또한 `Run` 메서드에서 필요한 모든 테이블, 큐 및 Blob 컨테이너가 존재하도록 합니다.

    작업자 역할 A와의 차이는 Blob 컨테이너 및 구독 큐가 없을 경우 리소스 사이에서 이들을 추가하여 만든다는 점입니다. Blob 컨테이너를 사용하여 전자 메일 본문의 일반 텍스트 및 HTML이 포함된 파일을 가져옵니다. 구독 큐는 구독 확인 전자 메일을 보내는 데 사용됩니다.

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

    `Run` 메서드는 두 가지 큐의 작업 항목을 처리하는 데, 하나는 전자 메일 목록에 보낸 메시지(작업자 역할 A가 만든 작업 항목)에 사용되는 큐이고 다른 하나는 구독 확인 전자 메일(MvcWebRole 프로젝트의 구독 API 메서드가 만든 작업 항목)에 사용되는 큐입니다.

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
                         err += " Inner Exception: " + ex.InnerException.Message;
                     }
                     if (msg != null)
                     {
                         err += " Last queue message retrieved: " + msg.AsString;
                     }
                     Trace.TraceError(err);
                     // Don't fill up Trace storage if we have a bug in either process loop.
                     System.Threading.Thread.Sleep(1000*60);
                 }
             }
         }

    이 코드는 작업자 역할이 종료될 때까지 무한 루프 상태에서 실행됩니다. 주 큐에 작업 항목이 있으면 코드는 그 항목을 처리한 다음 구독 큐를 확인합니다.

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

    두 큐 모두 대기가 없으면 코드는 루프를 계속하기 전에 60초 동안 유휴 상태가 됩니다.

                     if (messageFound == false)
                     {
                         System.Threading.Thread.Sleep(1000*60);
                     }

    절전 모드 시간의 목적은 [이전 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)에 설명된 대로 Azure 저장소 트랜잭션 비용을 최소화하기 위한 것입니다.

    [GetMessage](http://msdn.microsoft.com/en-us/library/windowsazure/ee741827.aspx) 메서드가 큐 항목을 큐에서 끌어올 때 이 큐 항목은 큐에 액세스하는 다른 모든 작업자 및 웹 역할에게 30초 동안 보이지 않습니다. 이는 하나의 작업자 역할 인스턴스만 지정된 큐 메시지를 선택해서 처리하도록 하기 위함입니다. [표시 제한 시간](http://msdn.microsoft.com/en-us/library/windowsazure/ee758454.aspx) 매개 변수를 `GetMessage` 메서드로 전달하여 이 *단독 임대* 시간(큐 항목이 안 보이는 시간)을 명시적으로 설정할 수 있습니다. 작업자 역할이 큐 메시지를 처리하는 데 30초 이상 걸리면 다른 역할 인스턴스가 동일한 메시지를 처리하지 못하도록 단독 임대 시간을 늘려야 합니다.

    반면, 단독 임대 시간을 아주 큰 값으로 설정하지는 않습니다. 예를 들어 단독 임대 시간이 48시간으로 설정되어 있고 큐에서 메시지를 제거한 후 작업자 역할이 예기치 않게 종료되면 다른 작업자 역할이 48시간 동안 메시지를 처리하지 못하게 됩니다. 단독 임대 최대값은 7일입니다.

    [GetMessages](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx) 메서드(이름 끝에 "s"가 있음)를 사용하여 한 번의 호출로 최대 32개의 메시지를 큐에서 끌어올 수 있습니다. 큐에 액세스할 때마다 약간의 트랜잭션 비용이 발생하고 메시지가 32개 반환되거나 하나도 반환되지 않거나 이 트랜잭션 비용은 같습니다. 다음 코드는 한 번의 호출로 메시지를 최대 32개까지 가져와 처리합니다.

    	foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
         {
             ProcessQueueMessage(msg);
             messageFound = true;
         }

    `GetMessages`를 사용하여 여러 메시지를 제거하는 경우 응용 프로그램에 모든 메시지를 처리하기에 충분한 표시 제한 시간이 제공되는지 확인합니다. 표시 제한 시간이 만료되면 다른 역할 인스턴스가 메시지에 액세스할 수 있고 그렇게 되면 첫 번째 인스턴스는 작업 항목 처리를 완료했을 때 메시지를 삭제할 수 없게 됩니다.

    `Run` 메서드는 주 큐에서 작업 항목을 찾으면 `ProcessQueueMessage`를 호출합니다.

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

    포이즌 메시지는 처리될 때 응용 프로그램에서 예외를 throw하게 만드는 메시지입니다. 큐에서 메시지를 6번 이상 끌어왔으면 그 메시지를 처리할 수 없다고 간주하고 계속 처리 시도하지 않도록 큐에서 제거합니다. 메시지를 삭제하는 대신 분석하기 위해 프로덕션 응용 프로그램에서 포이즌 메시지를 "데드 메시지" 큐로 이동하는 것이 좋습니다.

    코드는 파티션 키와 SendEmail 행을 검색하는 데 필요한 행 키 및 다시 시작 플래그로 큐 메시지를 구문 분석합니다.

             var messageParts = msg.AsString.Split(new char[] { ',' });
             var partitionKey = messageParts[0];
             var rowKey = messageParts[1];
             var restartFlag = messageParts[2];

    예기치 않은 종료 후에 이 메시지 처리를 다시 시작했으면 코드는 `messagearchive` 테이블을 검사하여 이 전자 메일이 이미 보내졌는지 확인합니다. 전자 메일이 이미 보내진 경우 `SendEmail` 행이 있으면 코드는 이 행을 삭제하고 큐 메시지를 삭제합니다.

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

    그런 다음 `message` 테이블에서 `SendEmail` 행을 가져옵니다. 이 행에는 전자 메일을 보내는 데 필요한 모든 정보(전자 메일의 일반 텍스트 본문 및 HTML이 포함된 Blob 제외)가 들어 있습니다.

             var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
             var retrievedResult = messageTable.Execute(retrieveOperation);
             var emailRowInMessageTable = retrievedResult.Result as SendEmail;
             if (emailRowInMessageTable == null)
             {
                 Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                     partitionKey, rowKey, GetRoleInstance());
                 return;
             }

    그러면 코드는 전자 메일을 보내고 `SendEmail` 행을 보관합니다.

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

    트랜잭션에서는 행을 messagearchive 테이블로 이동하면 여러 테이블에 영향을 주므로 이동할 수 없습니다.

    마지막으로, 다른 나머지를 모두 성공하면 큐 메시지가 삭제됩니다.

             sendEmailQueue.DeleteMessage(msg);

    SendGrid를 사용하여 전자 메일을 보내는 실제 작업은 `SendEmailToList` 메서드를 통해 이루어집니다. SendGrid가 아닌 다른 서비스를 사용하려면 이 메서드에서 코드를 변경하기만 하면 됩니다.

    **참고:** 프로젝트 설정의 자격 증명이 잘못되었으면 SendGrid 호출은 실패하지만 응용 프로그램은 실패 알림을 받지 않습니다. 프로덕션 응용 프로그램에서 SendGrid를 사용하면 관리자가 SendGrid 사용자 계정 암호를 변경할 때 자동으로 실패하는 것을 방지하기 위해 웹 API에 대해 별도의 자격 증명을 설정하는 것이 좋습니다. 자세한 내용은 [SendGrid MultiAuth - 여러 계정 자격 증명](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials)(영문)을 참조하십시오. <https://sendgrid.com/credentials>(영문)에서 자격 증명을 설정할 수 있습니다.

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

    `GetBlobText` 메서드에서 코드는 Blob 크기를 가져온 다음 성능 상의 이유로 그 값을 사용하여 `MemoryStream` 개체를 초기화합니다. 크기를 제공하지 않으면 `MemoryStream`은 256바이트를 할당한 후 다운로드가 그 값을 초과하면 512바이트를 더 할당하는 등 매번 두 배의 양을 할당합니다. 큰 Blob의 경우 이 프로세스는 다운로드 시작 시 올바른 양을 할당하는 것에 비해 비효율적입니다.

    `Run` 메서드는 구독 큐에서 작업 항목을 찾을 때 `ProcessSubscribeQueueMessage`를 호출합니다.

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

    이 메서드는 다음 작업을 수행합니다.

    -   메시지가 "포이즌" 메시지이면 로그하고 삭제합니다.
    -   큐 메시지에서 구독자 GUID를 가져옵니다.
    -   GUID를 사용하여 MailingList 테이블에서 구독자 정보를 가져옵니다.
    -   새 구독자에게 확인 전자 메일을 보냅니다.
    -   큐 메시지를 삭제합니다.

    전자 메일을 목록에 보내는 것과 마찬가지로, 전자 메일을 실제로 보내는 것은 별개의 메서드로 전송되어 원할 경우 다른 전자 메일 서비스로 변경하기 쉽습니다.

         private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
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


테스트작업자 역할 B 테스트
--------------------------

1.  F5 키를 눌러 응용 프로그램을 실행합니다.

2.  **메시지** 페이지로 이동하여 작업자 역할 A를 테스트하기 위해 만든 메시지를 봅니다. 몇 분 후 웹 페이지를 새로 고치면 해당 행이 보관되어 목록에서 보이지 않습니다.

3.  전자 메일을 받을 전자 메일 받은 편지함을 확인합니다. 전자 메일 클라이언트로 전송 또는 SendGrid를 통한 전자 메일 전송이 지연될 수 있으므로 전자 메일을 보려면 조금 기다려야 할 수 있습니다. 또한 정크 메일 폴더를 확인해야 할 수도 있습니다.


다음 단계다음 단계
------------------

이제 Azure 전자 메일 서비스 응용 프로그램이 빌드되었으며 다운로드한 완료된 프로젝트와 동일합니다. 클라우드로 배포하고 클라우드에서 테스트하고 프로덕션으로 수준을 올리기 위해 [두 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)에서 본 것과 동일한 절차를 사용할 수 있습니다. 다른 아키텍처를 빌드하도록 선택한 경우 Azure 웹 사이트로 MVC 프로젝트를 배포하는 방법에 대한 정보는 [Azure 웹 사이트 시작 자습서](/en-us/develop/net/tutorials/get-started)를 참조하십시오.

Azure 저장소에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 저장소 필수 지식](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx)(Bruno Terkaly의 블로그)(영문)

Azure 테이블 서비스에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [Azure 테이블 저장소 필수 지식](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx)(Bruno Terkaly의 블로그)(영문)
-   [Azure 테이블을 최대한 활용하는 방법](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx)(Azure 저장소 팀 블로그)(영문)
-   [.NET에서 테이블 저장소 서비스를 사용하는 방법](http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/)
-   [Azure Storage Client Library 2.0 테이블 자세히 알아보기](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx)(Azure 저장소 팀 블로그)(영문)
-   [실제 사용: Azure 테이블 저장소에 대한 확장 가능한 분할 전략 설계](http://msdn.microsoft.com/en-us/library/windowsazure/hh508997.aspx)(영문)를 참조하십시오.

Azure 큐 서비스 및 Azure 서비스 버스 큐에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [큐 중심 작업 패턴(Azure에서 실제 클라우드 앱 빌드)(영문)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
-   [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조](http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx)
-   [.NET에서 큐 저장소 서비스를 사용하는 방법](/en-us/develop/net/how-to-guides/queue-service/)

Azure Blob 서비스에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [구조화되지 않은 Blob 저장소(Azure에서 실제 클라우드 앱 빌드)(영문)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
-   [.NET에서 Azure Blob 저장소 서비스를 사용하는 방법](/en-us/develop/net/how-to-guides/blob-storage/)

Azure 클라우드 서비스 역할 자동 크기 조정에 대한 자세한 내용은 다음 리소스를 참조하십시오.

-   [자동 크기 조정 응용 프로그램 블록을 사용하는 방법](/en-us/develop/net/how-to-guides/autoscaling/)
-   [자동 크기 조정 및 Azure(영문)](http://msdn.microsoft.com/en-us/library/hh680945(v=PandP.50).aspx)
-   [Azure에서 탄력적이고 크기를 자동으로 조정할 수 있는 솔루션 빌드](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04)(MSDN channel 9 동영상)(영문)

감사의 말감사의 말
------------------

이러한 자습서 및 샘플 응용 프로그램은 [Rick Anderson](http://blogs.msdn.com/b/rickandy/)과 Tom Dykstra가 작성하였습니다. 협력해 주신 다음 분들께 감사드립니다.

-   Barry Dorrans(Twitter [@blowdart](https://twitter.com/blowdart))
-   [Cory Fowler](http://blog.syntaxc4.net/)(Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) )
-   [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
-   Don Glover
-   Jai Haridas
-   [Scott Hunter](http://blogs.msdn.com/b/scothu/)(Twitter: [@coolcsh](http://twitter.com/coolcsh))
-   [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
-   [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
-   다음은 피드백을 제공해 준 Developer Advisory Council의 멤버입니다.
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




[firsttutorial]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut2]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3configstorage]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
[tut4]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[queuehowto]: /en-us/develop/net/how-to-guides/queue-service/

[blobhowto]: /en-us/develop/net/how-to-guides/blob-storage/
[GetMessage]: http://msdn.microsoft.com/en-us/library/windowsazure/ee741827.aspx
[getstartedtutorial]: /en-us/develop/net/tutorials/get-started
[sbqueuecomparison]: http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx
[autoscalingappblock]: /en-us/develop/net/how-to-guides/autoscaling/


[mtas-new-worker-role-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
[mtas-add-new-role-project-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
[mtas-worker-b-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png
[mtas-worker-b-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png
[mtas-worker-b-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
[mtas-worker-b-manage-nuget]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
[mtas-worker-b-install-sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png


[mtas-worker-b-settings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
[autoscaling-and-windows-azure]: http://msdn.microsoft.com/en-us/library/hh680945(v=PandP.50).aspx