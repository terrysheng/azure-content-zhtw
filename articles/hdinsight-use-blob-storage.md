<properties 
	pageTitle="從 Hadoop 相容的 Blob 儲存體查詢巨量資料 | Azure" 
	description="HDInsight 使用 Hadoop 相容 Blob 存放區作為 HDFS 的大量資料存放區。了解如何從 Blob 存放區查詢並儲存分析的結果。" 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="mollybos"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>


# 從 Hadoop 相容的 Blob 儲存體查詢巨量資料，以便在 HDInsight 中分析

低成本 Blob 儲存體是強大、一般用途的 Hadoop 相容 Azure 儲存體解決方案，其完美整合了 HDInsight。透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的資料。在本教學課程中，您會學到如何設定 Blob 儲存體的容器，然後解決其中的資料。

將資料儲存在 Blob 儲存體中，可安全地刪除做為計算用途的 HDInsight 叢集，而不會遺失使用者資料。 

> [AZURE.NOTE]	HDInsight 叢集 3.0 版不支援 *asv://* 語法，而且未來版本中也不支援。這表示任何提交至 HDInsight 叢集 3.0 版，且明確使用 "asv://" 語法的工作都會失敗。請改用 *wasb://*語法。另外，如果工作提交至任何以現有 metastore 建立的 HDInsight 叢集 3.0 版，且 metastore 中使用 asv:// 語法來明確參考資源，則也會失敗。必須使用 wasb:// 來定址資源，以重新建立這些 metastore。

> HDInsight 目前僅支援區塊 Blob。

> 大部分 HDFS 命令仍可正常運作，例如 <b>ls</b>、<b>copyFromLocal</b>、<b>mkdir</b> 等。只有原生 HDFS 實作 (稱為 DFS) 的特定命令才會在 Azure Blob 儲存體上出現不同的行為，例如 <b>fschk</b> 和 <b>dfsadmin</b>。

