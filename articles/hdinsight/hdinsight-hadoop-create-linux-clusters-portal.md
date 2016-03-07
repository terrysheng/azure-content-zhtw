<properties
   	pageTitle="在 Linux 上使用入口網站在 HDInsight 中建立 Hadoop、HBase、Storm 或 Spark 叢集 | Microsoft Azure"
   	description="了解如何在 Linux 上使用網頁瀏覽器及 Azure Preview 入口網站，為 HDInsight 建立 Hadoop、HBase、Storm 或 Spark 叢集。"
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


#使用 Azure Preview 入口網站在 HDInsight 上建立 Linux 架構的叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-selector-create-clusters.md)]

Azure Preview 入口網站是 Web 架構的管理工具，可用來管理裝載於 Microsoft Azure 雲端中的服務和資源。利用本文件中的資訊，使用 Preview 入口網站和您的 Web 瀏覽器來建立 Linux 架構的新 HDInsight 叢集

##必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __現代 Web 瀏覽器__。Azure Preview 入口網站會使用 HTML5 和 Javascript，而且可能無法在舊版 Web 瀏覽器中正確運作。

##建立叢集

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。

2. 依序按一下 [新增]、[資料分析] 及 [HDInsight]。

    ![在 Azure Preview 入口網站中建立新的叢集](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "在 Azure Preview 入口網站中建立新的叢集")

3. 輸入 [叢集名稱]，針對 [叢集類型] 選取 [Hadoop]，然後從 [叢集作業系統] 下拉式清單中選取 [Ubuntu]。如果該叢集可用，其名稱旁會出現綠色核取記號。

    > [AZURE.NOTE] 若要佈建 HBase 或 Storm 叢集，請針對 [叢集類型] 下拉式清單選取適當值。

    ![輸入叢集名稱和類型](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "輸入叢集名稱和類型")

4. 如果您有多個訂用帳戶，可按一下 [訂用帳戶] 項目，以選取將用於該叢集的 Azure 訂用帳戶。

5. 按一下 [資源群組] 來查看現有資源群組的清單，然後選取其中一個來建立叢集。或者按一下 [建立新項目]，然後輸入新資源群組的名稱。出現綠色核取記號即表示新群組的名稱可用。

	> [AZURE.NOTE] 如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。

6. 按一下 [認證]，然後輸入 admin 使用者的密碼。您也必須輸入 [SSH 使用者名稱] 以及 [密碼] 或 [公開金鑰]，這將會用來驗證 SSH 使用者。建議使用公開金鑰的方法。按一下底部的 [選取] 以儲存認證組態。

	![提供叢集認證](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "提供叢集認證")

	如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)


7. 按一下 [資料來源] 來選擇該叢集的現有資料來源，或建立一個新的資料來源。

	![資料來源刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "提供資料來源組態")

	目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。使用下列資訊來了解 [資料來源] 刀鋒視窗上的項目。

	- **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。

	- **選取儲存體帳戶 / 建立新的帳戶**：按一下 [選取儲存體帳戶]，瀏覽並選取您要與叢集產生關聯的現有儲存體帳戶。或者按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。

	- **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

	- **位置**：儲存體帳戶所在或將建立的地理區域。

		> [AZURE.IMPORTANT] 選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。

	按一下 [選取] 以儲存資料來源組態。

8. 按一下 [節點定價層]，來顯示將針對此叢集建立的節點相關資訊。設定該叢集所需的背景工作角色節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。

	![節點定價層刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "指定叢集節點的數目")
    
    > [AZURE.IMPORTANT] 如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。
    >
    > 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

	按一下 [選取] 以儲存節點定價組態。

