<properties
	pageTitle="教學課程 - 開始使用 Azure Batch .NET 程式庫 | Microsoft Azure"
	description="了解 Azure Batch 的基本概念，以及利用簡單的案例了解如何開發 Batch 服務"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/23/2015"
	ms.author="yidingz"/>

# 開始使用適用於 .NET 的 Azure Batch 程式庫  

透過建立設定支援檔案的主控台應用程式，以及在 Azure Batch 集區中數個運算節點上執行的程式，來著手使用 Azure Batch .NET 程式庫。本教學課程中建立的工作會評估上傳到 Azure 儲存體的檔案，並傳回這些檔案中最常出現的單字。這些範例均以 C# 撰寫，並使用 [Azure Batch .NET 程式庫](https://msdn.microsoft.com/library/azure/mt348682.aspx)。

## 必要條件

- 帳戶：

	- **Azure 帳戶**：只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

	- **批次帳戶** - 請參閱[建立和管理 Azure Batch 帳戶](batch-account-create-portal.md)。

	- **儲存體帳戶** - 請參閱 [關於 Azure 儲存體帳戶](../storage-create-storage-account.md)中的＜建立儲存體帳戶＞一節。在本教學課程中，您會在此帳戶中建立名為 **testcon1** 的容器。

- Visual Studio 主控台應用程式專案：

	1.  開啟 Visual Studio，在 [檔案] 功能表上，按一下 [新增]，然後按一下 [專案]。

	2.	從 [**Windows**] 中，在 [**Visual C#**] 下按一下 [**主控台應用程式**]，並將專案命名為 **GettingStarted**，同時將方案命名為 **AzureBatch**，然後按一下 [**確定**]。

- NuGet 組件：

	1. 在 Visual Studio 中建立專案之後，以滑鼠右鍵按一下 [**方案總管**] 中的專案，然後選擇 [**管理 NuGet 封裝**]。在線上搜尋 **Azure.Batch**，然後按一下 [安裝] 以安裝 Microsoft Azure Batch 封裝與相依性。

	2. 在線上搜尋 **WindowsAzure.Storage**，然後按一下 [安裝] 以安裝 Azure 儲存體封裝與相依性。

> [AZURE.TIP]本教學課程會利用 [Azure Batch 的 API 基本概念](batch-api-basics.md) 中討論的一些核心 Batch 概念，如果您是第一次接觸 Batch，強烈建議您先閱讀該文章。

## 步驟 1：建立及上傳支援檔案

為支援應用程式，會在 Azure 儲存體中建立一個容器、建立文字檔案，然後文字檔案和支援檔案會上傳至容器。

### 設定儲存體連接字串

1. 開啟 GettingStarted 專案的 App.config 檔案，然後將 *&lt;appSettings&gt;* 元素加入至 *&lt;configuration&gt;*。

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	取代下列值：

	- **[account-name]**：您先前建立的儲存體帳戶的名稱。

	- **[account-key]**：儲存體帳戶的主索引鍵。您可以從 Azure Preview 入口網站的 [儲存體] 頁面中找到主索引鍵。

2. 儲存 App.config 檔案。

若要深入了解 Azure 儲存體連接字串，請參閱[設定 Azure 儲存體連接字串](../storage/storage-configure-connection-string.md)。

### 建立儲存體容器

1. 將這些 using 指示詞加入至 GettingStarted 專案中的 Program.cs 頂端：

		using System.Configuration;
		using System.IO;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. 在 [方案總管] 中，針對 GettingStarted 專案將 *System.Configuration* 加入至 **References**。

3. 將此方法加入至 Program 類別，此類別會取得儲存體連接字串、建立容器，以及設定權限：

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

4. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		CreateStorage();

5. 儲存 Program.cs 檔案。

	> [AZURE.NOTE]在實際執行環境中，建議您使用[共用存取簽章](https://msdn.microsoft.com/library/azure/ee395415.aspx)。

若要深入了解 Blob 儲存體，請參閱[如何使用 .NET 的 Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)

### 建立處理程式

1. 在 [方案總管] 中，建立一個名為 **ProcessTaskData** 的新主控台應用程式專案。

2. 在 Visual Studio 中建立專案之後，以滑鼠右鍵按一下 [方案總管] 中的專案，然後選擇 [管理 NuGet 封裝]。在線上搜尋 **WindowsAzure.Storage**，然後按一下 [安裝] 以安裝 Azure 儲存體封裝與相依性。

3. 將下列 using 指示詞新增至 Program.cs 檔案的頂端。

		using Microsoft.WindowsAzure.Storage.Blob;

4. 將此程式碼加入至處理檔案中文字的 Main 中：

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
		string[] words = content.Split(' ');
		var topNWords =
		  words.
			Where(word => word.Length > 0).
			GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
			OrderByDescending(x => x.Value).
			Take(numTopN).
			ToList();

		foreach (var pair in topNWords)
		{
			Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

5. 儲存並建置 ProcessTaskData 專案。

### 建立資料檔案

1. 在 GettingStarted 專案中，建立一個名為 **taskdata1.txt** 的新文字檔案並將下列文字複製到其中，然後儲存該檔案。

	當您需要彈性的資源來滿足您的商務需求時，可以使用「Azure 虛擬機器」來佈建依需求提供、可調整的計算基礎結構。您可以從組件庫建立執行 Windows、Linux 及企業應用程式 (例如 SharePoint 和 SQL Server) 的虛擬機器。或者，您可以擷取並使用自己的映像來建立自訂的虛擬機器。

2. 建立一個名為 **taskdata2.txt** 的新文字檔案並將下列文字複製到其中，然後儲存該檔案。

	使用「Azure 雲端服務」可以快速部署及管理功能強大的應用程式和服務。只要上傳您的應用程式，Azure 便會包辦部署細節 (從佈建和負載平衡，到為確保持續可用性所進行的健康狀況監視)。您的應用程式有居業界領導地位的每月 99.95% SLA 做為後盾。您只需要把焦點放在應用程式上，不需要顧慮基礎結構。

3. 建立一個名為 **taskdata3.txt** 的新文字檔案並將下列文字複製到其中，然後儲存該檔案。

	Azure 網站提供可調整、可靠且容易使用的環境來裝載 Web 應用程式。從一個範圍的架構與範本中選取，即可快速建立網站。可使用任何工具或作業系統，以 .NET、PHP、Node.js 或 Python 開發您的網站。從各式各樣的原始檔控制選項 (包括 TFS、GitHub 及 BitBucket) 中選擇，來設定連續整合及以小組方式進行開發。您可以利用其他 Azure 受管理服務 (例如儲存體、CDN 及 SQL Database)，讓您的網站功能與時俱進。

### 將檔案上傳至儲存體容器

1. 開啟 **GettingStarted** 專案的 Program.cs 檔案，然後加入這個可上傳檔案的方法：

		static void CreateFiles()
		{
		  CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
			ConfigurationManager.AppSettings["StorageConnectionString"]);
		  CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		  CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

		  CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
		  CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
		  CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
		  taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
		  taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
	  	taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

			CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
			storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

			CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
		  dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

		  Console.WriteLine("Uploaded the files. Press Enter to continue.");
		  Console.ReadLine();
		}

2. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		CreateFiles();

3. 儲存 Program.cs 檔案。

## 步驟 2.將集區新增至您的 Batch 帳戶

運算節點集區是您要執行工作時必須建立的第一組資源。

1.	將這些 using 指示詞加入至 GettingStarted 專案中的 Program.cs 頂端：

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. 將此程式碼加入至設定認證以呼叫 Azure Batch 服務的 Main 中：

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	使用與 Batch 帳戶相關聯的值取代加上括號的值，您可在 [Azure Preview 入口網站](https://portal.azure.com)找到這些值。若要找出這些值，請登入 [Azure Preview 入口網站](https://portal.azure.com)，然後遵循下列指示進行：

	- **[account-name]** - 按一下 [Batch 帳戶]，選取您稍早建立的 Batch 帳戶
	- **[account-url]** - 在 Batch 帳戶刀鋒視窗中，按一下 [屬性] > [URL]
	- **[account-key]** - 在 Batch 帳戶刀鋒視窗中，按一下 [屬性] > [金鑰] > [主要存取金鑰]

3.	將此方法加入至可建立集區的 Program 類別：

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		CreatePool(client);

5. 將此方法加入至可列出帳戶中的集區的 Program 類別。這有助於確認您是否已建立集區：

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		ListPools(client);

7. 儲存 Program.cs 檔案。

## 步驟 3：將作業加入至帳戶

建立一個作業，用來管理集區中執行的工作。所有工作都必須與作業相關聯。

1. 將此方法加入至可建立作業的 Program 類別：

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		CreateJob(client);

3. 將此方法加入至可列出帳戶中的作業的 Program 類別。這有助於確認您是否已建立作業：

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		ListJobs(client);

5. 儲存 Program.cs 檔案。

## 步驟 4：將工作加入至作業

建立作業之後，可以在其中加入工作。每個工作都會在運算節點上執行，並處理文字檔案。在本教學課程中，您會將三個工作加入至作業中。

1. 將此方法加入至 Program 類別，此類別會將三個工作加入至作業中：

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}


	**[account-name]** 必須替換成您先前建立的儲存體帳戶的名稱。將上述範例中的四個 **[account-name]** 執行個體全部更新。


2. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

			AddTasks(client);

3. 將此方法加入至 Program 類別，其中會列出與作業相關聯的工作：

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. 將此程式碼加入至 Main 以呼叫您剛才加入的方法：

		ListTasks(client);

5. 儲存 Program.cs 檔案。

## 步驟 5：刪除資源

您將為 Azure 中的資源支付費用，因此，刪除不再需要的資源永遠是一個不錯的做法。

### 刪除工作

1.	將此方法加入至可刪除工作的 Program 類別：

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		DeleteTasks(client);

3. 儲存 Program.cs 檔案。

### 刪除作業

1.	將此方法加入至可刪除作業的 Program 類別：

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		DeleteJob(client);

3. 儲存 Program.cs 檔案。

### 刪除集區

1. 將此方法加入至可刪除集區的 Program 類別：

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. 將此程式碼加入至可呼叫您剛才加入的方法的 Main 中：

		DeletePool(client);

3. 儲存 Program.cs 檔案。

## 步驟 6：執行應用程式

1. 啟動 GettingStarted 專案，您應該會在容器建立之後看到這個檔案出現在主控台視窗：

		Created the container. Press Enter to continue.

2. 按下 Enter，檔案便會建立並上傳，您現在應該會在視窗中看到新的一行：

		Uploaded the files. Press Enter to continue.

3. 按下 Enter，集區便會建立：

		Created the pool. Press Enter to continue.

4. 按下 Enter，您應該會看到新集區的這份清單：

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. 按下 Enter，作業便會建立：

		Created the job. Press Enter to continue.

6. 按下 Enter，您應該會看到新作業的這份清單：

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. 按下 Enter，工作便會加入至作業中。工作加入之後，便會自動執行：

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. 按下 Enter，您應該會看到工作及其狀態的清單：

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. 此時您可以進入 Azure Preview 入口網站，查看所建立的資源。若要刪除資源，按下 Enter，直到程式完成為止。

## 後續步驟

1. 既然您已經了解執行中工作的基本概念，您可以了解如何在應用程式的需求變更時，自動調整運算節點。若要這樣做，請參閱[自動調整 Azure Batch 集區中的運算節點](batch-automatic-scaling.md)

2. 有些應用程式會產生可能難以處理的大量資料。解決方法之一是透過[有效率的清單查詢](batch-efficient-list-queries.md)。

<!---HONumber=Nov15_HO3-->