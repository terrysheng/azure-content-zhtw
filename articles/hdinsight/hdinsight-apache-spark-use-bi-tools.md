<properties 
	pageTitle="在 Azure HDInsight 上搭配使用 BI 工具和 Apache Spark | Azure" 
	description="說明如何搭配使用 Notebook 和 Apache Spark 以根據原始資料建立結構描述、將它們儲存成 Hive 資料表，然後針對 Hive 資料表使用 BI 工具來分析資料等作業的逐步指示。" 
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


# 在 Azure HDInsight 上搭配使用 BI 工具和 Apache Spark

了解如何在 Azure HDInsight 中使用 Apache Spark 來執行下列作業：

* 取得原始範例資料並儲存成 Hive 資料表
* 使用 Power BI 和 Tableau 之類的 BI 工具來分析及視覺化資料。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Apache Spark 叢集。如需相關指示，請參閱[在 Azure HDInsight 中佈建 Apache Spark 叢集](hdinsight-apache-spark-provision-clusters.md)。
- 安裝 Microsoft Spark ODBC 驅動程式的電腦。您可以到[這裡](http://go.microsoft.com/fwlink/?LinkId=616229)安裝驅動程式。
- [Power BI](http://www.powerbi.com/) 或 [Tableau Desktop](http://www.tableau.com/products/desktop) 之類的 BI 工具。您可以從 [http://www.powerbi.com/](http://www.powerbi.com/) 取得免費的 Power BI 預覽訂用帳戶。

##<a name="hivetable"></a>將原始資料儲存成 Hive 資料表

在本節，我們在 HDInsight 中使用與 Apache Spark 叢集相關聯的 [Jupyter](https://jupyter.org) Notebook 來執行處理原始範例資料並儲存成 Hive 資料表的工作。範例資料是所有叢集在預設情況下均有的 .csv 檔案 (hvac.csv)。

一旦將資料儲存成 Hive 資料表之後，下一節我們將使用 Power BI 和 Tableau 等 BI 工具連接 Hive 資料表。

1. 啟動 Jupyter Notebook。在 Azure 入口網站選取 Spark 叢集，接著在底部的入口網站工作列按一下 [**Jupyter Notebook**]。出現提示時，輸入 Spark 叢集的系統管理員認證。

2. 建立新的 Notebook。按一下 [**新增**]，然後按一下 [**Python 2**]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.CreateNotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Note.Jupyter.Notebook.Name.png "提供 Notebook 的名稱")

4. 匯入所需的模組，然後建立 Spark 和 Hive 內容。將以下程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。

		from pyspark import SparkContext
		from pyspark.sql import *
		from pyspark.sql import HiveContext

		# Create Spark and Hive contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		hiveCtx = HiveContext(sc)

	每當您在 Jupyter 中執行工作時，網頁瀏覽器視窗標題會顯示 Notebook 標題和 **(忙碌)** 狀態。您也會在右上角的 **Python 2** 文字旁看到實心圓。工作完成後，實心圓將會變成空心圓。

	 ![Jupyter Notebook 工作的狀態](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Job.Status.png "Jupyter Notebook 工作的狀態")

4. 將範例資料載入暫存資料表。當您在 HDInsight 中佈建 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶，其路徑為 **\HdiSamples\SensorSampleData\hvac**。

	將以下程式碼片段貼入空白儲存格，然後按下 **SHIFT + ENTER**。此程式碼片段會將資料註冊到名為 **hvac** 的 Hive 資料表。


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date")
		hvac = hvacParts.map(lambda p: {"Date": str(p[0]), "Time": str(p[1]), "TargetTemp": int(p[2]), "ActualTemp": int(p[3]), "BuildingID": int(p[6])})
		
		# Infer the schema and create a table		
		hvacTable = hiveCtx.inferSchema(hvac)
		hvacTable.registerAsTable("hvactemptable")
		hvacTable.saveAsTable("hvac")

5. 確認資料表已成功建立。將以下程式碼片段貼入 Notebook 中的空白儲存格，然後按下 **SHIFT + ENTER**。

		hiveCtx.sql("SHOW TABLES").show()

	您應該會看到如下的輸出：

		tableName       isTemporary
		hvactemptable   true       
		hivesampletable false      
		hvac            false

	只有 **isTemporary** 資料行是 false 的資料表代表即將儲存在 metastore 中，並且可以從 BI 工具存取的 hive 資料表。在此教學課程中，我們將連接剛剛建立的 **hvac** 資料表。

6. 請確認資料表包含預期的資料。將以下程式碼片段貼入 Notebook 中的空白儲存格，然後按下 **SHIFT + ENTER**。

		hiveCtx.sql("SELECT * FROM hvac LIMIT 10").show()
	
7. 您現在可以重新啟動核心以結束 Notebook。在頂端功能表列中依序按一下 [**核心**] 和 [**重新啟動**]，然後再按一下提示字元中的 [**重新啟動**]。

	![重新啟動 Jupyter 核心](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Jupyter.Restart.Kernel.png "重新啟動 Jupyter 核心")

##<a name="powerbi"></a>使用 Power BI 分析 Hive 資料表中的資料

將資料儲存成 Hive 資料表後，您可以使用 Power BI 來連接資料並以視覺化方式呈現，以便建立報表、儀表板等。

1. 登入 [Power BI](http://www.powerbi.com/)。

2. 在 [歡迎使用] 畫面中，按一下 [**資料庫和其他資訊**]。

	![將資料送入 Power BI](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Get.Data.png "將資料送入 Power BI")

3. 在下一個畫面中按一下 [**Spark**]，然後按一下 [**連接**]。

4. 在 Azure HDInsight 頁面的 Spark 中，提供連接 Spark 叢集的值，然後按一下 [**連接**]。

	![連接 HDInsight 上的 Spark 叢集](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Connect.Spark.png "連接 HDInsight 上的 Spark 叢集")

	建立連線之後，Power BI 會開始從 Spark 叢集將資料匯入 HDInsight。。

5. Power BI 會匯入資料並顯示新的儀表板。新的資料集也會加入 [**資料集**] 標題下方。按一下儀表板中的 Spark 磚，開啟視覺化資料所需的工作表。

	![Power BI 儀表板上的 Spark 磚](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Tile.png "Power BI 儀表板上的 Spark 磚")

6. 請注意，右側的 [**欄位**] 清單會列出稍早建立的 **hvac** 資料表。展開資料表，查看資料表中的欄位是否與稍早在 Notebook 中定義的欄位相同。

	  ![列出 Hive 資料表](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Display.Tables.png "列出 Hive 資料表")

7. 建置視覺效果，顯示每棟建築物之目標溫度和實際溫度間的差異。若要這麼做，請拖放 [**軸**] 下方的 [**BuildingID**] 欄位，以及 [**值**] 下方的 [**ActualTemp**]/[**TargetTemp**] 欄位。

	![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.1.png "建立視覺效果")

	此外，請選取 **區域對應** \(顯示為紅色) 以視覺化資料。

8. 依預設，視覺效果會顯示 **ActualTemp** 和 **TargetTemp** 的總和。對於這兩個欄位，從下拉式清單選取 [**平均**] 可取得這兩項建置之實際和目標溫度的平均值。

	![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.2.png "建立視覺效果")

9. 資料的視覺效果應該與以下範例相似。在視覺效果上移動游標可取得相關資料的工具提示。

	![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.PowerBI.Visual.3.png "建立視覺效果")

10. 在上方的功能表按一下 [**儲存**] 並提供報告名稱。您也可以釘選視覺效果。釘選視覺效果時，系統會將它儲存在儀表板上，以便您追蹤最的新值。

	您可以針對同一個資料集加入滿足需求數量的視覺效果，並將它們釘選在儀表板上以取得資料快照。此外，HDInsight 上的 Spark 叢集已透過直接連線連接 Power BI。這表示 Power BI 隨時能取得叢集的最新資訊，因此您不需要排程資料集重新整理。

##<a name="tableau"></a>使用 Tableau Desktop 分析 Hive 資料表中的資料
	
1. 啟動 Tableau Desktop。在左窗格中，從要連接的伺服器清單按一下 [**Spark SQL**]。

2. 在 Spark SQL 連線對話方塊中，依照下圖所示提供值，然後按一下 [**確定**]。

	![連接 Spark 叢集](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Connect.png "連接 Spark 叢集")

	唯有當您已在電腦上安裝 [Microsoft Spark ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229)時，驗證下拉式清單才會將 **Windows** **Azure HDInsight Service** 列示為選項。

3. 在下一個畫面中，從 [**結構描述**] 下拉式清單按一下 [**尋找**] 圖示，然後再按一下 [**預設**]。

	![尋找結構描述](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Schema.png "尋找結構描述")

4. 對於 [**資料表**] 欄位，再按一下 [**尋找**] 圖示以列出叢集內的所有 Hive 資料表。您應該會看到稍早使用 Notebook 建立的 **hvac** 資料表。

	![尋找資料表](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Find.Table.png "尋找資料表")

5. 將資料表拖放到右側頂端的方塊。Tableau 會匯入資料，並以紅色方塊反白顯示結構描述。

	![將資料表加入 Tableau](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Table.png "將資料表加入 Tableau")

6. 按一下左下方的 [**工作表 1**] 索引標籤。針對每個日期，製作出顯示所有建築物之平均目標溫度和實際溫度的視覺效果。將 [**日期**] 和 [**建築物識別碼**] 拖曳到 [**資料行**]，將 [**實際溫度**]/[**目標溫度**] 拖曳到 [**資料列**]。在 [**標記**] 下方選取 [**區域**]，以使用區域對應視覺效果。

	 ![新增視覺效果的欄位](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Drag.Fields.png "新增視覺效果的欄位")

7. 根據預設，溫度欄位以彙總形式顯示。如果您想要改為顯示平均溫度，可以從下拉式清單執行操作，如下所示。

	![採取平均溫度](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Temp.Avg.png "採取平均溫度")

8. 您也可以將溫度對應加諸在其他對應之上，凸顯目標溫度和實際溫度之間的差異。將滑鼠移動到下方區域對應的角落，直到控點圖形上方出現反白顯示的紅色圓圈為止。將對應拖曳到上方的其他對應上，當您看到圖形出現反白顯示的紅色矩形時放開滑鼠。

	![合併對應](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Merge.png "合併對應")

	 資料視覺效果應該出現如下所示的變更：

	![視覺效果](./media/hdinsight-apache-spark-use-bi-tools/HDI.Spark.Tableau.Final.Visual.png "視覺效果")
	 
9. 按一下 [**儲存**] 以儲存工作表。您可以建立儀表板，並在儀表板中加入一個或多個工作表。

##<a name="seealso"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [快速入門：在 HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
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

<!---HONumber=August15_HO6-->