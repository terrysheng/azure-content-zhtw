<properties
   pageTitle="在 HDInsight 中建立 Windows 型 Hadoop 叢集 | Microsoft Azure"
   	description="了解如何建立 Azure HDInsight 的叢集。"
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
   ms.date="01/22/2016"
   ms.author="jgao"/>

# 在 HDInsight 中建立 Windows 型 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-create-windows-cluster-selector.md)]

了解如何規劃 HDInsight 叢集建立作業。

###必要條件：

開始執行本文中的指示之前，您必須擁有以下項目：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。


## 基本組態選項

下列是關於建立 HDInsight 叢集的基本組態選項。

- **叢集名稱**

	叢集名稱用來識別叢集。叢集名稱必須遵循下列方針：

	- 欄位必須是包含 3 到 63 個字元的字串。
	- 欄位只能包含字母、數字和連字號。

- **訂用帳戶名稱**

	一個 HDInsight 叢集與一個 Azure 訂用帳戶綁定。

- **資源群組名稱**

	Azure 資源管理員 (ARM) 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。
	
- **作業系統**

	您可以在下列其中一個作業系統上建立 HDInsight 叢集：
	- **Windows 上的 HDInsight (Windows Server 2012 R2 Datacenter)**：
	- **Linux 上的 HDInsight (Linux 版 Ubuntu 12.04 LTS)**：HDInsight 提供在 Azure 上設定 Linux 叢集的選項。如果您熟悉 Linux 或 Unix、要從現有的 Linux Hadoop 方案進行移轉，或想輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請設定 Linux 叢集。如需詳細資訊，請參閱[開始在 Linux 上的 HDInsight 中使用 Hadoop](hdinsight-hadoop-linux-get-started.md)。

- **叢集類型**和**叢集大小 (亦稱為資料節點)**

	HDInsight 可讓客戶部署各種不同的叢集類型，用於不同的資料分析工作負載。目前提供的叢集類型包括：

	- Hadoop 叢集：適用於查詢和分析工作負載
	- HBase 叢集：適用於 NoSQL 工作負載
	- Storm 叢集：適用於即時事件的處理工作負載
	- Spark 叢集 (預覽)：適用於記憶體內部處理、互動式查詢、資料流和機器學習工作負載。

	![HDInsight 叢集](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] Azure HDInsight 叢集也稱為 HDInsight 中的 Hadoop 叢集，或是 HDInsight 叢集。有時候，它可與 Hadoop 叢集互換使用。它們都代表裝載於 Microsoft Azure 環境中的 Hadoop 叢集。

	在特定叢集類型中，各節點有不同的角色，可讓客戶針對特定角色，根據適合其工作負載的詳細資料來調整節點的大小。舉例來說，若執行的分析作業類型會耗用大量記憶體，Hadoop 叢集將可使用大量記憶體來建立背景工作節點。

	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	HDInsight 的 Hadoop 叢集利用兩個角色部署：

	- 前端節點 (2 個節點)
	- 資料節點 (至少 1 個節點)

	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HDInsight 適用的 HBase 叢集會以三種角色來部署：
	- 前端伺服器 (2 個節點)
	- 區域伺服器 (至少 1 個節點)
	- 主要/Zookeeper 節點 (3 個節點)

	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	HDInsight 適用的 Storm 叢集會以三種角色來部署：
	- Nimbus 伺服器 (2 個節點)
	- 監督員伺服器 (至少 1 個節點)
	- Zookeeper 節點 (3 個節點)


	![HDInsight Hadoop 叢集角色](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	HDInsight 適用的 Spark 叢集會以三種角色來部署：
	- 前端伺服器 (2 個節點)
	- 背景工作伺服器 (至少 1 個節點)
	- Zookeeper 節點 (3 個節點) (為 A1 Zookeeper 免費提供)

	客戶需根據叢集的生命期，就這些節點的使用量支付費用。一旦建立叢集之後便會開始計費，而刪除叢集時便會停止計費 (無法取消配置或保留叢集)。叢集大小會影響叢集價格。為了方便學習，建議使用 1 個資料節點。如需關於 HDInsight 定價的詳細資訊，請參閱 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)。


	>[AZURE.NOTE] 叢集大小限制會隨著 Azure 訂用帳戶而有所不同。若要提高限制，請與帳務支援人員連絡。

- **HDInsight 版本**

	這可用來決定要用於此叢集的 HDInsight 版本。如需詳細資訊，請參閱 [HDInsight 中的 Hadoop 叢集版本和元件](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)。


- **位置 (區域)**

	HDInsight 叢集與其預設儲存體帳戶必須並存於相同的 Azure 位置。
	
	![Azure 區域](./media/hdinsight-provision-clusters/Azure.regions.png)

	如需支援的地區清單，請按一下 [HDInsight 定價](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)中的 [地區] 下拉式清單。

