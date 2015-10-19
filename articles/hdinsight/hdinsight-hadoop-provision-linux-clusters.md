<properties
   	pageTitle="在 HDInsight 中於 Linux 上佈建 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
   	description="了解如何使用管理入口網站、命令列及 .NET SDK，在 HDInsight 的 Linux 上佈建 Hadoop 叢集。"
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
   	ms.date="09/21/2015"
   	ms.author="nitinme"/>


#使用自訂選項在 HDInsight 中佈建 Hadoop Linux 叢集

在本文中，您將了解在 Azure HDInsight 上自訂佈建 Hadoop Linux 叢集的不同方式 - 使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 HDInsight .NET SDK。

## 什麼是 HDInsight 叢集？

您有沒有想過為什麼每次討論 Hadoop 或巨量資料時，都一定會提到叢集？ 那是因為 Hadoop 允許大型資料的分散式處理，散佈到叢集的不同節點中。叢集具有主要/背景工作架構，包含一個主要節點 (亦稱為前端節點或名稱節點) 和任意數目的背景工作節點 (亦稱為資料節點)。如需詳細資訊，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>。

![HDInsight Cluster][img-hdi-cluster]


HDInsight 叢集摘要了 Hadoop 實作詳細資料，因此您無需擔心如何與叢集的不同節點通訊。佈建 HDInsight 叢集時，您可以佈建包含 Hadoop 及相關應用程式的 Azure 運算資源。如需詳細資訊，請參閱 [HDInsight 中 Hadoop 的簡介](hdinsight-hadoop-introduction.md)。要變換的資料儲存在 Azure Blob 儲存體。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。


本文針對佈建叢集的不同方式提供指示。如果您想知道佈建叢集的快速入門方法，請參閱[在 Linux 上開始使用 Azure HDInsight](../hdinsight-hadoop-linux-get-started.md)。

**必要條件**

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **安全殼層 (SSH) 金鑰**。如果您想要使用帶有金鑰的 SSH (而非密碼) 遠端登入到 Linux 叢集。建議使用金鑰的方式，因為它較為安全。如需如何產生 SSH 金鑰的相關指示，請參閱下列文章：
	-  從 Linux 電腦 - [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
	-  從 Windows 電腦 - [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-windows.md)。

## <a id="configuration"></a>組態選項

### 其他儲存體

在設定期間，您必須指定 Azure Blob 儲存體帳戶和預設容器。叢集以此做為預設儲存位置。您可以選擇性地指定也將與叢集相關聯的其他 Blob。


如需有關使用次要 Blob 存放區的詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight](../hdinsight-use-blob-storage.md)。


### Metastore

Metastore 包含 Hive 資料表、資料分割、結構描述和資料行等相關資訊。Hive 會使用這些資訊來找出資料在 Hadoop 分散式檔案系統 (HDFS) 或適用於 HDInsight 的 Azure Blob 儲存體中的位置。依預設，Hive 會使用內嵌資料庫來儲存此資訊。

佈建 HDInsight 叢集時，您可以指定將包含 Hive Metastore 的 SQL Database。當您刪除叢集時，由於中繼資料資訊已儲存在外部的 SQL Database 中，因而得以保留下來。如需在 Azure 建立 SQL Database 的指示，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

### 使用指令碼動作來自訂叢集

您可以在佈建期間使用指令碼來安裝其他元件或自訂組態。這類指令碼會透過 [指令碼動作] 叫用。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

> [AZURE.IMPORTANT]佈建叢集之後，不支援加入其他元件，因為在重新安裝叢集節點的映像之後，便無法使用這些元件。透過指令碼動作安裝的元件會在重新安裝映像程序時進行重新安裝。

### 使用 Azure 虛擬網路

