<properties 
	pageTitle="透過 HDInsight Spark 叢集使用自訂程式庫來分析網站記錄 |Microsoft Azure" 
	description="透過 HDInsight Spark 叢集使用自訂程式庫來分析網站記錄" 
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
	ms.date="01/04/2016" 
	ms.author="nitinme"/>

# 在 HDInsight Spark 中使用自訂程式庫 (Linux) 分析記錄檔

此 Notebook 示範如何使用自訂程式庫與 HDInsight 上的 Spark 來分析記錄資料。我們使用的自訂程式庫是名為 **iislogparser.py** 的 Python 程式庫。

> [AZURE.TIP]本教學課程也適用於您在 HDInsight 中所建立 Spark (Linux) 叢集上的 Jupyter Notebook。Notebook 的體驗能讓您從 Notebook 本身執行 Python 程式碼片段。若要從 Notebook 中執行本教學課程，請建立 Spark 叢集、啟動 Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)，然後執行 **Python** 資料夾下的 Notebook **透過 Spark 使用自訂 library.ipynb 來分析記錄檔**。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 將原始資料儲存為 RDD

在本節中，我們會使用與 HDInsight 中的 Apache Spark 叢集相關聯的 [Jupyter](https://jupyter.org) Notebook，來執行會處理原始範例資料，並將這些資料儲存成 Hive 資料表的作業。範例資料是所有叢集在預設情況下均有的 .csv 檔案 (hvac.csv)。

一旦將資料儲存成 Hive 資料表之後，下一節我們將使用 Power BI 和 Tableau 等 BI 工具連接 Hive 資料表。

1. 在 [Azure Preview 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE]您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [新增]，然後按一下 [Python 2]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

4. 匯入所需的模組，然後建立 Spark 和 SQL 內容。將下列程式碼片段貼到空白儲存格中，然後按 **SHIFT + ENTER**。


		import pyspark
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		%matplotlib inline
		import matplotlib.pyplot as plt
		from pyspark.sql import Row
		from pyspark.sql.types import *
		import atexit
		sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
		sqlContext = SQLContext(sc)
		atexit.register(lambda: sc.stop())


5. 使用叢集上已有的範例記錄資料來建立 RDD。您可以在 **\\HdiSamples\\HdiSamples\\WebsiteLogSampleData\\SampleLog\\909f2b.log** 上存取與叢集相關聯之預設儲存體帳戶中的資料。


		logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. 擷取範例記錄組，以確認先前的步驟已順利完成。

		logs.take(5)

	您應該會看到如下所示的輸出：

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[u'#Software: Microsoft Internet Information Services 8.0',
		 u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## 使用自訂 Python 程式庫來分析記錄資料

7. 在上述輸出中，前幾行包含標頭資訊，其餘各行則符合該標頭中所說明的結構描述。剖析這類記錄檔可能是複雜的作業。因此，我們使用自訂 Python 程式庫 (**iislogparser.py**)，它可大幅簡化這類記錄檔的剖析。根據預設，此程式庫會包含在 HDInsight 上的 Spark 叢集中。

	不過，此程式庫不在 `PYTHONPATH` 中，因此無法藉由 `import iislogparser` 之類的匯入陳述式來使用它。若要使用此程式庫，我們必須將它散發到所有的背景工作角色節點。

	然後，您必須執行下列程式碼片段，以將程式庫散發到 Spark 叢集中的所有背景工作角色節點。


		sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser` 提供一個函數 `parse_log_line`，在記錄行為標頭資料列時會傳回 `None`，在發現記錄行時則傳回 `LogLine` 類別的執行個體。使用 `LogLine` 類別，僅從 RDD 擷取記錄行：

		def parse_line(l):
		    import iislogparser
		    return iislogparser.parse_log_line(l)
		logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. 擷取幾個記錄行，以確認步驟已順利完成。

		logLines.take(2)

	輸出應該類似如下範例：

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
 		2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. `LogLine` 類別也有一些有用的方法，像是 `is_error()`，它會傳回記錄項目是否有錯誤碼。它可用來計算已擷取的記錄行中有多少錯誤，然後將所有錯誤記錄到另一個檔案。

		errors = logLines.filter(lambda p: p.is_error())
		numLines = logLines.count()
		numErrors = errors.count()
		print 'There are', numErrors, 'errors and', numLines, 'log entries'
		errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

	您應該會看到如下的輸出：

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		There are 30 errors and 646 log entries

12. 您也可以使用 **Matplotlib** 來建構資料的視覺效果。例如，如果您想要找出要求長時間執行的原因，您可以尋找平均佔用最多服務資源的檔案。下列程式碼片段會擷取耗費最多時間為要求提供服務的前 25 個資源。

		def avgTimeTakenByKey(rdd):
		    return rdd.combineByKey(lambda line: (line.time_taken, 1),
		                            lambda x, line: (x[0] + line.time_taken, x[1] + 1),
		                            lambda x, y: (x[0] + y[0], x[1] + y[1]))\
		              .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
		    
		avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

	您應該會看到如下的輸出：

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[(u'/blogposts/mvc4/step13.png', 197.5),
		 (u'/blogposts/mvc2/step10.jpg', 179.5),
		 (u'/blogposts/extractusercontrol/step5.png', 170.0),
		 (u'/blogposts/mvc4/step8.png', 159.0),
		 (u'/blogposts/mvcrouting/step22.jpg', 155.0),
		 (u'/blogposts/mvcrouting/step3.jpg', 152.0),
		 (u'/blogposts/linqsproc1/step16.jpg', 138.75),
		 (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
		 (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
		 (u'/blogposts/nested/step2.jpg', 126.0),
		 (u'/blogposts/adminpack/step1.png', 124.0),
		 (u'/BlogPosts/datalistpaging/step2.png', 118.0),
		 (u'/blogposts/mvc4/step35.png', 117.0),
		 (u'/blogposts/mvcrouting/step2.jpg', 116.5),
		 (u'/blogposts/aboutme/basketball.jpg', 109.0),
		 (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
		 (u'/blogposts/mvc4/step12.png', 106.0),
		 (u'/blogposts/linq8/step0.jpg', 105.5),
		 (u'/blogposts/mvc2/step18.jpg', 104.0),
		 (u'/blogposts/mvc2/step11.jpg', 104.0),
		 (u'/blogposts/mvcrouting/step1.jpg', 104.0),
		 (u'/blogposts/extractusercontrol/step1.png', 103.0),
		 (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
		 (u'/blogposts/mvcrouting/step21.jpg', 101.0),
		 (u'/blogposts/mvc4/step1.png', 98.0)]


13. 您也可以用繪圖的形式呈現這項資訊。此圖會依時間將記錄檔分組，以查看在任何特定時間是否有任何不尋常的延遲尖峰。

		avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
		minutes = avgTimeTakenByMinute.map(lambda pair: pair[0]).collect()
		time = avgTimeTakenByMinute.map(lambda pair: pair[1]).collect()
		plt.plot(minutes, time, marker='o', linestyle='--')
		plt.xlabel('Time (min)')
		plt.ylabel('Average time taken for request (ms)')

	您應該會看到如下的輸出：

	![Matplotlib 輸出](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Matplotlib 輸出")

14. 應用程式執行完畢之後，您應該要關閉 Notebook 來釋放資源。方法是從 Notebook 的 [檔案] 功能表上，按一下 [關閉並停止]。這樣就能夠結束並關閉 Notebook。
	

## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)

### 建立及執行應用程式

* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)

* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### 擴充功能

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0107_2016-->