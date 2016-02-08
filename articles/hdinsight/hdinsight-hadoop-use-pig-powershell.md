<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Pig 與 PowerShell | Microsoft Azure"
   description="了解如何使用 Azure PowerShell 將 Pig 工作提交至 HDInsight 上的 Hadoop 叢集。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#使用 PowerShell 執行 Pig 工作

[AZURE.INCLUDE [Pig 選取器](../../includes/hdinsight-selector-use-pig.md)]

本文件提供使用 Azure PowerShell 將 Pig 工作提交至 HDInsight 叢集上的 Hadoop 的範例。Pig 可讓您使用可建立資料轉換模型的語言 (Pig Latin) 撰寫 MapReduce 工作，而不是撰寫對應和歸納函數。

> [AZURE.NOTE] 本文件不提供範例中使用的 Pig Latin 陳述式所執行的工作詳細的描述。如需此範例中使用的 Pig Latin 相關資訊，請參閱[在 HDInsight 上搭配 Hadoop 使用 Pig](hdinsight-use-pig.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **具有 Azure PowerShell 的工作站**。請參閱[安裝 Azure PowerShell 1.0 及更新版本](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)。


##<a id="powershell"></a>使用 PowerShell 執行 Pig 工作

Azure PowerShell 提供 *Cmdlet*，可讓您從遠端在 HDInsight 上執行 Pig 工作。在內部，您可以使用在 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集上執行 Pig 工作時，會使用下列 Cmdlet：

* **Login-AzureRmAccount**：驗證您 Azure 訂用帳戶的 PowerShell

* **New-AzureRmHDInsightPigJobDefinition**：使用指定的 Pig Latin 陳述式建立新的*工作定義*

* **Start-AzureRmHDInsightJob**：將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的*工作*物件

* **Wait-AzureRmHDInsightJob**：使用工作物件來檢查工作的狀態。它會等到工作完成，或等到等候時間超過。

* **Get-AzureRmHDInsightJobOutput**：用來擷取工作的輸出

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集上執行工作。

1. 使用編輯器，將下列程式碼儲存為 **pigjob.ps1**。您必須將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Login to your Azure subscription
		Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds=Get-Credential

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -ClusterCredential $creds

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

		# Display the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. 開啟新的 Azure PowerShell 命令提示字元。將目錄變更至 **pigjob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

		.\pigjob.ps1
        
    會先提示登入您的 Azure 訂用帳戶。接著，您還必須提供 HDInsight 叢集的 HTTPs/系統管理帳戶名稱和密碼。

7. 工作完成時，應該會傳回與下面類似的資訊：

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME.
        HttpEndpoint    : CLUSTERNAME.azurehdinsight.net
        State           : SUCCEEDED
        JobId           : job_1444852971289_0018
        ParentId        :
        PercentComplete : 100% complete
        ExitValue       : 0
        User            : admin
        Callback        :
        Completed       : done
        
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。若要檢視這項工作的錯誤資訊，請將下列命令新增至 **pigjob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            -DisplayOutputType StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##<a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 Pig 工作、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 Pig 的一般資訊：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0128_2016-->