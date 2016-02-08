<properties
   pageTitle="在 HDInsight 中建立 Hadoop 叢集 | Microsoft Azure"
   	description="了解如何使用 Azure 入口網站建立 Azure HDInsight 的叢集。"
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
   ms.date="01/06/2016"
   ms.author="jgao"/>

# 使用 Azure 入口網站在 HDInsight 中建立 Windows 型 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-create-windows-cluster-selector.md)]

了解如何使用 Azure 入口網站在 HDInsight 中建立 Hadoop 叢集。Microsoft [Azure 入口網站](azure-portal-overview.md)是一個可以佈建和管理 Azure 資源的中央位置。Azure 入口網站是其中一個可用來在 HDInsight 中建立 Linux 型或 Windows 型 Hadoop 叢集的工具。如需其他叢集建立工具和功能的資訊，請按一下此頁面頂端的索引標籤，或參閱[叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

###必要條件：

開始執行本文中的指示之前，您必須擁有以下項目：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## 建立叢集


**建立 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下 [新增]、[資料分析] 及 [HDInsight]。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "在 Azure 入口網站中建立新的叢集")

3. 輸入或選取下列值：

  * **叢集名稱**：請輸入叢集的名稱。如果該叢集名稱可使用，則名稱旁邊將會出現綠色的核取記號。
  * **叢集類型**：請選取 [Hadoop]。其他選項包括 **HBase**、**Storm** 和 **Spark**。
  * **叢集作業系統**：請選取 [Windows]。若要建立 Linux 型叢集，請選取 [Linux]。
  * **版本**：請參閱 [HDInsight 版本](hdinsight-component-versioning.md)。
  * **訂用帳戶**：請選取會用來建立此叢集的 Azure 訂用帳戶。
  * **資源群組**：選取現有資源群組或建立新的群組。如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。
  * **認證**：請設定 Hadoop 使用者 (HTTP 使用者) 的使用者名稱和密碼。如果您啟用該叢集的遠端桌面，您必須設定遠端桌面使用者的使用者名稱、密碼和帳戶到期日。請按一下底部的 [選取] 以儲存變更。

	   	![提供叢集認證](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **資料來源**：請建立新的 Azure 儲存體帳戶或選取現有的帳戶，來做為叢集的預設檔案系統。

   		![資料來源刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。
  		* **選取儲存體帳戶 / 建立新的帳戶**：按一下 [選取儲存體帳戶]，瀏覽並選取您要與叢集產生關聯的現有儲存體帳戶。或者按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。
  		* **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。
  		* **位置**：儲存體帳戶所在或將建立的地理區域。這個位置將會決定叢集位置。該叢集與預設儲存體帳戶必須並存於相同的 Azure 資料中心。
  	
  * **節點定價層**：設定您針對該叢集所需的背景工作角色節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。
  

		![節點定價層刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **選擇性組態**，以便選取叢集版本，以及設定其他選擇性設定 (例如聯結**虛擬網路**、設定**外部中繼存放區**來保存 Hive 和 Oozie 的資料)、使用指令碼動作自訂叢集來安裝自訂元件，或是使用其他有該叢集的儲存體帳戶。

  		* **HDInsight 版本**：選取您想要用於該叢集的版本。如需詳細資訊，請參閱[HDInsight 叢集版本](hdinsight-component-versioning.md).
  		* **虛擬網路**：如果您想要將叢集放入虛擬網路，請選取 Azure 虛擬網路和子網路。  

			![虛擬網路刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			如需搭配虛擬網路 (包含虛擬網路特定設定需求) 使用 HDInsight 的詳細資訊，請參閱[使用 Azure 虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **外部中繼存放區**：指定 Azure SQL 資料庫來儲存與該叢集相關聯的 Hive 和 Oozie 中繼資料。
 
            > [AZURE.NOTE] HBase 叢集類型無法使用中繼存放區組態。

			![自訂中繼存放區刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			針對 [使用 Hive 現有的 SQL DB] 中繼資料按一下 [是]、選取 SQL 資料庫，然後提供該資料庫的使用者名稱/密碼。如果您想要**使用 Oozie 中繼資料現有的 SQL DB**，請重複執行這些步驟。按一下 [選取]，直到您回到 [選擇性組態] 刀鋒視窗。


			>[AZURE.NOTE] 用於 metastore 的 Azure SQL Database 必須允許與其他 Azure 服務 (包括 Azure HDInsight) 的連線能力。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [**設定**]，然後在 [**Azure 服務**] 按一下 [**是**]，再按 [**儲存**]。
		
  		* **指令碼動作**：如果您想要在叢集建立時使用自訂指令碼來自訂該叢集。如需指令碼動作的詳細資訊，請參閱[作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md).請在 [指令碼動作] 刀鋒視窗上提供如螢幕擷取畫面所示的詳細資料。
  	

			![指令碼動作刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure 儲存體金鑰**：指定與該叢集相關聯的其他儲存體帳戶。在 [Azure 儲存體金鑰] 刀鋒視窗中，按一下 [加入儲存體金鑰]，然後選取現有的儲存體帳戶或建立新的帳戶。
    

			![其他儲存體刀鋒視窗](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. 按一下 [建立]。如果您選取 [釘選到「開始面板」]，您入口網站的「開始面板」將會新增該叢集的磚。該圖示可表示該叢集正在建立，並將在建立完成後變更為 HDInsight 圖示。
	
    建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或是頁面左側的 [通知] 項目來查看佈建進度。
	

5. 建立完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。此叢集刀鋒視窗提供該叢集的基本資訊，如名稱、其所屬的資源群組、位置、作業系統、叢集儀表板 URL 等。


	![叢集刀鋒視窗](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "叢集屬性")


	使用下列步驟來了解在這個刀鋒視窗頂端和 [基本功能] 區段中的圖示：


	* **設定**和**所有設定**：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。
	* **儀表板**、**叢集儀表板**和 **URL**：這些都是存取叢集儀表板的方式；叢集儀表板就是可針對叢集執行工作的 Web 入口網站。
	* **遠端桌面**：可讓您啟用/停用叢集節點上的遠端桌面。
	* **調整叢集**：可讓您變更此叢集的背景工作節點數目。
	* **刪除**：刪除 HDInsight 叢集。
	* **快速入門** (![雲和雷電圖示 = 快速入門](./media/hdinsight-provision-clusters/quickstart.png))：顯示可協助您開始使用 HDInsight 的資訊。
	* **使用者 (![使用者圖示](./media/hdinsight-provision-clusters/users.png))**：可讓您為自己 Azure 訂用帳戶的其他使用者設定針對此叢集的「入口網站管理」權限。
	

		> [AZURE.IMPORTANT] 這「只會」影響使用者在入口網站中對此叢集的存取權和權限，不會影響誰才能連線至 HDInsight 叢集或將工作提交給 DInsight 叢集。
		
	* **標記 (![標記圖示](./media/hdinsight-provision-clusters/tags.png))**：標記可讓您設定索引鍵/值組，以定義自訂的雲端服務分類法。例如，您可建立名為 __project__ 的索引鍵，然後使用與特定專案相關聯之所有服務的通用值。

##自訂叢集

- 請參閱[使用 Bootstrap 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-bootstrap.md)。
- 請參閱[使用指令碼動作自訂 Windows 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。

##後續步驟
在本文中，您學到幾種建立 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

* [開始使用 Azure HDInsight](hdinsight-get-started.md) - 了解如何開始使用 HDInsight 叢集
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以程式設計方式提交工作至 HDInsight
* [使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0128_2016-->