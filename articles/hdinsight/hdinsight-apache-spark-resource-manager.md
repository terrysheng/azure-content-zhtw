<properties 
	pageTitle="在 HDInsight 中使用資源管理員將資源配置給 Apache Spark 叢集 | Microsoft Azure" 
	description="了解如何在 HDInsight 上使用資源管理員，以便提升 Spark 叢集的效能。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# 在 Azure HDInsight 中管理 Apache Spark 叢集的資源 (Linux)

Azure HDInsight (Linux) 上的 Spark 提供 Ambari Web UI 來管理叢集資源及監視叢集的健康狀態。您也可以使用 Spark 歷程記錄伺服器來追蹤已在叢集上完成執行的應用程式。您可以使用 YARN UI 來監視目前在叢集上執行的應用程式。本文說明如何存取這些 UI，以及如何使用這些介面執行一些基本資源管理工作。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 如何啟動 Ambari Web UI？

1. 在 [Azure Preview 入口網站](https://ms.portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。 
 
2. 從 Spark 叢集刀鋒視窗中，按一下 [儀表板]。出現提示時，輸入 Spark 叢集的系統管理員認證。

	![啟動 Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "啟動資源管理員")

3. 這應會啟動 Ambari Web UI，如下所示。

	![Ambari Web UI](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Ambari Web UI")

## 如何啟動 Spark 歷程記錄伺服器？

1. 在 [Azure 預覽入口網站](https://ms.portal.azure.com/)的「開始面板」，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板的話)。

2. 從叢集刀鋒視窗的 [快速連結] 下，按一下 [叢集儀表板]。在 [叢集儀表板] 刀鋒視窗中，按一下 [Spark 歷程記錄伺服器]。

	![Spark 歷程記錄伺服器](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Spark 歷程記錄伺服器")

	出現提示時，輸入 Spark 叢集的系統管理員認證。


## 如何啟動 Yarn UI？

您可以使用 YARN UI 來監視目前在 Spark 叢集上執行的應用程式。存取 YARN UI 之前，您應該要啟用叢集的 SSH 通道。如需指示，請參閱[使用 SSH 通道存取 Ambari Web UI](hdinsight-linux-ambari-ssh-tunnel.md)

1. 如上一節所示啟動 Ambari Web UI。

2. 在 Ambari Web UI 中，選取頁面左邊清單中的 YARN。

3. 3\. 當 YARN 服務資訊顯示時，請選取 [快速連結]。叢集前端節點的清單隨即出現。選取其中一個前端節點，然後選取 [ResourceManager UI]。

	![啟動 YARN UI](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "啟動 YARN UI")

4. 這應會啟動 YARN UI，而且您應會看到如下所示的頁面：

	![YARN UI](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "YARN UI")

##<a name="scenariosrm"></a>如何使用 Ambari Web UI 管理資源？

以下是一些可能會發生在 Spark 叢集上的常見案例，以及如何使用 Ambari Web UI 因應的指示。

### 我沒有搭配使用 BI 和 Spark 叢集。如何回收資源？

1. 如上所示啟動 Ambari Web UI。從左側導覽窗格，按一下 [Spark]，然後按一下 [設定]。

2. 在可用的設定清單，尋找 [自訂 spark-thrift-sparkconf] 並將 **spark.executor.memory** 和 **spark.drivers.core** 的值變更為 **0**。

	![BI 的資源](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "BI 的資源")

3. 按一下 [儲存]。輸入您所做變更的描述，然後再按一下 [儲存]。

4. 在頁面頂端，您應會看到重新啟動 Spark 服務的提示。按一下 [重新啟動]，變更才會生效。


### 我的 Jupyter Notebook 並未如預期般執行。如何重新啟動服務？

1. 如上所示啟動 Ambari Web UI。從左側導覽窗格，依序按一下 [Jupyter]、[服務動作] 和 [全部重新啟動]。這會在所有前端節點上啟動 Jupyter 服務。

	![重新啟動 Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "重新啟動 Jupyter")

	


## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 建立及執行應用程式

* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 擴充功能

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0128_2016-->