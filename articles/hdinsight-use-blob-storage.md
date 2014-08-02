<properties  linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Blob Storage with HDInsight" pageTitle="Use Blob storage with HDInsight | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

# 在 HDInsight 上使用 Azure Blob 儲存體

Azure HDInsight 支援以 Hadoop 分散式檔案系統 (HDFS) 和 Azure Blob 儲存體來儲存資料。Blob
儲存體是一種健全、一般用途的 Azure 儲存體解決方案。Blob 儲存體提供全功能的 HDFS 介面，Hadoop
生態系統中的全部元件都可以直接處理資料 (依預設)，操作起來順暢無比。Blob 儲存體並非只是低成本的解決方案，將資料儲存在 Blob
儲存體中可安全地刪除用於計算的 HDInsight 叢集，而且不會遺失使用者資料。

> [WACOM.NOTE] HDInsight 叢集 3.0 版不支援 *asv://* 語法，而且未來版本中也不支援。這表示任何提交至
> HDInsight 叢集 3.0 版且明確使用 "asv://" 語法的工作都會失敗。請改用 *wasb://*
> 語法。另外，如果工作提交至任何以現有 metastore 建立的 HDInsight 叢集 3.0 版，且 metastore 中使用
> asv:// 語法來明確參考資源，則也會失敗。必須使用 wasb:// 來定址資源，以重新建立這些 metastore。

> [WACOM.NOTE] HDInsight 目前僅支援區塊 Blob。

> [WACOM.NOTE] 大部分 HDFS 命令仍可正常運作，例如 **ls**{:
> data-morhtml="true"}、**copyFromLocal**{:
> data-morhtml="true"}、**mkdir** 等。只有原生 HDFS 實作
> (稱為 DFS) 的特定命令才會在 Azure Blob 儲存體上出現不同的行為，例如 **fschk**{:
> data-morhtml="true"} 和 **dfsadmin**。

如需有關佈建 HDInsight 叢集的詳細資訊，請參閱[開始使用 HDInsight](../hdinsight-get-started/) 或[佈建 HDInsight 叢集](../hdinsight-provision-clusters/)。

## 本文內容

