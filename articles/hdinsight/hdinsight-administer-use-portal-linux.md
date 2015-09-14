<properties
	pageTitle="使用 Azure 入口網站管理 HDInsight 上的 Hadoop 叢集 | Microsoft Azure"
	description="了解如何管理 HDInsight 服務。建立 HDInsight 叢集、開啟互動式 JavaScript 主控台，以及開啟 Hadoop 命令主控台。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="larryfr"/>

# 使用 Azure 預覽入口網站管理 HDInsight 上的 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-portal-management-selector.md)]


使用 [Azure 預覽入口網站][preview-portal]，您可以佈建和管理 Azure HDInsight 中的 Linux 型 Hadoop 叢集。

> [AZURE.NOTE]此文件中的步驟是針對 Linux 型 Hadoop 叢集。如需使用 Windows 型叢集的相關資訊，請參閱[使用 Azure 預覽入口網站管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-management-portal.md)。


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## 用來管理 HDInsight 的其他工具
除了 Azure 入口網站之外，還有其他工具可以用來管理 HDInsight。

- [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)：Azure CLI 是跨平台的命令列工具，可讓您管理 Azure 服務。

- [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)：Azure PowerShell 提供 PowerShell Cmdlet 讓您管理 Azure 服務。

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

##佈建 HDInsight 叢集

您可以使用下列步驟，從 Azure 入口網站佈建 HDInsight 叢集：

1. 登入 [Azure 預覽入口網站][preview-portal]。

2. 依序選取 [新增]、[資料分析] 和 [HDInsight]。

	![在 Azure Preview 入口網站中建立新的叢集](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. 輸入 [叢集名稱]，然後選取您要建立的 [叢集類型]。如果該叢集可用，__叢集名稱__旁會出現綠色核取記號。

	![叢集名稱、叢集類型及 OS 類型](./media/hdinsight-administer-use-portal-linux/clustername.png)
	
	> [AZURE.NOTE]只有 Hadoop 叢集類型可用於 Linux 型 HDInsight 預覽。

4. 如果您有多個訂用帳戶，請選取 [訂用帳戶] 項目，以選取將用於該叢集的 Azure 訂用帳戶。

5. 針對 [資源群組]，您可以選取此項目以查看現有資源群組的清單，然後選取其中一個用來建立叢集。或者選取 [建立新群組]，然後輸入新資源群組的名稱。出現綠色核取記號即表示新群組的名稱可用。

	> [AZURE.NOTE]如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。

6. 選取 [認證]，然後輸入 [叢集登入使用者名稱] 的 [叢集登入密碼]。您也必須輸入 [SSH 使用者名稱] 和 [密碼] 或 [公開金鑰]，這會用來驗證 SSH 使用者。最後，使用 [選取] 按鈕來設定認證。本文件中不會使用遠端桌面，所以您可以將其停用。

	![叢集認證刀鋒視窗](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)

6. 針對 [資料來源]，您可以選取此項目，藉此選擇現有資料來源，或建立一個新的資料來源。

	![資料來源刀鋒視窗](./media/hdinsight-administer-use-portal-linux/datasource.png)
	
	目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。請使用下列資訊來了解 [資料來源] 刀鋒視窗上的項目。
	
	- __選取方法__：將此設為 [來自所有訂用帳戶]，即可瀏覽您訂用帳戶的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。
	
	- __建立新項目__：這可用來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。
	
	- __選擇預設容器__：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。
	
	- __位置__：儲存體帳戶所在地或將建立的地理區域。
	
		> [AZURE.IMPORTANT]選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。
		
	- __選取__：用來儲存資料來源組態。
	
7. 選取 [節點定價層] 會顯示將針對此叢集建立之節點的相關資訊。背景工作節點數目預設會設為 __4__。

	該叢集的預估成本將會顯示在此刀鋒視窗的底部。

	![節點定價層刀鋒視窗](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)
	
	使用 [選取] 按鈕以儲存 [節點定價層] 資訊。

8. 選取 [選用設定]。此刀鋒視窗可讓您設定下列項目：

	* __HDInsight 版本__：用於叢集的 HDInsight 版本。如需 HDInsight 版本的詳細資訊，請參閱 [HDInsight 元件版本](hdinsight-component-versioning.md)。
	* __外部中繼存放區__：這可讓您選取用來儲存 Oozie 和 Hive 組態資訊的 SQL 資料庫。這可讓您在刪除並重新建立叢集時重複使用組態，而不必每次都重新建立 Hive 和 Oozie 的組態。**__Azure 儲存體金鑰__：這可讓您將其他儲存體帳戶與 HDInsight 伺服器產生關聯。

		> [AZURE.NOTE]HDInsight 只能存取用來做為預設資料存放區、透過此組態區段加入或可公開存取的 Azure 儲存體帳戶。

	![選用設定刀鋒視窗](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

9. 請確定已選取 [釘選到儀表板]，然後選取 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

	| 佈建期間 | 佈建完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的佈建指示器](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。使用 [開始面板] 上的圖格或頁面左邊的 [通知] 項目，檢查佈建處理序。

##管理叢集

從 Azure 預覽入口網站中選取叢集將會顯示叢集的基本資訊，例如名稱、資源群組、作業系統和叢集儀表板 URL (用來存取適用於 Linux 叢集的 Ambari Web )。

![叢集詳細資料](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

使用下列資訊，了解在刀鋒視窗頂端以及 [基本功能] 和 [快速連結] 區段中的圖示：

* __設定__和__所有設定__：顯示該叢集的 [設定] 刀鋒視窗，可讓您存取該叢集的詳細組態資訊。

* __儀表板__、__叢集儀表板__和 __URL__：這些是存取叢集儀表板 (也就是適用於 Linux 型叢集之 Ambari Web) 的所有方法。

* __安全殼層__：存取使用 SSH 之叢集所需的資訊。

* __調整叢集__：可讓您變更此叢集的背景工作角色節點數目。

* __刪除__：刪除 HDInsight 叢集。

* __快速入門__ (![雲和雷電圖示 = 快速入門](./media/hdinsight-administer-use-portal-linux/quickstart.png))：顯示可協助您開始使用 HDInsight 的資訊。

* __使用者__ (![使用者圖示](./media/hdinsight-administer-use-portal-linux/users.png))：可讓您設定 Azure 訂用帳戶上其他使用者對此叢集的 [入口網站管理] 權限。

	> [AZURE.IMPORTANT]這「只」會影響在 Azure 預覽入口網站對此叢集的存取和權限，對於連線至 HDInsight 叢集或將工作提交至其上的使用者則沒有作用。

* __標記__ (![標記圖示](./media/hdinsight-administer-use-portal-linux/tags.png))：標記可讓您設定索引鍵/值組，以定義雲端服務的自訂分類。例如，您可能會建立名為 [專案] 的金鑰，然後使用與特定專案相關聯之所有服務的通用值。

* __文件__：Azure HDInsight 文件的連結。

> [AZURE.IMPORTANT]若要管理 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。如需使用 Ambari 的詳細資訊，請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

##監視叢集

HDInsight 叢集刀鋒視窗的 [使用量] 區段，會顯示訂用帳戶可搭配 HDInsight 使用的核心數目，以及配置給此叢集的核心數目和它們在此叢集中配置給節點方式的相關資訊。

![使用量資訊](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]若要監視 HDInsight 叢集所提供的服務，您必須使用 Ambari Web 或 Ambari REST API。如需使用 Ambari 的詳細資訊，請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

##後續步驟
在本文中，您已了解如何使用 Azure 入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [開始使用 Azure HDInsight](../hdinsight-get-started.md)
* [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=September15_HO1-->