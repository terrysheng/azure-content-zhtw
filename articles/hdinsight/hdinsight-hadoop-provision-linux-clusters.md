<properties
   	pageTitle="在 HDInsight 中於 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
   	description="了解如何在適用於 HDInsight 的 Linux 上，使用網頁瀏覽器、Azure CLI、Azure PowerShell、REST 或透過 SDK，建立 Hadoop、HBase 或 Storm 叢集。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/23/2015"
   	ms.author="nitinme"/>


#在 HDInsight 中建立以 Linux 為基礎的叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文件中，您將了解在 Azure 上建立以 Linux 為基礎的 HDInsight 叢集的不同方式，以及可用於叢集的選擇性組態。HDInsight 提供 Apache Hadoop、Apache Storm 和 Apache HBase 做為 Azure 雲端平台上的服務。

> [AZURE.NOTE]本文提供以不同方式建立叢集的指示。如果您在尋找建立叢集的快速入門方法，請參閱[開始在 Linux 上使用 Azure HDInsight](../hdinsight-hadoop-linux-get-started.md)。

## 什麼是 HDInsight 叢集？

Hadoop 叢集由數個虛擬機器 (節點) 組成，可用於分散處理叢集上的作業。Azure 將個別節點安裝和設定的實作細節抽象化，您只需要提供一般組態資訊即可。

###叢集類型

有數種 HDInsight 可用：

| 叢集類型 | 如果您需要...，請使用此類型 |
| ------------ | ----------------------------- |
| Hadoop | 查詢和分析 (批次工作) |
| HBase | NoSQL 資料儲存體 |
| Storm | 即時事件處理 |
| Spark (Windows 專用預覽) | 記憶體內處理、互動式查詢、微批次串流處理 |

