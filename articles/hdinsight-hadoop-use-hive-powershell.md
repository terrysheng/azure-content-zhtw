<properties
   pageTitle="在 HDInsight 上使用 Hadoop Hive | Azure"
   description="透過 PowerShell，在 HDInsight 中使用 Hadoop Hive。"
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

#使用 PowerShell 執行 Hive 查詢

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

本文件提供使用 PowerShell 在 HDInsight 叢集的 Hadoop 上執行 Hive 查詢的範例。

> [AZURE.NOTE] 這份文件不提供範例中使用 HiveQL 陳述式的執行工作詳細描述。如需此範例中使用的 HiveQL 相關資訊，請參閱 [<a href="../hdinsight-use-hive/" target="_blank">搭配使用 Hive 與 HDInsight 上的 Hadoop</a>]。


##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 或 Linux 型)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>使用 PowerShell 執行 Hive 查詢

Azure PowerShell 提供 *cmdlets*，可讓您從遠端在 HDInsight 上執行 Hive 查詢。在內部，您可以使用在 HDInsight 叢集上執行的 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集上執行 Hive 查詢時，會使用下列 Cmdlet。

* **Add-AzureAccount** - 驗證您的 Azure 訂閱的 PowerShell

* **New-AzureHDInsightHiveJobDefinition** - 使用指定的 HiveQL 陳述式，建立新的 *工作定義*

* **Start-AzureHDInsightJob** - 將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的 *工作*物件

* **Wait-AzureHDInsightJob** - 使用工作物件來檢查工作的狀態。它將等到工作完成，或已超過等待時間

* **Get-AzureHDInsightJobOutput** - 用來擷取工作的輸出

* **Invoke-Hive** - 用來執行 HiveQL 陳述式並封鎖其完成。然後，傳回結果

* **Use-AzureHDInsightCluster** - 設定要用於 **Invoke-Hive** 命令的目前叢集

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集上執行工作。 

1. 使用編輯器，將下列程式碼儲存為 **hivejob.ps1**。您必須將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. 開啟新的 **Microsoft Azure PowerShell** 提示字元。將目錄變更至 **hivejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼。

		.\hivejob.ps1

7. 工作完成時，應該會傳回與下面類似的資訊。

		Display the standard output...
		[ERROR]	3

4. 如前所述，**Invoke-Hive** 可以用來執行查詢，並等候回應。使用下列命令，並將 **CLUSTERNAME** 取代為您叢集的名稱。

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	The output will look like the following.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] 如果 HiveQL 查詢的時間很長，您可以使用 PowerShell Here-Strings 或 HiveQL 指令碼檔案。下列程式碼片段說明如何使用 *Invoke-Hive* 指令程式來執行 HiveQL 指令碼檔案。必須將 HiveQL 指令碼檔案上傳至 WASB。
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> 如需 Here-Strings 的詳細資訊，請參閱 [<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-Strings</a>] (英文)。

##<a id="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。若要檢視這項工作的錯誤資訊，請將下列內容新增至 **hivejob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##<a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 Hive 查詢、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊。

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](../hdinsight-use-hive/)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](../hdinsight-use-pig/)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](../hdinsight-use-mapreduce/)

<!--HONumber=47-->
