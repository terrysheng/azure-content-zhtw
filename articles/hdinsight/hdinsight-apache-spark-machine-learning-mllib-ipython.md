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


# 機器學習 - 使用 MLLib 和 HDInsight (Linux) 上的 Spark 對食品檢查資料進行預測分析

> [AZURE.TIP] 本教學課程也適用於您在 HDInsight 中所建立 Spark (Linux) 叢集上的 Jupyter Notebook。Notebook 的體驗能讓您從 Notebook 本身執行 Python 程式碼片段。若要從 Notebook 中執行本教學課程，請建立 Spark 叢集、啟動 Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)，然後執行 **Python** 資料夾下的 Notebook **Spark 機器學習服務 - 使用 MLLib.ipynb 對食品檢查資料進行預測分析**。


本文說明如何使用 Spark 的內建機器學習程式庫 **MLLib**，對開啟的資料集執行簡單的預測分析。MLLib 是核心 Spark 程式庫之一，提供多種可用於機器學習工作的公用程式，包括具有下列用途的公用程式：

* 分類

* 迴歸

* 叢集

* 主題模型

* 奇異值分解 (SVD) 和主體元件分析 (PCA)

* 假設測試和計算範例統計資料

本文將介紹一個透過羅吉斯迴歸的簡單*分類*方法。

## 分類和羅吉斯迴歸為何？

*分類*是很常見的機器學習工作，是指將輸入資料依類別排序的程序。它是以分類演算法指出如何為您所提供的輸入資料指派「標籤」的作業。例如，試想某個機器學習演算法以股市資訊做為輸入，並且將股票分成兩個類別：該賣的股票和該留的股票。

羅吉斯迴歸是您用於分類的演算法。Spark 的羅吉斯迴歸 API 可用於*二元分類*，或用來將輸入資料歸類到兩個群組之一。如需羅吉斯迴歸的詳細資訊，請參閱 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)。

總之，羅吉斯迴歸的程序會產生一個*羅吉斯函數*，此函數可用來預測輸入向量可能屬於哪一個群組的機率。

## 我們想要在本文中完成什麼？

