<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" urlDisplayName="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 4: Worker role A" metaKeywords="Azure tutorial, .NET multi-tier app, multi-tier architecture" description="The fourth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role A (email scheduler) for the Azure Email Service application - 4 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Azure 전자 메일 서비스 응용 프로그램에 대한 작업자 역할 A(전자 메일 스케줄러) 구축 - 4/5.
=========================================================================================

Azure 전자 메일 서비스 샘플 응용 프로그램을 빌드하고 배포하는 방법을 설명하는 5개 시리즈 중 네 번째 자습서입니다. 응용 프로그램 및 자습서 시리즈에 대한 자세한 내용은 [시리즈의 첫 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)를 참조하십시오.

이 자습서에서는 다음에 대해 알아봅니다.

-   Azure 저장소 테이블을 쿼리하고 업데이트하는 방법
-   다른 작업자 역할이 처리하도록 큐에 작업 항목을 추가하는 방법
-   `OnStop` 메서드를 재정의하여 계획된 종료를 처리하는 방법
-   중복 전자 메일을 발송하지 않고 놓치는 전자 메일이 없도록 하여 예상치 못한 종료를 처리하는 방법
-   Azure 저장소 탐색기를 사용하여 Azure 저장소 테이블을 사용하는 작업자 역할을 테스트하는 방법

클라우드 서비스 프로젝트를 만들 때 이미 작업자 역할 A 프로젝트를 만들었습니다. 따라서 이제는 이 작업자 역할을 프로그래밍하고 이 역할이 Azure 저장소 계정을 사용하도록 구성하기만 하면 됩니다.

프로젝트 참조 추가웹 프로젝트에 참조 추가
-----------------------------------------

엔터티 클래스가 정의되어 있기 때문에 웹 프로젝트에 대한 참조가 필요합니다. 작업자 역할 B에서도 동일한 엔터티 클래스를 사용하여 응용 프로그램에서 사용되는 Azure 테이블의 데이터를 읽고 쓰게 됩니다.

**참고:** 프로덕션 응용 프로그램에서는 작업자 역할 프로젝트에서 웹 프로젝트에 대한 참조를 설정하지 않습니다. 설정하는 경우 작업자 역할에서 원치 않거나 필요하지 않은 다수의 종속 어셈블리를 참조하게 되기 때문입니다. 일반적으로 공유 모델 클래스를 클래스 라이브러리 프로젝트에 유지하며, 웹 프로젝트 및 작업자 역할 프로젝트가 모두 이 클래스 라이브러리 프로젝트를 참조합니다. 이 자습서에서는 솔루션 구조를 간단하게 유지하기 위해 모델 클래스가 웹 프로젝트에 저장됩니다.

1.  WorkerRoleA 프로젝트를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

    ![WorkerRoleA 프로젝트에서 참조 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-reference-menu.png)

2.  **참조 관리자**에서 MvcWebRole 프로젝트에(또는 Azure 웹 사이트에서 웹 UI를 실행 중인 경우 웹 응용 프로그램 프로젝트에) 참조를 추가한 후 **확인**을 클릭합니다.

    ![MvcWebRole에 참조 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png)

SCL 1.7 참조 추가SCL 1.7 어셈블리에 대한 참조 추가
--------------------------------------------------

> [WACOM.NOTE] SDK 2.3 이상을 설치한 경우 이 단계를 건너뜁니다.

SCL(Storage Client Library) 2.0 버전에는 진단에 필요한 사항 중 일부만 있습니다. 따라서 1.7 어셈블리 중 하나에 대한 참조를 추가해야 합니다. 이전 자습서의 단계를 따랐다면 이미 추가했겠지만, 해당 단계를 누락한 경우에 대비하여 여기에 지침을 포함했습니다.

1.  WorkerRoleA 프로젝트를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

2.  대화 상자 아래쪽에 있는 **찾아보기...** 단추를 클릭합니다.

3.  다음 폴더로 이동합니다.

         C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  *Microsoft.WindowsAzure.StorageClient.dll*을 선택하고 **추가**를 클릭합니다.

5.  **참조 관리자** 대화 상자에서 **확인**을 클릭합니다.

SendEmail 모델 추가SendEmail 모델 추가
--------------------------------------

