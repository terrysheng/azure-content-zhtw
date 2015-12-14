<properties
	pageTitle="Batch 中的工作準備和清理| Microsoft Azure"
	description="採用工作層級準備任務以減少傳輸到 Azure Batch 計算節點的資料，並在工作完成時解除任務以清理節點。"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="10/15/2015"
	ms.author="v-marsma"/>

# 在 Azure Batch 計算節點上執行準備和完成的工作

Azure Batch 工作通常在執行之前需要某種形式的安裝，同樣地，在工作完成之後也需要某種工作後維護。Batch 以選擇性的*工作準備*和*工作解除*任務的形式提供此準備和維護機制。

在任何工作任務執行之前，工作準備任務會執行所有計算節點上排定執行的任務。工作一旦完成，工作解除任務會在集區中的每個節點上執行，集區至少會執行一個任務。工作準備和工作釋放工作皆允許您指定命令列在工作被叫用時執行，並提供許多功能，例如檔案下載、提升權限的執行、自訂環境變數、最大執行持續時間、重試計數、檔案保留時間。

在接下來幾節中，您將了解如何在 [Batch .NET][api_net] API 中利用 [JobPreparationTask][net_job_prep] 和 [JobReleaseTask][net_job_release] 來使用這兩種特殊類型的任務。

> [AZURE.TIP]工作準備和工作解除任務在「共用集區」環境中特別有用；在這種環境中，計算節點的集區會在工作執行之間保存，且在許多不同工作之間共用。

## 使用工作準備和工作解除任務的時機

許多情況受益於工作準備和工作解除任務。以下列出幾個：

- **常見任務資料的傳輸** - Batch 工作通常需要一組常用的資料做為工作任務的輸入。例如，在每日風險分析計算中，市場資料是工作專屬的，也是工作中所有任務通用的。這個市場資料 (通常是幾個 GB 的大小) 應該只下載到各個計算節點一次，讓在節點上執行的每個任務可以使用它。在其他任務執行之前使用*工作準備任務*將資料下載到每個節點。
- **工作刪除資料** - 在共用集區環境中，集區的計算節點不會在工作之間被解除委任，為了要節省節點上的磁碟空間或滿足組織的安全性原則，可能需要在執行之間刪除工作資料。使用*工作解除任務*來刪除工作準備任務所下載的資料或在任務執行期間產生的資料。
- **記錄保留** -您可能想要保留一份任務產生的記錄檔，或失敗應用程式所產生的損毀傾印檔案。在這類情況下使用*工作解除任務*來將這項資料壓縮並上傳至 [Azure 儲存體][azure_storage]帳戶。

## 工作準備任務

執行工作的任務之前，在排定執行任務的每個計算節點上會執行工作準備任務。依預設，Batch 服務會等工作準備任務完成，才執行節點上排定的任務，但您可以設定服務不要等。如果計算節點重新啟動，在該節點上工作準備任務會再次執行，但您也可以停用此行為。

工作準備任務只會在排定執行任務的節點上執行。這可避免未指派任務的節點執行不必要的準備任務，進而節省資料傳輸費用 (舉例)。當工作的任務數目小於集區中的節點數目，或是[並行的任務執行](batch-parallel-node-tasks.md)已啟用，便適用於這種情況，如果任務計數小於可能的並行任務總數，留下一些閒置節點。

> [AZURE.NOTE] [JobPreparationTask](net_job_prep_cloudjob) 和 [CloudPool.StartTask](pool_starttask) 的不同之處在於，JobPreparationTask 在每個工作開始時執行，而 StartTask 只在計算節點第一次加入集區或重新啟動時執行。

## 工作解除任務

工作一旦完成，工作解除任務會在集區中的每個節點上執行，集區至少會執行一個任務。透過發出終止要求將工作標示為完成。然後，Batch 服務將工作狀態設定為 *終止*，終止與工作相關聯的任何作用中或執行中任務，並執行工作解除任務。於是工作便進入*完成*狀態。

> [AZURE.NOTE]工作刪除也會執行工作解除任務。不過，如果先前已終止工作，當後來刪除該工作時，解除任務不會執行第二次。

## Batch .NET 中的工作準備和工作解除任務

