<properties
	pageTitle="教學課程 - 開始使用 Azure Batch .NET 程式庫 | Microsoft Azure"
	description="了解 Azure Batch 的基本概念，以及利用簡單的案例了解如何開發 Batch 服務"
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="01/08/2016"
	ms.author="marsma"/>

# 開始使用適用於 .NET 的 Azure Batch 程式庫  

在我們逐步討論 C# 範例應用程式時，了解 [Azure Batch][azure_batch] 和 [Batch .NET][net_api] 程式庫的基本概念，進而了解它如何利用 Batch 服務來處理雲端的平行工作負載，以及如何與 [Azure 儲存體](./../storage/storage-introduction.md)互動來預備和擷取檔案。了解常見的 Batch 應用程式工作流程技巧，並取得 Batch 的主要元件，例如作業、工作、集區和計算節點。

![Batch 方案工作流程 (最小)][11]<br/>

## 先決條件

本文假設您已熟悉 C# 和 Visual Studio，而且您可以滿足針對 Azure Batch 和儲存體服務所指定的帳戶建立需求。

### 帳戶

- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，只需幾分鐘就可以在 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)建立免費試用帳戶。
- **Batch 帳戶** - 擁有 Azure 訂用帳戶後，請[建立和管理 Azure Batch 帳戶](batch-account-create-portal.md)。
- **儲存體帳戶** - 請參閱 [關於 Azure 儲存體帳戶](../storage-create-storage-account.md)中的「建立儲存體帳戶」一節。

### Visual Studio

您必須擁有 **Visual Studio 2013 或更高版本**才能建置範例專案。您可以在 [Visual Studio 2015 產品的概觀][visual_studio]中找到免費試用版的 Visual Studio。

### *DotNetTutorial* 程式碼範例

[DotNetTutorial][github_dotnettutorial] 範例是在 GitHub 上 [azure-batch-samples][github_samples] 儲存機制中找到的許多程式碼範例之一。按一下儲存機制首頁上的 [下載 ZIP] 按鈕，或按一下 [azure-batch-samples-master.zip][github_samples_zip] 直接下載連結，即可下載此範例。將 ZIP 檔案的內容解壓縮後，您會在下列資料夾中找到方案：

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Azure Batch 總管 (選用)

[Azure Batch 總管][github_batchexplorer]是 GitHub 上 [azure-batch-samples][github_samples] 儲存機制隨附的免費公用程式。雖然不一定要完成此教學課程，強烈建議用於偵錯和管理您的 Batch 帳戶中的實體。您可以在 [Azure Batch 總管範例逐步解說][batch_explorer_blog]部落格文章中深入了解舊版的 Batch 總管。

## DotNetTutorial 範例專案概觀

*DotNetTutorial* 程式碼範例是由兩個專案所組成的 Visual Studio 2013 方案：**DotNetTutorial** 和 **TaskApplication**。

- **DotNetTutorial** 是與 Batch 和儲存體服務進行互動，以在運算節點 (虛擬機器) 上執行平行工作負載的用戶端應用程式。DotNetTutorial 會在本機工作站上執行。

- **TaskApplication** 是在 Azure 中的計算節點上執行進而執行實際工作的可執行檔。在此範例中，`TaskApplication.exe` 會剖析從 Azure 儲存體下載的檔案 (輸入檔) 中的文字，並產生文字檔 (輸出檔)，其中包含出現在輸入檔中的前三個單字清單。建立輸出檔之後，TaskApplication 會接著將此檔案上傳至 Azure 儲存體，以供用戶端應用程式下載。TaskApplication 會在 Batch 服務中的多個計算節點上平行執行。

下圖說明用戶端應用程式 (*DotNetTutorial*) 所執行的主要作業，以及工作所執行的應用程式 (*TaskApplication*)。此基本工作流程通常由使用 Batch 建立的許多計算方案所組成，但不會示範 Batch 服務中可用的每項功能，而幾乎每個 Batch 案例包含的程序都很類似。

![Batch 範例工作流程][8]<br/>