* [HDInsight 儲存架構](#architecture)
* [Azure Blob 儲存體的優點](#benefits)
* [準備 Blob 儲存體的容器](#preparingblobstorage)
* [定址 Blob 儲存體中的檔案](#addressing)
* [使用 PowerShell 存取 Blob](#powershell)
* [後續步驟](#nextsteps)

## <a id="architecture" ></a>HDInsight 儲存架構

下圖提供 HDInsight 儲存架構的摘要檢視：

![HDI.ASVArch](./media/hdinsight-use-blob-storage/HDI.ASVArch.png
"HDInsight Storage Architecture")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。可使用完整 URI 來存取此檔案系統。例如：

    hdfs://<namenodehost>/<path>

此外，HDInsight 也能夠存取 Blob 儲存體中儲存的資料。Blob 儲存體的存取語法如下：

    wasb[s]://<containername>@<a ccountname>.blob.core.windows.net/<path>

Hadoop 支援預設檔案系統的概念。預設檔案系統意味著預設配置和授權，也可用來解析相對路徑。HDInsight 佈建過程中會指定 Azure
儲存體帳戶和該帳戶中的特定 Blob 儲存容器，做為預設檔案系統。

除了此儲存體帳戶，您也可以在佈建過程中從相同 Azure 訂閱或不同 Azure
訂閱中新增其他儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight
叢集](../hdinsight-provision-clusters/)。

* **儲存體帳戶中連接到叢集的容器：** 因為帳戶名稱和金鑰儲存在 *core-site.xml* 中，所以您對這些容器中的 Blob
  具有完整存取權。
* **儲存體帳戶中「未」連接到叢集的公用容器或公用 Blob：** 您對容器中的 Blob 只有唯讀權限。


 
  > [WACOM.NOTE] 
  >      > 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。公用 Blob
  > 只在您知道確切的 URL 時才可讓您存取 Blob。如需詳細資訊，請參閱[限制對容器和 Blob 的存取][1]{:data-morhtml="true"}。

* **儲存體帳戶中「未」連接到叢集的私用容器：** 您無法存取容器中的 Blob。

Blob 儲存容器以機碼/值組來儲存資料，沒有目錄階層。然而，機碼名稱中可使用 "/" 字元，使檔案變成好像儲存在目錄結構中一樣。例如，Blob
的機碼可能是 *input/log1.txt*。實際上不存在 *input* 目錄，只是因為機碼名稱中有 "/" 字元，才形成檔案路徑的樣子。

## <a id="benefits" ></a>Azure Blob 儲存體的優點

計算和儲存體未並存於同處所隱含的效能損失，可經由將計算叢集佈建到靠近 Azure
資料中心內的儲存體帳戶資源來彌補，其中的高速網路可讓計算節點非常有效率地存取 Blob 儲存體內的資料。

將資料儲存在 Blob 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：** HDFS 中的資料位於計算叢集內。只有可存取計算叢集的應用程式，才能利用 HDFS API 來使用資料。可透過 HDFS API 或 [Blob 儲存體 REST API][2] 來存取 Blob 儲存體中的資料。因此，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。
* **資料封存：** 將資料儲存在 Blob 儲存體中，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。
* **資料儲存成本：** 長期將資料儲存在 DFS 中的成本高於將資料儲存在 Blob 儲存體中，因為計算叢集的成本高於 Blob
  儲存容器的成本。此外，因為不需要每次產生計算叢集時都重新載入資料，也能節省資料載入成本。
* **彈性的向外展延：** HDFS 提供向外延展的檔案系統，延展程度取決於您佈建給叢集的節點數目。變更延展規模較為複雜，倒不如使用 Blob
  儲存體自動提供的彈性延展功能。
* **地理區域複寫：** Blob 儲存容器可透過 Azure
  入口網站進行地理區域複寫。雖然這樣可支援地理區域復原和資料備援，但容錯移轉至地理區域複寫的位置會嚴重影響效能，可能引起額外的成本。因此，只有在資料的價值大於額外成本時，才建議您明智地選擇地理區域複寫。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您並不真的想要將這些結果儲存在 Blob
儲存容器中。在此情況下，您仍可選擇將資料儲存在本機 HDFS。事實上，在 Hive 工作和其他程序中，HDInsight 會使用 DFS
來儲存許多這些中繼結果。

## <a id="preparingblobstorage" ></a>準備 Blob 儲存體的容器

若要使用 Blob，首先要建立 [Azure 儲存體帳戶](/en-us/manage/services/storage/how-to-create-a-storage-account/)。在這過程中，需要指定 Azure 資料中心來儲存您以此帳戶所建立的物件。叢集和儲存體帳戶都必須在相同的資料中心 (Hive metastore SQL 資料庫和 Oozie metastore SQL 資料庫也必須位於相同的資料中心)。您所建立的每個 Blob
不論位於何處，都屬於您儲存體帳戶中的某個容器。此容器可能是在 HDInsight 外建立的現有 Blob 儲存容器，也可能是為 HDInsight 叢集建立的容器。

### 使用管理入口網站建立 HDInsight 的 Blob 容器

從 Azure 管理入口網站佈建 HDInsight 叢集時，有兩個選項：[快速建立]** 和 [自訂建立]**。快速建立選項需要事先建立 Azure 儲存體帳戶。相關指示請參閱[如何建立儲存體帳戶](/en-us/manage/services/storage/how-to-create-a-storage-account/)。

使用快速建立選項時，您可以選擇現有的儲存體帳戶。佈建程序會建立與 HDInsight 叢集名稱同名的新容器。此容器做為預設檔案系統。

![HDI.QuickCreate](./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
"HDInsight Cluster Quick Create")

使用自訂建立時，您可以選擇現有的 Blob 儲存容器或建立預設容器。預設容器與 HDInsight 叢集名稱具有相同名稱。

![HDI.CustomCreateStorageAccount](./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
"Custom Create Storage Account")

### 使用 Azure PowerShell 建立容器。

[Azure PowerShell](../install-configure-powershell/) 可用來建立 Blob 容器。下列是範例
PowerShell 指令碼：

    $subscriptionName = "<SubscriptionName>"
    $storageAccountName = "<WindowsAzureStorageAccountName>"
    $containerName="<BlobContainerToBeCreated>"
    
    Add-AzureAccount # The connection is good for 12 hours.
    Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

    # Create a storage context object
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <a id="addressing" ></a>定址 Blob 儲存體中的檔案

定址 Blob 儲存體中的檔案時，使用的 URI 配置如下：

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] 在儲存體模擬器 (在 HDInsight 模擬器上執行) 上定址檔案的語法是
> *wasb://<ContainerName>@storageemulator*。

URI 配置提供未加密存取，使用的是 *wasb:*首碼，也提供 SSL 加密存取，使用的是 *wasbs*。建議儘量使用
*wasbs*，即使存取相同 Azure 資料中心內的資料也一樣。

<BlobStorageContainerName> 識別 Blob
儲存容器的名稱。<StorageAccountName> 識別 Azure 儲存體帳戶名稱。需要使用完整網域名稱 (FQDN)。

如果 <BlobStorageContainerName> 和 <StorageAccountName>
都未指定，則會使用預設檔案系統。對於預設檔案系統上的檔案，您可以使用相對路徑或絕對路徑。例如，可使用下列語法來參考 HDInsight
叢集隨附的 hadoop-mapreduce-examples.jar 檔案：

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] 在 HDInsight 叢集 1.6 和 2.1 版上，檔案名稱是 *hadoop-examples.jar*{:
> data-morhtml="true"}。

<path> 是檔案或目錄 HDFS 路徑名稱。因為 Blob 儲存容器只是機碼值儲存，並沒有真正的階層式檔案系統。Blob
機碼內的 "/" 解釋為目錄分隔符號。例如，*hadoop-mapreduce-examples.jar* 的 Blob 名稱為：

    example/jars/hadoop-mapreduce-examples.jar

## <a id="powershell" ></a>使用 Azure PowerShell 存取 Blob

如需有關在工作站安裝和設定 Azure PowerShell 的詳細資訊，請參閱[安裝和設定 Azure
PowerShell](../install-configure-powershell/)。您可以使用 Azure PowerShell
主控台視窗或 PowerShell_ISE 來執行 PowerShell Cmdlet。

請使用下列命令來列出 Blob 相關的 Cmdlet：

    Get-Command *blob*

![Blob.PowerShell.cmdlets](./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png)

**上傳檔案的 PowerShell 範例**

請參閱[將資料上傳至 HDInsight](../hdinsight-upload-data/)。

**下載檔案的 PowerShell 範例**

下列指令碼將區塊 Blob 下載至目前的資料夾。執行指令碼之前，請將目錄切換至您具有寫入權限的資料夾。

    $storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName
    
    Write-Host "Create a context object ..." -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Download the blob ..."-ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "List the downloaded file ..."-ForegroundColor Green
    cat "./$blob"

**刪除檔案的 PowerShell 範例**

    $storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName
    
    Write-Host "Create a context object ..." -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "Delete the blob ..."-ForegroundColor Green
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 

**列出資料夾中的檔案的 PowerShell 範例**

    $storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blobPrefix = "example/data/"

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"      
    #Select-AzureSubscription $subscriptionName
    
    Write-Host "Create a context object ..." -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Write-Host "List the files in $blobPrefix ..."
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

## <a id="nextsteps" ></a> 後續步驟

在本文中，您學到如何在 HDInsight 上使用 Blob 儲存體，也了解 Blob 儲存體是 HDInsight 的基本元件。這可讓您以
Azure Blob 儲存體建立可擴充、長期封存的資料取得解決方案，並利用 HDInsight 來揭開儲存的資料內的資訊。

若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](../hdinsight-get-started/)
* [將資料上傳至 HDInsight](../hdinsight-upload-data/)
* [在 HDInsight 上使用 Hive](../hdinsight-use-hive/)
* [在 HDInsight 上使用 Pig](../hdinsight-use-pig/)



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179354.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dd135733.aspx