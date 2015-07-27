<properties 
	pageTitle="在 HDInsight 中使用資源管理員將資源配置給 Apache Spark 叢集 | Azure" 
	description="了解如何在 HDInsight 上使用資源管理員，以便提升 Spark 叢集的效能。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# 在 Azure HDInsight 中管理 Apache Spark 叢集的資源

資源管理員是 Spark 叢集儀表板的元件，它能讓您管理諸如在叢集上執行之每個應用程式所使用的核心和 RAM 等資源。

## <a name="launchrm"></a>如何啟動資源管理員？

1. 在 Azure 入口網站選取 Spark 叢集，接著在底部的入口網站工作列按一下 [**Spark 儀表板**]。

2. 在儀表板上方的窗格中，按一下 [**資源管理員**] 索引標籤。

##<a name="scenariosrm"></a>如何使用資源管理員修正這些問題？

以下是一些可能會發生在 Spark 叢集上的常見案例，以及如何使用資源管理員因應的指示。

### HDInsight 上的 Spark 叢集速度很慢

HDInsight 中的 Apache Spark 叢集是專為多租用戶所設計，因此資源會分割成多個元件 (Notebook、作業伺服器等)。這可讓您同時使用所有 Spark 元件，而不必擔心有元件會無法取得執行所需的資源，但由於資源已分散，因此每個元件都會變慢。您可以根據需求進行調整。


### 我只使用 IPython 搭配 Spark 叢集。該如何將所有資源配置給它？

1. 在 [**Spark 儀表板**] 中，按一下 [**Spark UI**] 索引標籤以找出可配置給應用程式的核心數目和 RAM 上限。

	![資源配置](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Resource.png "尋找配置給 Spark 叢集的資源")

	依照上述螢幕擷取畫面，您可以配置的核心數目上限為 7 個 (總共 8 個，其中有 1 正在使用中)，可配置的 RAM 上限為 9 GB (總共 12 GB RAM，其中 2 GB 必須保留供系統使用，有 1 GB 正供給其他應用程式使用)。

	您也應該將所有執行中的應用程式納入考量。您可以從 [**Spark UI**] 索引標籤查看執行中的應用程式。

	![執行中的應用程式](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Running.Apps.png "在叢集上執行的應用程式")

	
2. 在 HDInsight Spark 儀表板中按一下 [**資源管理員**] 索引標籤，接著再指定 [**預設應用程式核心計數**] 和 [**每個背景工作節點的預設執行程式記憶體**] 的值。將其他屬性設定為 0。

	![資源配置](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.Allocate.Resources.png "將資源配置給應用程式")

### 我沒有搭配使用 BI 工具和 Spark 叢集。該如何回收資源？ 

將 Thrift 伺服器核心計數和 Thrift 伺服器執行程式記憶體指定為 0。在未配置核心或記憶體的情況下，Thrift 伺服器將會進入**等候**狀態。

![資源配置](./media/hdinsight-apache-spark-resource-manager/HDI.Spark.UI.No.Thrift.png "未將資源配置給 Thrift 伺服器")

##<a name="seealso"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [在 HDInsight 叢集上佈建 Spark](hdinsight-apache-spark-provision-clusters.md)
* [在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO3-->