작업자 역할 A는 `Message` 테이블의 `SendEmail` 행을 만들고, 작업자 역할 B는 전자 메일을 보내는 데 필요한 정보를 가져오기 위해 이 행을 읽습니다. 다음 이미지는 `Message` 테이블에 있는 두 개의 `Message` 행 및 세 개의 `SendEmail` 행의 속성 하위 집합을 보여 줍니다.

    ![message table with sendmail][mtas-sendMailTbl]

`Message` 테이블의 이 행은 몇 가지 용도로 사용됩니다.

-   작업자 역할 B가 전자 메일 한 통을 보내는 데 필요한 모든 정보를 제공합니다.
-   오류 후에 작업자 역할이 다시 시작되는 경우 중복된 항목의 전송을 방지하기 위해 전자 메일 발송 여부를 추적합니다.
-   작업자 역할 A가 메시지의 모든 전자 메일이 전송된 시기를 확인하여 해당 메일을 `Complete`로 표시할 수 있게 합니다.

`SendEmail` 행을 읽고 쓰기 위해 모델 클래스가 필요합니다. 작업자 역할 A와 작업자 역할 B 모두가 이 클래스에 액세스할 수 있어야 하며 다른 모델 클래스가 모두 웹 프로젝트에 정의되어 있기 때문에 이 클래스도 웹 프로젝트에서 정의하는 것이 좋습니다.

1.  **솔루션 탐색기**에서 웹 프로젝트에 있는 Models 폴더를 마우스 오른쪽 단추를 클릭하고 **기존 항목 추가**를 선택합니다.

    ![웹 프로젝트의 Models 폴더에 기존 항목 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-add-existing-for-sendemail-model.png)

2.  샘플 응용 프로그램을 다운로드한 폴더로 이동하여 웹 프로젝트 Models 폴더에서 *SendEmail.cs* 파일을 선택한 후 추가를 클릭합니다.

3.  *SendEmail.cs*를 열고 코드를 검토합니다.

         public class SendEmail : TableEntity
         {
             public long MessageRef { get; set; }
             public string EmailAddress { get; set; }
             public DateTime
         ScheduledDate { get; set; }
             public String FromEmailAddress { get; set; }
             public string SubjectLine { get; set; }
             public bool
         EmailSent { get; set; }
             public string SubscriberGUID { get; set; }
             public string ListName { get; set; }
         }

    이 모델과 연결된 UI가 없기(MVC 컨트롤러에서 사용 안 됨) 때문에 DataAnnotations 특성이 포함되지 않았다는 점을 제외하고, 이 코드는 다른 모델 클래스와 비슷합니다.

작업자 역할 코드 추가작업 역할이 시작되면 실행되는 코드 추가
------------------------------------------------------------

1.  WorkerRoleA 프로젝트에서 *WorkerRole.cs*를 열고 코드를 검토합니다.

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
                 // see the MSDN topic at http://go.microsoft.com/fwlink/
         LinkId=166357.

                 return base.OnStart();
             }
         }

    작업자 역할의 기본 템플릿 코드입니다. 작업자 역할 인스턴스가 시작될 때만 실행되는 초기화 코드를 배치할 수 있는 `OnStart` 메서드가 있으며, `OnStart` 메서드가 완료된 후에 호출되는 `Run` 메서드가 있습니다. 이 코드를 고유한 초기화 코드로 바꾼 후에 코드를 실행합니다.

2.  *WorkerRole.cs*를 삭제한 후 WorkerRoleA 프로젝트를 마우스 오른쪽 단추로 클릭하고 **기존 항목 추가**를 선택합니다.

    ![작업자 역할 A에 기존 항목 추가](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-existing.png)

3.  샘플 응용 프로그램을 다운로드한 폴더로 이동하여 WorkerRoleA 프로젝트에서 *WorkerRoleA.cs* 파일을 선택한 후 **추가**를 클릭합니다.