**1.** 在 Azure Blob 儲存體中建立**容器**<br/> **2.** 將工作應用程式和輸入檔案上傳至容器<br/> **3.** 建立 Batch **集區**<br/> &nbsp;&nbsp;&nbsp;&nbsp;**3a.** 集區 **StartTask** 會在節點加入集區時將工作二進位檔 (TaskApplication) 下載至這些節點<br/> **4.** 建立 Batch **作業**<br/> **5.** 將**工作**新增至作業<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** 工作會排程在節點上執行<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** 每項工作會從 Azure 儲存體下載其輸入資料，然後開始執行<br/> **6.** 監視工作<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** 當工作完成時，它們會將其輸出資料上傳至 Azure 儲存體<br/> **7.** 從儲存體下載工作輸出

如上所述，並非每個 Batch 方案都會執行這些確切步驟，並且可能包含更多步驟，但 *DotNetTutorial* 範例應用程式會示範在 Batch 方案中找到的一般程序。

## 建置 *DotNetTutorial* 範例專案

您必須先在 *DotNetTutorial* 專案的 `Program.cs` 檔案中指定 Batch 和儲存體帳戶認證，才可以成功執行範例。如果您尚未這麼做，請在 `DotNetTutorial.sln` 方案檔案上連按兩下，或從 Visual Studio 使用 [檔案] > [開啟] > [專案/方案] 功能表，在 Visual Studio 中開啟方案。

開啟 *DotNetTutorial* 專案中的 `Program.cs`，然後如檔案頂端附近所指定新增您的認證：

```
// Update the Batch and Storage account credential strings below with the values unique to your accounts.
// These are used when constructing connection strings for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

您可以在 [Azure 入口網站][azure_portal]中每項服務的帳戶刀鋒視窗中尋找您的 Batch 和儲存體帳戶認證：

![入口網站中的 Batch 認證][9] ![入口網站中的儲存體認證][10]<br/>

您現已使用您的認證更新專案，以滑鼠右鍵按一下 [方案總管] 中的方案，然後按一下 [建置方案]。出現提示時，請確認任何 NuGet 封裝的還原。

> [AZURE.TIP]如果未自動還原 NuGet 封裝，或您看到有關封裝還原失敗的錯誤，請確定您已安裝 [NuGet 封裝管理員][nuget_packagemgr]，然後啟用遺失封裝的下載。若要啟用封裝下載，請參閱[在建置期間啟用封裝還原][nuget_restore]。

在下列各節中，我們會將範例應用程式細分為用來處理 Batch 服務中工作負載的數個步驟，並詳細討論這些步驟。建議您在進行本文的其餘部分時參閱 Visual Studio 中開啟的方案，因為並不會討論範例中的每一行程式碼。

瀏覽至 *DotNetTutorial* 專案的 `Program.cs` 中 `MainAsync` 方法的頂端，開始進行步驟 1。以下每個步驟大致會追隨 `MainAsync` 中方法呼叫的進展。

## 步驟 1：建立儲存體容器

![在 Azure 儲存體中建立容器][1] <br/>

Batch 包含可供與 Azure 儲存體互動的內建支援，而儲存體帳戶內的容器會提供在 Batch 帳戶中執行的工作所需執行的檔案，以及用來儲存所產生的輸出資料的位置。*DotNetTutorial* 用戶端應用程式首先會在 [Azure Blob 儲存體](./../storage/storage-introduction.md)中建立三個容器：

- **應用程式** -此容器將存放工作所要執行的應用程式，以及其相依性 (如 DLL)。
- **輸入** - 工作會從「輸入」容器下載他們所要處理的資料檔案。
- **輸出** - 當工作完成輸入檔案的處理時，它們會將其結果上傳至「輸出」容器。

為了與儲存體帳戶互動並建立容器，[適用於 .NET 的 Azure 儲存體用戶端程式庫][net_api_storage]用來建立帳戶與 [CloudStorageAccount][net_cloudstorageaccount] 的參考，並從中取得 [CloudBlobClient][net_cloudblobclient]：

```
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

