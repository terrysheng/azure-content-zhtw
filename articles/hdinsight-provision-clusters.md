<properties urlDisplayName="HDInsight Administration" pageTitle="在 HDInsight 中佈建 Hadoop 叢集 | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="jgao" />

#使用自訂選項在 HDInsight 上佈建 Hadoop 叢集

在本文中，您將了解在 Azure HDInsight 上自訂佈建 Hadoop 叢集的不同方式 - 使用 Azure 管理入口網站、PowerShell、命令列工具或 HDInsight .NET SDK。本文將針對佈建 Hadoop 叢集進行討論。如需如何佈建 HBase 叢集的指示，請參閱[在 HDInsight 上佈建 HBase 叢集][hdinsight-hbase-custom-provision]。請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop 和 HBase 之間有何不同？</a> 以了解為何您必須在兩者中擇一。

## 什麼是 HDInsight 叢集？

您有沒有想過為什麼我們每次針對 Hadoop 或 BigData 進行討論時，都一定會提到叢集？那是因為 Hadoop 允許大型資料的分散式處理，散佈到叢集的不同節點中。叢集具有主/從架構，包含一個主要 (亦稱為前端節點或名稱節點) 和任意數目的從屬 (亦稱為資料節點)。如需詳細資訊，請參閱 [Apache Hadoop][apache-hadoop]。

![HDInsight Cluster][img-hdi-cluster]

HDInsight 叢集摘要了 Hadoop 實作詳細資料，因此您無需擔心如何與叢集的不同節點通訊。佈建 HDInsight 叢集時，您可以佈建包含 Hadoop 及相關應用程式的 Azure 運算資源。如需詳細資訊，請參閱 [HDInsight 中 Hadoop 的簡介][hadoop-hdinsight-intro]。待分析的資料儲存在 Azure Blob 儲存體中，在 HDInsight 環境中又稱為 *Azure Storage - Blob* (簡稱為 WASB)。如需詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。

本文針對佈建叢集的不同方式提供指示。如果您想知道佈建叢集的快速入門方法，請參閱[開始使用 Azure HDInsight][hdinsight-get-started]。

**必要條件：**

開始閱讀本文之前，您必須符合下列必要條件：

- Azure 訂閱。Azure 是訂用帳戶型平台。HDInsight PowerShell Cmdlet 會為您的訂用帳戶執行相關工作。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][azure-purchase-options]、[成員優惠][azure-member-offers]或[免費試用][azure-free-trial]。

##本文內容

