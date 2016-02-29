<properties
   pageTitle="在 HDInsight 中佈建 Apache Spark 叢集 | Microsoft Azure"
   description="了解如何使用 Azure 入口網站、Azure PowerShell、命令列或 HDInsight .NET SDK 佈建 Azure HDInsight 的 Spark 叢集。"
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
    ms.date="12/08/2015"
    ms.author="nitinme"/>

# 使用自訂選項在 HDInsight 中建立 Apache Spark 叢集 (Windows)

> [AZURE.NOTE] HDInsight 現在於 Linux 上提供 Spark 叢集。如需如何在 HDInsight Linux 上自訂/建立 Spark 叢集的資訊，請參閱[在 HDInsight 中建立 Linux 型叢集](hdinsight-hadoop-provision-linux-clusters.md)。

在大部分的情況下，您可以使用[開始使用 HDInsight 上的 Apache Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) 所述的快速建立方法建立 Spark 叢集。在某些情況下，您可能想要建立自訂叢集。比方說，您可能會想要連接外部中繼資料存放區，讓 Hive 中繼資料的持續性超越叢集存留期，或是可能會想要使用額外的儲存體來搭配叢集。

對於這類案例和其他案例，本文章提供如何使用 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，在 HDInsight 上建立自訂 Spark 叢集的指示。


**必要條件：**

開始執行本文中的指示之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

##<a id="configuration"></a>有哪些相異的組態選項？

### 其他儲存體

在設定期間，您必須指定 Azure Blob 儲存體帳戶和預設容器。叢集以此做為預設儲存位置。您可以選擇性地指定將與叢集相關聯的其他 Azure 儲存體帳戶。

>[AZURE.NOTE] 請不要讓多個叢集共用一個 Blob 儲存體容器。不支援此做法。

如需使用次要 Blob 存放區的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight](hdinsight-hadoop-use-blob-storage.md)。

### Metastore

Spark 可讓您透過原始資料定義結構描述和 Hive 資料表。您可以將這些結構描述和資料表中繼資料儲存在外部 metastore。使用中繼存放區有助於保留 Hive 中繼資料，因此在建立新叢集時，您便無需重建 Hive 資料表。依預設，Hive 會使用內嵌資料庫來儲存此資訊。刪除叢集時，嵌入的資料庫將無法保留中繼資料。

如需有關如何在 Azure 中建立 SQL Database 的指示，請參閱[建立您的第一個 Azure SQL Database](../sql-database/sql-database-get-started.md)。

### 叢集自訂

您可以在建立期間使用指令碼來安裝其他元件或自訂組態。這類指令碼可透過**指令碼動作**叫用，指令碼動作是一個組態選項，其可從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集][hdinsight-customize-cluster]。


### 虛擬網路

[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)可讓您建立安全、持續的網路，其包含解決方案所需的資源。虛擬網路可讓您：