我們在整個應用程式中使用 `blobClient` 參考，並將它當作參數傳遞給數個方法。此範例是在緊接上述的程式碼區塊中，我們會在其中呼叫 `CreateContainerIfNotExistAsync` 以實際建立容器。

```
// Use the blob client to create the containers in Azure Storage if they don't yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(CloudBlobClient blobClient, string containerName)
{
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		if (await container.CreateIfNotExistsAsync())
		{
				Console.WriteLine("Container [{0}] created.", containerName);
		}
		else
		{
				Console.WriteLine("Container [{0}] exists, skipping creation.", containerName);
		}
}
```

建立容器之後，應用程式現在即可上傳工作所要使用的檔案。

> [AZURE.TIP] [How to use Blob storage from .NET](./../storage/storage-dotnet-how-to-use-blobs.md) 提供使用 Azure 儲存體容器和 Blob 的概觀，而當您開始使用 Batch 時應在您的閱讀清單的頂端附近。

## 步驟 2：上傳工作應用程式和輸入檔案

![將工作應用程式和輸入 (資料) 檔案上傳至容器][2] <br/>

在檔案上傳作業中，*DotNetTutorial* 會先定義**應用程式**和**輸入** 檔案路徑的集合 (因為其存在於本機電腦上)，然後將這些檔案上傳到在上一個步驟中建立的容器。

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication, allowing us to
    // determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the application that will
// process the data files, and will be executed by each of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(blobClient, appContainerName, applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of the tasks that are
// executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(blobClient, inputContainerName, inputFilePaths);
```

`Program.cs` 中有個方法涉及上傳程序：

- `UploadFilesToContainerAsync` - 此方法會傳回 [ResourceFile][net_resourcefile] 物件的集合 (下面討論)，並在內部呼叫 `UploadFileToContainerAsync` 以上傳在 *filePaths* 參數中傳入的每個檔案。
- `UploadFileToContainerAsync` - 這是實際執行檔案上傳並建立 [ResourceFile][net_resourcefile] 物件的方法。上傳檔案之後，它會取得此檔案的共用存取簽章 (SAS) 並傳回代表它的 ResourceFile 物件。下面也會討論共用存取簽章。

```
private static async Task<ResourceFile> UploadFileToContainerAsync(CloudBlobClient blobClient, string containerName, string filePath)
{
		Console.WriteLine("Uploading file {0} to container [{1}]...", filePath, containerName);

		string blobName = Path.GetFileName(filePath);

		CloudBlobContainer container = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
		await blobData.UploadFromFileAsync(filePath, FileMode.Open);

		// Set the expiry time and permissions for the blob shared access signature. In this case, no start time is specified,
		// so the shared access signature becomes valid immediately
		SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
		{
				SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
				Permissions = SharedAccessBlobPermissions.Read
		};

		// Construct the SAS URL for blob
		string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
		string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

		return new ResourceFile(blobSasUri, blobName);
}
```

### ResourceFiles

[ResourceFile][net_resourcefile] 提供 Batch 中的工作，以及 Azure 儲存體中將在工作執行前下載到計算節點之檔案的 URL。[ResourceFile.BlobSource][net_resourcefile_blobsource] 屬性會指定 Azure 儲存體中現有檔案的完整 URL，也可能包含可供安全存取檔案的共用存取簽章 (SAS)。Batch .NET 中的大部分工作類型都包含 *ResourceFiles* 屬性，包括：

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

DotNetTutorial 範例應用程式不會使用 JobPreparationTask 或 JobReleaseTask，但您可以在[在 Azure Batch 計算節點上執行作業準備和完成工作](batch-job-prep-release.md)中深入了解。

### 共用存取簽章 (SAS)

共用存取簽章納入為 URL 的一部分時，便是可供安全存取 Azure 儲存體中容器和 Blob 的字串DotNetTutorial 應用程式會使用 Blob 和容器 SAS URL，並示範如何從儲存體服務取得這些 SAS 字串。

- **Blob SAS** - DotNetTutorial 中集區的 StartTask 會在從儲存體下載應用程式二進位檔和輸入資料檔案時，使用 Blob 共用存取簽章 (請參閱下面步驟 3)。DotNetTutorial 的 `Program.cs` 中的 `UploadFileToContainerAsync` 方法包含可取得每個 Blob 的 SAS 的程式碼，並可藉由呼叫 [CloudblobData.GetSharedAccessSignature][net_sas_blob] 來取得 SAS。

- **容器 SAS** - 每個工作在計算節點上完成其工作時，便會將其輸出檔案上傳至 Azure 儲存體中的「輸出」容器。若要這樣做，TaskApplication 會使用容器 SAS，其在上傳檔案時提供寫入容器以成為路徑的一部分的存取權。取得容器 SAS 的方式類似於取得 Blob SAS，而在 DotNetTutorial 中，您會發現 `GetContainerSasUrl` 協助程式方法會呼叫 [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] 來進行此操作。您將在下面步驟 6「監視工作」中進一步了解 TaskApplication 如何使用容器 SAS。

> [AZURE.TIP]查看有關共用存取簽章的兩部分系列[第 1 部分：了解 SAS 模型](./../storage/storage-dotnet-shared-access-signature-part-1.md)和[第 2 部分：建立和使用 SAS 與 Blob 服務](./../storage/storage-dotnet-shared-access-signature-part-2.md)，進一步了解如何提供您儲存體帳戶中資料的安全存取。

## 步驟 3：建立 Batch 集區

![建立 Batch 集區][3] <br/>

將應用程式和資料檔案上傳至儲存體帳戶之後，*DotNetTutorial* 會使用 Batch .NET 程式庫開始與 Batch 服務互動。若要這樣做，會先建立 [BatchClient][net_batchclient]：

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);
using (BatchClient batchClient = BatchClient.Open(cred))
{
	...
```