4.  *WorkerRoleA.cs*를 열고 코드를 검토합니다.

    `OnStart` 메서드는 Azure 저장소 엔터티로 작업하는 데 필요한 컨텍스트 개체를 초기화합니다. 또한 `Run` 메서드에서 사용할 모든 테이블, 큐 및 Blob 컨테이너가 존재하도록 합니다. 이 작업을 수행하는 코드는 이전에 MVC 컨트롤러 생성자에서 본 것과 비슷합니다. 이 메서드에서 사용되는 연결 문자열을 나중에 구성할 것입니다.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

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

    전송 오류를 확인하는 초기화 코드를 반복해서 표시하는 Azure 저장소 작업에 대해 이전에 설명서를 본 적이 있을 수 있습니다. 이 작업은 더 이상 필요 없습니다. 이제 최대 3회까지 추가로 시도하여 일시적인 네트워크 오류를 흡수하는 재시도 메커니즘이 API에 기본으로 제공되기 때문입니다.

    `OnStart` 메서드가 호출하는 `ConfigureDiagnostics` 메서드는 `Trace.Information` 및 `Trace.Error` 메서드의 출력을 확인할 수 있도록 추적을 설정합니다. 이 메서드에 대해서는 [두 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/)에서 설명합니다.

    `OnStop` 메서드는 전역 변수 `onStopCalled`를 true로 설정한 다음 `Run` 메서드가 전역 변수 `returnedFromRunMethod`를 true로 설정(완전한 종료가 가능함을 나타냄)할 때까지 대기합니다.

         public override void OnStop()
         {
             onStopCalled = true;
             while (returnedFromRunMethod == false)
             {
                 System.Threading.Thread.Sleep(1000);
             }
         }

    `OnStop` 메서드는 다음 중 한 가지 원인으로 인해 작업자 역할이 종료될 때 호출됩니다.

    -   Azure에서 가상 컴퓨터(웹 역할 또는 작업자 역할 인스턴스) 또는 가상 컴퓨터를 호스트하는 물리적 컴퓨터를 다시 부팅해야 함.
    -   Azure 관리 포털에서 **중지** 단추를 사용하여 클라우드 서비스를 중지함.
    -   클라우드 서비스 프로젝트에 업데이트를 배포함.

    `Run` 메서드는 변수 `onStopCalled`를 모니터링하여 이 변수가 `true`로 변경되면 처리할 새 작업 항목을 가져오는 일을 중지합니다. `OnStop` 및 `Run` 메서드 사이의 이런 조정 덕분에 작업자 프로세스의 정상 종료가 가능합니다.

    Azure는 플랫폼의 보안, 안정성, 높은 성능 등을 보장하기 위해 주기적으로 운영 체제 업데이트를 설치합니다. 업데이트하려면 일반적으로 클라우드 서비스를 호스트하는 컴퓨터가 종료된 후 재부팅되어야 합니다. 자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)(영문)을 참조하십시오.

    `Run` 메서드는 두 가지 기능을 수행합니다.

    -   `message` 테이블을 검사하여 오늘 또는 이전에 발생하도록 예약되어 있지만 이를 위한 큐 작업 항목은 아직 생성되지 않은 메시지를 찾습니다.

    -   `message` 테이블을 검사하여 큐 작업 항목이 모두 생성되었음을 나타내는 상태가 있지만 일부 전자 메일이 아직 발송되지 않은 메시지를 찾습니다. 이런 메시지를 찾는 경우 `SendEmail` 행에서 해당 메시지를 검사하여 모든 전자 메일이 발송되었는지 확인하고 모두 발송된 경우에는 상태를 `Completed`로 업데이트하고 `message` 행을 보관합니다.

    이 메서드는 전역 변수 `onStopCalled`도 확인합니다. 이 변수가 `true`인 경우 메서드는 처리할 새 작업 항목을 가져오는 일을 중지하고 이미 시작된 작업이 완료되면 돌아갑니다.

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

    모든 작업이 `while` 블록에서 무한 루프를 통해서 완료되며, 처리되지 않은 예외를 방지하기 위해 `while` 블록의 모든 코드가 `try`-`catch` 블록에 래핑되어 있습니다. 처리되지 않은 예외가 발생하는 경우 Azure가 [UnhandledException](http://msdn.microsoft.com/en-us/library/system.appdomain.unhandledexception.aspx) 이벤트를 발생시키고, 작업자 프로세스가 종료되며, 해당 역할이 오프라인으로 전환됩니다. 작업자 역할이 Azure에 의해 다시 시작되지만, 여기에는 몇 분 정도 걸립니다. `try` 블록은 `TraceError`를 호출하여 오류를 기록한 후 오류가 지속되는 경우 오류 메시지가 과도하게 여러 번 반복되지 않도록 60초 동안 유휴 상태로 유지됩니다. 프로덕션 응용 프로그램에서 `try` 블록으로 전자 메일을 관리자에게 보낼 수 있습니다.

    `Run` 메서드는 `message` 테이블에서 내일 전 날짜로 예약된 `message` 행에 대한 쿼리를 처리합니다.

                     // Retrieve all messages that are scheduled for tomorrow or earlier
                     // and are in Pending or Queuing status.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

    **참고:** 처리된 후 메시지 행을 `messagearchive` 테이블로 이동하면 얻을 수 있는 장점 중 하나는 이 쿼리가 검색 조건으로 `PartitionKey` 및 `RowKey`만 지정하면 된다는 것입니다. 처리된 행을 보관하지 않는 경우, 쿼리는 키 필드가 아닌 필드(`Status`)도 지정해야 하며 더 많은 행을 검색해야 합니다. 그러면 테이블 크기가 증가하고 쿼리에 더 오래 걸리며 연속 토큰이 발생하기 시작할 수 있습니다.

    메시지가 `Pending` 상태이면 처리가 아직 시작되지 않은 것이며 `Queuing` 상태이면 처리가 이전에 시작되었지만 큐 메시지가 모두 만들어지기 전에 중단된 것입니다. 이런 경우, 작업자 역할 B가 각 전자 메일을 보낼 때 이 역할에서 추가 확인을 수행하여 전자 메일이 아직 발송되지 않았는지 확인해야 합니다. 이것이 바로 `restartFlag` 변수의 용도입니다.

                         string restartFlag = "0";
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

    다음으로, 코드는 `Pending` 상태인 `message` 행을 `Queuing`으로 설정합니다. 그런 다음, 이 행과 이미 `Queuing` 상태인 행에 대해 `ProcessMessage` 메서드를 호출하여 메시지의 전자 메일을 보내는 큐 작업 항목을 만듭니다.

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

    `Queuing` 상태인 메시지를 처리한 후에는 코드가 `Message` 열 상태를 `Processing`으로 설정합니다. `message` 테이블에서 `Pending` 또는 `Queuing` 상태가 아닌 행은 이미 `Processing` 상태이며, 이 행에 대해 코드는 메시지의 전자 메일이 모두 발송되었는지 여부를 확인하는 메서드를 호출합니다. 모든 전자 메일이 발송된 경우 `message` 행이 보관됩니다.

    쿼리에 의해 검색된 모든 레코드를 처리한 후 코드는 1분 동안 유휴 상태로 유지됩니다.

                 // Sleep for one minute to minimize query costs.
                 System.Threading.Thread.Sleep(1000*60);

    반환되는 데이터가 없는 경우에도 Azure 저장 쿼리별로 최소한의 요금이 부과됩니다. 따라서 지속적으로 다시 검사하면 불필요하게 Azure 비용이 추가됩니다. 이 자습서를 작성할 때 비용은 트랜잭션 100만 건당 \$0.10입니다(쿼리가 트랜잭션으로 계산됨). 따라서 유휴 시간을 1분에 훨씬 못 미치게 설정해도 테이블에서 보내야 할 메시지를 검사하는 비용은 여전히 최소 수준입니다. 가격 책정에 대한 자세한 내용은 [첫 번째 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)를 참조하십시오.

    **스레딩 및 최적 CPU 활용에 대한 참고 사항:** `Run` 메서드에는 전자 메일 큐 및 완료 메시지 확인 등의 두 가지 작업이 있으며, 이 작업은 단일 스레드에서 순차적으로 실행됩니다. 소형 VM(가상 컴퓨터)에는 1.75GB RAM과 단일 CPU가 있으므로, 이 작업을 단일 스레드에서 순차적으로 실행하는 것이 좋을 수 있습니다. 응용 프로그램을 효율적으로 실행하려면 소형 VM에서 제공되는 것보다 많은 메모리가 필요한 경우를 가정하겠습니다. 중간 규모의 VM에는 3.5GB RAM 및 2개의 CPU가 있지만, 이 응용 프로그램은 단일 스레드이기 때문에 CUP를 하나만 사용합니다. 모든 CPU를 활용하려면 CPU별로 작업자 스레드를 하나씩 만들어야 합니다. 이런 경우에도 단일 CPU를 하나의 스레드가 완전히 활용하지는 않습니다. 스레드가 네트워크 또는 I/O 호출을 만들면 I/O 또는 네트워크 호출이 완료될 때까지 대기해야 하며 대기하는 동안 유용한 작업을 수행하지 않습니다. `Run` 메서드가 두 개의 스레드를 사용하여 구현된 경우, 하나의 스레드는 네트워크 또는 I/O 작업이 완료되도록 대기하고 다른 한 스레드는 유용한 작업을 할 수 있습니다.

    `ProcessMessage` 메서드는 대상 전자 메일 목록의 모든 전자 메일 주소를 가져오고 각 전자 메일 주소의 큐 작업 항목을 만듭니다. 큐 작업 항목을 만들 때는 `Message` 테이블에 `SendEmail` 행도 만듭니다. 이 행은 작업자 역할 B에 전자 메일 발송에 필요한 정보를 제공하며 각 전자 메일이 발송되었는지 여부를 추적하는 `EmailSent` 속성을 포함합니다.

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

    이 코드는 먼저 대상 메일 그룹의 `mailinglist` 테이블에서 메일 그룹 행을 가져옵니다. 이 행에는 전자 메일을 보내기 위해 작업자 역할 B에게 제공해야 하는 "보내는 사람" 전자 메일 주소가 있습니다.

             // Get Mailing List info to get the "From" email address.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                 return;
             }

    그런 다음, `mailinglist` 테이블에서 대상 메일 그룹의 모든 구독자 행을 쿼리합니다.

             // Get email addresses for this Mailing List.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

    쿼리 결과를 처리하는 루프에서 이 코드는 구독자 전자 메일 주소가 확인되었는지, 그리고 확인되지 않았다면 전자 메일이 큐에 없는지 확인하여 시작합니다.

                 // Verify that the subscriber email address has been verified.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                     continue;
                 }

    다음으로, 코드는 `message` 테이블에 `SendEmail` 행을 만듭니다. 이 행은 작업자 역할 B가 전자 메일을 보내는 데 사용하는 정보를 포함합니다. 이 행은 `EmailSent` 속성을 `false`로 설정하여 만들어집니다.

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

    오류 후에 작업자 역할 A가 다시 시작되는 경우 이미 행이 있을 수 있기 때문에, 코드는 "upsert" 작업을 사용합니다.

    각 전자 메일 주소에 대해 수행할 마지막 작업은 작업자 역할 B가 전자 메일을 보내도록 트리거하는 큐 작업 항목을 만드는 것입니다. 이 큐 작업 항목에는 방금 만들어진 `SendEmail` 행의 파티션 키 및 행 키 값과 앞에서 설정한 다시 시작 플래그가 포함됩니다. `SendEmail` 행은 작업자 역할 B가 전자 메일을 보내기 위해 필요로 하는 모든 정보를 포함합니다.

                 // Create the queue message.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);

    `CheckAndUpdateStatusIfComplete` 메서드는 Processing 상태인 메시지를 확인하여 모든 전자 메일이 발송되었는지 여부를 확인합니다. 보내지 않은 전자 메일이 발견되지 않는 경우 메서드는 행 상태를 `Completed`로 업데이트하고 행을 보관합니다.

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

