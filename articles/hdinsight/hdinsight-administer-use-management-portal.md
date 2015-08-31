<properties
	pageTitle="使用 Azure 預覽入口網站管理 HDInsight 上的 Hadoop 叢集 | Microsoft Azure"
	description="了解如何管理 HDInsight 服務。建立 HDInsight 叢集、開啟互動式 JavaScript 主控台，以及開啟 Hadoop 命令主控台。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="jgao"/>

# 使用 Azure 預覽入口網站管理 HDInsight 上的 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-portal-management-selector.md)]

您可以使用 [Azure 預覽入口網站][azure-portal]在 Azure HDInsight 上佈建 Hadoop 叢集、變更 Hadoop 使用者密碼，以及啟用遠端桌面通訊協定 (RDP)，以便存取叢集上的 Hadoop 命令主控台。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [使用 Azure 入口網站管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]此文件中的步驟是針對 Windows 型 Hadoop 叢集。如需使用 Linux 型叢集的相關資訊，請參閱[使用 Azure 預覽入口網站管理 HDInsight 上的 Hadoop 叢集](hdinsight-administer-use-portal-linux.md)。



> [AZURE.NOTE]除了預覽入口網站之外，還有其他工具可以用來管理 HDInsight。
>
> - [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
> - [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)

**必要條件**

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure 儲存體帳戶** - HDInsight 叢集使用 Azure Blob 儲存體容器做為預設檔案系統。如需 Azure Blob 儲存體如何提供順暢 HDInsight 叢集使用體驗的詳細資訊，請參閱[搭配使用 Azure Blob 儲存體與 HDInsight](../hdinsight-use-blob-storage.md)。如需建立 Azure 儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶](../storage-create-storage-account.md)。


##佈建 HDInsight 叢集

如需使用預覽入口網站佈建的相關指示，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md#portal)。

##自訂 HDInsight 叢集佈建

HDInsight 可以與很多 Hadoop 元件搭配使用。如需已驗證和所支援元件的清單，請參閱 [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)(英文)。您可以使用下列其中一個選項來自訂 HDInsight：

