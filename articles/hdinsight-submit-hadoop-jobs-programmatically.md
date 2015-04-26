<properties 
	pageTitle="在 HDInsight 上提交 Hadoop 工作 | Azure" 
	description="了解如何將 Hadoop 工作提交至 Azure HDInsight Hadoop。" 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>

# 在 HDInsight 上提交 Hadoop 工作

本文中，您將了解如何使用 PowerShell 和 HDInsight .NET SDK 來提交 MapReduce 和 Hive 工作。

**必要條件：**

開始閱讀本文之前，您必須符合下列必要條件：

* Azure HDInsight 叢集。如需相關指示，請參閱[開始使用 HDInsight][hdinsight-get-started]或[佈建 HDInsight 叢集][hdinsight-provision]。
* 安裝並設定 Azure PowerShell。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。


## 本文內容

* [使用 PowerShell 提交 MapReduce 工作](#mapreduce-powershell)
* [使用 PowerShell 提交 Hive 工作](#hive-powershell)
* [使用 PowerShell 提交 Sqoop 工作](#sqoop-powershell)
* [使用 HDInsight .NET SDK 提交 MapReduce 工作](#mapreduce-sdk)
* [使用 HDInsight .NET SDK 提交 Hadoop 串流 MapReduce 工作](#streaming-sdk)
* [使用 HDInsight .NET SDK 提交 Hive 工作](#hive-sdk)
* [後續步驟](#nextsteps)

## <a id="mapreduce-powershell"></a> 使用 PowerShell 提交 MapReduce 工作
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。如需有關在 HDInsight 上使用 PowerShell 的詳細資訊，請參閱[使用 PowerShell 來管理 HDInsight][hdinsight-admin-powershell]。

Hadoop MapReduce 是一種可撰寫應用程式來處理大量資料的軟體架構。HDInsight 叢集隨附一個 jar 檔案，位於 *\example\jars\hadoop-examples.jar*，內有數個 MapReduce 範例。HDInsight 3.0 版叢集已將此檔案重新命名為 hadoop-mapreduce-examples.jar。其中一個範例是計算來源檔案中的文字出現率。在這堂課，您將了解如何從工作站使用 PowerShell 來執行字數統計範例。如需有關開發和執行 MapReduce 工作的詳細資訊，請參閱[使用 MapReduce 搭配 HDInsight][hdinsight-use-mapreduce]。

**使用 PowerShell 來執行字數統計 MapReduce 程式**

1.	開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

3. 執行下列 PowerShell 命令來設定這兩個變數：
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	訂用帳戶是您用來建立 HDInsight 叢集的訂用帳戶。HDInsight 叢集是您想要用來執行 MapReduce 工作的叢集。
	
5. 執行下列命令來建立 MapReduce 工作定義：

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。如需 wasb 首碼的詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。

6. 執行下列命令來執行 MapReduce 工作：

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	除了 MapReduce 工作定義，您也提供想要用來執行 MapReduce 工作的 HDInsight 叢集名稱。 

7. 執行下列命令來檢查 MapReduce 工作是否完成：

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		

8. 執行下列命令來檢查 MapReduce 工作執行時有無任何錯誤：	

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
					
	下列螢幕擷取畫面顯示執行成功的輸出。不然您會看到一些錯誤訊息。

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]

		
**擷取 MapReduce 工作的結果**

1. 開啟 **Azure PowerShell**。
2. 執行下列 PowerShell 命令來設定這三個變數：

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	Azure 儲存體帳戶是您在 HDInsight 叢集佈建期間指定的帳戶。儲存體帳戶用來代管當做預設 HDInsight 叢集檔案系統的 Blob 容器。  Blob 儲存容器名稱通常與 HDInsight 叢集同名，除非您在佈建叢集時指定不同的名稱。

3. 執行下列命令來建立 Azure 儲存體內容物件：

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	在您有多個訂用帳戶，而且預設訂用帳戶不是要使用的訂用帳戶時，可使用 Select-AzureSubscription 設定目前的訂用帳戶。 

4. 執行下列命令將 Blob 容器的 MapReduce 工作輸出下載至工作站：

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	 *example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。 *part-r-00000* 是 MapReduce 工作輸出的預設檔案名稱。  檔案會以相同資料夾結構下載至本機資料夾。例如，在下列螢幕擷取畫面中，目前資料夾是 C 根資料夾。  檔案會下載至 *C:\example\data\WordCountOutput\* 資料夾。

5. 執行下列命令來列印 MapReduce 工作輸出檔案：

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，內有文字和字數。  指令碼使用 findstr 命令列出包含 "there" 的所有文字。


> [AZURE.NOTE] 如果您在記事本中開啟 ./example/data/WordCountOutput/part-r-00000 (MapReduce 工作的多行輸出)，您會發現換行不正確。這是預期行為。









































































































































## <a id="hive-powershell"></a> 使用 PowerShell 提交 Hive 工作
Apache [hdinsight-use-hive][apache-hive] 提供一種透過 SQL 式指令碼語言執行 MapReduce 工作的工具，稱為  *HiveQL*，可用來總結、查詢和分析大量資料。 

HDInsight 叢集隨附一個範例 Hive 資料表，稱為  *hivesampletable*。在這堂課，您將使用 PowerShell 執行 Hive 工作，從 Hive 資料表中列出一些資料。 

**使用 PowerShell 執行 Hive 工作**

1.	開啟 **Azure PowerShell**。如需有關開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。

2. 在下列命令中設定前兩個變數，然後執行命令：
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring 是 HiveQL 查詢。

3. 執行下列命令來選取 Azure 訂用帳戶，以及用來執行 Hive 工作的叢集：

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. 提交 Hive 工作：

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	您可以使用 -File 參數來指定 HDFS 上的 HiveQL 指令碼檔案。

如需有關 Hive 的詳細資訊，請參閱[使用 Hive 搭配 HDInsight][hdinsight-use-hive]。

## <a id="sqoop-powershell"></a>使用 PowerShell 提交 Sqoop 工作

請參閱[使用 Sqoop 搭配 HDInsight][hdinsight-use-sqoop]。

## <a id="mapreduce-sdk"></a> 使用 HDInsight .NET SDK 提交 MapReduce 工作
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。HDInsight 叢集隨附一個 jar 檔案，位於 *\example\jars\hadoop-examples.jar*，內有數個 MapReduce 範例。其中一個範例是計算來源檔案中的文字出現率。在這堂課，您將了解如何建立 .NET 應用程式來執行字數統計範例。如需有關開發和執行 MapReduce 工作的詳細資訊，請參閱[使用 MapReduce 搭配 HDInsight][hdinsight-use-mapreduce]。


使用 SDK 佈建 HDInsight 叢集需要執行下列程序：

- 安裝 HDInsight .NET SDK
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**
您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2012。

2. 在 [檔案] 功能表中按一下 [新增]，然後按一下 [專案]。

3. 在 [新增專案] 中，輸入或選取下列值：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Property</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Value</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Category</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
	</table>

4. 按一下 [確定] 以建立專案。


5. 在 [**工具**] 功能表中按一下 [**程式庫封裝管理員**]，然後按一下 [**封裝管理員主控台**]。

6. 在主控台中執行下列命令，以安裝封裝。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	此命令會將 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。版本應該為 0.11.0.1 或更新版本。

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. 將下列函數定義加入至類別。此函數用來等待 Hadoop 工作完成。

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. 在 Main() 函數中，複製並貼上下列程式碼：
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	以上就是程式需要設定的所有變數。您可以從 [Azure 管理入口網站][azure-management-portal]取得 Azure 訂用帳戶名稱。 

	如需憑證的詳細資訊，請參閱[建立和上傳 Azure 的管理憑證][azure-certificate]。設定憑證的一種簡單方法就是執行  *Get-AzurePublishSettingsFile* 和 *Import-AzurePublishSettingsFile* PowerShell Cmdlet。它們會自動建立和上傳管理憑證。執行 PowerShell Cmdlet 之後，您可以從工作站開啟 *certmgr.msc*，並展開 *Personal/Certificates*。PowerShell Cmdlet 所建立的憑證 *Issued To* 和 *Issued By* 欄位具有 *Azure Tools*。

	Azure 儲存體帳戶名稱是您佈建 HDInsight 叢集時指定的帳戶。預設容器名稱與 HDInsight 叢集名稱相同。
	
11. 在 Main() 函數中，附加下列程式碼來定義 MapReduce 工作：


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。如需 wasb 首碼的詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。
		
12. 在 Main() 函數中，附加下列程式碼來建立 JobSubmissionCertificateCredential 物件：

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. 在 Main() 函數中，附加下列程式碼來執行工作並等待工作完成：

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. 在 Main() 函數中，附加下列程式碼來列印 MapReduce 工作輸出：

		// Print the MapReduce job output
		Stream stream = new MemoryStream();
		
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
		
		blockBlob.DownloadToStream(stream);
		stream.Position = 0;
		
		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());
		
        Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

	輸出資料夾是您定義 MapReduce 工作時指定的資料夾。預設檔案名稱是 *part-r-00000*。

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗會開啟並顯示應用程式的狀態及應用程式輸出。 

## <a id="streaming-sdk"></a> 使用 HDInsight .NET SDK 提交 Hadoop 串流工作
HDInsight 叢集隨附一個以 C# 開發的字數統計 Hadoop 串流程式。mapper 程式為 */example/apps/cat.exe*，而 reduce 程式為 */example/apps/wc.exe*。在這堂課，您將了解如何建立 .NET 應用程式來執行字數統計範例。 

如需建立 .Net 應用程式以提交 MapReduce 工作的詳細資料，請參閱[使用 HDInsight .NET SDK 提交 MapReduce 工作](#mapreduce-sdk)。

如需開發與部署 Hadoop 串流工作的詳細資訊，請參閱[開發 HDInsight 的 C# Hadoop 串流程式][hdinsight-develop-streaming-jobs]。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace SubmitStreamingJob
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {

				// Set the variables
				string subscriptionID = "<Azure subscription ID>";
				string certFriendlyName = "<certificate friendly name>";
		
				string clusterName = "<HDInsight cluster name>";
				string statusFolderName = @"/tutorials/wordcountstreaming/status";

	            // Define the Hadoop streaming MapReduce job
	            StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
	            {
	                JobName = "my word counting job",
	                StatusFolder = statusFolderName,
	                Input = "/example/data/gutenberg/davinci.txt",
	                Output = "/tutorials/wordcountstreaming/output",
	                Reducer = "wc.exe",
	                Mapper = "cat.exe"
	            };
	
	            myJobDefinition.Files.Add("/example/apps/wc.exe");
	            myJobDefinition.Files.Add("/example/apps/cat.exe");
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Create a hadoop client to connect to HDInsight
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	
	            // Run the MapReduce job
	            Console.WriteLine("----- Submit the Hadoop streaming job ...");
	            JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);
	
	            // Wait for the job to complete
	            Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
	            WaitForJobCompletion(mrJobResults, jobClient);
	
	            // Display the error log
	            Console.WriteLine("----- The hadoop streaming job error log.");
	            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }
	
	            // Display the output log
	            Console.WriteLine("----- The hadoop streaming job output log.");
	            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }
	
	            Console.WriteLine("----- Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}






## <a id="hive-sdk"></a> 使用 HDInsight .NET SDK 提交 Hive 工作 
HDInsight 叢集隨附一個範例 Hive 資料表，稱為  *hivesampletable*。在這堂課，您將建立 .NET 應用程式來執行 Hive 工作，以列出 HDInsight 叢集上建立的 Hive 資料表。如需有關使用 Hive 的詳細資訊，請參閱[使用 Hive 搭配 HDInsight][hdinsight-use-hive]。

使用 SDK 佈建 HDInsight 叢集需要執行下列程序：

- 安裝 HDInsight .NET SDK
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**
您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2012。

2. 在 [檔案] 功能表中按一下 [新增]，然後按一下 [專案]。

3. 在 [新增專案] 中，輸入或選取下列值：

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台應用程式</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
	</table>

4. 按一下 [確定] 以建立專案。


5. 在 [**工具**] 功能表中按一下 [**程式庫封裝管理員**]，然後按一下 [**封裝管理員主控台**]。

6. 在主控台中執行下列命令，以安裝封裝。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	此命令會將 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. 將下列函數定義加入至類別。此函數用來等待 Hadoop 工作完成。

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. 在 Main() 函數中，複製並貼上下列程式碼：
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
		
	
	以上就是程式需要設定的所有變數。您可以向系統管理員查詢 Azure 訂用帳戶識別碼。 

	如需憑證的詳細資訊，請參閱[建立和上傳 Azure 的管理憑證][azure-certificate]。設定憑證的一種簡單方法就是執行  *Get-AzurePublishSettingsFile* 和 *Import-AzurePublishSettingsFile* PowerShell Cmdlet。它們會自動建立和上傳管理憑證。執行 PowerShell Cmdlet 之後，您可以從工作站開啟 *certmgr.msc*，並展開 *Personal/Certificates*。PowerShell Cmdlet 所建立的憑證 *Issued To* 和 *Issued By* 欄位具有 *Azure Tools*。
	
11. 在 Main() 函數中，附加下列程式碼來定義 Hive 工作：

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	您可以使用 File 參數來指定 HDFS 上的 HiveQL 指令碼檔案。例如

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. 在 Main() 函數中，附加下列程式碼來建立 JobSubmissionCertificateCredential 物件：
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. 在 Main() 函數中，附加下列程式碼來執行工作並等待工作完成：

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. 在 Main() 函數中，附加下列程式碼來列印 Hive 工作輸出：

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。輸出如下：

	hivesampletable




## <a id="nextsteps"></a> 後續步驟
在本文中，您學到幾種佈建 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [佈建 HDInsight 叢集][hdinsight-provision]
* [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]
* [使用 Hive 搭配 HDInsight][hdinsight-use-hive]
* [使用 Pig 搭配 HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[powershell-install-configure]: ../install-configure-powershell/

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png 
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!--HONumber=42-->
