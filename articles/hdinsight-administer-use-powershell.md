<properties linkid="manage-services-hdinsight-administer-hdinsight-hadoop-clusters-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Manage Hadoop clusters in HDInsight with Azure PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, Hadoop, administration, administer" description="Learn how to perform administrative tasks for the Hadoop clusters in HDInsight using Azure PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Manage Hadoop clusters in HDInsight using Azure PowerShell" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 使用 Azure PowerShell 管理 HDInsight 上的 Hadoop 叢集

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。在本文中，您將了解如何使用本機 Azure PowerShell 主控台，透過使用 Windows PowerShell 來管理 HDInsight 上的 Hadoop 叢集。如需 HDInsight PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考文件][HDInsight Cmdlet 參考文件]。

**必要條件：**

開始閱讀本文之前，您必須符合下列必要條件：

-   Azure 訂用帳戶。Azure 是訂用帳戶型平台。HDInsight PowerShell Cmdlet 會為您的訂用帳戶執行相關工作。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][購買選項]、[成員優惠][成員優惠]或[免費試用][免費試用]。

-   具有 Azure PowerShell 的工作站。如需指示，請參閱[安裝並設定 Azure PowerShell][安裝並設定 Azure PowerShell]。

## 本文內容

-   [佈建叢集][佈建叢集]
-   [列出和顯示叢集][列出和顯示叢集]
-   [刪除叢集][刪除叢集]
-   [授與/撤銷 HTTP 服務存取][授與/撤銷 HTTP 服務存取]
-   [提交 MapReduce 工作][提交 MapReduce 工作]
-   [提交 Hive 工作][提交 Hive 工作]
-   [將資料上傳至 Blob 儲存體][將資料上傳至 Blob 儲存體]
-   [從 Blob 儲存體下載 MapReduce 輸出資料][從 Blob 儲存體下載 MapReduce 輸出資料]

## <span id="provision"></span></a>佈建 HDInsight 叢集

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存帳號和儲存容器，您才能建立 HDInsight 叢集。

**建立 Azure 儲存體帳戶**

匯入 publishsettings 檔案之後，您可以使用下列命令來建立儲存體帳戶：

    # Create an Azure storage account
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # For example, "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] 儲存體帳戶必須位在與 HDInsight 叢集相同的資料中心內。目前，您只能在下列資料中心佈建 HDInsight 叢集：

> -   東南亞
> -   北歐
> -   西歐
> -   美國東部
> -   美國西部

如需使用管理入口網站建立 Azure 儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶][如何建立儲存體帳戶]。

如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列命令來擷取資訊：

    # List storage accounts for the current subscription
    Get-AzureStorageAccount
    # List the keys for a storage account
    Get-AzureStorageKey <StorageAccountName>

如需有關使用管理入口網站取得資訊的詳細資訊，請參閱＜*作法：檢視、複製及重新產生儲存體存取金鑰*＞一節 (位於[如何管理儲存體帳戶][如何管理儲存體帳戶]中)。

**建立 Azure 儲存容器**

PowerShell 無法在 HDInsight 佈建程序期間建立 Blob 容器。您可以使用下列指令碼來建立 Blob 容器：

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Create a storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

**佈建叢集**

在儲存帳號和 Blob 容器準備就緒後，您即可建立叢集。

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Get the storage account key
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Create a new HDInsight cluster
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

下列螢幕擷取畫面顯示指令碼的執行：

![HDI.PS.Provision][HDI.PS.Provision]

## <span id="listshow"></span></a> 列出和顯示叢集詳細資料

使用下列命令，以列出並顯示叢集詳細資料：

**列出目前訂用帳戶中的所有叢集**

    Get-AzureHDInsightCluster 

**顯示目前訂用帳戶中特定叢集的詳細資料**

    Get-AzureHDInsightCluster -Name <ClusterName> 

## <span id="delete"></span></a> 刪除叢集

使用下列命令刪除叢集：

    Remove-AzureHDInsightCluster -Name <ClusterName> 

## <span id="httpservice"></span></a> 授與/撤銷 HTTP 服務存取

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

預設會授與這些服務的存取權。您可以撤銷/授與存取權。範例如下：

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

在範例中，*hdiv2* 是 HDInsight 叢集名稱。

