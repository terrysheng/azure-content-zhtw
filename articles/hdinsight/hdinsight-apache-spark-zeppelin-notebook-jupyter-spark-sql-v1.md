<properties 
	pageTitle="在 HDInsight 上佈建 Spark 叢集，並使用 Spark SQL 從 Zeppelin 和 Jupyter 執行互動式分析 | Azure" 
	description="說明如何在 HDInsight 中快速佈建 Apache Spark 叢集，接著使用 Spark SQL 從 Zeppelin 和 Jupyter Notebook 執行互動式查詢的逐步指示。" 
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
	ms.date="08/07/2015" 
	ms.author="nitinme"/>


# 快速入門：在 HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [在 HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)

了解如何使用 [快速建立] 選項在 HDInsight 中佈建 Apache Spark 叢集，然後使用網頁型的 [Zeppelin](https://zeppelin.incubator.apache.org) 和 [Jupyter](https://jupyter.org) Notebook 在 Spark 叢集上執行 Spark SQL 互動式查詢。


   ![開始使用 HDInsight 中的 Apache Spark](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.GetStartedFlow.Spark.png "開始使用 HDInsight 教學課程中的 Apache Spark說明的步驟：建立儲存體帳戶、佈建叢集、執行 Spark SQL 陳述式")

**必要條件：**

開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

##<a name="storage"></a>建立 Azure 儲存體帳戶

當您在 HDInsight 中佈建 HDInsight 叢集時，您需要指定 Azure 儲存體帳戶。您會將該帳戶的特定 Blob 儲存體容器指定為預設檔案系統。依預設，系統會在與您指定儲存體帳戶的相同資料中心內佈建 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。


**建立 Azure 儲存體帳戶**

1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下在左下角的 [**新增**]，然後輸入如圖所示的值。

	![Azure 入口網站，您可以在此入口網站中使用「快速建立」設定新的儲存體帳戶。](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.StorageAccount.QuickCreate.png "Azure 入口網站，您可以在此入口網站中使用「快速建立」設定新的儲存體帳戶。")

>[AZURE.NOTE]請務必在支援叢集的位置建立您的儲存體帳戶。

從清單中選取新儲存體帳戶，並按一下頁面底部的 [**管理存取金鑰**]。請記下 [**主要存取金鑰**] (或 [**次要存取金鑰**]，任一金鑰皆可)。稍後在教學課程中需要這些資訊。如需詳細資訊，請參閱＜[如何建立儲存體帳戶][azure-create-storageaccount]＞。
	
##<a name="provision"></a>佈建 HDInsight Spark 叢集

在本節中，您將佈建採用 Spark 1.3.1 版的 HDInsight 3.2 版叢集。如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱〈[HDInsight 元件版本設定](hdinsight-component-versioning.md)〉。

>[AZURE.NOTE]本文章中的步驟能使用基本組態設定在 HDInsight 中建立 Apache Spark 叢集。如需其他叢集組態設定 (例如使用其他儲存體、Azure 虛擬網路或 Hive 中繼存放區) 的相關資訊，請參閱[使用自訂選項佈建 HDInsight 叢集](hdinsight-apache-spark-provision-clusters.md)。


**佈建 Spark 叢集**

1. 登入 [Azure 入口網站][azure-management-portal]。 

2. 按一下在左下角的 [**新增**]，然後輸入如圖所示的值。

	![在 HDInsight 中建立 Spark 叢集](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.QuickCreateCluster.png "在 HDInsight 中建立 Spark 叢集")


##<a name="zeppelin"></a>使用 Zeppelin Notebook 執行互動式 Spark SQL 查詢

佈建叢集後，您可以使用網頁型的 Zeppelin Notebook 來針對 Spark HDInsight 叢集執行 Spark SQL 互動式查詢。在本節中，我們將使用叢集預設提供的範例資料檔案 (hvac.csv) 來執行一些互動式 Spark SQL 查詢。

>[AZURE.NOTE]叢集也預設會提供遵循以下指示建立的 Notebook。啟動 Zeppelin 後，您可以根據 **Zeppelin HVAC tutorial** 名稱來找到本 Notebook。

1. 啟動 Zeppelin Notebook。在 Azure 入口網站選取新建立的 Spark 叢集，接著在底部的入口網站工作列按一下 [Zeppelin Notebook]。出現提示時，輸入叢集的系統管理員認證。遵循頁面顯示的指示以啟動 Notebook。

2. 建立新的 Notebook。在標頭窗格中按一下 [Notebook]，然後按一下 [建立新 Note]。

	![建立新的 Zeppelin Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.CreateNewNote.png "建立新的 Zeppelin Notebook")

	在同一個頁面的 [Notebook] 標題下方，您應該會看到名稱開頭為 **Note XXXXXXXXX** 的新 Notebook。按一下新的 Notebook。

3. 在新 Notebook 的網頁上按一下標題，需要的話可以變更 Notebook 的名稱。按下 ENTER 以儲存名稱變更。此外，請確定 Notebook 標頭在右上角顯示 [已連接] 狀態。

	![Zeppelin Notebook 狀態](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.NewNote.Connected.png "Zeppelin Notebook 狀態")

4. 將範例資料載入暫存資料表。當您在 HDInsight 中佈建 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶，其路徑為 **\\HdiSamples\\SensorSampleData\\hvac**。

	將以下程式碼片段貼入新 Notebook 中預設建立的空白段落。

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	按下 **SHIFT + ENTER**，或按一下 [開始] 按鈕讓段落執行程式碼片段。段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。輸出會顯示在同一個段落的底部。螢幕擷取畫面如下所示：

	![從原始資料建立暫存資料表](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.LoadDataIntoTable.png "從原始資料建立暫存資料表")

	您也可以為每個段落提供標題。在右下角按一下 [設定] 圖示，然後按一下 [顯示標題]。

5. 您現在可以針對 **hvac** 資料表執行 Spark SQL 陳述式。將以下查詢貼入新段落。此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。按下 **SHIFT + ENTER**。

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Spark SQL 解譯器。您可以在 Notebook 標頭中的 [解譯器] 索引標籤查看已定義的解譯器。

	以下螢幕擷取畫面顯示輸出。

	![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.SparkSQLQuery1.png "使用 Notebook 執行 Spark SQL 陳述式")

	 按一下顯示選項 (以矩形反白顯示) 以針對相同輸出切換不同的表示法。按一下 [設定] 以選擇構成輸出中索引鍵和值的項目。上述螢幕擷取畫面使用 **buildingID** 做為索引鍵、平均 **temp\_diff** 做為值。

	
6. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。下一個程式碼片段示範如何在查詢中利用您可能會用來查詢的值定義 **Temp** 變數。當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	將此程式碼片段貼入新段落，然後按下 **SHIFT + ENTER**。以下螢幕擷取畫面顯示輸出。

	![使用 Notebook 執行 Spark SQL 陳述式](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.SparkSQLQuery2.png "使用 Notebook 執行 Spark SQL 陳述式")

	對於後續的查詢，您可以從下拉式清單選取新的值，然後再次執行查詢。按一下 [設定] 以選擇構成輸出中索引鍵和值的項目。上述螢幕擷取畫面使用 **buildingID** 做為索引鍵、平均 **temp\_diff** 做為值，而 **targettemp** 做為群組。

7. 重新啟動 Spark SQL 解譯器以結束應用程式。按一下頂端的 [解譯器] 索引標籤，然後針對 Spark 解譯器按一下 [重新啟動]。

	![重新啟動 Zeppelin 解譯器](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Zeppelin.Restart.Interpreter.png "重新啟動 Zeppelin 解譯器")

##<a name="jupyter"></a>使用 Jupyter Notebook 執行 Spark SQL 查詢

在本節中，您會使用 Jupyter Notebook 來針對 Spark 叢集執行 Spark SQL 查詢。

>[AZURE.NOTE]叢集也預設會提供遵循以下指示建立的 Notebook。啟動 Jupyter 後，您可以根據 **HVACTutorial.ipynb** 名稱來找到本 Notebook。

1. 啟動 Jupyter Notebook。在 Azure 入口網站選取 Spark 叢集，接著在底部的入口網站工作列按一下 [Jupyter Notebook]。出現提示時，輸入 Spark 叢集的系統管理員認證。
2. 建立新的 Notebook。按一下 [新增]，再按 [Python2]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.Jupyter.CreateNotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Note.Jupyter.Notebook.Name.png "提供 Notebook 的名稱")