9. 按一下 [選擇性組態] 來選取叢集版本，以及設定其他選擇性設定，例如，新增 [虛擬網路]、設定 [外部中繼存放區] 來保存 Hive 和 Oozie 的資料、使用 [指令碼動作] 來自訂要安裝自訂元件的叢集，或是針對叢集使用其他儲存體帳戶。

	* 按一下 [HDInsight 版本] 下拉式清單，然後選取您想要用於該叢集的版本。如需詳細資訊，請參閱 [HDInsight 叢集版本](hdinsight-component-versioning.md)。

	* **虛擬網路**：如果您想要將叢集放入虛擬網路，請選取 Azure 虛擬網路和子網路。

		![虛擬網路刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "指定虛擬網路詳細資料")

    	如需搭配虛擬網路 (包含虛擬網路特定設定需求) 使用 HDInsight 的詳細資訊，請參閱[使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

	* 按一下 [外部中繼存放區] 來指定您想要用來儲存與叢集相關聯之 Hive 和 Oozie 中繼資料的 SQL 資料庫。
    
        > [AZURE.NOTE] HBase 叢集類型無法使用中繼存放區組態。

		![自訂中繼存放區刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "指定外部中繼存放區")

		針對 [使用 Hive 現有的 SQL DB] 中繼資料按一下 [是]、選取 SQL 資料庫，然後提供該資料庫的使用者名稱/密碼。如果您想要**使用 Oozie 中繼資料現有的 SQL DB**，請重複執行這些步驟。按一下 [選取]，直到您回到 [選擇性組態] 刀鋒視窗。

		>[AZURE.NOTE] 用於 metastore 的 Azure SQL Database 必須能夠連線至其他 Azure 服務 (包括 Azure HDInsight)。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。

	* **指令碼動作**：如果您想要在叢集建立時使用自訂指令碼來自訂該叢集。如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。

		![指令碼動作刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "指定指令碼動作")

	* 按一下 [Azure 儲存體金鑰] 來指定與該叢集相關聯的其他儲存體帳戶。在 [Azure 儲存體金鑰] 刀鋒視窗中，按一下 [加入儲存體金鑰]，然後選取現有的儲存體帳戶或建立新的帳戶。

		![其他儲存體刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "指定其他儲存體帳戶")

		按一下 [選取]，直到您回到 [新的 HDInsight 叢集] 刀鋒視窗。

10. 在 [新的 HDInsight 叢集] 刀鋒視窗中，確認已選取 [釘選到「開始面板」]，然後按一下 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

	| 佈建期間 | 佈建完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的佈建指示器](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] 建立叢集需要一些時間，通常約 15 分鐘左右。使用 [「開始面板」] 上的磚，或頁面左邊的 [通知] 項目，以檢查佈建進度。

11. 佈建完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。

	![叢集刀鋒視窗](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "叢集屬性")

	使用下列步驟來了解在這個刀鋒視窗頂端和 [基本功能] 區段中的圖示：

	* **設定**和**所有設定**：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。

	* **儀表板**、**叢集儀表板**和 **URL**：這些是存取叢集儀表板 (也就是可在叢集上執行作業的 Web 入口網站) 的所有方法。

	* **安全殼層**：使用 SSH 存取叢集所需的資訊。

	* **刪除**：刪除 HDInsight 叢集。

	* **快速入門** (![雲和雷電圖示 = 快速入門](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png))：顯示可協助您開始使用 HDInsight 的資訊。

	* **使用者** (![使用者圖示](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png))：可讓您設定 Azure 訂用帳戶上其他使用者對此叢集的「入口網站管理」權限。

		> [AZURE.IMPORTANT] 這「只」會影響在 Azure Preview 入口網站對此叢集的存取和權限，對於連線至 HDInsight 叢集或將作業提交至其上的使用者則沒有作用。

	* **標記** (![標記圖示](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png))：標記可讓您設定索引鍵/值組，以定義雲端服務的自訂分類。例如，您可建立名為 __project__ 的索引鍵，然後使用與特定專案相關聯之所有服務的通用值。

##自訂叢集

- 請參閱[使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。
- 請參閱[使用指令碼動作自訂 Windows 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。

##後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集：

###Hadoop 叢集

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

###HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux)

###Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

###Spark 叢集

* [使用 Scala 來建立獨立的應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行工作](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark 和 BI：搭配 BI 工具來使用 HDInsight 中的 Spark 以執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習：使用 HDInsight 中的 Spark 來預測食物檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0224_2016-->