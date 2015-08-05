<properties 
   pageTitle="在 HDInsight 中佈建 Apache Spark 叢集 | Azure" 
   description="了解如何使用 Azure 入口網站、Azure PowerShell、命令列或 HDInsight .NET SDK 佈建 Azure HDInsight 的 Spark 叢集" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# 使用自訂選項在 HDInsight 上佈建 Apache Spark 叢集

在大部分的情況下，您可以使用[開始使用 HDInsight 上的 Apache Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) 所述的快速建立方法佈建 Spark 叢集。在某些情況下，您可能想要佈建自訂叢集。比方說，您可能會想要連接外部中繼資料存放區，讓 Hive 中繼資料的持續性超越叢集存留期，或是可能會想要使用額外的儲存體來搭配叢集。

對於這類案例和其他案例，本文章提供如何使用 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上佈建自訂 Spark 叢集的指示。


**必要條件：**

開始執行本文中的指示之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

##<a id="configuration"></a>有哪些相異的組態選項？

###其他儲存體

在設定期間，您必須指定 Azure Blob 儲存體帳戶和預設容器。叢集以此做為預設儲存位置。您可以選擇性地指定將與叢集相關聯的其他 Azure 儲存體帳戶。

>[AZURE.NOTE] 請不要讓多個叢集共用一個 Blob 儲存體容器。不支援此做法。

如需使用次要 Blob 存放區的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight](hdinsight-use-blob-storage.md)。

###Metastore

Spark 可讓您透過原始資料定義結構描述和 Hive 資料表。您可以將這些結構描述和資料表中繼資料儲存在外部 metastore。使用中繼存放區有助於保留 Hive 中繼資料，因此在佈建新叢集時，您便無需重建 Hive 資料表。依預設，Hive 會使用內嵌資料庫來儲存此資訊。刪除叢集時，嵌入的資料庫將無法保留中繼資料。

### 叢集自訂

您可以在佈建期間使用指令碼來安裝其他元件或自訂組態。這類指令碼可透過**指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-customize-cluster]。


###虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，其包含解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

	![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

	![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

如需虛擬網路特性、優點和功能的詳細資訊，請參閱＜[虛擬網路概觀](http://msdn.microsoft.com/library/azure/jj156007.aspx)＞。

> [AZURE.NOTE] 您必須先建立 Azure 虛擬網路，才能佈建叢集。如需詳細資訊，請參閱＜[虛擬網路組態工作](http://msdn.microsoft.com/library/azure/jj156206.aspx)＞。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。
>
> 強烈建議您一個叢集只指定一個子網路。

##<a id="portal"></a>使用 Azure 入口網站

HDInsight 上的 Spark 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。相同的資料中心上必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。如需建立 Azure 儲存帳號的詳細資訊，請參閱[如何建立儲存帳號][azure-create-storageaccount]。

**使用自訂建立選項建立 HDInsight 叢集**

1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下頁面底部的 [+新增]，然後依序按一下 [資料服務]、[HDInsight]、[自訂建立]。
3. 在 [叢集詳細資料] 頁面上，輸入或選擇下列值：

	![提供 HDInsight 叢集上 Spark 的詳細資料](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "提供 HDInsight 叢集上 Spark 的詳細資料")

    <table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>叢集名稱</td>
		<td><p>為叢集命名。</p>
			<ul>
			<li>網域名稱系統 (DNS) 名稱的開頭與結尾都必須是英數字元，且可包含連字號。</li>
			<li>欄位必須是 3 到 63 個字元的字串。</li>
			</ul></td></tr>
	<tr><td>叢集類型</td>
		<td>針對叢集類型，選取 [Spark]<strong></strong>。</td></tr>
	<tr><td>作業系統</td>
		<td>選取 [Windows Server 2012 R2 Data Center]<b></b> 來佈建於 Windows 上執行的 Spark 叢集。</td></tr>
	<tr><td>HDInsight 版本</td>
		<td>選擇版本。對於 Spark 來說，唯一可用的版本選項是 <b>HDInsight 3.2</b>，其使用 Spark 1.3.1。</td></tr>
	</table>
	輸入或選取如資料表中所示的值，然後按一下向右箭頭。

4. 在 [設定叢集] 頁面上，輸入或選取下列值：

	![提供叢集的節點數目和節點類型](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "提供叢集的節點數目和節點類型")

	<table border="1">
<tr><th>名稱</th><th>值</th></tr>
<tr><td>資料節點</td><td>您要部署的資料節點數。請建立單一節點叢集，以供測試之用。<br />叢集大小限制會隨著 Azure 訂用帳戶而不同。若要提高限制，請與 Azure 帳務支援人員連絡。</td></tr>
<tr><td>區域/虛擬網路</td><td><p>請選擇與您在前一個程序中建立的儲存體帳戶相同的區域。使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。</p>。<br/>如果您已建立 Azure 虛擬網路，您可以選取將設定 HDInsight 叢集為使用的網路。</p><p>如需建立 Azure 虛擬網路的詳細資訊，請參閱<a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">虛擬網路組態工作</a>。</p></td></tr>
<tr><td>前端節點大小</td><td><p>選取前端節點的虛擬機器 (VM) 大小。</p></td></tr>
<tr><td>資料節點大小</td><td><p>選取資料節點的 VM 大小。</p></td></tr>
</table>
	>[AZURE.NOTE] 根據選擇的 VM ，您的成本可能會有所不同。HDInsight 針對叢集節點會使用所有標準層 VM。如需 VM 大小對您價格影響的相關資訊，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 價格</a>。


5. 在 [Configure Cluster User] 頁面上，提供下列值：

    ![提供系統管理使用者和 RDP 使用者詳細資料](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "提供系統管理使用者和 RDP 使用者詳細資料")

    <table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>HTTP 使用者名稱</td>
		<td>指定 HDInsight 叢集使用者名稱。</td></tr>
	<tr><td>HTTP 密碼/確認密碼</td>
		<td>指定 HDInsight 叢集使用者密碼。</td></tr>
	<tr><td>為叢集啟用遠端桌面</td>
		<td>選取此核取方塊，以指定遠端桌面使用者的使用者名稱、密碼和到期日期，使其在佈建之後可以遠端進入叢集節點。您也可以稍後在叢集佈建後啟用遠端桌面。如需指示，請參閱<a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">使用 RDP 連接至 HDInsight 叢集</a>。</td></tr>
	<tr><td>輸入 Hive/Oozie 中繼存放區</td>
		<td>勾選此核取方塊，在與叢集相同的資料中心上指定 SQL 資料庫，以做為 Hive/Oozie 中繼存放區使用。如果您選取此核取方塊，則必須在精靈的後續頁面指定 Azure SQL Database 的詳細資料。即使在將叢集刪除之後，如果您想要保留 Hive/Oozie 工作的相關中繼資料，此選項將會很有幫助。如需建立 metastore 的指示，請參閱<a href="https://azure.microsoft.com/zh-tw/documentation/articles/sql-database-get-started/" target="_blank">建立您的第一個 Azure SQL Database</a>。</td></tr>
	</td></tr>
</table>
	>[AZURE.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [設定]，然後在 [Windows Azure 服務] 中，依序按一下 [是] 和 [儲存]。

    按一下向右箭頭。

6. 在 [設定 Hive/Oozie 中繼存放區] 頁面上，提供下列值：

    ![提供 Hive/Oozie Metastore 資料庫詳細資料](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "提供 Hive/Oozie Metastore 資料庫詳細資料")

	指定要做為 Hive/OOzie Metastore 的 Azure SQL Database。您可以為 Hive 和 Oozie Metastore 指定相同的資料庫。此 SQL 資料庫必須與 HDInsight 叢集位於相同的資料中心內。清單方塊只會列出與您在 [叢集詳細資料]<strong></strong> 頁面上指定之相同資料中心內的 SQL 資料庫。同時請指定使用者名稱及密碼以連線到所選取的 Azure SQL Database。

    >[AZURE.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。

    按一下向右箭頭。

7. 在 [**儲存體帳戶**] 頁面上，提供下列值：

    ![提供儲存體帳戶詳細資料](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "提供儲存體帳戶詳細資料")

	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>儲存體帳戶</td>
		<td>指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存帳號。您可以從三個選項中擇一使用：
		<ul>
			<li><strong>使用現有的儲存體</strong></li>
			<li><strong>建立新的儲存體</strong></li>
			<li><strong>從另一個訂用帳戶使用儲存體</strong></li>
		</ul>
		</td></tr>
	<tr><td>帳戶名稱</td>
		<td><ul>
			<li>如果要使用現有儲存體，請在 [帳戶名稱]<strong></strong> 中選取現有儲存體帳戶。下拉式清單列出的儲存體帳戶僅限位於與您選擇佈建叢集相同的資料中心。</li>
			<li>如果選擇 [建立新的儲存體]<strong></strong> 或 [從另一個訂用帳戶使用儲存體]<strong></strong> 選項，則您必須提供儲存體帳戶名稱。</li>
		</ul></td></tr>
	<tr><td>帳戶金鑰</td>
		<td>如果選擇 [從另一個訂用帳戶使用儲存體]<strong></strong> 選項，請指定該儲存體帳戶的帳戶金鑰。</td></tr>
	<tr><td>預設容器</td>
		<td><p>指定儲存體帳戶上的預設容器做為 HDInsight 叢集的預設檔案系統。如果您在 [儲存體帳戶] <strong></strong>欄位中選擇了 [使用現有的儲存體] <strong></strong>，且該帳戶中沒有現有的容器，根據預設，將使用叢集名稱建立名稱相同的容器。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。例如 mycontainer1、mycontainer2，依此類推。然而，如果現有儲存體帳戶已有容器存在，且其名稱與您指定的叢集名稱不同，您仍可以使用該容器。</p>
        <p>如果選擇建立新的儲存體或從另一個 Azure 訂用帳戶使用儲存體，則必須指定預設容器名稱。</p>
    </td></tr>
	<tr><td>其他儲存體帳戶</td>
		<td>HDInsight 支援多個儲存體帳戶。叢集可使用的其他儲存體帳戶沒有數量上的限制。但如果您使用 Azure 入口網站建立叢集，則會因為 UI 的限制而只能使用最多七個儲存體帳戶。您所指定的每個其他儲存體帳戶都會在精靈上另外加入一個 **儲存體帳戶** 頁面，您可在此指定帳戶資訊。以上方的螢幕擷取畫面為例，在選取一個其他儲存體帳戶後，對話方塊因此加入了頁面 5。</td></tr>
</table>
	按一下向右箭頭。

8. 如果您選擇為叢集設定其他儲存體，請在 [儲存體帳戶] 頁面上，輸入其他儲存體帳戶的帳戶資訊：

	![提供額外的儲存體帳戶詳細資料](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "提供額外的儲存體帳戶詳細資料")

    在這裡，您可以選擇現有儲存體、建立新儲存體或使用其他 Azure 訂用帳戶的儲存體。提供值的程序會與上個步驟類似。

    > [AZURE.NOTE] 在您為 HDInsight 叢集選擇 Azure 儲存體帳戶後，您將無法刪除帳戶，也無法將帳戶變更為不同的帳戶。

8. 若要在建立叢集時自訂叢集，請在 [指令碼動作] 頁面上，按一下 [加入指令碼動作] 並提供有關您將執行的自訂指令碼詳細資料。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-customize-cluster]。

	如果需要使用指令碼動作，您必須提供下列輸入。
	
	<table border='1'>
	<tr><th>屬性</th><th>值</th></tr>
	<tr><td>名稱</td>
		<td>指定指令碼動作的名稱。</td></tr>
	<tr><td>指令碼 URI</td>
		<td>針對自訂叢集所叫用的指令碼，指定統一資源識別項 (URI)。</td></tr>
	<tr><td>節點類型</td>
		<td>指定執行自訂指令碼的節點。您可以選擇 [<b>所有節點</b>]、[<b>僅限前端節點</b>] 或 [<b>僅限背景工作節點</b>]。
	<tr><td>參數</td>
		<td>如果指令碼要求，請指定參數。</td></tr>
</table>
	您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，請按一下核取記號以開始佈建叢集。



##<a id="powershell"></a>使用 Azure PowerShell

本節提供如何使用 Azure PowerShell 在 Azure HDInsight 中佈建 Apache Spark 叢集的指示。如需設定工作站以執行 HDInsight Windows Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]。如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料][hdinsight-powershell-reference]。

> [AZURE.NOTE] 本節的指令碼可用來設定 Azure 虛擬網路上的 HDInsight 叢集，但不會建立 Azure 虛擬網路。如需建立 Azure 虛擬網路的相關資訊，請參閱[虛擬網路組態工作](http://msdn.microsoft.com/library/azure/jj156206.aspx)。

以下是使用 Azure PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

您可以使用 Windows PowerShell 主控台或 Windows PowerShell 整合式指令碼環境 (ISE) 來執行指令碼。
 
HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存體帳戶和儲存體容器，才能夠建立 HDInsight 叢集。儲存體帳戶必須與 HDInsight 叢集位於相同的資料中心。

**連接到您的 Azure 帳戶**

		Add-AzureAccount 

系統會提示您輸入 Azure 帳號認證。

**建立 Azure 儲存體帳戶**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

如果您已有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，可以使用下列 Windows PowerShell 命令來擷取資訊：

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**建立 Azure Blob 儲存體容器**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

- 從 Azure PowerShell 主控台視窗執行下列命令：

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE] 您指定的認證會用來建立叢集的 Hadoop 使用者帳戶。您將使用此帳戶來連接到叢集並執行工作。如果從 Azure 入口網站使用快速建立選項佈建叢集，則預設 Hadoop 使用者名稱是 "admin"。請不要將此帳戶與遠端桌面通訊協定 (RDP) 使用者帳戶相混淆。RDP 使用者帳戶必須與 Hadoop 使用者帳戶不同。如需詳細資訊，請參閱[使用 Azure 管理入口網站在 HDInsight 上管理 Hadoop 叢集][hdinsight-admin-portal]。

	叢集佈建可能需要幾分鐘的時間才會完成。

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "在 PowerShell 中使用基本組態佈建 Spark 叢集")


**使用自訂組態選項佈建 HDInsight 叢集**

佈建叢集時，您可以使用其他組態選項，例如連線到多個 Azure Blob 儲存體容器，或使用 Azure SQL Database 來處理 Hive 和 Oozie Metastore。這可讓您區隔資料和中繼資料的存留期與叢集的存留期。

> [AZURE.NOTE] Windows PowerShell Cmdlet 是唯一建議用來在 HDInsight 叢集中變更組態變數的方法。透過遠端桌面連接至叢集時對 Hadoop 組態檔所做的變更，可能會在叢集修補時遭到覆寫。透過 Azure PowerShell 所設定的組態值在叢集修補時會保留下來。

- 從 Windows PowerShell 視窗執行下列命令：

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [設定]，然後在 [Windows Azure 服務] 中，依序按一下 [是] 和 [儲存]。

	叢集佈建可能需要幾分鐘的時間才會完成。

**列出 HDInsight 叢集**

- 從 Azure PowerShell 主控台視窗執行下列命令，以列出 HDInsight 叢集並確認是否已成功佈建叢集：

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a>使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。

以下是使用 SDK 佈建 HDInsight 叢集時必須執行的程序：

- 安裝 HDInsight .NET SDK
- 建立自我簽署憑證
- 建立主控台應用程式
- 執行應用程式


**安裝 HDInsight .NET SDK**

您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

建立自我簽署憑證，將它安裝到工作站，然後上傳至 Azure 訂用帳戶。如需指示，請參閱[建立自我簽署憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。


**建立 Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2013。

2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

3. 在 [**新增專案**] 中，輸入或選取下列值：

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. 按一下 [確定] 以建立專案。

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6. 在主控台中執行下列命令，以安裝套件：

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令會將 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。

7. 從 [方案總管] 中，按兩下 [Program.cs] 將它開啟。

8. 在檔案頂端新增下列 using 陳述式：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. 在 Main() 函數中，複製並貼上下列程式碼：

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. 取代 main() 函數開頭處的變數。

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。建立 HDInsight 叢集可能需要幾分鐘的時間。


##<a id="nextsteps"></a> 後續步驟

* 請參閱[在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)，以了解如何在 HDInsight 中搭配使用 Apache Spark 和 BI 工具 (如 Power BI 和 Tableau)。 
* 請參閱[在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)，以了解如何在 HDInsight 上使用 Apache Spark 建置機器學習應用程式。
* 請參閱[在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)，以了解如何在 HDInsight 上使用 Apache Spark 建置串流應用程式。
* 請參閱[在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)，以了解如何使用資源管理員來管理配置給 Spark 叢集的資源。


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "搭配 HDInsight 使用 Sqoop"

<!----HONumber=July15_HO4-->