> [WACOM.NOTE] 透過授與/撤銷存取權，您將重設叢集使用者的使用者名稱和密碼。

這也可以使用 Windows Azure 管理入口網站來完成。請參閱[使用管理入口網站來管理 HDInsight][使用管理入口網站來管理 HDInsight]。

## <span id="mapreduce"></span></a> 提交 MapReduce 工作

HDInsight 叢集配送提供一些 MapReduce 範例。其中一個範例是計算來源檔案中的文字出現率。

**提交 MapReduce 工作**

下列 PowerShell 指令碼會提交字數統計範例工作：

    $clusterName = "<HDInsightClusterName>"            

    # Define the MapReduce job
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Run the job and show the standard error 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] *hadoop-examples.jar* 隨附於 2.1 版 HDInsight 叢集。在 3.0 版 HDInsight 叢集上，該檔案已重新命名為 *hadoop-mapreduce.jar*。

如需 WASB 首碼的詳細資訊，請參閱 [對於 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

**下載 MapReduce 工作輸出**

下列 PowerShell 指令碼會從最後一個程序中擷取 MapReduce 工作輸出：

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Create the storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Download the output to local computer
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

如需開發和執行 MapReduce 工作的詳細資訊，請參閱[將 MapReduce 與 HDInsight 搭配使用][將 MapReduce 與 HDInsight 搭配使用]。

## <span id="hive"></span></a> 提交 Hive 工作

HDInsight 叢集配送提供稱為 *hivesampletable* 的範例 Hive 資料表。您可以使用 HiveQL "show tables;"，列出叢集上的 Hive 資料表。

**提交 Hive 工作**

下列指令碼會提交 Hive 工作，以列出 Hive 資料表：

    $clusterName = "<HDInsightClusterName>"               

    # HiveQL query
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

Hive 工作會先顯示叢集上所建立的 Hive 資料表，以及從 hivesampletable 傳回的資料。

如需使用 Hive 的詳細資訊，請參閱[將 Hive 與 HDInsight 搭配使用][將 Hive 與 HDInsight 搭配使用]。

## <span id="upload"></span></a>將資料上傳至 Blob 儲存體

請參閱[將資料上傳至 HDInsight][將資料上傳至 HDInsight]。

## <span id="download"></span></a>從 Blob 儲存體下載 MapReduce 輸出

請參閱本文中的[提交 MapReduce 工作][提交 MapReduce 工作]工作階段。

## 另請參閱

-   [HDInsight Cmdlet 參考文件][HDInsight Cmdlet 參考文件]
-   [使用管理入口網站管理 HDInsight][使用管理入口網站來管理 HDInsight]
-   [使用命令列介面管理 HDInsight][使用命令列介面管理 HDInsight]
-   [佈建 HDInsight 叢集][佈建 HDInsight 叢集]
-   [將資料上傳到 HDInsight][將資料上傳至 HDInsight]
-   [以程式設計方式提交 Hadoop 工作][以程式設計方式提交 Hadoop 工作]
-   [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]

  [HDInsight Cmdlet 參考文件]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn479228.aspx
  [購買選項]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [佈建叢集]: #provision
  [列出和顯示叢集]: #listshow
  [刪除叢集]: #delete
  [授與/撤銷 HTTP 服務存取]: #httpservices
  [提交 MapReduce 工作]: #mapreduce
  [提交 Hive 工作]: #hive
  [將資料上傳至 Blob 儲存體]: #upload
  [從 Blob 儲存體下載 MapReduce 輸出資料]: #download
  [如何建立儲存體帳戶]: ../storage-create-storage-account/
  [如何管理儲存體帳戶]: ../storage-manage-storage-account/
  [HDI.PS.Provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
  [使用管理入口網站來管理 HDInsight]: ../hdinsight-administer-use-management-portal/
  [將 MapReduce 與 HDInsight 搭配使用]: ../hdinsight-use-mapreduce/
  [將 Hive 與 HDInsight 搭配使用]: ../hdinsight-use-hive/
  [將資料上傳至 HDInsight]: ../hdinsight-upload-data/
  [使用命令列介面管理 HDInsight]: ../hdinsight-administer-use-command-line/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
  [hdinsight-storage]: ../hdinsight-use-blob-storage/