저장소 구성저장소 연결 문자열 구성
----------------------------------

웹 역할의 저장소 계정 자격 증명을 구성할 때 작업자 역할 A의 저장소 계정 자격 증명을 구성하지 않은 경우 지금 구성하십시오.

1.  솔루션 탐색기에서 **AzureEmailService** 클라우드 프로젝트의 **역할** 아래에 있는 **WorkerRoleA**를 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.

2.  **서비스 구성** 드롭다운 목록에서 **모든 구성**이 선택되었는지 확인합니다.

3.  **설정** 탭을 선택하고 **설정 추가**를 클릭합니다.

4.  **이름** 열에 *StorageConnectionString*을 입력합니다.

5.  **유형** 드롭다운 목록에서 **연결 문자열**을 선택합니다.

6.  줄 오른쪽 끝에 있는 줄임표(**...**)를 클릭하여 새 연결 문자열을 만듭니다.

7.  **저장소 계정 연결 문자열** 대화 상자에서 **Your subscription**을 클릭합니다.

8.  올바른 **구독** 및 **계정 이름**을 선택한 후 **확인**을 클릭합니다.

9.  진단 연결 문자열을 설정합니다. 진단 연결 문자열에 동일한 저장소 계정을 사용해도 되지만, 모범 사례는 추적(진단) 정보용으로 다른 저장소 계정을 사용하는 것입니다.

