<properties 
	pageTitle="在 HDInsight 上使用 Apache Spark 建置機器學習應用程式 | Microsoft Azure" 
	description="說明如何搭配使用 Notebook 和 Apache Spark 來建置機器學習應用程式的逐步指示" 
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


# 在 Azure HDInsight 上使用 Apache Spark 建置機器學習服務應用程式 (Linux)

了解如何在 HDInsight 中使用 Apache Spark 叢集建置機器學習應用程式。本文說明如何使用叢集隨附的 Jupyter Notebook 來建置及測試應用程式。應用程式使用所有叢集預設提供的範例 HVAC.csv 資料。

> [AZURE.TIP] 本教學課程也適用於您在 HDInsight 中所建立 Spark (Linux) 叢集上的 Jupyter Notebook。Notebook 的體驗能讓您從 Notebook 本身執行 Python 程式碼片段。如要從 Notebook 中執行本教學課程，請建立 Spark 叢集、啟動 Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)，然後執行 **Python** 資料夾中的 Notebook [Spark 機器學習 - 利用 HVAC data.ipynb 來預測建築物溫度]。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。 

##<a name="data"></a>顯示資料

在開始建置應用程式之前，我們先來了解資料的結構，以及要針對資料執行哪種分析。

在本文中，我們會使用所有 HDInsight 叢集預設提供的範例 **HVAC.csv** 資料檔案 (位於 **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**)。下載及開啟 CSV 檔案，以取得資料的快照。

![HVAC 資料快照](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "HVAC 資料的快照")

這項資料會顯示安裝 HVAC 系統之建築物的目標溫度和實際溫度。我們假設 [System] 資料行代表系統識別碼，而 [SystemAge] 資料行代表 HVAC 系統安裝在建築物中的年數。

在指定系統識別碼和系統年期的情況下，我們可以使用這些資料來預測建築物的溫度會比目標溫度高或低。

##<a name="app"></a>使用 Spark MLlib 編寫機器學習應用程式