* 在私人網路中將雲端資源連接在一起 (僅限雲端)。

	![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* 使用虛擬私人網路 (VPN) 將雲端資源連接到本機資料中心網路 (站對站，或點對站)。

	站對站組態可讓您使用硬體 VPN 或路由及遠端存取服務，從資料中心將多項資源連接至 Azure 虛擬網路。

	![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	點對站組態可讓您使用軟體 VPN，將特定資源連接到 Azure 虛擬網路。

	![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

如需搭配虛擬網路 (包含虛擬網路特定設定需求) 使用 HDInsight 的詳細資訊，請參閱[使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

##<a id="portal"></a>使用 Azure Preview 入口網站

HDInsight 上的 Spark 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。相同的資料中心上必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。如需建立 Azure 儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶][azure-create-storageaccount]。

**使用自訂建立選項建立 HDInsight 叢集**

1. 登入 [Azure 預覽入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[資料分析] 及 [HDInsight]。

    ![在 Azure Preview 入口網站中建立新的叢集](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.1.png "在 Azure Preview 入口網站中建立新的叢集")

3. 輸入 [叢集名稱]，再為 [叢集類型] 選取 [Spark]，然後從 [叢集作業系統] 下拉式清單中選取 [Windows Server 2012 R2 資料中心]。如果該叢集可用，其名稱旁會出現綠色核取記號。

	![輸入叢集名稱和類型](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.2.png "輸入叢集名稱和類型")

4. 如果您有多個訂用帳戶，可按一下 [訂用帳戶] 項目，以選取將用於該叢集的 Azure 訂用帳戶。

5. 按一下 [資源群組] 來查看現有資源群組的清單，然後選取其中一個來建立叢集。或者按一下 [建立新項目]，然後輸入新資源群組的名稱。出現綠色核取記號即表示新群組的名稱可用。

	> [AZURE.NOTE] 如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。

6. 按一下 [認證]，然後輸入 [叢集登入使用者名稱] 和 [叢集登入密碼]。若您想要啟用叢集節點上的遠端桌面功能，請為 [啟用遠端桌面] 按一下 [是]。如果叢集的遠端桌面存取過期，請選取日期並提供遠端桌面使用者的使用者名稱/密碼。按一下底部的 [選取] 以儲存認證組態。

	![提供叢集認證](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.3.png "提供叢集認證")

7. 按一下 [資料來源] 為該叢集選擇現有資料來源，或是建立新的資料來源。

	![資料來源刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.4.png "提供資料來源組態")

	目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。使用下列資訊來了解 [資料來源] 刀鋒視窗上的項目。

	- **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。

	- **選取儲存體帳戶 / 建立新的帳戶**：按一下 [選取儲存體帳戶]，瀏覽並選取您要與叢集產生關聯的現有儲存體帳戶。或者按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。

	- **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

	- **位置**：儲存體帳戶所在或將建立的地理區域。

		> [AZURE.IMPORTANT] 選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。

	按一下 [選取] 以儲存資料來源組態。

8. 按一下 [節點定價層]，來顯示將針對此叢集建立的節點相關資訊。設定該叢集所需的背景工作角色節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。

	![節點定價層刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.5.png "指定叢集節點的數目")

	按一下 [選取] 以儲存節點價格組態。

9. 按一下 [選擇性組態] 來選取叢集版本，以及設定其他選擇性設定，例如，新增 [虛擬網路]、設定 [外部中繼存放區] 來保存 Hive 和 Oozie 的資料、使用 [指令碼動作] 來自訂要安裝自訂元件的叢集，或是針對叢集使用其他儲存體帳戶。

	* 按一下 [HDInsight 版本] 下拉式清單，然後選取您想要用於該叢集的版本。如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

	* 按一下[虛擬網路] 提供設定詳細資料，以將叢集設定為虛擬網路的一部分。在 [虛擬網路] 刀鋒視窗中按一下 [虛擬網路]，然後按一下所要使用的網路。同樣地，選取該網路的 [子網路]，然後按一下 [選取]，以儲存虛擬網路設定。

		![虛擬網路刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.6.png "指定虛擬網路詳細資料")

	* 按一下 [外部中繼存放區] 來指定您想要用來儲存與叢集相關聯之 Hive 和 Oozie 中繼資料的 SQL 資料庫。

		![自訂中繼存放區刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.7.png "指定外部中繼存放區")

		針對 [使用 Hive 現有的 SQL DB] 中繼資料按一下 [是]、選取 SQL 資料庫，然後提供該資料庫的使用者名稱/密碼。如果您想要**使用 Oozie 中繼資料現有的 SQL DB**，請重複執行這些步驟。按一下 [選取]，直到您回到 [選擇性組態] 刀鋒視窗。

		>[AZURE.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。

	* 若要在建立叢集時，使用自訂指令碼自訂叢集，請按一下 [指令碼動作]。如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。

		![指令碼動作刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.8.png "指定指令碼動作")

		按一下 [選取] 儲存指令碼動作的設定變更。

	* 按一下 [Azure 儲存體金鑰] 來指定與該叢集相關聯的其他儲存體帳戶。在 [Azure 儲存體金鑰] 刀鋒視窗中，按一下 [加入儲存體金鑰]，然後選取現有的儲存體帳戶或建立新的帳戶。

		![其他儲存體刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.9.png "指定其他儲存體帳戶")

		按一下 [選取]，直到您回到 [新的 HDInsight 叢集] 刀鋒視窗。

10. 在 [新的 HDInsight 叢集] 刀鋒視窗中，確認已選取 [釘選到「開始面板」]，然後按一下 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示會顯示叢集正在建立中，並會在叢集建立完成時變更為 HDInsight 圖示。

	| 建立時 | 建立完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的建立指示器](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![已建立叢集磚](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE] 建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或頁面左邊的 [通知] 項目來以檢查建立進度。

11. 建立完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

	![叢集刀鋒視窗](./media/hdinsight-apache-spark-provision-clusters/hdispark.cluster.blade.png "叢集屬性")

	使用下列資訊，了解在刀鋒視窗頂端以及 [程式集] 和 [快速連結] 區段中的圖示：

	* **設定**和**所有設定**：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。

	* **儀表板**和 **URL**：所有可以用於存取叢集儀表板 (亦即對叢集執行作業的 Web 入口網站) 的方法盡在其中。

	* **遠端桌面**：可讓您在叢集節點上啟用/停用遠端桌面。

	* **調整叢集**：可讓您變更此叢集的背景工作節點數目。

	* **刪除**：刪除 HDInsight 叢集。

	* **快速入門** (![雲和雷電圖示 = 快速入門](./media/hdinsight-apache-spark-provision-clusters/quickstart.png))：顯示可協助您開始使用 HDInsight 的資訊。

	* **使用者** (![使用者圖示](./media/hdinsight-apache-spark-provision-clusters/users.png))：可讓您設定 Azure 訂用帳戶上其他使用者對此叢集的「入口網站管理」權限。

		> [AZURE.IMPORTANT] 這「只」會影響在 Azure Preview 入口網站對此叢集的存取和權限，對於連線至 HDInsight 叢集或將作業提交至其上的使用者則沒有作用。

	* **標記** (![標記圖示](./media/hdinsight-apache-spark-provision-clusters/tags.png))：標記可讓您設定索引鍵/值組，以定義雲端服務的自訂分類。例如，您可以建立名為 __project__ 的索引鍵，然後使用與特定專案相關聯之所有服務的通用值。

	* **叢集儀表板**：啟動叢集儀表板刀鋒視窗，以從中啟動叢集儀表板本身，或啟動 Zeppelin 和 Jupyter Notebook。


##<a id="powershell"></a>使用 Azure PowerShell

請參閱[建立 HDInsight 叢集](hdinsight-provision-clusters.md#create-using-azure-powershell)。

在 New-AzureRmHDInsightCluster Cmdlet 中指定 Spark 叢集類型轉換：

	-ClusterType Spark


## 使用搭載 ARM 的 HDInsight .NET SDK

請參閱[建立 HDInsight 叢集](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk)。

指定 Spark 叢集類型：

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


##<a id="nextsteps"></a> 後續步驟

* 請參閱[在 HDInsight 中搭配 BI 工具使用 Spark 和執行互動式資料分析](hdinsight-apache-spark-use-bi-tools-v1.md)，了解如何在 HDInsight 中搭配 Power BI 和 Tableau 這類 BI 工具使用 Apache Spark。
* 請參閱[在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)，了解如何在 HDInsight 上使用 Apache Spark 建置機器學習應用程式。
* 請參閱[在 HDInsight 中使用 Spark 建置即時資料流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)，了解如何在 HDInsight 上使用 Apache Spark 建置資料流應用程式。
* 請參閱[在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager-v1.md)，了解如何使用資源管理員來管理配置給 Spark 叢集的資源。


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


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "搭配 HDInsight 使用 Sqoop"

<!---HONumber=AcomDC_0218_2016-->