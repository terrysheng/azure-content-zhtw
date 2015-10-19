<properties
	pageTitle="在 HDInsight 上提交 Hadoop 工作 | Microsoft Azure"
	description="了解如何將 Hadoop 工作提交至 Azure HDInsight Hadoop。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="jgao"/>

# 在 HDInsight 上提交 Hadoop 工作

了解如何使用 Azure PowerShell 提交 MapReduce 和 Hive 工作，以及如何使用 HDInsight .NET SDK 提交 MapReduce、Hadoop 資料流和 Hive 工作。

> [AZURE.NOTE]此文章中的步驟必須從 Windows 用戶端執行。如需利用 Linux、OS X 或 Unix 用戶端來使用 HDInsight 上的 MapReduce、Hive 或 Pig 的相關資訊，請參閱下列文章，並選取每篇文章中的 **SSH** 或 **Curl** 連結：
>
> - [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
> - [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
> - 〈[搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)〉

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

* **Azure HDInsight 叢集**。相關指示請參閱[開始使用 HDInsight][hdinsight-get-started] 或[在 HDInsight 建立 Hadoop 叢集][hdinsight-provision]。
- **具有 Azure PowerShell 的工作站**。請參閱[安裝及使用 Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。



##使用 Azure PowerShell 提交 MapReduce 工作
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。如需關於搭配 HDInsight 使用 Azure PowerShell 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]。

Hadoop MapReduce 是一種可撰寫應用程式來處理大量資料的軟體架構。HDInsight 叢集隨附一個 JAR 檔案 (位於 *\\example\\jars\\hadoop-mapreduce-examples.jar*)，內含數個 MapReduce 範例。

其中一個範例是計算來源檔案中的文字出現率。在這堂課中，您將了解如何從工作站使用 Azure PowerShell 執行字數統計範例。如需關於開發和執行 MapReduce 工作的詳細資訊，請參閱[搭配 HDInsight 使用 MapReduce][hdinsight-use-mapreduce]。

**使用 Azure PowerShell 執行字數統計 MapReduce 程式**

1.	開啟 **Azure PowerShell**。如需如何開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝和設定 Azure PowerShell][powershell-install-configure]。

3. 執行以下 Azure PowerShell 命令來設定下列變數：

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"

	訂用帳戶名稱是您用來建立 HDInsight 叢集的訂用帳戶。HDInsight 叢集是您想要用來執行 MapReduce 工作的叢集。

5. 執行下列命令來建立 MapReduce 工作定義：

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	有兩個引數。第一個是來源檔案名稱，第二個是輸出檔案路徑。如需關於 wasb:// 首碼的詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

6. 執行下列命令來執行 MapReduce 工作：

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

	除了 MapReduce 工作定義之外，您也提供了想要用來執行 MapReduce 工作的 HDInsight 叢集名稱。

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
2. 執行以下 Azure PowerShell 命令來設定下列變數：

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

	儲存體帳戶名稱是您在 HDInsight 叢集建立期間指定的 Azure 儲存體帳戶。儲存體帳戶會用來代管當做預設 HDInsight 叢集檔案系統的 Blob 容器。除非您在建立叢集時指定不同的名稱，否則容器名稱通常與 HDInsight 叢集同名。

3. 執行以下命令來建立 Azure Blob 儲存體內容物件：

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	如果您有多個訂用帳戶，而且預設訂用帳戶不是要使用的帳戶時，可使用 **Select-AzureSubscription** 設定目前的訂用帳戶。

4. 執行以下命令將 Blob 容器的 MapReduce 工作輸出下載至工作站：

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*example/data/WordCountOutput* 資料夾是您執行 MapReduce 工作時指定的輸出資料夾。*part-r-00000* 是 MapReduce 工作輸出的預設檔案名稱。檔案會下載至本機資料夾中的同一個資料夾結構。例如，在以下螢幕擷取畫面中，目前的資料夾是 C: 根資料夾。檔案會下載至：

**C:\\example\\data\\WordCountOutput*

5. 執行下列命令來列印 MapReduce 工作輸出檔案：

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	MapReduce 工作會產生一個名為 *part-r-00000* 的檔案，且檔案內含有文字和計數。指令碼會使用 **findstr** 命令列出含有 "there" 的所有文字。


> [AZURE.NOTE]如果您在記事本中開啟 ./example/data/WordCountOutput/part-r-00000 (MapReduce 工作的多行輸出)，會發現換行未正確轉譯。這是預期行為。









































































































































##使用 Azure PowerShell 提交 Hive 工作
[Apache Hive][apache-hive] 提供一種方法，稱為 *HiveQL*，可透過類似 SQL 的指令碼語言執行 MapReduce 工作，藉此總結、查詢和分析大量資料。

HDInsight 叢集隨附一個範例 Hive 資料表，稱為 *hivesampletable*。在這堂課中，您將使用 Azure PowerShell 執行 Hive 工作，從 Hive 資料表中列出一些資料。

**使用 Azure PowerShell 執行 Hive 工作**

1.	開啟 **Azure PowerShell**。如需如何開啟 Azure PowerShell 主控台視窗的指示，請參閱[安裝和設定 Azure PowerShell][powershell-install-configure]。

2. 在下列命令中設定前兩個變數，然後執行命令：

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring 是 HiveQL 查詢。

3. 執行以下命令來選取 Azure 訂用帳戶，以及用來執行 Hive 工作的叢集：

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. 執行以下命令提交 Hive 工作：

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	您可以使用 **-File** 參數，指定 Hadoop 分散式檔案系統 (HDFS) 中的 HiveQL 指令碼檔。

如需 Hive 的詳細資訊，請參閱[在 HDInsight 上使用 Hive][hdinsight-use-hive]。


## 使用 Visual Studio 提交 Hive 工作

請參閱[開始使用 HDInsight Hadoop Tools for Visual Studio][hdinsight-visual-studio-tools]。

##使用 Azure PowerShell 提交 Sqoop 工作

請參閱[在 HDInsight 上使用 Sqoop][hdinsight-use-sqoop]。

##使用 HDInsight .NET SDK 提交 MapReduce 工作
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。HDInsight 叢集隨附一個 JAR 檔案 (位於 *\\example\\jars\\hadoop-mapreduce-examples.jar*)，內含數個 MapReduce 範例。其中一個範例是計算來源檔案中的文字出現率。在這堂課，您將了解如何建立 .NET 應用程式來執行字數統計範例。如需關於開發和執行 MapReduce 工作的詳細資訊，請參閱[搭配 HDInsight 使用 MapReduce][hdinsight-use-mapreduce]。

**提交 WordCount MapReduce 工作**

1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 NuGet Package Manager Console 執行下列命令：

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. 在 Program.cs 檔案中使用下列 using 陳述式：

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. 在 Main() 函式中加入下列程式碼：

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. 將下列 helper 函式加入類別。

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. 按 **F5** 鍵執行應用程式。










##使用 HDInsight .NET SDK 提交 Hadoop 串流工作
HDInsight 叢集會隨附一個以 C# 開發的字數統計 Hadoop 資料流程式。對應器程式為 */example/apps/cat.exe*，而歸納器程式為 */example/apps/wc.exe*。在這堂課中，您將了解如何建立 .NET 應用程式來執行字數統計範例。

如需關於建立 .NET 應用程式以提交 MapReduce 工作的詳細資料，請參閱[使用 HDInsight .NET SDK 提交 MapReduce 工作](#mapreduce-sdk)。

如需關於開發與部署 Hadoop 資料流工作的詳細資訊，請參閱[開發 HDInsight 的 C# Hadoop 資料流程式][hdinsight-develop-streaming-jobs]。

下列程序只適用於 Windows 上的 HDInsight 叢集。Linux 叢集尚不支援 C# 串流處理。不過，您可以使用 .NET 程式來提交以 Linux 叢集支援的其他程式設計語言撰寫的串流工作。例如 Python。如需 Python 串流處理範例，請參閱[開發適用於 HDInsight 的 Python 串流處理程式](hdinsight-hadoop-streaming-python.md)。

**提交 WordCount MapReduce 工作**

1. 從 Visual Studio Package Manager Console 執行下列 Nuget 命令以匯入套件。

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. 在 Program.cs 檔案中使用下列 using 陳述式：

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. 在 Main() 函式中加入下列程式碼：

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. 將 help 函式加入類別。

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. 按 **F5** 鍵執行應用程式。

##使用 HDInsight .NET SDK 提交 Hive 工作
HDInsight 叢集隨附一個範例 Hive 資料表，稱為 *hivesampletable*。在這堂課中，您將建立 .NET 應用程式來執行 Hive 工作，以列出在 HDInsight 叢集中建立的 Hive 資料表。如需關於使用 Hive 的詳細資訊，請參閱[搭配 HDInsight 使用 Hive][hdinsight-use-hive]。

使用 SDK 建立 HDInsight 叢集需要執行以下程序：

- 安裝 HDInsight .NET SDK
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK** 您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 版本。下一個程序會顯示相關指示。

**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2013 或 2015。

2. 以下列設定建立新的專案：

	|屬性|值|
	|--------|-----|
	|範本|Templates/Visual C#/Windows/Console Application|
	|名稱|SubmitHiveJob|

3. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。
4. 在主控台中執行下列命令，以安裝套件：

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	這些命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

5. 在 [方案總管] 中按兩下 **Program.cs** 予以開啟，貼上下列程式碼，並提供變數的值：

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. 按 **F5** 鍵執行應用程式。

##使用 HDInsight Tools for Visual Studio 提交工作

您可以使用 HDInsight Tools for Visual Studio 來執行 Hive 查詢和 Pig 指令碼。請參閱[開始使用 Visual Studio Hadoop tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md)。


##後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [在 HDInsight 中建立 Hadoop 叢集][hdinsight-provision]
* [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]
* [搭配 HDInsight 使用 Hivet][hdinsight-use-hive]
* [搭配 HDInsight 使用 Pig][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Oct15_HO2-->