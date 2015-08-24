<properties 
	pageTitle="HDInsight 中的 Apache Spark 概觀 | Microsoft Azure" 
	description="介紹 HDInsight 中的 Apache Spark，以及在應用程式中使用 HDInsight 上之 Spark 的案例。" 
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
	ms.date="07/19/2015" 
	ms.author="nitinme"/>

# 概觀：Azure HDInsight 上的 Apache Spark 
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。Spark 處理引擎是專為速度、易用性及精密分析打造的產品。Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。Spark 也能與 Azure Blob 儲存體 (WASB) 相容，因此您可以輕鬆地透過 Spark 處理儲存在 Azure 中的現有資料。

在 HDInsight 中佈建 Spark 叢集時，即是佈建安裝及設定 Spark 的 Azure 計算資源。在 HDInsight 中佈建 Spark 叢集只需要約十分鐘。系統會將要處理的資料儲存在 Azure Blob 儲存體。請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

![Azure HDInsight 上的 Apache Spark](./media/hdinsight-apache-spark-overview/SparkArchitecture.png "Azure HDInsight 上的 Apache Spark")


**想要開始使用 Azure HDInsight 上的 Apache Spark 嗎？** 請參閱[快速入門：在 HDInsight 上佈建 Spark 叢集並使用 Jupyter 和 Zeppelin 執行範例應用程式](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)。



觀賞描述 Azure HDInsight 上之 Apache Spark 的快速概觀影片。

> [AZURE.VIDEO announcing-apache-spark-on-azure-hdinsight]

## 為什麼要使用 Azure HDInsight 上的 Spark？ 

Azure HDInsight 提供完全受管理的 Spark 服務。在 HDInsight 上使用 Spark 的優點如下：

| 功能 | 說明 |
|-------------------------------------|-------------------|
| 輕鬆佈建 | 您可以使用 Azure 管理入口網站、Azure PowerShell 或 HDInsight .NET SDK，在幾分鐘之內於 HDInsight 上佈建新的 Spark 叢集。請參閱[在 HDInsight 中佈建 Spark 叢集](hdinsight-apache-spark-provision-clusters.md) |
| 容易使用 | HDInsight 叢集中的 Spark 包含預先設定的 Zeppelin 和 Jupyter Notebook。您可以使用它們來進行互動式的資料處理和視覺化。您可以從叢集儀表板啟動這些 Notebook，並直接伴隨著 Spark 叢集運作。|
| REST API | HDInsight 中的 Spark 包含 Spark 作業伺服器，它是讓使用者從遠端提交及監視執行中工作的 REST API 伺服器。 |
| 並行查詢 | HDInsight 中的 Spark 支援並行查詢。它能讓一位使用者執行多個查詢，或讓不同的使用者執行多個查詢，以及讓應用程式共用相同的叢集資源。 |
| SSD 快取 | 您可以選擇將資料快取在記憶體中，或快取在連接叢集節點的 SSD 中。記憶體快取能提供最高的查詢效能，但可能所費不疵。SSD 快取是改善查詢效能的絕佳選項，而且您不需要根據記憶體中的整個資料集建立滿足其需求的叢集規模。|
| Azure 服務整合 | HDInsight 上的 Spark 附有連接 Azure 事件中樞的連接器。除了 Spark 提供的 [Kafka](http://kafka.apache.org/) 之外，客戶還可以使用事件中樞來建置串流應用程式。 |
| BI 工具整合 | HDInsight 的 Spark 提供連接常用 BI 工具 (如[Power BI](http://www.powerbi.com/) 和[Tableau](http://www.tableau.com/products/desktop)) 以進行資料分析的連接器。|
| 預先載入的 Anaconda 程式庫 | HDInsight 上的 Spark 叢集附有預先安裝的 Anaconda 程式庫。[Anaconda](http://docs.continuum.io/anaconda/) 提供將近 200 個用於機器學習、資料分析、視覺化等的程式庫。|
| 延展性 | 雖然您可以在建立時指定叢集的節點數，但您可以擴大或縮小叢集以配合工作負載。所有 HDInsight 叢集都允許您變更叢集中的節點數目。此外，由於所有資料都儲存在 Azure Blob 儲存體內，因此您可以在不遺失資料的情況下卸除 Spark 叢集。 |
| 全天候支援 | HDInsight 上的 Spark 附有企業級的全天候支援和保證正常運作時間達 99.9% 的 SLA。|



## HDInsight 上的 Spark 有哪些使用案例？

HDInsight 中的 Apache Spark 適用於以下重要案例。

### 互動式資料分析和 BI

[觀看教學課程](hdinsight-apache-spark-use-bi-tools.md)

HDInsight 中的 Apache Spark 會將資料儲存在 Azure Blob 內。商務專家和重要決策者可以利用這些資料來進行分析及建立報告，並使用 Microsoft Power BI 來根據分析資料建置互動式報告。分析師可以從 Azure 儲存體內的非結構化/半結構化資料著手、使用 Notebook 來定義資料的結構描述，然後再使用 Microsoft Power BI 來建置資料模型。HDInsight 中的 Spark 也支援 Tableau、Qlikview 及 SAP Lumira 等多個協力廠商 BI 工具，因此能成為資料分析師、商務專家及重要決策者的理想平台。

### 反覆的機器學習

[觀看教學課程](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

Apache Spark 附有 [MLlib](http://spark.apache.org/mllib/)，它是建置於 Spark 之上的機器學習程式庫。此外，HDInsight 上的 Spark 也包含 Anaconda，它是提供多種機器學習封裝的 Python 散發。搭配上內建的 Jupyter Notebook 支援，您將擁有最先進的機器學習應用程式建立環境。

### 串流和即時資料分析

[觀看教學課程](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

不論是藉由在資料抵達時進行處理來縮短取得資料見解的時間，或是建置實用的串流解決方案，這些案例都是即時資料分析的適用範圍內。HDInsight 中的 Spark 提供豐富的支援供您建置即時分析解決方案。雖然 Spark 已附有從 Kafka、Flume、Twitter、ZeroMQ 或 TCP 通訊端等眾多來源擷取資料的連接器，不過 HDInsight 中的 Spark 仍加入首屈一指的支援，供您從 Azure 事件中樞擷取資料。事件中樞是 Azure 上最廣泛使用的佇列服務。擁有立即可用的事件中樞支援，讓 HDInsight 中的 Spark 成為建置即時分析管線的理想平台。

##<a name="next-steps"></a>Spark 叢集含有哪些元件？

依預設，HDInsight 中的 Spark 能經由叢集提供下列元件。

- [Spark 1.3.1](https://spark.apache.org/docs/1.3.1/)。附有 Spark Core、Spark SQL、Spark 串流 API、GraphX 及 MLlib。
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Spark 作業伺服器](https://github.com/spark-jobserver/spark-jobserver)
- [Zeppelin Notebook](https://zeppelin.incubator.apache.org)
- [Jupyter Notebook](https://jupyter.org)

HDInsight 中的 Spark 另提供 [ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229)，它能讓您從 Microsoft Power BI 和 Tableau 等 BI 工具連接 HDInsight 中的 Spark 叢集。

##<a name="see-also"></a>另請參閱

* [快速入門：搭配使用 HDInsight 中的 Spark 和 Zeppelin Notebook 執行互動式資料分析](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [在 HDInsight 中佈建 Spark 叢集](hdinsight-apache-spark-provision-clusters.md)
* [在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [從遠端將工作提交到 Azure HDInsight 上的 Apache Spark 叢集](hdinsight-apache-spark-job-server.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=August15_HO7-->