- 使用 [指令碼動作] 來執行可自訂叢集的自訂指令碼，以變更叢集組態或安裝自訂元件 (如 Giraph 或 Solr)。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。
- 在叢集佈建期間，使用 HDInsight .NET SDK 或 Azure PowerShell 中的叢集自訂參數。即會在叢集的存留期保留這些組態變更，而且它們不受叢集節點重新製作映像的影響，而 Azure 平台會定期執行重新製作映像以進行維護。如需使用叢集自訂參數的詳細資訊，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。
- 您可以使用 JAR 檔案形式在叢集上執行一些原生 Java 元件 (例如 Mahout 和 Cascading)。這些 JAR 檔案可以配送至 Azure Blob 儲存體，並透過 Hadoop 工作提交機制提交至 HDInsight 叢集。如需詳細資訊，請參閱[以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)。


	>[AZURE.NOTE]如果您在將 JAR 檔案部署至 HDInsight 叢集或在 HDInsight 叢集上呼叫 JAR 檔案時發生問題，請連絡 [Microsoft 支援](http://azure.microsoft.com/support/options/)。

	> Cascading 不受 HDInsight 支援，而且不符合「Microsoft 支援」的資格。如需所支援元件的清單，請參閱 [HDInsight 所提供叢集版本的新功能](hdinsight-component-versioning.md) (英文)。


不支援使用遠端桌面連線在叢集上安裝自訂軟體。您應該避免在前端節點的磁碟機上儲存任何檔案，因為這些檔案會在您需要重新建立叢集時遺失。建議您將檔案儲存至 Azure Blob 儲存體。Blob 儲存體是持續性的。

## 熟悉叢集入口網站介面

**存取叢集**

1. 登入[預覽入口網站][azure-portal]。
2. 在左側功能表按一下 [瀏覽全部]，開啟 [瀏覽] 刀鋒視窗。
3. 按一下 [HDInsight 叢集] 開啟 [HDInsight 叢集] 刀鋒視窗。
4. 按一下清單上其中一個叢集，開啟叢集刀鋒視窗：

	![HDInsight 入口網站](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. 按一下 [設定] 查看組態詳細資料並設定叢集：

	![HDInsight 入口網站的叢集設定](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|設定|說明|
	|-----|-----|
	|屬性| 顯示叢集屬性。|
	|Azure 儲存體金鑰| 顯示預設的 Azure 儲存體帳戶資訊。 |
	|叢集登入| 授與/撤銷 HTTP Web 服務存取權，並設定叢集登入資訊。 |
	|外部中繼存放區| 顯示 Hive/Oozie 中繼存放區資訊。|
	|調整叢集| 增加/減少叢集的背景工作節點數目。|
	|遠端桌面| 啟用/停用遠端桌面連線，透過遠端桌面連接到叢集。|


##授與/撤銷 HTTP 服務存取

HDInsight 叢集具有下列 HTTP Web 服務 (所有這些服務都有 RESTful 端點)：

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

預設會授與這些服務的存取權。您可以從 Azure 入口網站撤銷/授與存取權。

>[AZURE.NOTE]透過授與/撤銷存取權，您將重設叢的使用者名稱和密碼。

**授與/撤銷 HTTP Web 服務存取**

1. 登入[預覽入口網站][azure-portal]。
2. 依序按一下左側功能表的 [瀏覽全部]、[HDInsight 叢集] 及您的叢集名稱。
3. 在頂端功能表按一下 [設定]，再按 [叢集登入]。
5. 按一下 [叢集登入存取] 上的 [開啟] 或 [關閉]。  
6. 針對 [叢集登入使用者名稱] 和 [叢集登入密碼]，分別輸入叢集的新使用者名稱和密碼。
7. 按一下 [儲存]。

	![hdinsight grand remove http web service access](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


這也可以透過 Azure PowerShell Cmdlet 來完成：

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

請參閱[使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。

##變更 HDInsight 叢集使用者名稱和密碼
HDInsight 叢集可以有兩個使用者帳戶。HDInsight 叢集使用者帳戶是在佈建程序期間所建立。您也可以建立 RDP 使用者帳戶，以透過 RDP 來存取叢集。請參閱[啟用遠端桌面](#connect-to-hdinsight-clusters-by-using-rdp)。

**變更 HDInsight 叢集使用者名稱和密碼**

1. 登入[預覽入口網站][azure-portal]。
2. 依序按一下左側功能表的 [瀏覽全部]、[HDInsight 叢集] 及您的叢集名稱。
3. 在頂端功能表按一下 [設定]，再按 [叢集登入]。
4. 變更 [叢集登入名稱] 及/或 [叢集登入密碼]，然後按一下 [儲存]。

	![hdinsight change cluster user username password http user](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##調整叢集
叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的背景工作節點數目，而不需要重新建立叢集。

>[AZURE.NOTE]只支援使用 HDInsight 3.1.3 版或更高版本的叢集。如果不確定您的叢集版本，您可以檢查 [屬性] 頁面。請參閱[熟悉叢集入口網站介面](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface)。

變更 HDInsight 支援的每一種叢集所用的資料節點數目會有何影響：

- Hadoop

	您可以順暢地增加正在執行的 Hadoop 叢集中背景工作節點數目，而不會影響任何擱置或執行中的工作。您也可以在作業進行當中提交新工作。系統會順暢處理失敗的調整作業，讓叢集永保正常運作狀態。

	減少資料節點數目以縮減 Hadoop 叢集時，系統會重新啟動叢集中的部分服務。這會導致所有執行中和擱置的工作在調整作業完成時失敗。但您可以在作業完成後重新提交這些工作。

- HBase

	您可以順暢地在 HBase 叢集運作時對其新增或移除資料節點。區域伺服器會在完成調整作業的數分鐘之內自動取得平衡。但是，您也可以手動平衡區域伺服器，方法是登入叢集的前端節點，然後從命令提示字元視窗執行下列命令：

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	如需使用 HBase 殼層的詳細資訊，請參閱
- Storm

	您可以順暢地在 Storm 叢集運作時對其新增或移除資料節點。但在調整作業順利完成後，您需要重新平衡拓撲。

	您可以使用兩種方式來完成重新平衡作業：

	* Storm Web UI
	* 命令列介面 (CLI) 工具

	如需詳細資訊，請參閱 [Apache Storm 文件](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

	HDInsight 叢集上有提供 Storm Web UI：

	![hdinsight storm scale rebalance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	以下是如何使用 CLI 命令重新平衡 Storm 拓撲的範例：

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**調整叢集**

1. 登入[預覽入口網站][azure-portal]。
2. 依序按一下左側功能表的 [瀏覽全部]、[HDInsight 叢集] 及您的叢集名稱。
3. 在頂端功能表按一下 [設定]，再按 [調整叢集]。
4. 輸入**背景工作節點的數目**。叢集節點的數目限制會因 Azure 訂用帳戶而有所不同。請連絡帳務支援提高限制。成本資訊會反映您對節點數目所做的變更。


	![hdinsight hadoop hbase storm spark scale](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##使用 RDP 連線到 HDInsight 叢集

您在建立叢集時所提供的叢集認證可以存取叢集上的服務，但是無法透過遠端桌面來存取叢集本身。當您佈建叢集時或叢集佈建完成後，可以開啟遠端桌面存取。如需佈建時啟用遠端桌面的指示，請參閱[佈建 HDInsight 叢集](hdinsight-provision-clusters.md)。

**啟用遠端桌面**

1. 登入[預覽入口網站][azure-portal]。
2. 依序按一下左側功能表的 [瀏覽全部]、[HDInsight 叢集] 及您的叢集名稱。
3. 在頂端功能表按一下 [設定]，再按 [遠端桌面]。
4. 輸入 [到期日]、[遠端桌面使用者名稱] 和 [遠端桌面密碼]，然後按一下 [啟用]。

	![hdinsight enable disable configure remote desktop](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	[到期日] 的預設值是一週。
> [AZURE.NOTE]您也可以使用 HDInsight .NET SDK，在叢集上啟用遠端桌面。以下列方式在 HDInsight 用戶端物件上使用 **EnableRdp** 方法：**client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**。同樣地，若要在叢集上停用遠端桌面，您可以使用 **client.DisableRdp(clustername, location)**。如需這些方法的詳細資訊，請參閱 [HDInsight .NET SDK 參考](http://go.microsoft.com/fwlink/?LinkId=529017)。這僅適用於在 Windows 上執行的 HDInsight 叢集。

**使用 RDP 連線到叢集**

1. 登入[預覽入口網站][azure-portal]。
2. 依序按一下左側功能表的 [瀏覽全部]、[HDInsight 叢集] 及您的叢集名稱。
3. 在頂端功能表按一下 [設定]，再按 [遠端桌面]。
4. 按一下 [連接] 並遵循指示。如果 [連接] 已停用，您必須先加以啟用。請務必使用遠端桌面使用者的使用者名稱和密碼。不可使用叢集使用者的認證。

##開啟 Hadoop 命令列

若要使用遠端桌面連線到叢集，並使用 Hadoop 命令列，則您必須先啟用對叢集的遠端桌面存取 (如上一節所述)。

**開啟 Hadoop 命令列**

1. 使用遠端桌面連接到叢集。
8. 從桌面上，按兩下 [Hadoop 命令列]。

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	如需 Hadoop 命令的詳細資訊，請參閱 [Hadoop 命令參考](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)。

在前一個螢幕擷取畫面上，資料夾名稱已內嵌 Hadoop 版本號碼。版本號碼會根據叢集上所安裝的 Hadoop 元件版本而變更。您可以使用 Hadoop 環境變數來參照那些資料夾。例如：

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%


##後續步驟
在本文中，您已了解如何使用預覽入口網站建立 HDInsight 叢集，以及如何開啟 Hadoop 命令列工具。若要深入了解，請參閱下列文章：

* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [使用 Azure CLI 管理 HDInsight](hdinsight-administer-use-command-line.md)
* [佈建 HDInsight 叢集](hdinsight-provision-clusters.md)
* [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
* [開始使用 Azure HDInsight](../hdinsight-get-started.md)
* [Azure HDInsight 提供 Hadoop 的什麼版本？](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "叢集登陸頁面"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop 命令列"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=August15_HO8-->