- **節點大小**

	![hdinsight vm 節點大小](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	選取 VM 的節點大小。如需詳細資訊，請參閱[雲端服務的大小](cloud-services-sizes-specs.md)。您可以選取叢集使用的運算資源大小。例如，如果您知道將會執行需要大量記憶體的作業，您可以選取具有較多記憶體的運算資源。

	>[AZURE.NOTE] 叢集所使用的節點不會算做虛擬機器，因為節點所使用的虛擬機器映像是 HDInsight 服務的實作詳細資料。不過，節點所使用的計算核心則會算入訂用帳戶可用的計算核心總數。您可以在建立 HDInsight 叢集時，於 [節點定價層] 刀鋒視窗的摘要區段中查看叢集將會使用的核心數目以及可用核心數目。

	不同的叢集類型具有不同的節點類型、節點數目和節點大小。例如，Hadoop 叢集類型有兩個_前端節點_和預設的四個_資料節點_，而 Storm 叢集類型有兩個 _Nimbus 節點_、三個 _Zookeeper 節點_和預設的四個_監督員節點_。

	> [AZURE.IMPORTANT] 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。

	使用 Azure Preview 入口網站設定叢集時，節點大小會透過 [節點定價層] 刀鋒視窗公開，也會顯示與不同節點大小相關聯的成本。

	> [AZURE.IMPORTANT] 建立叢集後就開始計費，只有在刪除叢集時才會停止計費。如需價格的詳細資訊，請參閱 [HDInsight 價格詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)。


- **HDInsight 使用者**

	HDInsight 叢集可讓您在佈建期間設定兩個使用者帳戶：

	- HTTP 使用者。預設使用者名稱是在 Azure 入口網站上使用基本組態的 admin。
	- RDP 使用者 (Windows 叢集)：用來連線到使用 RDP 的叢集。當您建立帳戶時，必須將到期日設為從今天算起的 90 天內。
	- SSH 使用者 (Linux 叢集)：用來連線到使用 SSH 的叢集。叢集建立之後，您便可以依照[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md) 中的步驟建立其他 SSH 使用者帳戶。



- **Azure 儲存體帳戶**

	原始的 HDFS 會使用叢集上的多個本機磁碟。HDInsight 則會使用 Azure Blob 儲存體來儲存資料。Azure Blob 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。透過 Hadoop 分散式檔案系統 (HDFS) 介面，HDInsight 中的完整元件集可直接處理 Blob 儲存體中的結構化或非結構化資料。將資料儲存在 Blob 儲存體中，您便可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

	在設定期間，您必須指定 Azure 儲存體帳戶，並在該 Azure 儲存體帳戶中指定 Azure Blob 儲存體容器。某些建立程序會要求您事先建立 Azure 儲存體帳戶和 Blob 儲存體容器。叢集會以該 Blob 儲存體容器做為預設儲存位置。您也可以選擇指定叢集可存取的其他 Azure 儲存體帳戶 (連結的儲存體)。此外，叢集也可以存取任何設有完整公用讀取權限或僅限對 blob 之公用讀取權的 Blob 容器。如需限制存取的詳細資訊，請參閱[管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)。

	![HDInsight 儲存體](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Blob 儲存體容器提供一組 Blob 群組，如圖所示：

	![Azure Blob](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] 請不要讓多個叢集共用一個 Blob 儲存體容器。不支援此做法。

	如需使用次要 Blob 存放區的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight](hdinsight-use-blob-storage.md)。

- **Hive/Oozie 中繼存放區**

	中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行。使用中繼存放區能協助您保留 Hive 和 Oozie 中繼資料，因此在建立新叢集時，您便無需重建 Hive 資料表或 Oozie 工作。根據預設，Hive 會使用內嵌的 Azure SQL 資料庫來儲存此資訊。刪除叢集時，嵌入的資料庫將無法保留中繼資料。舉例來說，您使用 Hive 中繼存放區來建立叢集。您已建立一些 Hive 資料表。當您刪除叢集並透過相同的 Hive 中繼存放區重建叢集之後，便可以看到您在原始的叢集中建立的 Hive 資料表。
    
    > [AZURE.NOTE] HBase 叢集類型無法使用中繼存放區組態。

## 使用 HDInsight 叢集自訂功能 (Bootstrap) 來自訂叢集

有時候，您可能需要設定組態檔：

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

叢集無法保留重新製作映像所造成的變更。如需詳細資訊，請參閱[角色執行個體由於作業系統升級而重新啟動](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (英文)。若要在叢集存留期間保留變更，您可以在建立程序期間使用 HDInsight 叢集自訂。我們建議您用這個方法來變更叢集的組態，並讓組態在這些 Azure 重新安裝映像、重新啟動、重新開始事件的過程中，都能保持不變。這些組態變更會在服務啟動之前套用，因此服務不需要重新啟動。

如需相關範例，請參閱[使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。

## 使用指令碼動作來自訂叢集

您可以在建立期間使用指令碼來安裝其他元件或自訂組態。這類指令碼可透過**指令碼動作**來叫用；指令碼動作是可從入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用的組態選項。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。


## 使用 Azure 虛擬網路

[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

如需有關搭配虛擬網路 (包含虛擬網路特定設定需求) 使用 HDInsight 的資訊，請參閱[使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

## 叢集建立方法

在本文中，您已了解建立 Windows 型 HDInsight 叢集的基本資訊。請利用下表，尋找如何使用最符合需求的方法建立叢集的具體資訊：

| 使用此選項可建立叢集... | 使用網頁瀏覽器... | 使用命令列 | 使用 REST API | 使用 SDK | 從 Linux、Mac OS X 或 Unix | 從 Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure 入口網站](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [ARM 範本](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0128_2016-->