테스트작업자 역할 A 테스트
--------------------------

1.  F5 키를 눌러 응용 프로그램을 실행합니다.

> [WACOM.NOTE] Visual Studio 2013 및 최신 SDK의 경우 `LogLevel` 참조와 관련하여 "모호한 참조" 오류가 발생할 수 있습니다. `LogLevel`을 마우스 오른쪽 단추로 클릭하고 해결을 클릭한 후 `Microsoft.WindowsAzure.Diagnostics.LogLevel`을 선택하십시오.

1.  관리자 웹 페이지를 사용하여 메일 그룹을 만들고 이 메일 그룹의 구독자를 만듭니다. 하나 이상의 구독자에 대해 `Verified` 속성을 `true`로 설정하고 메일을 수신할 수 있는 주소로 전자 메일 주소를 설정합니다.

    작업자 역할 B를 구현할 때까지는 전자 메일이 발송되지 않지만, 작업자 역할 B를 테스트하는 데 동일한 테스트 데이터를 사용합니다.

2.  만든 메일 그룹에 보낼 메시지를 만들고 예약 날짜를 오늘이나 과거 날짜로 설정합니다.

    ![대기 상태인 새 메시지](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png)

3.  1분 정도 후에(Run 메서드의 1분 유휴 시간 때문) 메시지 웹 페이지를 새로 고치면 상태가 Processing으로 변경된 것을 볼 수 있습니다. 먼저 Queuing으로 변경되는 것이 보일지도 모르지만, Queuing에서 Processing으로 변경되는 속도가 빨라서 Queuing이 보이지 않을 확률이 높습니다.

    ![처리 중 상태인 새 메시지](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png)