接著，呼叫 `CreatePoolAsync` 以在 Batch 帳戶中建立計算節點集區。`CreatePoolAsync` 會使用 [BatchClient.PoolOperations.CreatePool][net_pool_create] 方法在 Batch 服務中實際建立此集區。

```
private static async Task CreatePoolAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
    // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
				poolId: poolId,
				targetDedicated: 3,           // 3 compute nodes
				virtualMachineSize: "small",  // single-core, 1.75 GB memory, 225 GB disk
				osFamily: "4");               // Windows Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join the pool.
    // In this case, we copy the StartTask's resource files (that will be automatically downloaded
    // to the node by the StartTask) into the shared directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application files to the
        // node's shared directory. Every compute node in a Batch pool is configured with a number
        // of pre-defined environment variables that can be referenced by commands or applications
        // run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
        // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
        // StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

利用 [CreatePool][net_pool_create] 建立集區時，您會指定一些參數，例如計算節點數目、[節點大小](./../cloud-services/cloud-services-sizes-specs.md)以及節點的[作業系統](./../cloud-services/cloud-services-guestos-update-matrix.md)。

> [AZURE.IMPORTANT]您需對 Batch 中的計算資源付費。若要將成本降到最低，您可以在執行範例前，將 `targetDedicated` 降為 1。

透過這些實體節點屬性，您也可以指定集區的 [StartTask][net_pool_starttask]。StartTask 將在每個節點加入集區以及每次重新啟動節點時，於該節點上執行。StartTask 特別適合用於在工作執行前，在計算節點上安裝應用程式。例如，如果您的工作使用 Python 指令碼來處理資料，您可以使用 StartTask 在計算節點上安裝 Python。

在此範例應用程式中，StartTask 會將它從儲存體下載的檔案 (使用 StartTask 的 *ResourceFiles* 屬性所指定)，從 StartTask 工作目錄複製到在節點上執行的「所有」工作可以存取的共用目錄。

此外，在上述程式碼片段中值得注意的是在 StartTask的 *CommandLine* 屬性中使用的兩個環境變數：`%AZ_BATCH_TASK_WORKING_DIR%` 和 `%AZ_BATCH_NODE_SHARED_DIR%`。Batch 集區中的每個計算節點都會自動以 Batch 特有的數個環境變數進行設定，而工作所執行的任何程序都可以存取這些環境變數。

> [AZURE.TIP]若要深入了解 Batch 集區中計算節點上可用的環境變數，以及有關工作的工作目錄資訊，請參閱 [Azure Batch 功能概觀](batch-api-basics.md)中的**工作的環境設定**和**檔案和目錄**章節。

## 步驟 4：建立 Batch 作業

![建立 Batch 作業][4]<br/>

Batch 工作基本上是與計算節點集區相關聯的工作集合，不僅用來組織及追蹤相關工作負載中的工作，也可以強加特定條件約束，例如作業 (並延伸至其工作) 的最大執行階段，以及相對於 Batch 帳戶中其他作業的作業優先順序。不過，在此範例中，作業只與在步驟 3 建立的集區相關聯，而且不會設定任何其他屬性。

所有 Batch 作業都會與特定集區相關聯。這表示將會在哪些節點上執行作業的工作，並且使用 [CloudJob.PoolInformation][net_job_poolinfo] 屬性來完成，如下列程式碼片段所示。

```
private static async Task CreateJobAsync(BatchClient batchClient, string jobId, string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

現已建立一個作業，便會加入工作來進行工作。

## 步驟 5：將工作加入至作業

![將工作加入至作業][5]<br/> *(1) 工作已加入至作業，(2) 工作已排定在節點上執行，以及 (3) 工作會下載要處理的資料檔案*

若要實際進行工作，必須將工作加入至作業。每個 [CloudTask][net_task] 都會以命令列進行設定，至於集區的 StartTask，則會以工作在其命令列自動執行前下載至節點的 [ResourceFiles][net_task_resourcefiles] 設定。在 *DotNetTutorial* 範例專案中，每個工作只會處理一個檔案，因此其 ResourceFiles 集合只包含單一元素。

```
private static async Task<List<CloudTask>> AddTasksAsync(BatchClient batchClient, string jobId, List<ResourceFile> inputFiles, string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on whichever node each task will run.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format("cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 "{1}"", inputFile.FilePath, outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection opposed to a separate AddTask call for each. Bulk task submission
    // helps to ensure efficient underlying API calls to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT]存取環境變數 (例如 `%AZ_BATCH_NODE_SHARED_DIR%`) 或執行在節點的 `PATH` 中找不到的應用程式時，工作命令列的前面必須加上 `cmd /c`，才能明確執行命令直譯器並指示它在執行命令之後終止。如果您的工作在節點的 PATH 中執行應用程式 (例如 *robocopy.exe* 或 *powershell.exe*)，而且未使用任何環境變數，這就不是必要條件。

在上述程式碼片段中的 `foreach` 迴圈內，您可以看到已建構工作的命令列，以致有三個命令列引數傳遞至 *TaskApplication.exe*：

1. **第一個引數**是要處理之檔案的路徑。這是節點上現有檔案的本機路徑。第一次在上述 `UploadFileToContainerAsync` 中建立 ResourceFile 物件時，檔案名稱會用於此屬性 (做為 ResourceFile 建構函式的參數)，因而表示可以在與 *TaskApplication.exe* 相同的目錄中找到檔案。

2. **第二個引數**指定最前面 *N* 個單字應該寫入輸出檔案。在此範例中，此為硬式編碼，所以前 3 個單字將會寫入輸出檔案。

3. **第三個引數**是共用存取簽章 (SAS)，可提供 Azure 儲存體中**輸出**容器的寫入存取權。*TaskApplication.exe* 會在輸出檔案上傳至 Azure 儲存體時使用此 SAS URL，因此在 TaskApplication 專案的 `Program.cs` 檔案的 `UploadFileToContainer` 方法中可以找到其程式碼：

```
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
		string blobName = Path.GetFileName(filePath);

		// Obtain a reference to the container using the SAS URI.
		CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

		// Upload the file (as a new blob) to the container
		try
		{
				CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
				blob.UploadFromFile(filePath, FileMode.Open);

				Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
				Console.WriteLine();
		}
		catch (StorageException e)
		{

				Console.WriteLine("Write operation failed for SAS URL " + containerSas);
				Console.WriteLine("Additional error information: " + e.Message);
				Console.WriteLine();

				// Indicate that a failure has occurred so that when the Batch service sets the
				// CloudTask.ExecutionInformation.ExitCode for the task that executed this application,
				// it properly indicates that there was a problem with the task.
				Environment.ExitCode = -1;
		}
}
```

## 步驟 6：監視工作

![監視工作][6]<br/> *用戶端應用程式 (1) 會監視工作的完成和成功狀態，以及 (2) 將結果資料上傳至 Azure 儲存體的工作*

工作新增至作業時，會自動排入佇列及排程，以便在與作業相關聯的集區中的計算節點上執行。根據您指定的設定，Batch 會為您處理所有工作佇列、排程、重試和其他工作管理責任。監視工作執行的方法有許多種 - DotNetTutorial 顯示了一個只報告完成和工作失敗或成功狀態的簡單範例。

DotNetTutorial 的 `Program.cs` 中的 `MonitorTasks` 方法內，有三個 Batch .NET 概念值得討論 (底下依其出現的順序列出)：

1. **ODATADetailLevel** - 一定要在清單作業 (例如取得作業的工作清單) 中指定 [ODATADetailLevel][net_odatadetaillevel]，以確保 Batch 應用程式效能。如果您計劃在 Batch 應用程式內進行任何類型的狀態監視，請將[有效率地查詢 Azure Batch 服務](batch-efficient-list-queries.md)加入至您的閱讀清單。

2. **TaskStateMonitor** - [TaskStateMonitor][net_taskstatemonitor] 提供給 Batch .NET 應用程可用來監視工作狀態的協助公用程式。在 `MonitorTasks` 中，*DotNetTutorial* 會等候所有工作在時限內達到 [TaskState.Completed][net_taskstate]，然後終止作業。

3. **TerminateJobAsync** - 透過 [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] 終止作業 (或封鎖 JobOperations.TerminateJob) 會將該作業標記為已完成。如果您的 Batch 方案使用 [JobReleaseTask][net_jobreltask] ([作業準備和完成工作](batch-job-prep-release)中詳述的一種特殊類型的工作)，則必須這麼做。

*DotNetTutorial* 的 `Program.cs` 中的 `MonitorTasks` 方法如下所示：

```
private static async Task<bool> MonitorTasks(BatchClient batchClient, string jobId, TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use a detail level to
    // specify that only the "id" property of each task should be populated. Using a detail level for
    // all list operations helps to lower response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks = await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we will wait for all tasks to
    // reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(tasks, TaskState.Completed, timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state, however, this does not guarantee all tasks completed successfully.
        // Here we further check each task's ExecutionInfo property to ensure that it did not encounter a scheduling error
        // or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo properties.
        // We refresh the tasks below, and need only this information for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the node. It is important to note that
                // the task's state can be "Completed," yet still have encountered a scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}", task.Id, task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## 步驟 7：下載工作輸出

![從儲存體下載工作輸出][7]<br/>

現已完成作業，可以從 Azure 儲存體下載工作的輸出。在 *DotNetTutorial* 的 `Program.cs` 中呼叫 `DownloadBlobsFromContainerAsync` 即可完成此操作：

```
private static async Task DownloadBlobsFromContainerAsync(CloudBlobClient blobClient, string containerName, string directoryPath)
{
		Console.WriteLine("Downloading all files from container [{0}]...", containerName);

		// Retrieve a reference to a previously created container
		CloudBlobContainer container = blobClient.GetContainerReference(containerName);

		// Get a flat listing of all the block blobs in the specified container
		foreach (IListBlobItem item in container.ListBlobs(prefix: null, useFlatBlobListing: true))
		{
				// Retrieve reference to the current blob
				CloudBlob blob = (CloudBlob)item;

				// Save blob contents to a file in the specified folder
				string localOutputFile = Path.Combine(directoryPath, blob.Name);
				await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
		}

		Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE]在 *DotNetTutorial* 應用程式中呼叫 `DownloadBlobsFromContainerAsync`，可讓您指定檔案應下載到您的 `%TEMP%` 資料夾。您可隨意修改此輸出位置。

## 步驟 8：刪除容器

因為您需對位於 Azure 儲存體中的資料付費，所以建議您移除您的 Batch 作業不再需要的所有 Blob。在 DotNetTutorial 的 `Program.cs` 中，呼叫協助程式方法 `DeleteContainerAsync` 三次即可辦到：

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

此方法本身只會取得容器的參考，然後呼叫 [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]：

```
private static async Task DeleteContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.", containerName);
    }
}
```

## 步驟 9：刪除作業和集區

在最後一個步驟中，系統會提示使用者刪除 DotNetTutorial 應用程式所建立的作業和集區。雖然您不需要對作業和工作本身付費，但您需支付計算節點的費用，因此建議您只配置必要的節點，而刪除未使用的集區可能成為您的維護程序的一部分。

BatchClient 的 [JobOperations][net_joboperations] 和 [PoolOperations][net_pooloperations] 兩者都有對應的刪除方法 (會在使用者確認刪除時呼叫)。

```
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT]請記住，您需支付計算資源的費用，而刪除未使用的集區會將成本降到最低。請注意，刪除集區也會刪除該集區內的所有計算節點，而一旦刪除集區，將無法復原節點上的任何資料。

