<properties 
	pageTitle="HDInsight 中的 Apache Spark 概觀 | Microsoft Azure" 
	description="介紹 HDInsight 中的 Apache Spark，以及在應用程式中使用 HDInsight 上之 Spark 的案例。" 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>

# 概觀：Azure HDInsight 上的 Apache Spark (Linux)
 
<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。Spark 處理引擎是專為速度、易用性及精密分析打造的產品。Spark 的記憶體內計算功能，使其成為機器學習和圖表計算中反覆演算法的絕佳選擇 。Spark 也能與 Azure Blob 儲存體 (WASB) 相容，因此您可以輕鬆地透過 Spark 處理儲存在 Azure 中的現有資料。

當您在 HDInsight 中建立 Spark 叢集時，就是建立了已安裝及設定 Spark 的 Azure 計算資源。在 HDInsight 中建立 Spark 叢集只需要約十分鐘。系統會將要處理的資料儲存在 Azure Blob 儲存體。請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

![Azure HDInsight 上的 Apache Spark](./media/hdinsight-apache-spark-overview/hdispark.architecture.png "Azure HDInsight 上的 Apache Spark")


**想要開始使用 Azure HDInsight 上的 Apache Spark 嗎？** 請參閱[快速入門：在 HDInsight Linux 上建立 Spark 叢集，並利用 Jupyter 來執行範例應用程式](hdinsight-apache-spark-jupyter-spark-sql.md)。

>[AZURE.NOTE] 如需目前版本的已知問題及限制清單，請參閱 [Azure HDInsight 中 Apache Spark 的已知問題及限制](hdinsight-apache-spark-jupyter-spark-sql.md)。


## 為什麼要使用 Azure HDInsight 上的 Spark？ 

Azure HDInsight 提供完全受管理的 Spark 服務。在 HDInsight 上使用 Spark 的優點如下：

| 功能 | 說明 |
|-------------------------------------|-------------------|
| 容易建立 | 您可以使用 Azure 管理入口網站、Azure PowerShell 或 HDInsight .NET SDK，在幾分鐘之內於 HDInsight 上建立新的 Spark 叢集。請參閱[開始使用 HDInsight 中的 Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md) |
| 容易使用 | HDInsight 叢集中的 Spark 包含預先設定的 Jupyter Notebook。您可以使用它們來進行互動式的資料處理和視覺化。URL 是 https://CLUSTERNAME.azurehdinsight.net/jupyter。請用您 Spark HDInsight 叢集的名稱取代 __CLUSTERNAME__。|
| REST API | HDInsight 中的 Spark 包含 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)，它是 REST-API 式 Spark 作業伺服器，能用來遠端提交及監視執行中的工作。 |
| 並行查詢 | HDInsight 中的 Spark 支援並行查詢。它能讓一位使用者執行多個查詢，或讓不同的使用者執行多個查詢，以及讓應用程式共用相同的叢集資源。 |
| SSD 快取 | 您可以選擇將資料快取在記憶體中，或快取在連接叢集節點的 SSD 中。記憶體快取能提供最高的查詢效能，但可能所費不疵。SSD 快取是改善查詢效能的絕佳選項，而且您不需要根據記憶體中的整個資料集建立滿足其需求的叢集規模。|
| Azure 服務整合 | HDInsight 上的 Spark 附有連接 Azure 事件中樞的連接器。除了 Spark 已經提供的 [Kafka](http://kafka.apache.org/) 之外，客戶還可以使用事件中樞來建置串流應用程式。 |
| BI 工具整合 | HDInsight 的 Spark 會為常用的 BI 工具 (例如 [Power BI](http://www.powerbi.com/) 和 [Tableau](http://www.tableau.com/products/desktop)) 提供資料分析所需的連接器。|
| 預先載入的 Anaconda 程式庫 | HDInsight 上的 Spark 叢集附有預先安裝的 Anaconda 程式庫。[Anaconda](http://docs.continuum.io/anaconda/) 為機器學習、資料分析、視覺化等主題提供將近 200 個程式庫。|
| 延展性 | 雖然您可以在建立時指定叢集的節點數，但您可以擴大或縮小叢集以配合工作負載。所有 HDInsight 叢集都允許您變更叢集中的節點數目。此外，由於所有資料都儲存在 Azure Blob 儲存體內，因此您可以在不遺失資料的情況下卸除 Spark 叢集。 |
| 全天候支援 | HDInsight 上的 Spark 附有企業級的全天候支援和保證正常運作時間達 99.9% 的 SLA。|



## HDInsight 上的 Spark 有哪些使用案例？

HDInsight 中的 Apache Spark 適用於以下重要案例。

### 互動式資料分析和 BI

[觀看教學課程](hdinsight-apache-spark-use-bi-tools.md)

HDInsight 中的 Apache Spark 會將資料儲存在 Azure Blob 內。商務專家和重要決策者可以利用這些資料來進行分析及建立報告，並使用 Microsoft Power BI 來根據分析資料建置互動式報告。分析師可以從 Azure 儲存體內的非結構化/半結構化資料著手、使用 Notebook 來定義資料的結構描述，然後再使用 Microsoft Power BI 來建置資料模型。HDInsight 中的 Spark 也支援 Tableau、Qlikview 及 SAP Lumira 等多個協力廠商 BI 工具，因此能成為資料分析師、商務專家及重要決策者的理想平台。

### 反覆的機器學習

[看看教學課程：利用 HVAC 資料來預測建築物的溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[看看教學課程：預測食物檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark 隨附 [MLlib](http://spark.apache.org/mllib/)，這是為 Spark 所建置的機器學習程式庫。此外，HDInsight 上的 Spark 也包含 Anaconda，它是提供多種機器學習封裝的 Python 散發。搭配上內建的 Jupyter Notebook 支援，您將擁有最先進的機器學習應用程式建立環境。

### 串流和即時資料分析

[觀看教學課程](hdinsight-apache-spark-eventhub-streaming.md)

不論是藉由在資料抵達時進行處理來縮短取得資料見解的時間，或是建置實用的串流解決方案，這些案例都是即時資料分析的適用範圍內。HDInsight 中的 Spark 提供豐富的支援供您建置即時分析解決方案。雖然 Spark 已附有從 Kafka、Flume、Twitter、ZeroMQ 或 TCP 通訊端等眾多來源擷取資料的連接器，不過 HDInsight 中的 Spark 仍加入首屈一指的支援，供您從 Azure 事件中樞擷取資料。事件中樞是 Azure 上最廣泛使用的佇列服務。擁有立即可用的事件中樞支援，讓 HDInsight 中的 Spark 成為建置即時分析管線的理想平台。

##<a name="next-steps"></a>Spark 叢集包含哪些元件？

依預設，HDInsight 中的 Spark 能經由叢集提供下列元件。

- [Spark Core](https://spark.apache.org/docs/1.5.1/)。包括 Spark Core、Spark SQL、Spark 串流 API、GraphX 及 MLlib。
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Jupyter Notebook](https://jupyter.org)

HDInsight 中的 Spark 也提供 [ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229)，讓您能從 BI 工具 (例如 Microsoft Power BI 和 Tableau) 連線到 HDInsight 中的 Spark 叢集。

## 我該從哪裡開始？

請從建立 HDInsight Linux 上的 Spark 叢集開始。請參閱[快速入門：在 HDInsight Linux 上建立 Spark 叢集，並利用 Jupyter 來執行範例應用程式](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 後續步驟

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### 建立及執行應用程式

* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 工具和擴充功能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-storage]: ../hdinsight-use-blob-storage/

<!---HONumber=AcomDC_0211_2016-->