4. 匯入所需的模組，然後建立 Spark 和 SQL 內容。將以下程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	每當您在 Jupyter 中執行工作時，網頁瀏覽器視窗標題會顯示 Notebook 標題和 **(忙碌)** 狀態。您也會在右上角的 **Python 2** 文字旁看到實心圓。工作完成後，實心圓將會變成空心圓。

	 ![Jupyter Notebook 工作的狀態](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Jupyter.Job.Status.png "Jupyter Notebook 工作的狀態")

4. 將範例資料載入暫存資料表。當您在 HDInsight 中佈建 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶，其路徑為 **\\HdiSamples\\SensorSampleData\\hvac**。

	將以下程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。此程式碼片段會將資料註冊到名為 **hvac** 的暫存資料表。


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. 待工作順利完成後，畫面會出現以下輸出。

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. 重新啟動核心以結束應用程式。在頂端功能表列中依序按一下 [核心] 和 [重新啟動]，然後再按一下提示字元中的 [重新啟動]。

	![重新啟動 Jupyter 核心](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/HDI.Spark.Jupyter.Restart.Kernel.png "重新啟動 Jupyter 核心")


##<a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [在 HDInsight 叢集上佈建 Spark](hdinsight-apache-spark-provision-clusters.md)
* [在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中使用 Spark 建置即時串流應用程式](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO8-->