## 執行 *DotNetTutorial* 範例

當您執行範例應用程式時，主控台輸出大致如下。在執行期間，啟動集區的計算節點時，您將在 `Awaiting task completion, timeout in 00:30:00...` 遇到暫停。在執行期間和之後，使用 [Batch 總管][github_batchexplorer]來監視集區、計算節點、作業和工作。使用 [Azure 入口網站][azure_portal]或其中一個[可用的 Azure 儲存體總管][storage_explorers]來檢視應用程式所建立的儲存體資源 (容器和 Blob)。

以預設組態執行應用程式時，一般的執行時間**大約 5 分鐘**。

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## 後續步驟

您可隨意變更 *DotNetTutorial* 和 *TaskApplication*，以試驗不同的計算案例。嘗試將執行延遲新增至 *TaskApplication* (例如使用 [Thread.Sleep][net_thread_sleep])，以模擬長時間執行的工作並以 Batch 總管的「熱圖」功能監視這些工作。嘗試新增更多工作，或調整計算節點的數目。新增邏輯來檢查並允許使用現有的集區加速執行時間 (*提示*：簽出 [azure-batch-samples][github_samples] 中 [Microsoft.Azure.Batch.Samples.Common][github_samples_common] 專案的 `ArticleHelpers.cs`)。

既然您已熟悉 Batch 方案的基本工作流程，現在可以深入了解 Batch 服務的其他功能。

- [Azure Batch 功能概觀](batch-api-basics.md) - 這篇文章提供許多 Batch 功能的概觀，建議閱讀服務的新功能。
- 從 [Batch 學習路徑][batch_learning_path]中的**深入開發**之下的其他 Batch 開發文章著手
- 使用 [TopNWords][github_topnwords] 範例，查看處理「前 N 個單字」工作負載的不同實作方式

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "在 Azure 儲存體中建立容器"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "將工作應用程式和輸入 (資料) 檔案上傳至容器"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "建立 Batch 集區"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "建立 Batch 作業"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "將工作加入至作業"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "監視工作"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "從儲存體下載工作輸出"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Batch 方案工作流程 (完整圖表)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "入口網站中的 Batch 認證"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "入口網站中的儲存體認證"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Batch 方案工作流程 (最小圖表)"

<!---HONumber=AcomDC_0114_2016-->