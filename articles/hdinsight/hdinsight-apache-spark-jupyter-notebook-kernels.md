<properties 
	pageTitle="Linux 上的 HDInsight Spark 叢集可供 Jupyter Notebook 使用的核心| Microsoft Azure" 
	description="了解 HDInsight Linux 上的 Spark 叢集可用的其他 Jupyter Notebook 核心。" 
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


# HDInsight (Linux) 上的 Spark 叢集可供 Jupyter Notebook 使用的核心

在 HDInsight (Linux) 上的 Apache Spark 叢集包含可用來測試應用程式的 Jupyter Notebook。根據預設，Jupyter Notebook 附有 **Python2** 核心。HDInsight Spark 叢集另外還提供兩種核心，可讓您用於 Jupyter Notebook。它們是：

1. **Spark** (適用於以 Scala 撰寫的應用程式)
2. **PySpark** (適用於以 Python 撰寫的應用程式)

在本文中，您將了解使用這些核心的方法與好處。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 核心要如何使用？ 

1. 在 [Azure Preview 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 以新核心建立新的 Notebook。按一下 [新增]，然後按一下 [Pyspark] 或 [Spark]。Spark 核心適用於 Scala 應用程式，PySpark 核心則適用於 Python 應用程式。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "建立新的 Jupyter Notebook")

3. 這應該會開啟使用您所選核心的新 Notebook。

## 為何要使用新的核心？

使用新核心有幾項好處。

1. 使用預設 **Python2** 核心時，您必須先設定 Spark、SQL 或 Hive 內容，才能開始使用您所開發的應用程式。如果您使用新的核心 (**Spark** 或 **PySpark**)，依預設會將這些內容提供給您使用。這些內容包括：

	* **sc** - 代表 Spark 內容
	* **sqlContext** - 代表 SQL 內容
	* **hiveContext** - 代表 Hive 內容


	因此，您不需要執行如下的陳述式來設定這些內容：

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	您可以直接在您的應用程式中使用現有的內容。
	
2. 您可以直接使用 **%sql** 和 **%hive** Magic，以分別使用 SQL 或 Hive 查詢。因此，諸如此類的作業將可直接運作，而不需要任何前置的程式碼陳述式。

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## 使用新核心的考量

無論您使用何種核心 (Python2、PySpark 或 Spark)，讓 Notebook 持續執行，都將會耗用您的叢集資源。使用 Python2 Notebook 時，由於您會明確地建立內容，因此您也可以在結束應用程式時刪除這些內容。

但在使用 PySpark 和 Spark 核心時，由於內容會預先設定，因此您無法明確刪除內容。因此，在您剛結束 Notebook 之後，內容可能仍會執行，而使用您的叢集資源。使用 PySpark 和 Spark 核心時，理想的做法是使用 Notebook 的 [檔案] 功能表中的 [關閉並停止] 選項。這將會先刪除內容，再結束 Notebook。


## 請舉例說明

當您開啟 Jupyter Notebook 時，您會在根層級看到兩個可用資料夾。

* **Python** 資料夾含有使用預設 **Python2** 核心的範例 Notebook。
* **Scala** 資料夾含有使用新 **Spark** 核心的範例 Notebook。

您可以從這兩個資料夾中開啟相同的 (例如 **READ ME FIRST - 認識 HDInsight 上的 Spark**) Notebook，而觀察到 Python2 Notebook 一律會以必要內容的設定啟動，而 Spark Notebook 則只會使用預先設定的內容。

## 意見反應

新核心尚處於開發初期，將會隨著時間持續發展。這或許也意味著，API 可能會隨著這些核心的成熟而改變。您在使用這些新核心時如有任何意見，我們都非常樂於知道。這對於這些核心最終版本的定調，將很有幫助。您可以將您的評論/意見反應填寫在本文最後的**評論**一節底下。


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

### 工具和擴充功能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->