1. 在 [Azure Preview 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [新增]，然後按一下 [Python 2]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

3. 開始建置機器學習服務應用程式。在此應用程式中，我們會使用 Spark ML 管線來執行文件分類。在管線中，我們將文件分割成單字、將單字轉換成數值特性向量，最後再使用特性向量和標籤建立預測模型。

	若要開始建置應用程式，您需要先匯入所需的模組，並將資源指派給應用程式。將下列程式碼片段貼到新 Notebook 的空白儲存格中，然後按下 **SHIFT + ENTER**。


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row, SQLContext
		
		import os
		import sys
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		# Assign resources to the application
		conf = SparkConf()
		conf.setMaster('yarn-client')
		conf.setAppName('pysparkregression')
		conf.set("spark.cores.max", "4")
		conf.set("spark.executor.memory", "4g")
		
		sc = SparkContext(conf=conf)
		sqlContext = SQLContext(sc)

	每當您在 Jupyter 中執行工作時，網頁瀏覽器視窗標題會顯示 Notebook 的標題和 **(忙碌)** 狀態。您也會在右上角的 **Python 2** 文字旁看到實心圓。工作完成後，實心圓將會變成空心圓。

	 ![Jupyter Notebook 工作的狀態](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.jupyter.job.status.png "Jupyter Notebook 工作的狀態")
 
4. 您現在必須載入資料 (hvac.csv)、剖析資料，以及利用它來為模型定型。為此，您需要定義檢查建築物之實際溫度是否高於目標溫度的函示。如果實際溫度較高，代表建築物是熱的，我們以 **1.0** 值來表示這個狀態。如果實際溫度較低，代表建築物是冷的，我們以 **0.0** 值來表示這個狀態。

	將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。

		
		# List the structure of data for better understanding. Becuase the data will be
		# loaded as an array, this structure makes it easy to understand what each element
		# in the array corresponds to

		# 0 Date
		# 1 Time
		# 2 TargetTemp
		# 3 ActualTemp
		# 4 System
		# 5 SystemAge
		# 6 BuildingID

		LabeledDocument = Row("BuildingID", "SystemInfo", "label")

		# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
		
		def parseDocument(line):
    		values = [str(x) for x in line.split(',')]
    		if (values[3] > values[2]):
        		hot = 1.0
    		else:
        		hot = 0.0        
    
    		textValue = str(values[4]) + " " + str(values[5])
    
    		return LabeledDocument((values[6]), textValue, hot)

		# Load the raw HVAC.csv file, parse it using the function
		data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

		documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
		training = documents.toDF()


5. 設定包含三個階段的 Spark 機器學習管線：tokenizer、hashingTF 及 lr。如需了解什麼是管線，以及管線的運作方式，請參閱 <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 機器學習管線</a>。

	將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. 讓管線符合訓練文件。將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。

		model = pipeline.fit(training)

7. 驗證訓練文件以根據應用程式的進度設立檢查點。將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。

		training.show()

	如此應該會產生如下所示的輸出：

		+----------+----------+-----+
		|BuildingID|SystemInfo|label|
		+----------+----------+-----+
		|         4|     13 20|  0.0|
		|        17|      3 20|  0.0|
		|        18|     17 20|  1.0|
		|        15|      2 23|  0.0|
		|         3|      16 9|  1.0|
		|         4|     13 28|  0.0|
		|         2|     12 24|  0.0|
		|        16|     20 26|  1.0|
		|         9|      16 9|  1.0|
		|        12|       6 5|  0.0|
		|        15|     10 17|  1.0|
		|         7|      2 11|  0.0|
		|        15|      14 2|  1.0|
		|         6|       3 2|  0.0|
		|        20|     19 22|  0.0|
		|         8|     19 11|  0.0|
		|         6|      15 7|  0.0|
		|        13|      12 5|  0.0|
		|         4|      8 22|  0.0|
		|         7|      17 5|  0.0|
		+----------+----------+-----+


	返回並根據原始 CSV 檔案驗證輸出。例如，CSV 檔案中第一個資料列的資料為：

	![HVAC 資料快照](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "HVAC 資料的快照")

	請注意，實際溫度比目標溫度低的情況代表建築物處於低溫狀態。因此在訓練輸出中，第一個資料列的 [label] 值為 [0.0]，這代表建築物不是熱的。

8.  準備要做為定型模型之執行依據的資料集。方法是傳遞系統識別碼和系統年期 (在訓練輸出中以 **SystemInfo** 來代表)，然後模型會預測該系統識別碼和系統年期所屬的建築物溫度是較熱 (以 1.0 表示) 或較冷 (以 0.0 表示)。

	將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。
		
		# SystemInfo here is a combination of system ID followed by system age
		Document = Row("id", "SystemInfo")
		test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
    		.map(lambda x: Document(*x)).toDF() 

9. 最後，根據測試資料進行預測。將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。

		# Make predictions on test documents and print columns of interest
		prediction = model.transform(test)
		selected = prediction.select("SystemInfo", "prediction", "probability")
		for row in selected.collect():
		    print row

10. 您應該會看到如下所示的輸出：

		Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
		Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
		Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
		Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
		Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
		Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

	從預測的第一個資料列可看出，對於識別碼為 20 且年期為 25 年的 HVAC 系統而言，建築物將會是熱的 (**prediction=1.0**)。DenseVector (0.49999) 的第一個值對應到預測 0.0，而第二個值 (0.5001) 對應到預測 1.0。在輸出中，即使第二個值只是稍微高一點，模型仍舊顯示 **prediction=1.0**。

11. 應用程式執行完畢之後，您應該要關閉 Notebook 來釋放資源。方法是從 Notebook 的 [檔案] 功能表上，按一下 [關閉並停止]。這樣就能夠結束並關閉 Notebook。
	  	   

##<a name="anaconda"></a>使用適用於機器學習的 Anaconda scikit-learn 程式庫

HDInsight 上的 Apache Spark 叢集包含 Anaconda 程式庫。其中也包含適用於機器學習的 **scikit-learn** 程式庫。此程式庫另包含用來直接從 Jupyter Notebook 建置範例應用程式的各種資料集。如需使用 scikit-learn 程式庫的範例，請參閱 [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html)。

##<a name="seealso"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：搭配 BI 工具來使用 HDInsight 中的 Spark 以執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習：使用 HDInsight 中的 Spark 來預測食物檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

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


[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-storage]: ../hdinsight-use-blob-storage/


[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0211_2016-->