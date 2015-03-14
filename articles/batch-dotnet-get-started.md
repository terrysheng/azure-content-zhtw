<properties 
	pageTitle="教學課程 - 開始使用適用於 .NET 的 Azure 批次程式庫" 
	description="了解 Azure 批次的基本概念，以及利用簡單的案例了解如何使用批次服務" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="12/03/2014" 
	ms.author="yidingz, kabatta"/>

#開始使用適用於 .NET 的 Azure 批次程式庫  

本文包含下列兩個教學課程，幫助您開始使用 Azure 批次服務的 .NET 程式庫進行開發。  

-	[教學課程 1：適用於 .NET 的 Azure 批次程式庫](#tutorial1)
-	[教學課程 2：適用於 .NET 的 Azure 批次應用程式庫](#tutorial2)  


如需「Azure 批次」的背景資訊及案例，請參閱 [Azure 批次技術概觀](http://azure.microsoft.com/documentation/articles/batch-technical-overview/)。

##<a name="tutorial1"></a>教學課程 1：適用於 .NET 的 Azure 批次程式庫
  	
本教學課程將示範如何使用Azure 批次服務來建立主控台應用程式，以設定一組虛擬機器之間的分散式計算。本教學課程中建立的工作會從 Azure 儲存體中檔案評估文字，並傳回最常使用的單字。這些範例均以 C# 程式碼撰寫，並使用「適用於 .NET 的 Azure 批次程式庫」。


>[WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。 
>
>您需要使用 NuGet 來取得 **Microsoft.Azure.Batch.dll** 組件。在 Visual Studio 中建立專案之後，以滑鼠右鍵按一下 [方案總管]**** 中的專案，然後選擇 [管理 NuGet 封裝]****。在線上搜尋 **Azure.Batch**，然後按一下 [安裝] 以安裝「Azure 批次」封裝與相依性。
>
>請確定您的「Nuget 封裝管理員」版本是 2.8 或更新版本。您可以從 Visual Studio -> [說明] -> [關於 Microsoft Visual Studio] 對話方塊中尋找版本號碼。如果您的「Nuget 封裝管理員」版本較舊，則需要更新 Visual Studio，否則下載正確版本的 Nuget 相依性時可能會有問題。
> 
>此外，您也可以參考 MSDN 上的 [Azure 批次 Hello World 範例](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c)，以取得與此處討論的程式碼類似的範例。



###概念
批次服務用於排程可調整和分散式的計算。它可讓您執行分散於多部虛擬機器的大量工作負載。這些工作負載為大量計算提供有效支援。當您使用批次服務時，您可以利用下列資源：  

-	**帳戶** - 服務內唯一識別的實體。所有處理都是都透過批次帳戶執行。
-	**集區** - 執行工作應用程式的一組工作虛擬機器。
-	**工作虛擬機器** - 指派給集區的電腦，供指派給集區中執行之作業的工作使用。
-	**工作虛擬機器使用者** - 工作虛擬機器上的使用者帳戶。
-	**工作項目** - 指定在集區中的工作虛擬機器上執行計算的方式。
-	**作業** - 工作項目的執行中執行個體，由一組工作所組成。
-	**工作** - 與作業關聯並在工作虛擬機器上執行的應用程式。
-	**檔案** - 包含工作所處理的資訊。

讓我們從最基本的用法開始。

###建立 Azure 批次帳戶
您可以使用管理入口網站來建立批次帳戶。建立帳戶之後會提供金鑰給您。如需詳細資訊，請參閱 [Azure 批次技術概觀](http://azure.microsoft.com/documentation/articles/batch-technical-overview/)。  

###作法：將集區加入至帳戶
工作虛擬機器集區是您要執行工作時必須建立的第一組資源。  

1.	開啟 Microsoft Visual Studio 2013，在 [檔案]**** 功能表上，按一下 [新增]****，然後按一下 [專案]****。

2.	從 [Windows]**** 的 [Visual C#]**** 下，按一下 [主控台應用程式]****，將專案命名為 **GettingStarted**、將方案命名為 **AzureBatch**，然後按一下 [確定]****。

3.	將下列命名空間宣告加入至 Program.cs 的頂端：

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	確定已參考 Microsoft.Azure.Batch.dll 組件。

4.	將下列變數加入至 Program 類別：

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	取代下列值：
	-	**[name-of-pool]** - 您想要用於集區的名稱。
	-	**[name-of-batch-account]** -「批次」帳戶的名稱。
	-	**[key-of-batch-account]** - 提供給您用於「批次」帳戶的金鑰。
5.	將下列程式碼加入至 Main，以定義要使用的認證：

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	將下列程式碼加入 Main，以建立用來執行作業的用戶端：

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	將下列程式碼加入至 Main，以建立集區 (如果不存在的話)：

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName, 
		         osFamily: "3", 
		         vmSize: "small", 
		         targetDedicated: NumOfMachines);
		       newPool.Commit();                  
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	儲存並執行程式。已成功加入之集區的狀態為 **Active**。  

###作法：列出帳戶中的集區
如果您不知道現有集區的名稱，您可以取得帳戶中的集區清單。  

1.	將下列程式碼加入至 Main，以定義要使用的認證：  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	將下列程式碼加入 Main，以建立用來執行作業的用戶端：

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	將 Main 程序更新為下列程式碼，以寫出帳戶中所有集區的名稱和狀態，並建立集區 (如果不存在的話)：

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);
		
		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();
		
		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  
		
		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	儲存並執行程式。您會看到下列輸出。

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###作法：列出帳戶中的工作項目
如果您不知道現有工作項目的名稱，您可以取得帳戶中的工作項目清單。  

1.	將下列程式碼加入至 Main 結尾，以寫出帳戶中所有工作項目的名稱和狀態：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }   
		}
		Console.WriteLine("Press <Enter> to continue."); 
		Console.ReadLine();
7.	儲存並執行程式。因為我們尚未提交任何工作項目，您可能看不到任何輸出。我們將在下一節討論如何加入工作項目。  

##作法：將工作項目加入至帳戶
您必須建立工作項目來定義集區中執行工作的方式。  

1.	將下列變數加入至 Program 類別：

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	建立工作項目時，也會同時建立作業。您可以指派名稱給工作項目，但指派給作業的名稱一律為 **job-0000000001**。將下列程式碼加入至 Main (在清單工作項目程式碼之前)，以加入工作項目：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	儲存並執行程式。已成功加入之工作項目的狀態為 **Active**。您應該會看見下列輸出。

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.
		
		Workitem successfully added. Press <Enter> to continue.
		
		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###作法：將工作加入至作業
不含工作的工作項目沒有任何作用。建立工作項目並建立工作之後，您可以將工作加入至作業。讓我們將簡單的工作加入至作業。  

1.	將下列變數加入至 Program 類別：

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	將下列程式碼加入至 Main，以便將工作加入至作業，等候它們執行，然後報告結果：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }
		
		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	儲存並執行程式。結果應該看起來如下：

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1
		
		Task taskdata2 says:
		I am taskdata2
		
		Task taskdata3 says:
		I am taskdata3

###建立工作處理程式
現在，我們可以在 VM 上執行 hello world，讓我們實際操作一下。在本節中，我們將建立工作處理程式，並將它上傳至執行工作的工作虛擬機器。  

1.	在 [方案總管] 的 **AzureBatch** 方案中，建立一個名為 **ProcessTaskData** 的新主控台應用程式專案。

2.	將下列命名空間宣告加入至 Program.cs 的頂端：

		using System.Net;

3.	將下列程式碼加入至 Main，以用來處理資料：

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);
		
		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);
		
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