在設定期間，您將選取其中一種叢集類型。您可以使用[指令碼動作](#scriptaction)，將其他技術 (例如 Hue、Spark 或 R) 加入至這些基本類型。

每個叢集類型對於叢集內的節點，都有它自己術語，而每個節點類型也有各自的節點數目和預設 VM 大小：

> [AZURE.IMPORTANT]如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。

![HDInsight Hadoop 叢集節點](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

HDInsight 的 Hadoop 叢集有兩種節點：

- 前端節點 (2 個節點)
- 資料節點 (至少 1 個節點)

![HDInsight HBase 叢集節點](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

HDInsight 的 HBase 叢集有三種節點：- 前端伺服器 (2 個節點) - 區域伺服器 (至少 1 個節點) - 主要/Zookeeper 節點 (3 個節點)

![HDInsight Storm 叢集節點](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

HDInsight 的 Storm 叢集有三種節點：- Nimbus 節點 (2 個節點) - 監督員伺服器 (至少 1 個節點) - Zookeeper 節點 (3 個節點)


![HDInsight Spark 叢集節點](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

HDInsight 的 Spark 叢集有三種節點：- 前端節點 (2 個節點) - 背景工作節點 (至少 1 個節點) - Zookeeper 節點 (3 個節點) (A1 Zookeeper VM大小免費)

###HDInsight 的 Azure 儲存體

每個叢集類型有一個或多個 Azure 儲存體帳戶與叢集相關聯。HDInsight 使用這些儲存體帳戶中的 Azure blob 做為叢集的資料存放區。隔開資料和叢集可讓您刪除不在使用中的叢集，但仍可保留您的資料。如有需要，您可以將相同的儲存體帳戶用於新的叢集來執行更多分析。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。

## <a id="configuration"></a>基本組態選項

下列各節說明建立 HDInsight 叢集時可用的必要組態選項。

###叢集名稱

叢集名稱為叢集提供唯一識別碼，在透過網際網路存取叢集時也做為網域名稱的一部分。例如，名為 _mycluster_ 的叢集在網際網路上會成為 _mycluster_.azurehdinsight.net。

叢集名稱必須遵循下列方針：

- 欄位必須是包含 3 到 63 個字元的字串。
- 欄位只能包含字母、數字和連字號。

###叢集類型

叢集類型可讓您為叢集選取特殊用途的組態。以下是以 Linux 為基礎的 HDInsight 可用的類型：

| 叢集類型 | 如果您需要...，請使用此類型 |
| ------------ | ----------------------------- |
| Hadoop | 查詢和分析 (批次工作) |
| HBase | NoSQL 資料儲存體 |
| Storm | 即時事件處理 |
| Spark (Windows 專用預覽) | 記憶體內處理、互動式查詢、微批次串流處理 |

您可以使用[指令碼動作](#scriptaction)，將其他技術 (例如 Hue、Spark 或 R) 加入這些基本類型。

###叢集作業系統

您可以將 HDInsight 叢集佈建在下列兩個作業系統上：

- **Windows 上的 HDInsight (Windows Server 2012 R2 Datacenter)**：如果您需要整合將於叢集上搭配 Hadoop 執行的 Windows 型服務和技術，或想要從現有的 Windows 型 Hadoop 散發移轉，請選取此選項。

- **Linux 上的 HDInsight (Ubuntu 12.04 LTS for Linux)**：如果您熟悉 Linux 或 Unix、想要從現有的以 Linux 為基礎的 Hadoop 方案移轉，或想要輕鬆整合針對 Linux 所建置的 Hadoop 生態系統元件，請選取此選項。如需詳細資訊，請參閱[開始在 Linux 上的 HDInsight 中使用 Hadoop](hdinsight-hadoop-linux-get-started.md)。

> [AZURE.NOTE]本文件的資訊假設您使用以 Linux 為基礎的 HDInsight 叢集。如需有關以 Windows 為基礎的叢集的資訊，請參閱[在 HDInsight 中建立以 Windows 為基礎的 Hadoop 叢集](hdinsight-provision-clusters.md)。

###訂用帳戶名稱

如果您有多個 Azure 訂用帳戶，請使用此選項來選取您建立 HDInsight 叢集時要使用的訂用帳戶。

###資源群組

應用程式通常由許多元件組成，例如，Web 應用程式、資料庫、資料庫伺服器、儲存體和協力廠商服務。Azure 資源管理員 (ARM) 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。您可以檢視整個群組的彙總成本，為您的組織釐清計費。如需詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。

###認證

有兩種驗證可用於 HDInsight 叢集：

* __系統管理員__或 __HTTPs__ 使用者：叢集的系統管理員帳戶主要用於存取叢集所公開的 Web 或 REST 服務。它不能用來直接登入叢集。

* __SSH 使用者名稱__：SSH 使用者帳戶用於透過[安全殼層](https://en.wikipedia.org/wiki/Secure_Shell)用戶端從遠端存取叢集。這最常用來支援透過遠端命令列存取叢集前端節點。

系統管理員帳戶受密碼保護，使用系統管理員帳戶對叢集進行的所有 Web 通訊，都應該透過安全 HTTPS 連接來執行。

您可以使用密碼或憑證來驗證 SSH 使用者。憑證驗證是最安全的選項，但是需要建立和維護一組公開和私人憑證。

如需有關搭配使用 SSH 與 HDInsight 的詳細資訊，包括如何建立和使用 SSH 金鑰，請參閱下列其中一份文件：

* [Linux、Unix 和 Mac OS X 用戶端 - 搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Windows 用戶端 - 搭配使用 SSH 與 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

###資料來源

HDInsight 使用 Azure Blob 儲存體做為叢集的基礎儲存體。就內部而言，Hadoop 與叢集上的其他軟體會將此儲存體視為 Hadoop 分散式檔案系統 (HDFS) 相容的系統。

建立新的叢集時，您必須建立新的 Azure 儲存體帳戶，或使用現有的帳戶。

> [AZURE.IMPORTANT]您為儲存體帳戶選取的地理位置，也決定 HDInsight 叢集的位置，因為叢集和預設儲存體帳戶必須位於相同的資料中心。
>
> 如需支援的區域清單，請按一下 [HDInsight 價格](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)中的 [區域] 下拉式清單。

####預設儲存體容器

HDInsight 也會在儲存體帳戶上建立_預設儲存體容器_。這是 HDInsight 叢集的預設儲存體。

根據預設，此容器與 HDInsight 叢集的名稱相同。如需有關 HDInsight 和 Azure Blob 儲存體如何一起使用的詳細資訊，請參閱[在 HDInsight 上搭配使用 HDFS 相容的 Azure Blob 儲存體與 Hadoop](hdinsight-hadoop-use-blob-storage.md)。

>[AZURE.WARNING]請不要讓多個叢集共用一個容器。不支援此做法。

###節點大小

您可以選取叢集使用的運算資源大小。例如，如果您知道將會執行需要大量記憶體的作業，您可以選取具有較多記憶體的運算資源。

不同的叢集類型具有不同的節點類型、節點數目和節點大小。例如，Hadoop 叢集類型有兩個_前端節點_和預設的四個_資料節點_，而 Storm 叢集類型有兩個 _Nimbus 節點_、三個 _Zookeeper 節點_和預設的四個_監督員節點_。

> [AZURE.IMPORTANT]如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。

使用 Azure 入口網站設定叢集時，節點大小會透過 [節點定價層] 刀鋒視窗公開，也會顯示與不同節點大小相關聯的成本。

> [AZURE.IMPORTANT]建立叢集後就開始計費，只有在刪除叢集時才會停止計費。如需價格的詳細資訊，請參閱 [HDInsight 價格詳細資料](https://azure.microsoft.com/pricing/details/hdinsight/)。

##<a id="optionalconfiguration"></a>選擇性組態

下列各節說明選擇性組態選項，以及需要使用這些組態的案例。

### HDInsight 版本

請使用此選項決定要用於此叢集的 HDInsight 版本。如需詳細資訊，請參閱 [HDInsight 中的 Hadoop 叢集版本和元件](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)。

### 使用 Azure 虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

    | 站對站組態 | 點對站組態 |
    | -------------------------- | --------------------------- |
    | 站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。<br />![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | 點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。<br />![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

如需虛擬網路特性、優點和功能的詳細資訊，請參閱＜[虛擬網路概觀](http://msdn.microsoft.com/library/azure/jj156007.aspx)＞。

> [AZURE.NOTE]建立叢集之前，必須先建立 Azure 虛擬網路。如需詳細資訊，請參閱[如何建立虛擬網路](virtual-networks-create-vnet.md)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。使用 Azure PowerShell Cmdlet Get-AzureVNetConfig 來檢查現有的 Azure 虛擬網路是否以位置為基礎。如果您的虛擬網路並非以位置為基礎，您會有下列選項：
>
> - 將現有的虛擬網路組態匯出，然後建立新的虛擬網路。根據預設，所有新的虛擬網路都是以位置為基礎。
> - 移轉到以位置為基礎的虛擬網路。請參閱[將現有服務移轉到區域範圍](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/) (英文)。
>
> 強烈建議您一個叢集只指定一個子網路。
>
> 目前 (2015/8/25) 在 Azure 虛擬網路中只能佈建一個以 Linux 為基礎的叢集。
>
> 您不能在 Linux 的 HDInsight 使用 v1 (傳統) Azure 虛擬網路。虛擬網路必須是 v2 (Azure 資源管理員)，才能在 Azure 入口網站中的 HDInsight 叢集建立程序期間列出來做為選項，或者在以 Azure CLI 或 Azure PowerShell 建立叢集時使用。
>
> 如果您在 v1 網路上有資源，而想要讓這些資源可透過虛擬網路直接存取 HDInsight，請參閱[將傳統 VNet 連接到新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)，以取得如何將 v2 虛擬網路連接到 v1 虛擬網路的相關資訊。一旦建立此連線之後，您便可以在 v2 虛擬網路中建立 HDInsight 叢集。

### Metastore

中繼存放區包含 Hive 和 Oozie 中繼資料，例如 Hive 資料表、資料分割、結構描述和資料行的相關資訊。使用中繼存放區有助於保留您的 Hive 和 Oozie 中繼資料，讓您在建立新的叢集時，不需要重建 Hive 資料表或 Oozie 工作。

使用中繼存放區組態選項可讓您指定一個使用 SQL Database 的外部中繼存放區。當您刪除叢集時，由於中繼資料資訊已儲存在外部資料庫中，因而得以保留下來。如需有關如何在 Azure 中建立 SQL Database 的指示，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

> [AZURE.NOTE]HBase 叢集類型無法使用中繼存放區組態。

###<a id="scriptaction"></a>指令碼動作

您可以在叢集佈建期間使用指令碼來安裝其他元件或自訂組態。這類指令碼透過 [指令碼動作] 叫用。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

> [AZURE.IMPORTANT]建立叢集之後，不支援加入其他元件，因為在重新安裝叢集節點的映像之後，便無法使用這些元件。透過指令碼動作安裝的元件會在重新安裝映像程序時進行重新安裝。

### 其他儲存體

在某些情況下，您可能想要將更多儲存體加入至叢集。例如，如果您有多個 Azure 儲存體帳戶在不同的地理區域，或用於不同的服務，但想要全部透過 HDInsight 來分析。

如需有關使用次要 Blob 存放區的詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight](../hdinsight-use-blob-storage.md)。

##<a id="nextsteps"></a><a id="options"></a> 建立方法

在本文中，您已了解建立以 Linux 為基礎的 HDInsight 叢集的基本資訊。請利用下表，尋找如何使用最符合需求的方法建立叢集的具體資訊：

| 使用此選項可建立叢集... | 使用網頁瀏覽器... | 使用命令列 | 使用 REST API | 使用 SDK | 從 Linux、Mac OS X 或 Unix | 從 Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure 入口網站](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |



[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "列出和顯示叢集"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "搭配 HDInsight 使用 Sqoop"

<!---HONumber=AcomDC_1203_2015-->