佈建 HDInsight 叢集上的資訊，請參閱[開始使用 HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。

## 本文內容

* [HDInsight 儲存架構](#architecture)
* [Azure Blob 儲存體的優點](#benefits)
* [準備 Blob 儲存體的容器](#preparingblobstorage)
* [定址 Blob 儲存體中的檔案](#addressing)
* [使用 PowerShell 存取 Blob](#powershell)
* [後續步驟](#nextsteps)

## <a id="architecture"></a>HDInsight 儲存架構
下圖提供 HDInsight 儲存架構的摘要檢視：

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight 可以存取本機連接至計算節點的分散式檔案系統。可使用完整 URI 來存取此檔案系統。例如： 

	hdfs://<namenodehost>/<path>

此外，HDInsight 也能夠存取 Blob 儲存體中儲存的資料。Blob 儲存體的存取語法如下：

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop 支援預設檔案系統的概念。預設檔案系統意味著預設配置和授權，也可用來解析相對路徑。HDInsight 佈建過程中會指定 Azure 儲存體帳戶和該帳戶中的特定 Blob 儲存容器，做為預設檔案系統。

除了此儲存體帳戶，您也可以在佈建過程中從相同或不同的 Azure 訂用帳戶中新增其他儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。 

- **儲存體帳戶中的容器連接  叢集：**因為帳戶名稱和金鑰儲存在 *core-site.xml* 中，所以您對這些容器中的 Blob 具有完整存取權。
- **儲存體帳戶中未連接到叢集的公用容器或公用 Blob：**您擁有容器中 Blob 的唯讀權限。

	> [AZURE.NOTE]
        > 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。公用 Blob 只在  您知道確切的 URL 時才可讓您存取 Blob。如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179354.aspx">限制容器和 Blob 的存取權</a>。

- **儲存體帳戶中未連接到叢集的私人容器：**除非您定義儲存體帳戶，否則當您提交 WebHCat 工作時將無法存取容器中的 Blob。本文稍後會加以說明。


在佈建程序中所定義的儲存體帳戶及其金鑰會儲存在 %HADOOP_HOME%/conf/core-site.xml 中。  HDInsight 的預設行為是使用 core-site.xml 檔案中定義的儲存體帳戶。因為叢集前端節點 (主要) 有可能會隨時重新製作映像或進行移轉，屆時將會遺失對這些檔案所做的任何變更，所以我們不建議您編輯 core-site.xml 檔案。

多個 WebHCat 工作 (包括 Hive、MapReduce、Hadoop 串流和 Pig) 可隨身夾帶儲存體帳戶的說明和中繼資料 (儲存體帳戶目前適用於 Pig，但中繼資料不適用)。在[使用 PowerShell 存取 Blob](#powershell) 一節中，有此功能的範例。如需詳細資訊，請參閱[在其他儲存體帳戶和 Metastores 上使用 HDInsight 叢集](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 儲存容器以機碼/值組來儲存資料，沒有目錄階層。然而，機碼名稱中可使用 "/" 字元，使檔案變成好像儲存在目錄結構中一樣。例如，Blob 的機碼可能是 *input/log1.txt*。實際上不存在 *input* 目錄，只是因為機碼名稱中有 "/" 字元，才形成檔案路徑的外觀。










## <a id="benefits"></a>Azure Blob 儲存體的優點
運算和儲存體未並存於同處所隱含的效能損失，可經由將運算叢集佈建到靠近 Azure 資料中心內的儲存體帳戶資源來彌補，其中的高速網路可讓運算節點非常有效率地存取 Blob 儲存體內的資料。

將資料儲存在 Blob 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：**HDFS 中的資料位於運算叢集內。只有可存取運算叢集的應用程式可以使用利用 HDFS API 的資料。Blob 儲存體中的資料，可以透過 HDFS API 或透過 [Blob 儲存體 REST API][blob-storage-restAPI] 來加以存取。因此，可以用來大量應用程式 (包括其他 HDInsight 叢集) 和工具來產生及取用資料。
* **資料保存：**將資料儲存在 Blob 儲存體中，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。 
* **資料儲存成本：**在 DFS 中儲存資料，長期來看成本高於在 Blob 儲存體中儲存資料，因為運算叢集的成本高於 Blob 儲存體容器的成本。此外，因為資料不需在每次產生運算叢集時都重新載入，您也會節省資料載入成本。
* **彈性向外擴充：**雖然 HDFS 提供向外擴充檔案系統，但規模取決於您為叢集佈建的節點數目。變更規模可能是更複雜的程序，這是相較於仰賴 Blob 儲存體彈性調整功能可自動化完成而言。
* **地理複寫：** Blob 儲存體容器可以透過 Azure 入口網站進行地理複寫。雖然這會提供地理復原和資料備援，容錯移轉至地理複寫位置會嚴重影響效能，而且可能會產生額外的成本。因此我們建議您明智地只有在資料價值值得您付出額外成本時選擇地理複寫。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您並不真的想要將這些結果儲存在 Blob 儲存容器中。在此情況下，您仍可選擇將資料儲存在本機 HDFS。事實上，在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。 





## <a id="preparingblobstorage"></a>準備 Blob 儲存體的容器
若要使用 Blob，首先要建立 [Azure 儲存體帳戶][azure-storage-create]。在這過程中，需要指定 Azure 資料中心來儲存您以此帳戶所建立的物件。叢集和儲存體帳戶都必須在相同的資料中心 (Hive metastore SQL 資料庫和 Oozie metastore SQL 資料庫也必須位於相同的資料中心)。您所建立的每個 Blob 不論位於何處，都屬於您儲存體帳戶中的某個容器。此容器可能是在 HDInsight 外建立的現有 Blob 儲存容器，也可能是為 HDInsight 叢集建立的容器。 



### 使用管理入口網站建立 HDInsight 的 Blob 容器

從 Azure 管理入口網站佈建 HDInsight 叢集時，有兩個選項： *quick create* 和 *custom create*。快速建立選項需要事先建立 Azure 儲存體帳戶。  相關指示請參閱[如何建立儲存體帳戶][azure-storage-create]。 

使用快速建立選項時，您可以選擇現有的儲存體帳戶。佈建程序會建立與 HDInsight 叢集名稱同名的新容器。如果已有相同名稱的容器存在，便會使用 <clusterName>-<x>。例如，myHDIcluster-1。此容器做為預設檔案系統。

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
使用自訂建立時，您可以對預設儲存體帳戶使用下列其中一個選項：

- 使用現有儲存體
- 建立新的儲存體
- 使用其他訂用帳戶的儲存體

您也可以選擇建立自己專屬的 Blob 容器或使用現有 Blob 容器。
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### 使用 Azure PowerShell 建立容器。
[Azure PowerShell][powershell-install] 可用來建立 Blob 容器。下列是範例 PowerShell 指令碼：

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


## <a id="addressing"></a>定址 Blob 儲存體中的檔案

定址 Blob 儲存體中的檔案時，使用的 URI 配置如下： 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] 在儲存體模擬器 (在 HDInsight Emulator 上執行) 上定址檔案的語法是 <i>wasb://&lt;ContainerName&gt;@storageemulator</i>。



URI 配置提供  *wasb:* 首碼的未加密存取，以及  *wasbs* 的 SSL 加密存取。建議盡可能使用 *wasbs*，即使存取同一個 Azure 資料中心內的資料也是如此。
	
&lt;BlobStorageContainerName&gt; 識別 Blob 儲存容器的名稱。
&lt;StorageAccountName&gt; 識別 Azure 儲存體帳戶名稱。需要使用完整網域名稱 (FQDN)。
	
如果 &lt;BlobStorageContainerName&gt; 和 &lt;StorageAccountName&gt; 都未指定，則會使用預設檔案系統。對於預設檔案系統上的檔案，您可以使用相對路徑或絕對路徑。例如，可使用下列語法來參考 HDInsight 叢集隨附的 hadoop-mapreduce-examples.jar 檔案：

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE] 檔案名稱為 <i>hadoop-examples.jar</i> 在 HDInsight 叢集 1.6 和 2.1 版上。


&lt;path&gt; 是檔案或目錄 HDFS 路徑名稱。因為 Blob 儲存容器只是機碼值儲存，並沒有真正的階層式檔案系統。Blob 機碼內的 "/" 解釋為目錄分隔符號。例如， *hadoop-mapreduce-examples.jar* 的 Blob 名稱為：

	example/jars/hadoop-mapreduce-examples.jar
	

## <a id="powershell"></a>使用 Azure PowerShell 存取 Blob

如需有關在工作站安裝並設定 Azure PowerShell 的詳細資訊，請參閱[安裝並設定 Azure PowerShell][powershell-install]。您可以使用 Azure PowerShell 主控台視窗或 PowerShell_ISE 來執行 PowerShell Cmdlet。 

請使用下列命令來列出 Blob 相關的 Cmdlet：

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**上傳檔案的 PowerShell 範例**

請參閱[將資料上傳至 HDInsight][hdinsight-upload-data]。

**下載檔案的 PowerShell 範例**

下列指令碼將區塊 Blob 下載至目前的資料夾。執行指令碼之前，請將目錄切換至您具有寫入權限的資料夾。 


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**刪除檔案的 PowerShell 範例**

下列指令碼說明如何刪除檔案。

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**列出資料夾中檔案的 PowerShell 範例**

下列指令碼說明如何列出「資料夾」內的檔案。下個範例說明如何使用 Invoke-Hive Cmdlet 來執行 dfs ls 命令，以列出資料夾。

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**用來存取未定義儲存體帳戶的 PowerShell 範例**
	
本範例說明如何從未在佈建程序期間定義的儲存體帳戶列出資料夾。
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
## <a id="nextsteps"></a>後續步驟

在本文中，您學到如何在 HDInsight 上使用 Blob 儲存體，也了解 Blob 儲存體是 HDInsight 的基本元件。這可讓您以 Azure Blob 儲存體建立可擴充、長期封存的資料取得解決方案，並利用 HDInsight 來揭開儲存的資料內的資訊。

若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [使用 Hive 搭配 HDInsight][hdinsight-use-hive]
* [使用 Pig 搭配 HDInsight][hdinsight-use-pig]

[powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  
<!--HONumber=42-->