[Azure 虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，上面有您的解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

	![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

	![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png)

如需虛擬網路特性、優點和功能的詳細資訊，請參閱＜[虛擬網路概觀](http://msdn.microsoft.com/library/azure/jj156007.aspx)＞。

> [AZURE.NOTE]您必須先建立 Azure 虛擬網路，才能佈建叢集。如需詳細資訊，請參閱[如何建立虛擬網路](virtual-networks-create-vnet.md)。
>
> Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。使用 Azure PowerShell Cmdlet Get-AzureVNetConfig 來檢查現有的 Azure 虛擬網路是否以位置為基礎。如果您的虛擬網路並非以位置為基礎，您會有下列選項：
>
> - 將現有的虛擬網路組態匯出，然後建立新的虛擬網路。根據預設，所有新的虛擬網路都是以位置為基礎。
> - 移轉到以位置為基礎的虛擬網路。請參閱[將現有服務移轉到區域範圍](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)。
>
> 強烈建議您一個叢集只指定一個子網路。
>
> 目前 (8/25/2015) 您只能在 Azure 虛擬網路中佈建一個以 Linux 為基礎的叢集。
>
> 您不能在 Linux 的 HDInsight 使用 v1 (傳統) Azure 虛擬網路。虛擬網路必須是 v2 (Azure 資源管理員)，才能在 Azure Preview 入口網站中的 HDInsight 叢集建立程序期間列出來做為選項，或者在以 Azure CLI 或 Azure PowerShell 建立叢集時使用。
>
> 如果您在 v1 網路上擁有資源，而您想要讓 HDInsight 透過虛擬網路直接存取這些資源，請參閱[將傳統 VNet 連線到新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)，了解如何將 v2 虛擬網路連線到 v1 虛擬網路的相關資訊。一旦建立此連線之後，您便可以在 v2 虛擬網路中建立 HDInsight 叢集。

## <a id="options"></a>佈建 HDInsight Linux 叢集的選項

您可以從 Linux 電腦以及 Windows 電腦佈建 HDInsight Hadoop Linux 叢集。下表提供不同作業系統中可用佈建選項的相關資訊，以及各個選項之指示的連結。

從執行此 OS 的電腦佈建 Linux 叢集 | 使用 Azure 入口網站 | 使用 Azure CLI | 使用 Azure PowerShell | 使用 .NET SDK
-----------------| --------| ------------------------| -------------------| ---------- | ---------
Linux| 按一下[這裡](#portal) | 按一下[這裡](#cli) | 不適用 | 不適用
Mac OS X | 按一下[這裡](#portal) | 按一下[這裡](#cli) | 不適用 | 不適用
Windows | 按一下[這裡](#portal) | 按一下[這裡](#cli) | 按一下[這裡](#powershell) | 按一下[這裡](#sdk)

### <a id="portal"></a>使用 Azure 入口網站

HDInsight 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。相同的資料中心內必須要有 Azure 儲存體帳戶，您才能建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)。如需建立 Azure 儲存帳號的詳細資訊，請參閱[如何建立儲存帳號](../storage-create-storage-account.md)。


> [AZURE.NOTE]目前只有**東南亞**、**北歐**、**美國東部**和**美國中南部**等地區可代管 HDInsight Linux 叢集。

**建立 HDInsight 叢集**

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[資料分析] 及 [HDInsight]。

    ![在 Azure Preview 入口網站中建立新的叢集](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "在 Azure Preview 入口網站中建立新的叢集")

3. 輸入 [叢集名稱]，針對 [叢集類型] 選取 [Hadoop]，然後從 [叢集作業系統] 下拉式清單中選取 [Ubuntu]。如果該叢集可用，其名稱旁會出現綠色核取記號。


	> [AZURE.NOTE]若要佈建 HBase 或 Storm 叢集，請在 [叢集類型] 下拉式清單選取適當值。


	![輸入叢集名稱和類型](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "輸入叢集名稱和類型")

4. 如果您有多個訂用帳戶，請按一下 [訂用帳戶] 項目，以選取將用於該叢集的 Azure 訂用帳戶。

5. 按一下 [資源群組] 來查看現有資源群組的清單，然後選取其中一個來建立叢集。或者按一下 [建立新項目]，然後輸入新資源群組的名稱。出現綠色核取記號即表示新群組的名稱可用。

	> [AZURE.NOTE]如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。

6. 按一下 [認證]，然後輸入 admin 使用者的密碼。您也必須輸入 [SSH 使用者名稱] 和 [密碼] 或 [公開金鑰]，這會用來驗證 SSH 使用者。建議使用公開金鑰的方法。在底部按一下 [選取] 以儲存認證組態。

	![提供叢集認證](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "提供叢集認證")

	如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)


7. 按一下 [資料來源] 為叢集選擇現有的資料來源，或建立一個新的資料來源。

	![資料來源刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "提供資料來源組態")

	目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。請使用下列資訊來了解 [資料來源] 刀鋒視窗上的項目。

	- **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。

	- **選取儲存體帳戶 / 建立新項目**：按一下 [選取儲存體帳戶] 瀏覽並選取您要與叢集相關聯的現有儲存體帳戶。或按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。

	- **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

	- **位置**：儲存體帳戶所在地或將建立的地理區域。

		> [AZURE.IMPORTANT]選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。

	按一下 [選取] 以儲存資料來源組態。

8. 按一下 [節點定價層] 會顯示將針對此叢集建立之節點的相關資訊。設定該叢集所需的背景工作節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。

	![節點定價層刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "指定叢集節點的數目")

	按一下 [選取] 以儲存此節點定價組態。

9. 按一下 [選擇性組態] 以選取叢集的版本，以及設定其他選擇性設定，例如加入 [虛擬網路]、設定 [外部中繼存放區] 來保存 Hive 和 Oozie 的資料、使用 [指令碼動作] 來自訂要安裝自訂元件的叢集，或對叢集使用其他儲存體帳戶。

	* 按一下 [HDInsight 版本] 下拉式清單，選取您想要用於該叢集的版本。如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。


	* **虛擬網路**：如果您想要將叢集放入虛擬網路，請選取 Azure 虛擬網路和子網路。

		![虛擬網路刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.6.png "指定虛擬網路詳細資料")

    	>[AZURE.NOTE]以 Windows 為主的 HDInsight 叢集只能放置到傳統的虛擬網路。


	* 按一下 [外部中繼存放區] 來指定您想要用來儲存與叢集相關聯之 Hive 和 Oozie 中繼資料的 SQL 資料庫。

		![自訂中繼存放區刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "指定外部中繼存放區")

		在 [為 Hive 使用現有的 SQL DB] 中繼資料按一下 [是]，選取 SQL 資料庫，然後提供該資料庫的使用者名稱/密碼。如果您想要**為 Oozie 中繼資料使用現有的 SQL DB**，請重複這些步驟。按一下 [選取]，直到您回到 [選擇性組態] 刀鋒視窗。

		>[AZURE.NOTE]用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。


	* **指令碼動作** 如果您想要在該叢集建立時使用自訂指令碼來自訂叢集。如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。

		![指令碼動作刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.8.png "指定指令碼動作")


	* 按一下 [Azure 儲存體金鑰] 來指定與該叢集相關聯的其他儲存體帳戶。在 [Azure 儲存體金鑰] 刀鋒視窗中，按一下 [加入儲存體金鑰]，然後選取現有的儲存體帳戶或建立新的帳戶。

		![其他儲存體刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "指定其他儲存體帳戶")

		按一下 [選取]，直到您回到 [新的 HDInsight 叢集] 刀鋒視窗。

10. 在 [新的 HDInsight 叢集] 刀鋒視窗中，確認已選取 [釘選到「開始面板」]，然後按一下 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

	| 佈建期間 | 佈建完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的佈建指示器](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。使用「開始面板」上的磚或頁面左邊的 [通知] 項目，檢查佈建程序。

11. 佈建完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

	![叢集刀鋒視窗](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "叢集屬性")

	使用下列資訊，了解此刀鋒視窗頂端和 [基本功能] 區段中的圖示：

	* **設定**和**所有設定**：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。

	* **儀表板**、**叢集儀表板**和 **URL**：這些是存取叢集儀表板 (可在叢集上執行工作的 Web 入口網站) 的所有方法。

	* **安全殼層**：使用 SSH 存取叢集所需的資訊。

	* **刪除**：刪除 HDInsight 叢集。

	* **快速入門** (![雲和雷電圖示 = 快速入門](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png))：顯示可協助您開始使用 HDInsight 的資訊。

	* **使用者** (![使用者圖示](./media/hdinsight-hadoop-provision-linux-clusters/users.png))：可讓您設定 Azure 訂用帳戶上其他使用者對此叢集的「入口網站管理」權限。

		> [AZURE.IMPORTANT]這「只」會影響在 Azure Preview 入口網站對此叢集的存取和權限，對於連線至 HDInsight 叢集或將工作提交至其上的使用者則沒有作用。

	* **標記** (![標記圖示](./media/hdinsight-hadoop-provision-linux-clusters/tags.png))：標記可讓您設定索引鍵/值組，以定義雲端服務的自訂分類。例如，您可能會建立名為 __project__ 的索引鍵，然後使用與特定專案相關聯之所有服務的通用值。

### <a id="cli"></a>使用 Azure CLI

Azure CLI 是可讓您管理 Azure 服務的跨平台命令列公用工具。它可搭配 Azure 資源管理範本用來佈建 HDInsight 叢集，以及相關聯的儲存體帳戶和其他服務。

Azure 資源管理範本是描述__資源群組__與其中所有資源 (例如 HDInsight) 的 JSON 文件。 這個以範本為基礎的方法可讓您定義一個範本中 HDInsight 所需的所有資源，並透過可將變更套用至群組的__部署__整體管理群組的變更。

下列步驟將逐步完成使用 Azure CLI 和範本建立新 HDInsight 叢集的程序︰

1. 如果尚未安裝，請依照[安裝和設定 Azure CLI](../xplat-cli-install.md) 文件中的步驟執行。

2. 從命令列、終端機或殼層，使用下列命令來驗證您的 Azure 訂用帳戶：

        azure login

    系統會提示您提供使用者名稱與密碼。如果您有多個 Azure 訂用帳戶，則可以使用 `azure account set <subscriptionname>` 設定 Azure CLI 命令將使用的訂用帳戶。

3. 使用下列命令來切換至 Azure 資源管理員模式︰

        azure config mode arm

4. 建立 HDInsight 叢集的範本。以下是一些基本範例範本：

    * [以 Linux 為基礎的叢集 (使用 SSH 公開金鑰)](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpublickey)
    * [以 Linux 為基礎的叢集 (使用 SSH 帳戶的密碼)](https://github.com/matt1883/azure-quickstart-templates/tree/master/hdinsight-linux-sshpassword)

    這兩個範本也會建立 HDInsight 所使用的預設 Azure 儲存體帳戶。

    您需要的檔案是 __azuredeploy.json__ 和 __azuredeploy.parameters.json__。

5. 在編輯器中開啟 __azuredeploy.parameters.json__ 檔案，並在 `parameters` 區段中提供項目的值：

    * __location__︰將在其中建立資源的資料中心。您可以檢視 __azuredeploy.json__ 檔案中的 `location` 區段，以取得允許的位置清單。
    * __clusterName__︰HDInsight 叢集的名稱。這個名稱必須是唯一的，否則部署將會失敗。
    * __clusterStorageAccountName__︰將會針對 HDInsight 叢集建立的 Azure 儲存體帳戶名稱。這個名稱必須是唯一的，否則部署將會失敗。
    * __clusterLoginPassword__︰叢集 admin 使用者的密碼。這應該是安全的密碼，因為它是用來存取網站和叢集上的 REST 服務。
    * __sshUserName__︰要為此叢集建立的第一個 SSH 使用者名稱。SSH 將利用此帳戶用來從遠端存取此叢集。此名稱必須是唯一的，不能是叢集上使用中的帳戶名稱。無效的名稱，包含 root, storm 以及 hbase。
    * __sshPublicKey__︰如果您使用的範本需要 SSH 公開金鑰，您就必須在下面這行上新增您的公開金鑰。如需產生和使用公開金鑰的詳細資訊，請參閱下列文章：

        * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__︰如果您使用的範本需要 SSH 密碼，您就必須在下面這行上新增密碼。

    完成後，請儲存並關閉檔案。

5. 使用下列來建立空的資源群組。以您要用於此群組的名稱取代 __RESOURCEGROUPNAME__。以應建立群組的資料中心取代 __LOCATION__︰

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]如果位置名稱包含空格，請將它放在雙引號中。例如 "South Central US"。

6. 使用下列命令來建立此資源群組的初始部署。以 __azuredeploy.json__ 範本檔案的路徑取代 __PATHTOTEMPLATE__。以 __azuredeploy.parameters.json__ 檔案的路徑取代 __PATHTOPARAMETERSFILE__。以您在上一個步驟中建立的群組的名稱取代 __RESOURCEGROUPNAME__︰

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    一旦接受部署，您應該會看到類似 `group deployment create command ok` 的訊息。

7. 可能需要一些時間 (大約 15 分鐘) 才能完成部署。您可以使用下列命令檢視有關部署的資訊。以上一個步驟中使用的資源群組名稱取代 __RESOURCEGROUPNAME__︰

        azure group log show -l RESOURCEGROUPNAME

    如果在部署期間發生失敗，您可以使用下列命令取得更多有關失敗的資訊

        azure group log show -l -v RESOURCEGROUPNAME

### <a id="powershell"></a>使用 Azure PowerShell

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。本節提供如何使用 Azure PowerShell 佈建 HDInsight 叢集的指示。如需設定工作站以執行 HDInsight Windows Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/dn858087.aspx)。


以下是使用 Azure PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 資源群組
- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

佈建 Linux 叢集時所必須設定的兩個最重要的參數，是用來指定 OS 類型和 SSH 使用者詳細資料的參數：

- 請務必將 **-OSType** 參數指定為 **Linux**。
- 若要對叢集上的遠端工作階段使用 SSH，您可以指定 SSH 使用者密碼或 SSH 公開金鑰。如果您同時指定 SSH 使用者密碼或 SSH 公開金鑰，系統會忽略公開金鑰。如果您想要對遠端工作階段使用 SSH 金鑰，您必須在出現密碼提示時指定空白的 SSH 密碼。如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)


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
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]您對 **$clusterCredentials** 指定的值會用來建立叢集的 Hadoop 使用者帳戶。您將使用此帳戶來連線到叢集。您對 **$sshCredentials** 指定的值會用來建立叢集的 SSH 使用者。您會使用此帳戶在叢集上啟動遠端 SSH 工作階段並執行工作。如果從 Azure 入口網站使用 [快速建立] 選項佈建叢集，則預設 Hadoop 使用者名稱是 "admin"，預設 SSH 使用者名稱是 "hdiuser"。

叢集佈建可能需要幾分鐘的時間才會完成。

![HDI.CLI.Provision][image-hdi-ps-provision]



###<a id="sdk"></a>使用 HDInsight .NET SDK
HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您能夠輕鬆地從 .NET Framework 應用程式使用 HDInsight。請遵循下列指示建立 Visual Studio 主控台應用程式，並貼上建立叢集的程式碼。

**建立 C# Visual Studio 主控台應用程式**

1. 開啟 Visual Studio 2013 或 2015。
2. 使用下列設定建立新的 Visual Studio 專案

	|屬性|值|
	|--------|-----|
	|範本|Templates/Visual C#/Windows/Console Application|
	|名稱|CreateHDICluster|

5. 在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。
6. 在主控台中執行下列命令，以安裝套件：

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	這些命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

6. 在 [方案總管] 中按兩下 **Program.cs** 予以開啟，貼上下列程式碼，並提供變數的值：

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const string NewClusterVersion = "3.2";
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;

		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";

		        private const string NewClusterSshUserName = "sshuser";
		        private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
					Comment: ""rsa-key-20150731""
					AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
					gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
					yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
					WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
					pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
					zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
					---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. 取代類別成員值。

7. 按 **F5** 鍵執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。系統也會提示您輸入 Azure 帳戶認證。建立 HDInsight 叢集可能需要幾分鐘的時間。



## 後續步驟
在本文中，您已學到幾種方式可以在 Linux 上佈建 HDInsight Hadoop 叢集。若要深入了解，請參閱下列文章：

- [在 Linux 上使用 HDInsight](hdinsight-hadoop-linux-information.md)：了解在 Linux 上使用 HDInsight 叢集的細微差異。
- [使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)：了解如何使用 Ambari Web 或 Ambari REST API 在 HDInsight 叢集上監視及管理以 Linux 為基礎的 Hadoop。
- [搭配 HDInsight 使用 MapReduce](hdinsight-use-mapreduce.md)：了解在叢集上執行 MapReduce 工作的不同方法。
- [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)：了解在叢集上執行 Hive 查詢的不同方法。
- [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)：了解在叢集上執行 Pig 工作的不同方法。
- [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage.md)：了解 HDInsight 如何使用 Azure Blob 儲存體來儲存 HDInsight 叢集的資料。
- [將資料上傳到 HDInsight](hdinsight-upload-data.md)：了解如何使用 HDInsight 叢集的 Azure Blob 儲存體中儲存的資料。


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

<!---HONumber=Oct15_HO2-->