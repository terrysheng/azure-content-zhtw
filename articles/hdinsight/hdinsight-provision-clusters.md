<properties
   pageTitle="在 HDInsight 中自訂佈建 Hadoop 叢集 |Microsoft Azure"
   	description="了解如何使用 Azure Preview 入口網站、Azure PowerShell、命令列或 .NET SDK 自訂佈建 Azure HDInsight 的叢集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/11/2015"
   ms.author="jgao"/>

# 在 HDInsight 中佈建 Hadoop 叢集 (英文)

了解如何規劃 HDInsight 叢集佈建作業。

[AZURE.INCLUDE [選取器](../../includes/hdinsight-portal-management-selector.md)]

* [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [在 HDInsight 中佈建 Hadoop 叢集](hdinsight-provision-clusters-v1.md)

**必要條件：**

開始執行本文中的指示之前，您必須擁有以下項目：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。


## 基本組態選項


- **叢集名稱**

	叢集名稱用來識別叢集。叢集名稱必須遵循下列方針：

	- 欄位必須是包含 3 到 63 個字元的字串。
	- 欄位只能包含字母、數字和連字號。

- **訂用帳戶名稱**

	一個 HDInsight 叢集與一個 Azure 訂用帳戶綁定。

- **作業系統**

	您可以在下列兩個作業系統其中之一佈建 HDInsight 叢集：- **Windows 上的 HDInsight (Windows Server 2012 R2 Datacenter)**：- **Linux 上的 HDInsight (Linux 版 Ubuntu 12.04 LTS) (預覽版)**：HDInsight 提供在 Azure 上設定 Linux 叢集的選項。如果您熟悉 Linux 或 Unix、要從現有的 Linux Hadoop 方案進行移轉，或想輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請設定 Linux 叢集。如需詳細資訊，請參閱[開始在 Linux 上的 HDInsight 中使用 Hadoop](hdinsight-hadoop-linux-get-started.md)。


- **HDInsight 版本**

	這可用來決定要用於此叢集的 HDInsight 版本。如需詳細資訊，請參閱 [HDInsight 中的 Hadoop 叢集版本和元件](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **叢集類型**和**叢集大小 (亦稱為資料節點)**

	HDInsight 可讓客戶部署各種不同的叢集類型，用於不同的資料分析工作負載。目前提供的叢集類型包括：

	- Hadoop 叢集：適用於查詢和分析工作負載
	- HBase 叢集：適用於 NoSQL 工作負載
	- Storm 叢集：適用於即時事件的處理工作負載
	- Spark 叢集 (預覽)：適用於記憶體內部處理、互動式查詢、資料流和機器學習工作負載。

	![HDInsight 叢集](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] *Azure HDInsight 叢集*也稱為 *HDInsight 中的 Hadoop 叢集*，或是 *HDInsight 叢集*。有時候，它可與 *Hadoop 叢集*互換使用。它們都代表裝載於 Microsoft Azure 環境中的 Hadoop 叢集。

	在特定叢集類型中，各節點有不同的角色，可讓客戶針對特定角色，根據適合其工作負載的詳細資料來調整節點的大小。舉例來說，若執行的分析作業類型會耗用大量記憶體，Hadoop 叢集將可使用大量記憶體來佈建背景工作節點。

	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight 的 Hadoop 叢集利用兩個角色部署：

	- 前端節點 (2 個節點)
	- 資料節點 (至少 1 個節點)

	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight 適用的 HBase 叢集會以三種角色來部署：- 前端伺服器 (2 個節點) - 區域伺服器 (至少 1 個節點) - 主要/Zookeeper 節點 (3 個節點)

	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight 適用的 Storm 叢集會以三種角色來部署：- Nimbus 伺服器 (2 個節點) - 監督員伺服器 (至少 1 個節點) - Zookeeper 節點 (3 個節點)


	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight 適用的 Spark 叢集會以三種角色來部署：- 前端伺服器 (2 個節點) - 背景工作伺服器 (至少 1 個節點) - Zookeeper 節點 (3 個節點) (為 A1 Zookeeper 免費提供)

	客戶需根據叢集的生命期，就這些節點的使用量支付費用。一旦建立叢集之後便會開始計費，而刪除叢集時便會停止計費 (無法取消配置或保留叢集)。叢集大小會影響叢集價格。為了方便學習，建議使用 1 個資料節點。如需關於 HDInsight 定價的詳細資訊，請參閱 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。


	>[AZURE.NOTE]叢集大小限制會隨著 Azure 訂用帳戶而有所不同。若要提高限制，請與帳務支援人員連絡。

- **地區/虛擬網路 (即位置)**

	![Azure 區域](./media/hdinsight-provision-clusters/Azure.regions.png)

	如需支援地區的清單，請按一下 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)中的 [地區] 下拉式清單。

- **節點大小**

	![hdinsight vm 節點大小](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	選取 VM 的節點大小。如需詳細資訊，請參閱[雲端服務的大小](cloud-services-sizes-specs.md)

	根據選擇的 VM ，您的成本可能會有所不同。HDInsight 針對叢集節點會使用所有標準層 VM。如需 VM 大小對您價格影響的相關資訊，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 定價</a>。


- **HDInsight 使用者**

	HDInsight 叢集可讓您在佈建期間設定兩個使用者帳戶：

	- HTTP 使用者。預設使用者名稱是在 Azure Preview 入口網站上使用基本組態的 admin。
	- RDP 使用者 (Windows 叢集)：用來連線到使用 RDP 的叢集。當您建立帳戶時，必須將到期日設為從今天算起的 90 天內。
	- SSH 使用者 (Linux 叢集)：用來連線到使用 SSH 的叢集。當您依照[針對 Linux、Unix 或 OS X 上的 HDInsight 使用 SSH 搭配 Linux 架構的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md) 中的步驟建立叢集之後，您便可以建立其他 SSH 使用者帳戶。



- **Azure 儲存體帳戶**

	原始的 HDFS 會使用叢集上的多個本機磁碟。HDInsight 則會使用 Azure Blob 儲存體來儲存資料。Azure Blob 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的結構化或非結構化資料。將資料儲存在 Blob 儲存體中，您便可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

	在設定期間，您必須指定 Azure 儲存體帳戶，並在該 Azure 儲存體帳戶中指定 Azure Blob 儲存體容器。某些佈建程序會要求您事先建立 Azure 儲存體帳戶和 Blob 儲存體容器。叢集會以該 Blob 儲存體容器做為預設儲存位置。您也可以選擇指定叢集可存取的其他 Azure 儲存體帳戶 (連結的儲存體)。此外，叢集也可以存取任何設有完整公用讀取權限或僅限對 blob 之公用讀取權的 Blob 容器。如需關於限制存取的詳細資訊，請參閱[管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)。

	![HDInsight 儲存體](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Blob 儲存體容器提供一組 Blob 群組，如圖所示：

	![Azure Blob](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]請不要讓多個叢集共用一個 Blob 儲存體容器。不支援此做法。

	如需使用次要 Blob 存放區的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight](hdinsight-use-blob-storage.md)。

- **Hive/Oozie 中繼存放區**

	中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。使用中繼存放區能協助您保留 Hive 和 Oozie 中繼資料，因此在佈建新叢集時，您便無需重建 Hive 資料表或 Oozie 工作。根據預設，Hive 會使用內嵌的 Azure SQL 資料庫來儲存此資訊。刪除叢集時，嵌入的資料庫將無法保留中繼資料。舉例來說，您使用 Hive 中繼存放區來佈建叢集。您已建立一些 Hive 資料表。當您刪除叢集並透過相同的 Hive 中繼存放區重建叢集之後，便可以看到您在原始的叢集中建立的 Hive 資料表。

## 進階組態選項

>[AZURE.NOTE]本區段目前僅適用於以 Windows 為基礎的 HDInsight 叢集。

### 使用 HDInsight 叢集自訂功能來自訂叢集

有時候，您可能需要設定組態檔。以下是一些需要設定的組態檔。

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

叢集無法保留重新製作映像所造成的變更。如需詳細資訊，請參閱[角色執行個體由於作業系統升級而重新啟動](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (英文)。若要在叢集存留期間保留變更，您可以在佈建程序期間使用 HDInsight 叢集自訂。

以下是自訂 Hive 組態的 Azure PowerShell 指令碼範例：

	# hive-site.xml configuration
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60

	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues

	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

以下是更多自訂其他組態檔的範例：

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

如需詳細資訊，請參閱 Azim Uddin 部落格的[自訂 HDInsight 叢集佈建](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)。




### 使用指令碼動作來自訂叢集

您可以在佈建期間使用指令碼來安裝其他元件或自訂組態。這類指令碼可透過**指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Preview 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。


### 使用 Azure 虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

如需虛擬網路特性、優點和功能的詳細資訊，請參閱＜[虛擬網路概觀](http://msdn.microsoft.com/library/azure/jj156007.aspx)＞。

> [AZURE.NOTE]您必須先建立 Azure 虛擬網路，才能佈建 HDInsight 叢集。如需詳細資訊，請參閱[將 Hadoop 叢集佈建至虛擬網路](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。使用 Azure PowerShell Cmdlet Get-AzureVNetConfig 來檢查現有的 Azure 虛擬網路是否以位置為基礎。如果您的虛擬網路並非以位置為基礎，您會有下列選項：
>
> - 將現有的虛擬網路組態匯出，然後建立新的虛擬網路。根據預設，所有新的虛擬網路都是以位置為基礎。
> - 移轉到以位置為基礎的虛擬網路。請參閱[將現有服務移轉到區域範圍](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)。
>
> 強烈建議您一個叢集只指定一個子網路。

## 佈建工具

- Azure Preview 入口網站
- Azure PowerShell
- HDInsight .NET SDK

### 使用 Preview 入口網站

您可以參考 [基本組態選項] 和 [進階組態選項] 以取得有關欄位的說明。

**建立 HDInsight 叢集**

1. 登入 [Azure Preview 入口網站][azure-preview-portal]。
2. 請按一下 [新增]、[資料分析]，然後按一下 [HDInsight]。

    ![在 Azure Preview 入口網站中建立新的叢集](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "在 Azure Preview 入口網站中建立新的叢集")

3. 輸入或選取下列值：
  - **叢集名稱**：輸入叢集的名稱。如果該叢集名稱可使用，則名稱旁邊將會出現綠色的核取記號。
  - **叢集類型**：選取 [Hadoop]。
  - **叢集作業系統**：選取 [Windows Server 2012 R2 資料中心]。
  - **訂用帳戶**：選取將用於佈建此叢集的 Azure 訂用帳戶。
  - **資源群組**：選取現有資源群組或建立新的。如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。
  - **認證**：設定 Hadoop 使用者 (HTTP 使用者) 的使用者名稱和密碼。如果您啟用該叢集的遠端桌面，您必須設定遠端桌面使用者的使用者名稱、密碼和帳戶到期日。在底部按一下 [選取] 以儲存此變更。

	![提供叢集認證](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "提供叢集認證")
  - **資料來源**：建立新的 Azure 儲存體帳戶，或選取現有的帳戶，使其做為叢集的預設檔案系統。

	   ![資料來源刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "提供資料來源組態")

  	- **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。
  	- **選取儲存體帳戶 / 建立新的**：請按一下 [選取儲存體帳戶] 瀏覽並選取您要與叢集相關聯的現有儲存體帳戶。或按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。
    - **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。
  	- **位置**：儲存體帳戶所在地或將建立的地理區域。這個位置將會決定叢集位置。該叢集與預設儲存體帳戶必須並存於相同的 Azure 資料中心。

  - **節點定價層**：設定您針對該叢集所需的背景工作節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。

	![節點定價層刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "指定叢集節點的數目")

  - [選用設定] 以選取叢集的版本，以及設定其他選擇性設定，例如聯結 [虛擬網路]、設定 [外部中繼存放區] 來保存 Hive 和 Oozie 的資料、使用 [指令碼動作] 來自訂要安裝自訂元件的叢集，或使用具有該叢集的其他儲存體帳戶。

  	- **HDInsight 版本**：選取您想要用於該叢集的版本。如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。
  	- **虛擬網路**：如果您想要將叢集放置到虛擬網路，請選擇 Azure 虛擬網路和子網路。  

		![虛擬網路刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "指定虛擬網路詳細資料")

    >[AZURE.NOTE]以 Windows 為主的 HDInsight 叢集只能放置到傳統的虛擬網路。

  	- **外部中繼存放區**：指定 Azure SQL 資料庫來儲存與該叢集相關聯的 Hive 和 Oozie 中繼資料。

		![自訂中繼存放區刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "指定外部中繼存放區")

		在 [使用 Hive 現有的 SQL DB] 中繼資料按一下 [是]，選取 SQL 資料庫，然後提供該資料庫的使用者名稱/密碼。如果您要**使用 Oozie 中繼資料現有的 SQL DB**，請重複這些步驟。按一下 [選取]，直到您回到 [選用設定] 刀鋒視窗。

		>[AZURE.NOTE]用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。

  	- **指令碼動作** 如果您想要在該叢集正在建立使用自訂指令碼來自訂叢集。如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。

		![指令碼動作刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "指定指令碼動作")

    - **Azure 儲存體金鑰**：指定與該叢集相關聯的其他儲存體帳戶。在 [Azure 儲存體金鑰] 刀鋒視窗中，按一下 [加入儲存體金鑰]，然後選取現有的儲存體帳戶或建立新的帳戶。

		![其他儲存體刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "指定其他儲存體帳戶")

4. 按一下 [建立]。選取 [釘選到「開始面板」] 會將叢集的圖隔加入 Preview 入口網站的「開始面板」。該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

	| 佈建期間 | 佈建完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的佈建指示器](./media/hdinsight-provision-clusters/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。使用「開始面板」上的圖格或頁面左邊的 [通知] 項目，檢查佈建處理序。

5. 佈建完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

	![叢集刀鋒視窗](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "叢集屬性")

	使用下列步驟，以了解在刀鋒視窗頂端和 [基本功能] 區段中的圖示：

	* **設定**和**所有設定**：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。

	* **儀表板**、**叢集儀表板**和 **URL**：這些是存取叢集儀表板 (也就是可在叢集上執行作業之 Web 入口網站) 的所有方法。

	* **遠端桌面**：可讓您在叢集節點上啟用/停用遠端桌面。

	* **調整叢集**：可讓您變更此叢集的背景工作角色節點數目。

	* **刪除**：刪除 HDInsight 叢集。

	* **快速入門** (![雲和雷電圖示 = 快速入門](./media/hdinsight-provision-clusters/quickstart.png))：顯示可協助您開始使用 HDInsight 的資訊。

	* **使用者** (![使用者圖示](./media/hdinsight-provision-clusters/users.png))：可讓您設定 Azure 訂用帳戶上其他使用者對此叢集的 [入口網站管理] 權限。

		> [AZURE.IMPORTANT]這只會影響在 Preview 入口網站對此叢集的存取和權限，對於連線至 HDInsight 叢集或將工作提交至其上的使用者並沒有作用。

	* **標記** (![標記圖示](./media/hdinsight-provision-clusters/tags.png))：標記可讓您設定索引鍵/值組，以定義雲端服務的自訂分類。例如，您可能會建立名為 [專案] 的金鑰，然後使用與特定專案相關聯的所有服務之通用值。




### 使用 Azure PowerShell
Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。本節提供如何使用 Azure PowerShell 佈建 HDInsight 叢集的指示。如需設定工作站以執行 HDInsight Windows Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/dn858087.aspx)。


以下是使用 Azure PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 資源群組
- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop


	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)


### 使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。請遵循下列指示建立 Visual Studio 主控台應用程式，並貼上建立叢集的程式碼。

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

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [管理解決方案的 Nuget 套件]。在對話方塊內的 [搜尋] 文字方塊，搜尋 [HDInsight]。從顯示的結果安裝下列項目：

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	搜尋 Azure 驗證，並從顯示的結果安裝 **Microsoft.Azure.Common.Authentication**。

6. 在 [方案總管] 中按兩下 **Program.cs** 予以開啟，貼上下列程式碼，並提供變數的值：


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. 按 **F5** 鍵執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。系統也會提示您輸入 Azure 帳號認證。建立 HDInsight 叢集可能需要幾分鐘的時間。


##<a id="nextsteps"></a> 後續步驟
在本文中，您學到幾種佈建 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](hdinsight-get-started.md) - 了解如何開始使用 HDInsight 叢集
* [使用 Sqoop 搭配 HDInsight](hdinsight-use-sqoop.md) - 了解如何在 HDInsight 與 SQL Database 或 SQL Server 之間複製資料
* [使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md) - 了解如何使用 PowerShell 處理 HDInsight
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以程式設計方式提交工作至 HDInsight
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation] - 探索 HDInsight SDK


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com

<!---HONumber=August15_HO8-->