4.	儲存並建置專案。  

###準備資源以執行工作

當您使用批次服務建立分散式計算案例時，您將使用下列的基本工作流程：  

1.	將您要在分散式計算案例中使用的檔案，上傳到 Azure 儲存體帳戶。這些檔案必須在儲存體帳戶中，批次服務才能夠存取它們。當工作執行時，檔案會載入到工作虛擬機器中。
2.	將相依的二進位檔案上傳到儲存體帳戶。二進位檔案包含工作所執行的程式及相依組件。這些檔案也必須從儲存體中存取，然後載入到工作虛擬機器中。
3.	建立工作虛擬機器的集區。建立集區時，您可以指派要使用的工作虛擬機器大小。當工作執行時，系統會從這個集區指派虛擬機器給它。
4.	建立工作項目。工作項目可讓您管理工作的作業。當您建立工作項目時會自動建立作業。
5.	將工作新增至作業。每個工作都會使用您已上傳的程式來處理您所上傳檔案中的資訊。
6.	監視輸出的結果。  

我們已經示範步驟 #3 到 #6。現在來看看如何準備「Azure 儲存體」以執行工作。

####取得儲存體帳戶
您需要有儲存體帳戶，才能繼續完成本教學課程的其餘部分。如果您不知道如何執行這項操作，請參閱[建立 Azure 儲存體帳戶](#tutorial1_storage)。

####將資料上傳

1. 在「Azure 儲存體」帳戶中建立稱為 "gettingstarted" 的容器。這可以在 Azure 入口網站中完成。請確定您將 [存取] 欄位設定為 [公用容器]。

2. 將 "ProcessTaskData.exe" 上傳至容器。

3. 建立三個文字檔 (taskdata1.txt、taskdata2.txt、taskdata3.txt)，各包含下面其中一個段落，然後上傳至容器：

		當您需要彈性的資源來滿足您的商務需求時，可以使用「Azure 虛擬機器」來佈建依需求提供、可調整的計算基礎結構。您可以從組件庫建立執行 Windows、Linux 及企業應用程式 (例如 SharePoint 和 SQL Server) 的虛擬機器。或者，您可以擷取並使用自己的映像來建立自訂的虛擬機器。
		
		使用「Azure 雲端服務」可以快速部署及管理功能強大的應用程式和服務。只要上傳您的應用程式，Azure 便會包辦部署細節 (從佈建和負載平衡，到為確保持續可用性所進行的健康狀況監視)。您的應用程式有居業界領導地位的每月 99.95% SLA 做為後盾。您只需要把焦點放在應用程式上，不需要顧慮基礎結構。
		
		Azure 網站提供可調整、可靠且容易使用的環境來裝載 Web 應用程式。從一個範圍的架構與範本中選取，即可快速建立網站。可使用任何工具或作業系統，以 .NET、PHP、Node.js 或 Python 開發您的網站。從各式各樣的原始檔控制選項 (包括 TFS、GitHub 及 BitBucket) 中選擇，來設定連續整合及以小組方式進行開發。您可以利用其他 Azure 受管理服務 (例如儲存體、CDN 及 SQL Database)，讓您的網站功能與時俱進。


>[WACOM.NOTE] 在實際執行環境中，建議您使用共用的存取簽章。


>[WACOM.NOTE] 「Azure 儲存體」小組有發表一篇[部落格文章](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)，其中列出可協助您上傳檔案的「Azure 儲存體」總管。



###更新工作提交程式碼

1.	將下列變數加入至 Program 類別：

		private const string BlobPath = "[storage-path]"; 
	取代下列值：
	-	**[storage-path]** - 儲存體中 Blob 的路徑。例如: http://yiding.blob.core.windows.net/gettingstarted/

2. 更新工作提交程式碼，如下所示。

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit(); 

3. 儲存並執行程式。您應該會看到：

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.
		
		Workitem successfully added. Press <Enter> to continue.
		
		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.
		
		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3
		
		Task taskdata2 says:
		and 5
		application 3
		the 3
		
		Task taskdata3 says:
		a 5
		and 5
		to 3

###作法：刪除集區和工作項目
在處理您的工作項目之後，您可以透過刪除集區和工作項目來釋放資源。

####刪除集區
1.	將下列程式碼加入至 Main 結尾，以刪除集區：

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	儲存並執行程式。


####刪除工作項目
1.	將下列程式碼加入至 Main，以刪除工作項目：

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	儲存並執行程式。

###<a name="tutorial1_storage"></a>附錄：建立 Azure 儲存體帳戶
您對Azure 中的儲存體帳戶必須具有存取權，才能執行本教學課程中的程式碼。  

1.	登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。
2.	在瀏覽窗格的底部，按一下 [新增]****。  
![][1]
3.	依序按一下 [資料服務]****、[儲存體]****、[快速建立]****。
![][2]

4.	在 [URL]**** 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此值會成為 URI 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。
5.	選擇要在其中尋找儲存體的「位置/同質群組」****。
6.	(選用) 您可以啟用地理區域複寫。
7.	按一下 [建立儲存體帳戶]****。  

如需有關「Azure 儲存體」的詳細資訊，請參閱[如何使用 .NET 的 Azure Blob 儲存體服務](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)。  


##<a name="tutorial2"></a>教學課程 2：適用於 .NET 的 Azure 批次應用程式庫
本教學課程說明您如何在 Azure 批次上，使用批次應用程式服務來執行平行計算工作負載。

批次應用程式是Azure 批次的一項功能，提供以應用程式為中心的方法來管理和執行批次工作負載。使用批次應用程式 SDK 可讓您建立封裝，以批次啟用應用程式，並將這些封裝部署在您自己的帳戶中，或提供給其他批次使用者使用。批次也提供資料管理、作業監督、內建的診斷和記錄，以及工作之間相依性的支援。此外，它還包含一個管理入口網站，可供您管理作業、檢視記錄檔及下載輸出，讓您不需撰寫自己的用戶端。

在批次應用程式案例中，您可以使用批次應用程式雲端 SDK 來撰寫程式碼，將作業分割成平行工作、描述這些工作之間的任何相依性，以及指定如何執行每個工作。此程式碼會部署到批次帳戶。接著，用戶端只需指定作業類型和 REST API 的輸入檔，即可執行作業。

>[WACOM.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。只需要幾分鐘的時間，您就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。您可以使用 NuGet 來同時取得 <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">批次應用程式雲端 (Batch Apps Cloud)</a> 組件和 <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">批次應用程式用戶端 (Batch Apps Client)</a> 組件。在 Visual Studio 中建立專案之後，以滑鼠右鍵按一下 [方案總管]**** 中的專案，然後選擇 [管理 NuGet 封裝]****。您也可以下載「適用於批次應用程式的 Visual Studio 擴充功能」，當中包含可啟用應用程式雲端功能的專案範本和部署應用程式的功能，方法是從 <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">這裡</a> 下載，或在 Visual Studio 中透過 [擴充功能和更新] 功能表項目搜尋「批次應用程式」****。您也可以尋找 <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">MSDN 上的端對端範例。</a>
>

###Azure 批次應用程式的基本概念 
批次主要是用來處理現有的大量計算應用程式。它會利用現有的應用程式碼，並在動態、虛擬化、一般用途的環境中執行它。為了讓應用程式能夠與「批次應用程式」一起運作，必須先進行一些準備工作：

1.	準備您現有應用程式可執行檔 (亦即會在傳統伺服器陣列或叢集中執行的相同可執行檔) 的 zip 檔案，以及它所需要的任何支援檔案。然後使用管理入口網站或 REST API，將此 zip 檔案上傳到您的「批次」帳戶。
2.	建立「雲端組件」(會將您的工作負載分派給應用程式) 的 zip 檔案，然後透過管理入口網站或 REST API 上傳它。雲端組件包含兩個依據「雲端 SDK」建置的元件：
	1.	作業分割器 - 將作業細分成可以獨立處理的工作。例如，在動畫案例中，作業分割器可將影片作業細分成個別的畫面。 
	2.	工作處理器 - 叫用特定工作的應用程式可執行檔。例如，在動畫案例中，工作處理器會叫用轉譯程式，以轉譯手邊工作所指定的單一畫面。 
3.	提供可將作業提交給 Azure 中已啟用之應用程式的方法。這可能是您的應用程式 UI 或 Web 入口網站中的外掛程式，或甚至是執行管線中的自動服務。請參閱 MSDN 上的 <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">範例</a> 來取得相關範例。



###批次應用程式的重要概念
批次應用程式的程式設計和使用方式模型以下列重要概念為中心：

####作業
「作業」****係指使用者所提交的一件工作。當提交作業時，使用者將指定作業的類型、這項作業的任何設定，以及作業所需的資料。啟用的實作可以代替使用者決定這些詳細資料，或者，在某些情況下，使用者可以透過用戶端明確地提供這項資訊。作業會傳回結果。每個作業都有主要輸出，可能還有預覽輸出。如有需要，作業也可以傳回額外的輸出。

####工作
「工作」****係指在作業中要完成的一件工作。當使用者提交作業時，它會細分為較小的工作。然後，服務會處理這些個別的工作，再將工作結果組合成完整的作業輸出。工作的本質視作業類型而定。作業分割器會根據應用程式主要處理的工作部分，定義如何將作業細分為工作。下載工作輸出時，也可以個別下載，而這在某些情況下可能很有用，例如使用者可能會想要從動畫作業中下載個別的工作。

####合併工作
「合併工作」****係指一種將個別工作的結果組合成最終作業結果的特殊工作。在影片轉譯作業中，合併工作可能將轉譯的畫面組合成影片，或將所有轉譯的畫面壓縮成單一檔案。即使不需要實際進行任何「合併」，每個作業仍都會有合併工作。

####檔案
「檔案」****係指用來輸入到作業中的一份資料。作業可能沒有相關聯的輸入檔，也可能有一或多個輸入檔。同一個檔案也可用於多個作業中，例如在影片轉譯作業中，檔案可能是材質、模型等。在資料分析作業中，檔案可能是一組觀察值或測量值。

###啟用雲端應用程式
您的應用程式必須包含靜態欄位或屬性，其中含有應用程式的所有詳細資料。它指定應用程式的名稱，以及應用程式所處理的作業類型。使用 SDK 中的範本 (可透過「Visual Studio 組件庫」下載) 時，便會提供這個欄位或屬性。 

平行工作負載的雲端應用程式宣告的範例如下：

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####實作作業分割器和工作處理器
對於不易平行的工作負載，您必須實作作業分割器和工作處理器。 

####實作 JobSplitter.SplitIntoTasks
SplitIntoTasks 實作必須傳回一連串的工作。每個工作代表一件個別的工作，將加入佇列中由計算節點處理。每個工作都必須是獨立的，而且必須設有工作處理器所需的一切資訊。 

作業分割器指定的工作以 TaskSpecifier 物件表示。TaskSpecifier 有一些您可以在傳回工作之前設定的屬性。
  

-	TaskIndex 會被忽略，但可供工作處理器使用。您可以利用此屬性將索引傳遞給工作處理器。如果您不需要傳遞索引，則不需要設定此屬性。
-	Parameters 預設是一個空集合。您可以將項目加入它，或取代為新的集合。您可以使用 WithJobParameters 或 WithAllJobParameters 方法，從作業參數集合中複製項目。  


RequiredFiles 預設為空集合。您可以將項目加入它，或取代為新的集合。您可以使用 RequiringJobFiles 或 RequiringAllJobFiles 方法，從作業檔案集合中複製項目。

您可以指定相依於另一個特定工作的工作。若要這樣做，請設定 TaskSpecifier.DependsOn 屬性，並傳遞它依存之工作的識別碼：

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

工作必須等到相依的工作產生輸出之後才會執行。   

您也可以指定整組工作必須等到另一組工作完成之後才開始處理。在此情況下，您可以設定 TaskSpecifier.Stage 屬性。具有給定 Stage 值的工作必須等到 Stage 值更小的所有工作完成之後，才會開始處理。例如，必須等到 Stage 0、 1 或 2 的所有工作完成之後，具有 Stage 3 的工作才會開始處理。Stage 必須從 0 開始，各個 Stage 的順序必須沒有間隔，並且 SplitIntoTasks 必須依 Stage 順序傳回工作：例如，在傳回 Stage 為 1 的工作之後傳回 Stage 為 0 的工作，即為錯誤。

每個平行作業以一項特殊工作來結束，稱為合併工作。合併工作的任務是將平行處理工作的結果組合成最後的結果。「批次應用程式」會自動為您建立合併工作。  

在罕見的情況下，您可能想要明確地控制合併工作，例如自訂其參數。在此情況下，您可以傳回 TaskSpecifier 並將其 IsMerge 屬性設定為 true，以指定合併工作。這會覆寫自動合併工作。如果您要建立明確的合併工作：  

-	您可以只建立一個明確的合併工作
-	它必須是序列中的最後一個工作
-	它必須將 IsMerge 設定為 true，而且每個其他工作必須將 IsMerge 設為 false  


但請記住，通常您不需要明確地建立合併工作。  

下列程式碼示範 SplitIntoTasks 的簡單實作。  

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;
	 
	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####實作 ParallelTaskProcessor.RunExternalTaskProcess

從作業分割器傳回的每個非合併工作都呼叫 RunExternalTaskProcess。它應該使用適當的引數來叫用您的應用程式，並傳回需要保留以供稍後使用的輸出集合。

下列片段示範如何呼叫名為 application.exe 的程式。請注意，您可以使用 ExecutablePath helper 方法來建立絕對檔案路徑。

雲端 SDK 中的ExternalProcess 類別提供實用的 helper 邏輯來執行您的應用程式可執行檔。ExternalProcess 可以處理取消作業、將結束代碼轉譯為例外狀況、擷取標準輸出和標準錯誤，以及設定環境變數。此外，您也可以視需要直接使用 .NET Process 類別來執行您的程式。

RunExternalTaskProcess 方法會傳回 TaskProcessResult，其中包含輸出檔案的清單。至少必須包含合併所需的所有檔案。您可以選擇性地傳回記錄檔、預覽檔案和中繼檔 (例如工作失敗時進行診斷)。請注意，您的方法會傳回檔案路徑，而不是傳回檔案內容。

每個檔案都必須以它所包含之輸出的類型 (output (也就是最終作業輸出的一部分)、preview、log 或 intermediate) 來識別。這些值來自 TaskOutputFileKind 列舉。下列片段會傳回單一工作輸出，但沒有預覽或記錄檔。TaskProcessResult.FromExternalProcessResult 方法可以簡化從命令列程式中擷取結束代碼、處理器輸出和輸出檔案的常見案例：

下列程式碼示範 ParallelTaskProcessor.RunExternalTaskProcess 的簡單實作。

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");
	 
	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);
	 
	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();
	
	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####實作 ParallelTaskProcessor.RunExternalMergeProcess

這是在合併工作中呼叫。它應該叫用應用程式來結合先前工作的輸出 (以適合您應用程式的任何方式)，然後傳回所結合的輸出。 

RunExternalMergeProcess 的實作非常類似於 RunExternalTaskProcess，除了：  

-	RunExternalMergeProcess 會取用先前工作的輸出，而不是取用使用者輸入檔。因此，您應該根據工作識別碼來判斷您想要處理之檔案的名稱，而不是從 Task.RequiredFiles 集合來判斷。
-	RunExternalMergeProcess 傳回 JobOutput 檔案，以及選擇性地 JobPreview 檔案。


下列程式碼示範 ParallelTaskProcessor.RunExternalMergeProcess 的簡單實作。

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";
	 
	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);
	 
	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();
	 
	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###提交作業給批次應用程式 
作業描述要執行的工作負載，而且必須包含工作負載的所有資訊 (檔案內容除外)。例如，作業包含從用戶端流向作業分割器，再流向工作的組態設定。MSDN 上提供的範例示範如何提交工作，並提供多個用戶端，包括 Web 入口網站和命令列用戶端。 
 

<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg



<!--HONumber=35.2-->

<!--HONumber=46--> 
