<properties
   pageTitle="搭配使用 MapReduce 和 PowerShell 與 Hadoop | Microsoft Azure"
   description="了解如何使用 PowerShell 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop。"
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

#使用 PowerShell 搭配執行 Hive 查詢與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [MapReduce 選取器](../../includes/hdinsight-selector-use-mapreduce.md)]

本文件提供使用 Azure PowerShell 在 HDInsight 叢集的 Hadoop 中執行 MapReduce 工作的範例。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目。

- **Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 型或 Linux 型)**

- **具有 Azure PowerShell 的工作站**。請參閱[安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

##<a id="powershell"></a>使用 Azure PowerShell 執行 MapReduce 工作

Azure PowerShell 提供 *Cmdlet*，可讓您從遠端在 HDInsight 上執行 MapReduce 工作。在內部，您可以使用在 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 的 REST 呼叫來達到此目的。

在遠端 HDInsight 叢集中執行 MapReduce 工作時，會使用下列 Cmdlet。

* **Login-AzureRmAccount**：驗證您 Azure 訂用帳戶的 PowerShell

* **New-AzureRmHDInsightMapReduceJobDefinition**：使用指定的 MapReduce 資訊建立新的*工作定義*

* **Start-AzureRmHDInsightJob**：將工作定義傳送至 HDInsight、啟動工作，然後傳回可用來檢查工作狀態的*工作*物件

* **Wait-AzureRmHDInsightJob**：使用工作物件來檢查工作的狀態。它會等到工作完成，或等到等候時間超過。

* **Get-AzureRmHDInsightJobOutput**：用來擷取工作的輸出

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作。

1. 使用編輯器，將下列程式碼儲存為 **mapreducejob.ps1**。您必須將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

		#Specify the values
		$clusterName = "CLUSTERNAME"
        		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Login-AzureRmAccount
		}
        
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasb:///example/data/gutenberg/davinci.txt", `
                "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
		# Print the output
		Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. 開啟新的 **Azure PowerShell** 命令提示字元。將目錄變更至 **mapreducejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

		.\mapreducejob.ps1
    
    當您執行指令碼時，可能會提示驗證您的 Azure 訂用帳戶。您還必須提供 HDInsight 叢集的 HTTPS/系統管理帳戶名稱和密碼。

3. 工作完成之後，您應該會收到與下列類似的輸出：

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	此輸出表示工作已順利完成。

	> [AZURE.NOTE]如果 **ExitCode** 的值不是 0，請參閱[疑難排解](#troubleshooting)。

    此範例也會將已下載的檔案儲存到您執行指令碼所在目錄的 **example/data/WordCountOutput** 資料夾中。

##檢視輸出

MapReduce 工作的輸出會儲存在名稱為 *part-r-#####* 的檔案中。使用文字編輯器開啟 **example/data/WordCountOutput/part-r-00000** 檔案，以查看工作所產生的單字和計數。

> [AZURE.NOTE]MapReduce 工作的輸出檔是固定不變的。因此，如果您重新執行此範例，則需要變更輸出檔的名稱。

##<a id="troubleshooting"></a>疑難排解

如果在工作完成時未傳回任何資訊，則可能是處理期間發生錯誤。若要檢視這項工作的錯誤資訊，請將下列命令新增至 **mapreducejob.ps1** 檔案的結尾，並儲存它，然後重新予以執行。

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            -DisplayOutputType StandardError

這會傳回執行工作時寫入至伺服器上之 STDERR 的資訊，而且可能有助於判斷工作的失敗原因。

##<a id="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 MapReduce 工作、監視工作狀態，以及擷取輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

<!---HONumber=AcomDC_1210_2015-->