* [組態選項](#configuration)
* [使用 Azure 管理入口網站](#portal)
* [使用 Azure PowerShell](#powershell)
* [使用跨平台命令列](#cli)
* [使用 HDInsight .NET SDK](#sdk)
* [後續步驟](#nextsteps)

##<a id="configuration"></a>組態選項

###其他儲存體

在設定期間，您必須指定 Azure Blob 儲存體帳戶和預設容器。叢集以此做為預設儲存位置。您可以選擇性地指定也將與叢集相關聯的其他 Blob。

如需有關使用次要 Blob 存放區的詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-use-blob-storage/)。

###Metastore

Metastore 包含 Hive 資料表、資料分析、結構描述、資料行等的相關資訊。Hive 使用此資訊來找出資料在 HDFS (或 HDInsight 中的 WASB) 上的儲存位置。依預設，Hive 會使用內嵌資料庫來儲存此資訊。

佈建 HDInsight 叢集時，您可以指定將包含 Hive Metastore 的 SQL Database。當您刪除叢集時，由於中繼資料資訊已儲存在外部的 SQL Database 中，因而得以保留下來。

### 叢集自訂

在建立 HDInsight 叢集的同時，您可以安裝額外的元件或自訂叢集組態。透過編寫在叢集建立期間執行的指令碼，即可自訂叢集。這類指令碼是使用**指令碼動作**叫用，其為可透過 HDInsight PowerShell Cmdlet 或 HDInsight .NET SDK 使用的組態選項。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-customize-cluster]。


###虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/zh-tw/documentation/services/virtual-network/) 可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源結合在一起 (僅限雲端)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接到 Azure 虛擬網路

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

如需虛擬網路特性、優點和功能的詳細資訊，請參閱 [Azure 虛擬網路概觀](http://msdn.microsoft.com/library/azure/jj156007.aspx)。

> [WACOM.NOTE] 您必須先建立 Azure 虛擬網路，再佈建 HDInsight 叢集。如需詳細資訊，請參閱[虛擬網路組態工作](http://msdn.microsoft.com/zh-tw/library/azure/jj156206.aspx)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。
>
> 強烈建議您一個叢集只指定一個子網路。

##<a id="portal"></a> 使用 Azure 管理入口網站

HDInsight 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。相同的資料中心上必須要有 Azure 儲存體帳戶，您才能建立 HDInsight 叢集。如需詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。如需建立 Azure 儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶][azure-create-storageaccount]。


> [WACOM.NOTE] 目前，只有**東亞**、**東南亞**、**北歐**、**西歐**、**美國東部**、**美國西部**、**美國中北部**和**美國中南部**區域可託管 HDInsight 叢集。

**使用自訂建立選項建立 HDInsight 叢集**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下頁面底部的 [**新增**]，按一下 [**資料服務**] 及 [**HDInsight**]，然後按一下 [**自訂建立**]。
3. 在 [**叢集詳細資料**] 頁面上，輸入或選擇下列值：

	![HDI.CustomCreateCluster][image-hdi-customcreatecluster]

    <table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>叢集名稱</td>
			<td><p>為叢集命名。 </p>
				<ul>
				<li>DNS 名稱的開頭與結尾都必須是英數字元，名稱中可包含連字號。</li>
				<li>欄位必須是 3 到 63 個字元的字串。</li>
				</ul></td></tr>
		<tr><td>叢集類型</td>
			<td>針對叢集類型，選取 <strong>Hadoop</strong>。</td></tr>
		<tr><td>HDInsight 版本</td>
			<td>選擇版本。若是 Hadoop，預設值為採用 Hadoop 2.4 的 HDInsight 3.1 版。</td></tr>
		</table>

	輸入或選取如資料表中所示的值，然後按一下向右箭頭。

4. 在 [**設定叢集**] 頁面上，輸入或選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值</th></tr>
	<tr><td>資料節點</td><td>您要部署的資料節點數。請建立單一節點叢集，以供測試之用。<br />叢集大小限制會隨著 Azure 訂用帳戶而不同。若要提高限制，請與 Azure 帳務支援人員連絡。</td></tr>
	<tr><td>區域/虛擬網路</td><td><p>請選擇與您在前一個程序中建立的儲存體帳戶相同的區域。使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。</p><p>可用的區域如下： <strong>東亞</strong>、 <strong>東南亞</strong>、 <strong>北歐</strong>、 <strong>西歐</strong>、 <strong>美國東部</strong>、 <strong>美國西部</strong>、 <strong>美國中北部</strong>、 <strong>美國中南部</strong><br/>如果您已建立 Azure 虛擬網路，您可以選取將設定 HDInsight 叢集使用的網路。</p><p>如需有關建立 Azure 虛擬網路的詳細資訊，請參閱 <a href="http://msdn.microsoft.com/zh-tw/library/azure/jj156206.aspx">虛擬網路組態工作</a>。</p></td></tr>
	</table>


5. 在 [**Configure Cluster User**] 頁面上，提供下列值：

    ![HDI.CustomCreateCluster.ClusterUser][image-hdi-customcreatecluster-clusteruser]

    <table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>使用者名稱</td>
			<td>指定 HDInsight 叢集使用者名稱。</td></tr>
		<tr><td>密碼/確認密碼</td>
			<td>指定 HDInsight 叢集使用者密碼。</td></tr>
		<tr><td>輸入 Hive/Oozie Metastore</td>
			<td>勾選此核取方塊，在與叢集相同的資料中心上指定 SQL Database，以作為 Hive/Oozie metastore 使用。即使在將叢集刪除之後，如果您想要保留 Hive/Oozie 工作的相關中繼資料，此選項將會很有幫助。</td></tr>
		<tr><td>Metastore 資料庫</td>
			<td>指定要作為 Hive/OOzie metastore 的 Azure SQL 資料庫。此 SQL 資料庫必須與 HDInsight 叢集位於相同的資料中心內。清單方塊只會列出您在 <strong>叢集詳細資料</strong> 頁面上指定之資料中心內的 SQL 資料庫。</td></tr>
		<tr><td>資料庫使用者</td>
			<td>指定將用來連接到資料庫的 SQL 資料庫使用者。</td></tr>
		<tr><td>資料庫使用者密碼</td>
			<td>指定 SQL 資料庫使用者密碼。</td></tr>
	</table>

	>[WACOM.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Windows Azure 服務**] 中，依序按一下 [**是**] 和 [**儲存**]。

    按一下向右箭頭。


6. 在 [**儲存體帳戶**] 頁面上，提供下列值：

    ![HDI.CustomCreateCluster.StorageAccount][image-hdi-customcreatecluster-storageaccount]

	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>儲存體帳戶</td>
			<td>指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存體帳戶。您可以從三個選項中擇一使用：
			<ul>
				<li>使用現有儲存體</li>
				<li>建立新的儲存體</li>
				<li>使用其他訂用帳戶的儲存體</li>
			</ul>
			</td></tr>
		<tr><td>帳戶名稱</td>
			<td><ul>
				<li>如果您選擇使用現有儲存體，請對 <strong>帳戶名稱</strong>，選取現有的儲存體帳戶。下拉式清單所列出的儲存體帳戶僅限位於與您選擇佈建叢集相同的資料中心。</li>
				<li>如果選取 <strong>建立新的儲存體</strong> 或 <strong>使用其他訂用帳戶的儲存體</strong> 選項，您必須提供儲存體帳戶名稱。</li>
			</ul></td></tr>
		<tr><td>帳戶金鑰</td>
			<td>如果選取 <strong>使用其他訂用帳戶的儲存體</strong> 選項，請指定該儲存體帳戶的帳戶金鑰。</td></tr>
		<tr><td>預設容器</td>
			<td><p>指定儲存體帳戶上的預設容器作為 HDInsight 叢集的預設檔案系統使用。如果選取 <strong>使用現有儲存體</strong> (針對 <strong>儲存體帳戶</strong> )，但該帳戶中沒有現有容器，則系統便會依預設建立容器，其名稱將會與叢集名稱相同。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。例如 mycontainer1、mycontainer2，依此類推。然而，如果現有儲存體帳戶有容器存在，且其名稱與您指定的叢集名稱不同，則您也可以使用該容器。</p>
            <p>如果選擇建立新儲存體或使用其他 Azure 訂用帳戶的儲存體，您必須指定預設容器名稱</p>
        </td></tr>
		<tr><td>其他儲存體帳戶</td>
			<td>HDInsight 支援多個儲存體帳戶。叢集可使用的其他儲存體帳戶並沒有數量上的限制。但如果您使用管理入口網站建立叢集，則會因為 UI 的限制而限定為七個帳戶。您所指定的每個其他儲存體帳戶都會在精靈上另外新增一個 [儲存體帳戶] 頁面，您可在此指定帳戶資訊。以上面的螢幕擷取畫面為例，在選取一個其他儲存體帳戶後，對話方塊因此新增了頁面 5。</td></tr>
	</table>

	按一下向右箭頭。

7. 在 [**儲存體帳戶**] 頁面上，輸入附加儲存體帳戶的帳戶資訊：

	![HDI.CustomCreateCluster.AddOnStorage][image-hdi-customcreatecluster-addonstorage]

    在這裡，您可以選擇現有儲存體、建立新儲存體或使用其他 Azure 訂用帳戶的儲存體。提供值的程序會與上個步驟類似。

    按一下核取記號以開始佈建叢集。佈建完成時，狀態欄便會顯示 [**執行中**]。

	> [WACOM.NOTE] 在您為 HDInsight 叢集選擇 Azure 儲存體帳戶後，您將無法刪除帳戶，也無法將帳戶變更為不同的帳戶。

##<a id="powershell"></a> 使用 Azure PowerShell
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。本節提供如何佈建 HDInsight 叢集的指示，如需設定工作站以執行 HDInsight Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。如需有關在 HDInsight 上使用 PowerShell 的詳細資訊，請參閱[使用 PowerShell 來管理 HDInsight][hdinsight-admin-powershell]。如需 HDInsight PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考文件][hdinsight-powershell-reference]。

> [WACOM.NOTE] 本節的指令碼可用來設定 Azure 虛擬網路上的 HDInsight 叢集，但不會建立 Azure 虛擬網路。如需有關建立 Azure 虛擬網路的相關資訊，請參閱[虛擬網路組態工作](http://msdn.microsoft.com/zh-tw/library/azure/jj156206.aspx)。

以下是使用 PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存體帳戶和儲存容器，您才能建立 HDInsight 叢集。儲存體帳戶必須與 HDInsight 叢集位於相同的資料中心。


**建立 Azure 儲存體帳戶**

- 從 Azure PowerShell 主控台視窗執行下列命令：

		$storageAccountName = "<StorageAcccountName>"	# Provide a storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

	如果您已有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，您可以使用下列 PowerShell 命令來擷取資訊：

		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"

**建立 Azure Blob 儲存體容器**

- 從 Azure PowerShell 主控台視窗執行下列命令：

		$storageAccountName = "<StorageAccountName>"	# Provide the storage account name
		$storageAccountKey = "<StorageAccountKey>"		# Provide either primary or secondary key
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

> [WACOM.NOTE] PowerShell Cmdlet 是唯一可在 HDInsight 叢集中變更組態變數的方法。透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。透過 PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Azure PowerShell 主控台視窗執行下列命令：

		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container.
		$containerName = "<ContainerName>"				# Azure Blob container that is used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	出現提示時，請輸入叢集的認證。叢集佈建可能需要幾分鐘的時間。

	![HDI.CLI.Provision][image-hdi-ps-provision]



**使用自訂組態選項佈建 HDInsight 叢集**

佈建叢集時，您可以使用其他組態選項，例如連線到多個 Azure Blob 儲存體、使用虛擬網路，或使用 Azure SQL Database 來處理 Hive 和 Oozie metastore。這可讓您區隔資料和中繼資料的存留期與叢集的存留期。

> [WACOM.NOTE] PowerShell Cmdlet 是唯一可在 HDInsight 叢集中變更組態變數的方法。透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。透過 PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Windows PowerShell 視窗執行下列命令：

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[WACOM.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Windows Azure 服務**] 中，依序按一下 [**是**] 和 [**儲存**]。

**列出 HDInsight 叢集**

- 從 Azure PowerShell 主控台視窗執行下列命令，以列出 HDInsight 叢集並確認是否已成功佈建叢集：

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> 使用跨平台命令列

> [WACOM.NOTE] 從 2014/8/29 開始，無法使用跨平台命令列介面將叢集與 Azure 虛擬網路建立關聯。

另一個佈建 HDInsight 叢集的選項，是跨平台命令列介面。此命令列工具會在 Node.js 中實作。此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。此命令列工具採用開放原始碼。原始程式碼會在 GitHub 中受到管理，網址： <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>。如需如何使用此命令列介面的一般指引，請參閱[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具][azure-command-line-tools]。如需完整的參考文件，請參閱[適用於 Mac 和 Linux 的 Azure 命令列工具][azure-command-line-tool]。本文僅提供從 Windows 使用此命令列介面的相關資訊。

以下是使用跨平台命令列佈建 HDInsight 叢集時所需執行的程序：

- 安裝跨平台命令列
- 下載及匯入 Azure 帳戶發佈設定
- 建立 Azure 儲存體帳戶
- 佈建叢集

此命令列介面可使用 *Node.js 封裝管理員 (NPM)* 或 Windows Installer 進行安裝。Microsoft 建議您僅使用下列兩個選項的其中一個選項進行安裝。

**使用 NPM 安裝命令列介面**

1.	瀏覽至 **www.nodejs.org**。
2.	按一下 [**安裝**]，並依照指示使用預設設定操作。
3.	從您的工作站開啟 [**命令提示字元**] (或是 *Azure 命令提示字元或 *VS2012 開發人員命令提示字元*)。
4.	在命令提示字元視窗中執行下列命令。

		npm install -g azure-cli

	> [WACOM.NOTE] 如果出現「找不到 NPM 命令」的錯誤，請驗證下列路徑是否在 PATH 環境變數中： <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> 或 <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	執行下列命令以驗證安裝：

		azure hdinsight -h

	您可以在不同層級使用 *-h* 參數，以顯示說明資訊。例如：

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**使用 Windows Installer 安裝命令列介面**

1.	瀏覽至 **http://azure.microsoft.com/zh-tw/downloads/**。
2.	向下捲動至 [**命令列工具**] 區段，然後按一下 [**跨平台命令列介面**] 並依照 Web Platform Installer 精靈操作。

**下載及匯入發佈設定**

使用命令列介面之前，您必須先設定工作站與 Azure 之間的連線。命令列介面會使用您的 Azure 訂用帳戶資訊連接到您的帳戶。這項資訊可從 Azure 的發佈設定檔案取得。接著，發佈設定檔案可匯入為持續性的本機組態設定，供命令列介面用於後續的作業。您的發佈設定只需匯入一次即可。

> [WACOM.NOTE] 發佈設定檔案包含敏感資訊。Microsoft 建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。在 Windows 中，修改資料夾屬性或使用 BitLocker。


1.	開啟 [**命令提示字元**]。
2.	執行下列命令以下載發佈設定檔案。

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	此命令會開啟可下載發行設定檔案的網頁。

3.	出現儲存檔案的提示時，請按一下 [**儲存**] 並提供檔案必須儲存的位置。
5.	在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

		azure account import <file>

	在上一個螢幕擷取畫面中，發佈設定檔案已儲存至工作站上的 C:\HDInsight 資料夾。

**建立 Azure 儲存體帳戶**

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存體帳戶，您才能建立 HDInsight 叢集。儲存體帳戶必須位於相同的資料中心內。

- 在命令提示字元視窗中執行下列命令，以建立 Azure 儲存體帳戶：

		azure storage account create [options] <StorageAccountName>

	出現位置提示時，請選取可佈建 HDINsight 叢集的位置。儲存體必須與 HDInsight 叢集位於相同的位置。目前，只有**東亞**、**東南亞**、**北歐**、**西歐**、**美國東部**、**美國西部**、**美國中北部**和**美國中南部**區域可託管 HDInsight 叢集。  

如需使用 Azure 管理入口網站建立 Azure 儲存體帳戶的相關資訊，請參閱[建立、管理或刪除儲存體帳戶][azure-create-storageaccount]。

如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則您可以使用下列命令來擷取資訊：

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

如需有關使用管理入口網站取得資訊的詳細資訊，請參閱*作法：檢視、複製和重新產生儲存體存取金鑰*小節，其屬於[建立、管理或刪除儲存體帳戶][azure-create-storageaccount]。

HDInsight 叢集還要求儲存體帳戶內要有容器。如果您所提供的儲存體帳戶沒有容器，則 *azure hdinsight cluster create* 會提示您輸入容器名稱並建立容器。不過，如果您想要預先建立容器，您可以使用下列命令：

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

- 在命令提示字元視窗中執行下列命令：

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**使用組態檔佈建 HDInsight 叢集**

一般而言，您會佈建 HDInsight 叢集、執行工作，然後將此叢集刪除，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次佈建叢集時皆可加以重複使用。

- 在命令提示字元視窗中執行下列命令：


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Windows Azure 服務**] 中，依序按一下 [**是**] 和 [**儲存**]。


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**列出並顯示叢集詳細資料**

- 使用下列命令，以列出並顯示叢集詳細資料：

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**刪除叢集**

- 使用下列命令刪除叢集：

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> 使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。

以下是使用 SDK 佈建 HDInsight 叢集時必須執行的程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started)安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2013。

2. 在 [檔案] 功能表中按一下 [**新增**]，然後按一下 [**專案**]。

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. 按一下 [**確定**] 以建立專案。

5. 在 [**工具**] 功能表中按一下 [**Nuget 套件管理員**]，然後按一下 [**Package Manager Console**]。

6. 在主控台中執行下列命令，以安裝封裝。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會將 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. 在 Main() 函數中，複製並貼上下列程式碼：

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. 取代 main() 函數開頭處的變數。

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。建立 HDInsight 叢集可能需要幾分鐘的時間。



##<a id="nextsteps"></a> 後續步驟
在本文中，您學到幾種佈建 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight][hdinsight-get-started] - 了解如何開始使用 HDInsight 叢集
* [使用 Sqoop 搭配 HDInsight][89e2276a] - 了解如何在 HDInsight 與 SQL Database 或 SQL Server 之間複製資料
* [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell] - 了解如何使用 PowerShell 處理 HDInsight
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs] - 了解如何以程式設計方式提交工作至 HDInsight
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation] - 探索 HDInsight SDK

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/zh-tw/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /zh-tw/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=35.1-->