4.  Azure 저장소 탐색기를 열고 테스트 저장소 계정을 선택합니다.

5.  Azure 저장소 탐색기에서 **저장소 형식** 아래에 있는 **큐**를 선택한 후 **azuremailqueue**를 선택합니다.

    대상 전자 메일 목록에서 확인된 구독자별로 하나의 큐 메시지가 표시됩니다.

    ![ASE의 큐 메시지](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png)

6.  큐 메시지를 두 번 클릭한 후 **Message Detail** 대화 상자에서 **Message** 탭을 선택합니다.

    큐 메시지의 내용이 표시됩니다. 파티션 키(날짜 2012-12-14), 행 키(MessageRef 값과 전자 메일 주소), 다시 시작 플래그가 쉼표로 구분되어 표시됩니다.

    ![ASE의 큐 메시지 내용](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png)

7.  **Message Detail** 대화 상자를 닫습니다.

8.  **저장소 형식**에서 **테이블**을 선택한 후 **Message** 테이블을 선택합니다.

9.  **쿼리**를 클릭하여 테이블의 모든 행을 표시합니다.

    예약한 메시지가 행 키에 "Message"가 포함되어 표시되며, 그 뒤에 확인된 각 구독자에 해당하는 행이 행 키에 전자 메일 주소가 포함되어 표시됩니다.

    ![ASE의 메시지 테이블 행](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png)

10. 행 키에 전자 메일 주소가 있는 행을 두 번 클릭하여 작업자 역할 A가 만든 `SendEmail` 행의 내용을 표시합니다.

    ![메시지 테이블의 SendEmail 행](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png)

다음 단계다음 단계
------------------

지금까지 작업자 역할 A를 작성했으며 이 역할이 큐 메시지 및 작업자 역할 B가 전자 메일을 보내는 데 필요로 하는 테이블 행을 만드는 것을 확인했습니다. [다음 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)에서는 작업자 역할 B를 작성하고 테스트합니다.

Azure 저장소 테이블, 큐 및 Blob 작업 관련 추가 리소스에 대한 링크는 [이 시리즈의 마지막 자습서](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) 끝 부분을 참조하십시오.
[자습서 5](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)

