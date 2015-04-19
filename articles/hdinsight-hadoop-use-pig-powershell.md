<properties
   pageTitle="在 HDInsight 中使用 Hadoop Pig | Azure"
   description="了解如何使用 PowerShell 將 Pig 工作提交至 HDInsight 上的 Hadoop。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#使用 PowerShell 執行 Pig 工作

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

本文件提供使用 PowerShell 將 Pig 工作提交至 HDInsight 叢集上的 Hadoop 的範例。Pig 可讓您使用可建立資料轉換模型的語言 (Pig Latin) 撰寫 MapReduce 工作，而不是撰寫對應和縮減函數。

> [AZURE.NOTE] 這份文件不提供範例中使用 Pig Latin 陳述式的執行工作詳細描述。如需此範例中使用的 Pig Latin 相關資訊，請參閱 [<a href="../hdinsight-use-pig/" target="_blank">搭配使用 Pig 與 HDInsight 上的 Hadoop</a>]。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 或 Linux 型)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>使用 PowerShell 執行 Pig 工作

Azure PowerShell 提供 *cmdlets*，可讓您從遠端執行 HDInsight 上的 Pig 工作。在內部，您可以使用在 HDInsight 叢集上執行的 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集上執行 Pig 工作時，會使用下列 Cmdlet。

* **Add-AzureAccount** - 驗證您 Azure 訂閱的 PowerShell

* **New-AzureHDInsightPigJobDefinition** - 使用指定的 Pig Latin 陳述式，建立新的 *工作定義*

* **Start-AzureHDInsightJob** - 將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的 *工作*物件

* **Wait-AzureHDInsightJob** - 使用工作物件來檢查工作的狀態。它將等到工作完成，或已超過等待時間

* **Get-AzureHDInsightJobOutput** - 用來擷取工作的輸出。

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集上執行工作。 

1. 使用編輯器，將下列程式碼儲存為 **pigjob.ps1**。您必須將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Login to your Azure subscription
		Add-AzureAccount

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

		# Print the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

2. 開啟新的 **Microsoft Azure PowerShell** 提示字元。將目錄變更至 **pigjob.ps1** 檔案的位置，然後使用下列命令來執行指令碼。

		.\pigjob.ps1

7. 工作完成時，應該會傳回與下面類似的資訊。

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            :
		PercentComplete : 100% complete
		Query           : LOGS = LOAD 'wasb:///example/data/sample.log';LEVELS = foreach LOGS generate REGEX_EXTRACT($0,
			'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is
			not null;GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;FREQUENCIES = foreach GROUPEDLEVELS
			generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;RESULT = order FREQUENCIES by
			COUNT desc;DUMP RESULT;
			State           : Completed
			StatusDirectory : /tutorial/pig/status
			SubmissionTime  : 11/20/2014 4:04:58 PM
			JobId           : job_1415949758166_0023

			Display the standard output ...
			(TRACE,816)
			(DEBUG,434)
			(INFO,96)
			(WARN,11)
			(ERROR,6)
			(FATAL,2)

##<a id="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。若要檢視這項工作的錯誤資訊，請將下列內容新增至 **pigjob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##<a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 Pig 工作、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Pig 的一般資訊。

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)
<!--HONumber=47-->