指定工作準備任務是藉由建立及設定 [JobPreparationTask][net_job_prep] 並將它指派給工作的 [CloudJob.JobPreparationTask][net_job_prep_cloudjob] 屬性而完成　。同樣地，初始化 [JobReleaseTask][net_job_release] 並將它指派給工作的 [CloudJob.JobReleaseTask][net_job_prep_cloudjob] 屬性即可設定工作的解除任務。

在此程式碼片段中，`myBatchClient` 是 [BatchClient][net_batch_client] 完全初始化的執行個體，`myPool` 是 Batch 帳戶內的現有集區。

		// Create the CloudJob for CloudPool "myPool"
		CloudJob myJob = myBatchClient.JobOperations.CreateJob("JobPrepReleaseSampleJob",
															   new PoolInformation() { PoolId = "myPool" });

		// Specify the command lines for the job preparation and release tasks
		string jobPrepCmdLine = "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
		string jobReleaseCmdLine = "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

		// Assign the job preparation task to the job
		myJob.JobPreparationTask = new JobPreparationTask { CommandLine = jobPrepCmdLine };

		// Assign the job release task to the job
		myJob.JobReleaseTask = new JobPreparationTask { CommandLine = jobReleaseCmdLine };

		await myJob.CommitAsync();

如上所述，終止或刪除工作時會執行解除任務。Batch .NET API 的終止工作，是藉由呼叫 [PoolOperations.TerminateJobAsync][net_job_terminate] 來執行，而刪除工作會藉由 [PoolOperations.DeleteJobAsync][net_job_delete] 執行，兩者通常都是在工作的任務完成或達到定義之逾時時完成。

		// Terminate the job to mark it as Completed; this will initiate the Job Release Task on any node
		// that executed job tasks. Note that the Job Release Task is also executed when a job is deleted,
		// thus you need not call Terminate if you typically delete your jobs upon task completion.
		await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");

## 後續步驟

### GitHub 上的範例專案

請看 GitHub 上的範例專案 [JobPrepRelease][job_prep_release_sample]，了解工作準備和工作解除的運作。此主控台應用程式會做這些事：

1. 建立包含兩個「小」節點的集區
2. 建立具有工作準備、解除和標準任務的工作
3. 執行工作準備任務，將節點識別碼第一次寫入節點的「共用」目錄的文字檔中
4. 在每個節點上執行任務，將其任務識別碼寫入同一文字檔案中
5. 一旦完成所有任務 (或達到逾時)，會列印每個節點的文字檔案的內容到主控台
6. 在工作完成時，執行工作解除任務將該檔案從節點刪除
6. 列印執行工作準備和工作解除任務的每個節點上這些任務的結束代碼
7. 暫停執行以允許工作和/或集區的刪除確認

範例應用程式的輸出類似這樣：

```
Attempting to create pool: JobPrepReleaseSamplePool
The pool already existed when we tried to create it
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-3105992504_1-20151015t150030z:
-------------------------------------------
tvm-3105992504_1-20151015t150030z tasks:
  task001
  task002
  task006
  task007

Contents of shared\job_prep_and_release.txt on tvm-3105992504_2-20151015t150030z:
-------------------------------------------
tvm-3105992504_2-20151015t150030z tasks:
  task003
  task005
  task004
  task008

Waiting for job JobPrepReleaseSampleJob to reach state Completed
....

tvm-3105992504_1-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-3105992504_2-20151015t150030z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
no

Sample complete, hit ENTER to exit...
```

### Inspect job preparation and release 以 Batch 總管檢查工作準備和工作解除任務

[Batch 總管][batch_explorer_article] (在 GitHub 上的[範例程式碼存放庫][batch_explorer_project]中也能找到) 是以 Azure Batch 解決方案進行開發時的絕佳工具。，例如，當執行上述的範例應用程式時，您可以使用 Batch 總管檢視工作和其任務的屬性，或甚至下載工作的任務修改過的共用文字檔案。

以下螢幕擷取畫面醒目顯示當在 [工作] 索引標籤中選取 *JobPrepReleaseSampleJob* 工作時，在 [工作詳細資料] 窗格中工作準備和工作解除任務的屬性。

![Batch 總管][1]

*顯示工作準備和解除任務的 Batch 總管螢幕擷取畫面*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_article]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/batchexplorer-01.png
[2]: ./media/batch-job-prep-release/batchexplorer-02.png

<!----HONumber=Nov15_HO1-->