您將使用 Spark 對透過[芝加哥市資料入口網站](https://data.cityofchicago.org/)取得的食品檢查資料 (**Food\_Inspections1.csv**) 執行某項預測分析。此資料集包含在芝加哥進行食品檢查的相關資訊，其中包括每個受檢食品業者、發現的違規情事 (如果有的話) 和檢查結果的相關資訊。

在下列步驟中，您會開發一個模型來觀察能否通過食品檢查的標準為何。

## 開始使用 Spark MLlib 建置機器學習應用程式

1. 在 [Azure Preview 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [新增]，然後按一下 [Python 2]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

3. 開始建置機器學習服務應用程式。首先，您應設定 Pyspark 環境。若要這樣做，請將游標放在儲存格中，然後按 **SHIFT + ENTER** 鍵。


		import pyspark
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		%matplotlib inline
		import matplotlib.pyplot as plt
		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		from pyspark.sql.functions import UserDefinedFunction
		from pyspark.sql.types import *
		import atexit
		
		sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
		sqlContext = SQLContext(sc)
		atexit.register(lambda: sc.stop())


## 建構輸入資料框架

我們已有可用來對結構化資料執行轉換的 SQLContext。第一項工作是將範例資料 ((**Food\_Inspections1.csv**)) 載入 Spark SQL *資料框架*中。下列程式碼片段假設資料已上傳至與 Spark 叢集相關聯的預設儲存體容器。

1. 由於原始資料採用 CSV 格式，因此我們必須使用 Spark 內容，將檔案的每一行以非結構化文字的形式提取至記憶體中，然後您再使用 Python 的 CSV 程式庫個別剖析每一行。 


		def csvParse(s):
		    import csv
		    from StringIO import StringIO
		    sio = StringIO(s)
		    value = csv.reader(sio).next()
		    sio.close()
		    return value
		
		inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
		                .map(csvParse)


2. 現在，我們已有做為 RDD 的 CSV 檔案。我們將從 RDD 中擷取一個資料列，以了解資料的結構描述。


		inspections.take(1)


	您應該會看到如下的輸出：

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[['413707',
	      'LUNA PARK INC',
	      'LUNA PARK  DAY CARE',
	      '2049789',
	      "Children's Services Facility",
	      'Risk 1 (High)',
	      '3250 W FOSTER AVE ',
	      'CHICAGO',
	      'IL',
	      '60625',
	      '09/21/2010',
	      'License-Task Force',
	      'Fail',
	      '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
	      '41.97583445690982',
	      '-87.7107455232781',
	      '(41.97583445690982, -87.7107455232781)']]


3. 上方的輸出讓我們對輸入檔案的結構描述有了概念；檔案中包含每個業者的名稱、營業類型、地址、檢查的資料，以及地點等項目。現在，我們要選取幾個對我們的預測分析有幫助的資料行，並將結果分類為資料框架。


		schema = StructType([
		        StructField("id", IntegerType(), False), 
		        StructField("name", StringType(), False), 
		        StructField("results", StringType(), False), 
		        StructField("violations", StringType(), True)])
		
		df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)

4. 現在我們已有*資料框架* `df`，可讓我們據以執行分析。我們在資料框架中加入了 4 個相關資料行：**識別碼**、**名稱**、**結果**和**違規情事**。現在要取得資料的小型樣本：


		df.show(5)

	您應該會看到如下的輸出：

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		+------+--------------------+-------+--------------------+
	    |    id|                name|results|          violations|
	    +------+--------------------+-------+--------------------+
	    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
	    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
	    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
	    |413708|BENCHMARK HOSPITA...|   Pass|                    |
	    |413722|           JJ BURGER|   Pass|                    |
	    +------+--------------------+-------+--------------------+

## 了解資料

我們要著手了解資料集的內容為何。例如，**結果**資料行中有哪些不同的值？


	df.select('results').distinct().show()

	
您應該會看到如下的輸出：

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	+--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    
快速的視覺效果有助於我們研判這些結果的分佈。

	countResults = df.groupBy('results').count().collect()
	labels = [row.results for row in countResults]
	sizes = [row.count for row in countResults]
	colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
	plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
	plt.axis('equal')


您應該會看到如下的輸出：

    
![結果輸出](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


您可以看到，一項檢查可以有 5 個不同的結果

* 找不到該業者 
* 不合格
* 通過
* 有條件通過，以及
* 已結束營業 

我們要根據給定的違規標準，開發可以猜測食品檢查結果的模型。由於羅吉斯迴歸是一種二元分類方法，因此將資料分成兩個類別，是很合理的：**不合格**和**通過**。「有條件通過」仍屬於「通過」，因此在訓練模型時，我們會將兩種結果視為相同。具有其他結果 (「找不到業者」、「已結束營業」) 的資料沒有用處，因此我們將其從訓練集中移除。這應該沒有問題，因為這兩種類別在結果中所佔的百分比非常小。

我們繼續進行，並將現有資料框架 (`df`) 轉換為新的資料框架，其中每項檢查都以一組「標籤-違規」來表示。在我們的案例中，標籤 `0.0` 代表失敗，標籤 `1.0` 代表成功，標籤 `-1.0` 代表此二者以外的某種結果。在計算新的資料框架時，我們將此類其他結果排除在外。


	def labelForResults(s):
	    if s == 'Fail':
	        return 0.0
	    elif s == 'Pass w/ Conditions' or s == 'Pass':
	        return 1.0
	    else:
	        return -1.0
	label = UserDefinedFunction(labelForResults, DoubleType())
	labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


我們要從加上標籤的資料中擷取一個資料列，看看會是如何。


	labeledData.take(1)


您應該會看到如下的輸出：

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	[Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## 從輸入資料框架建立羅吉斯迴歸模型

我們的最後一項工作，是將加上標籤的資料轉換成可依羅吉斯迴歸進行分析的格式。羅吉斯迴歸演算法的輸入應該是一組*標籤-特性向量配對*，其中，「特性向量」是以某種方式代表輸入點的數字向量。因此，我們需要以某種方式，將半結構化、且包含許多自然語言評論的「違規情事」資料行，轉換成機器可輕易辨識的實數陣列。

可用來處理自然語言的標準機器學習方法之一，是為每個不同的字指派一個「索引」，然後將向量傳至機器學習演算法，使每個索引的值包含該字在文字字串中出現的相對頻率。

MLLib 可提供簡單的方法來執行此作業。首先，我們將「語彙基元化」每個違規字串，以取得每個字串中的每個字，然後我們將使用 `HashingTF`，將每一組語彙基元轉換成後續可傳至羅吉斯迴歸演算法以建構模型的特性向量。我們將使用「管線」循序執行前述所有步驟。


	tokenizer = Tokenizer(inputCol="violations", outputCol="words")
	hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
	lr = LogisticRegression(maxIter=10, regParam=0.01)
	pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
	
	model = pipeline.fit(labeledData)


## 以個別的測試資料集評估模型

我們可以使用先前建立的模型，根據我們所觀察的違規情事，*預測*新的檢查會有何種結果。我們已在資料集 **Food\_Inspections1.csv** 上訓練此模型。現在，我們要使用第二個資料集 **Food\_Inspections2.csv**，*評估*此模型對於新資料的強度。第二個資料集 (**Food\_Inspections2.csv**) 應已在與叢集相關聯的預設儲存體容器中。

下列程式碼片段會建立新的資料框架 **predictionsDf**，其中包含模型所產生的預測。


	testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
	             .map(csvParse) \
	             .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
	testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
	predictionsDf = model.transform(testDf)
	predictionsDf.columns


您應該會看到如下的輸出：

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------
	
	['id',
     'name',
     'results',
     'violations',
     'words',
     'features',
     'rawPrediction',
     'probability',
     'prediction']

請看其中一個預測。執行此程式碼片段：

	predictionsDf.take(1)

您會看到對於測試資料集中的第一個項目所做的預測。

`model.transform()` 方法會將相同的轉換套用至具有相同結構描述的任何新資料，並做出關於如何分類資料的預測。我們可以做一些簡單的統計，了解一下我們的預測精準度如何：


	numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
	                                      (prediction = 1 AND (results = 'Pass' OR 
	                                                           results = 'Pass w/ Conditions'))""").count()
	numInspections = predictionsDf.count()
	
	print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
	print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

輸出顯示如下：

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate


透過 Spark 使用羅吉斯迴歸，讓我們找出了英文版的違規情事說明與指定企業是否能通過食品檢查之間的關聯性，而建立了兩者關聯性的精確模型。我們可以建構最後的視覺效果，以利研判此測試的結果：

	
	failSuccess = predictionsDf.where("prediction = 0 AND results = 'Fail'").count()
	failFailure = predictionsDf.where("prediction = 0 AND results <> 'Fail'").count()
	passSuccess = predictionsDf.where("prediction = 1 AND results <> 'Fail'").count()
	passFailure = predictionsDf.where("prediction = 1 AND results = 'Fail'").count()
	labels = ['True positive', 'False positive', 'True negative', 'False negative']
	sizes = [failSuccess, failFailure, passSuccess, passFailure]
	plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
	plt.axis('equal')


您應該會看見下列輸出。

![預測輸出](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


在此圖中，「肯定」結果是指未通過的食品檢查，否定結果則是指通過的檢查。這大致上對應至 12.6% 的誤否定率和 16.0% 的誤肯定率。

## 關閉 Notebook

應用程式執行完畢之後，您應該要關閉 Notebook 來釋放資源。方法是從 Notebook 的 [檔案] 功能表上，按一下 [關閉並停止]。這樣就能夠結束並關閉 Notebook。


## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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

<!---HONumber=AcomDC_0211_2016-->