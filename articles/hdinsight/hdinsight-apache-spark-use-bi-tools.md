<properties 
	pageTitle="在 Azure HDInsight 上搭配使用 BI 工具和 Apache Spark | Microsoft Azure" 
	description="說明如何搭配使用 Notebook 和 Apache Spark 以根據原始資料建立結構描述、將它們儲存成 Hive 資料表，然後針對 Hive 資料表使用 BI 工具來分析資料等作業的逐步指示。" 
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
	ms.date="03/21/2016" 
	ms.author="nitinme"/>


# 在 Azure HDInsight 上搭配使用 BI 工具和 Apache Spark (Linux)

了解如何在 Azure HDInsight 中使用 Apache Spark 來執行下列作業：

* 取得原始範例資料並儲存成 Hive 資料表
* 使用 Power BI 和 Tableau 之類的 BI 工具來分析及視覺化資料。

> [AZURE.TIP] 本教學課程也適用於您在 HDInsight 中所建立 Spark (Linux) 叢集上的 Jupyter Notebook。Notebook 的體驗能讓您從 Notebook 本身執行 Python 程式碼片段。如要從 Notebook 中執行本教學課程，請建立 Spark 叢集、啟動 Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)，然後執行 **Python** 資料夾中的 Notebook [搭配 HDInsight.ipynb 上的 Apache Spark 來使用 BI 工具]。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
- 一台已安裝 Microsoft Spark ODBC 驅動程式的電腦 (HDInsight 上的 Spark 需要此電腦才能使用 Tableau)。您可以前往[這裡](http://go.microsoft.com/fwlink/?LinkId=616229)來安裝驅動程式。
- BI 工具，例如 [Power BI](http://www.powerbi.com/) 或 [Tableau Desktop](http://www.tableau.com/products/desktop)。您可以從 [http://www.powerbi.com/](http://www.powerbi.com/) 取得 Power BI 的免費預覽訂用帳戶。

##<a name="hivetable"></a>將原始資料儲存成 Hive 資料表

在本節中，我們會使用與 HDInsight 中的 Apache Spark 叢集相關聯的 [Jupyter](https://jupyter.org) Notebook，來執行會處理原始範例資料，並將這些資料儲存成 Hive 資料表的工作。範例資料是所有叢集在預設情況下均有的 .csv 檔案 (hvac.csv)。

一旦將資料儲存成 Hive 資料表之後，下一節我們將使用 Power BI 和 Tableau 等 BI 工具連接 Hive 資料表。

1. 在 [Azure 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [**新建**]，然後按一下 [**PySpark**]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

4. 您使用 PySpark 核心建立 Notebook，因此不需要明確建立任何內容。當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark、SQL 和 Hive 內容。首先，您可以匯入此案例所需的類型。若要這樣做，請將游標放在儲存格中，然後按 **SHIFT + ENTER** 鍵。

		from pyspark.sql import *
		
	
5. 將範例資料載入暫存資料表。當您在 HDInsight 中建立 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶中 (位於 **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**)。

	將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。此程式碼片段會將資料註冊到名為 **hvac** 的 Hive 資料表中。


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create a schema for our data
		Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
		hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
		
		# Infer the schema and create a table       
		hvacTable = hiveContext.createDataFrame(hvac)
		hvacTable.registerTempTable('hvactemptable')
		dfw = DataFrameWriter(hvacTable)
		dfw.saveAsTable('hvac')

5. 確認資料表已成功建立。您可以使用 `%%hive` magic 直接執行 Hive 查詢。如需有關 `%%hive` magic 及 PySpark 核心提供的其他 magic 的詳細資訊，請參閱[使用 Spark HDInsight 叢集之 Jupyter Notebook 上可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)。

		%%hive
		SHOW TABLES

	您應該會看到如下的輸出：

		+---------------+-----------+
		|      tableName|isTemporary|
		+---------------+-----------+
		|  hvactemptable|       true|
		|hivesampletable|      false|
		|           hvac|      false|
		+---------------+-----------+


	只有 **isTemporary** 資料行為 false 的資料表，為將會儲存在中繼存放區，且可以從 BI 工具存取的 Hive 資料表。在此教學課程中，我們會連線到剛剛建立的 **hvac** 資料表。

6. 請確認資料表包含預期的資料。將下列程式碼片段貼到 Notebook 的空白儲存格中，然後按下 **SHIFT + ENTER**。

		%%hive
		SELECT * FROM hvac LIMIT 10
	
7. 現在，您可以關閉 Notebook 來釋放資源。方法是從 Notebook 的 [檔案] 功能表上，按一下 [關閉並停止]。這樣就能夠結束並關閉 Notebook。

##<a name="powerbi"></a>使用 Power BI 分析 Hive 資料表中的資料

將資料儲存成 Hive 資料表後，您可以使用 Power BI 來連接資料並以視覺化方式呈現，以便建立報表、儀表板等。

1. 登入 [Power BI](http://www.powerbi.com/)。

2. 在 [歡迎使用] 畫面中，按一下 [資料庫和其他資訊]。

	![將資料送入 Power BI](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "將資料送入 Power BI")

3. 在下一個畫面中，按一下 [Azure HDInsight 上的 Spark]，然後按一下 [連線]。在畫面出現提示時，輸入叢集的 URL (`mysparkcluster.azurehdinsight.net`)，以及要連線到叢集所需的認證。

	建立連線之後，Power BI 會開始從 Spark 叢集將資料匯入 HDInsight。。

5. Power BI 會匯入資料，並在 [資料集] 標頭下方新增 Spark 資料集。按一下資料集來開啟新的工作表，以便將資料視覺化。您也可以把工作表儲存成報告。如要儲存工作表，請按一下 [檔案] 功能表中的 [儲存]。

	![Power BI 儀表板上的 Spark 磚](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Power BI 儀表板上的 Spark 磚")

6. 請注意，右側的 [欄位] 清單會列出您先前建立的 **hvac** 資料表。展開資料表，查看資料表中的欄位是否與稍早在 Notebook 中定義的欄位相同。

	  ![列出 Hive 資料表](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "列出 Hive 資料表")

7. 建置視覺效果，顯示每棟建築物之目標溫度和實際溫度間的差異。請選取**區域圖**圖示 (在紅色方框中) 來將資料視覺化。如要定義軸線，請把 [BuildingID] 欄位拖放到 [軸] 的下方，然後把 [ActualTemp]/[**TargetTemp**] 欄位拖放到 [值] 的下方。

	![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "建立視覺效果")


8. 根據預設，視覺效果會顯示 **ActualTemp** 和 **TargetTemp** 的總和。對於這兩個欄位，請選取下拉式清單中的 [平均]，來取得這兩棟建築物的實際和目標溫度的平均值。

	![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
    
9. 資料的視覺效果應該與以下範例相似。在視覺效果上移動游標可取得相關資料的工具提示。

	![建立視覺效果](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

10. 按一下頂端功能表的 [儲存]，並提供報告名稱。您也可以釘選視覺效果。釘選視覺效果時，系統會將它儲存在儀表板上，以便您追蹤最的新值。

	您可以針對同一個資料集加入滿足需求數量的視覺效果，並將它們釘選在儀表板上以取得資料快照。此外，HDInsight 上的 Spark 叢集已透過直接連線連接 Power BI。這表示 Power BI 隨時能取得叢集的最新資訊，因此您不需要排程資料集重新整理。

##<a name="tableau"></a>使用 Tableau Desktop 來分析 Hive 資料表中的資料
	
1. 啟動 Tableau Desktop。在左窗格上可連線的伺服器清單中，按一下 [Spark SQL]。如果左窗格沒有預設列出 Spark SQL，您可以按一下 [更多伺服器] 來尋找它。 

2. 在 Spark SQL 連線對話方塊中，提供下圖所示的值，然後按一下 [確定]。

	![連接 Spark 叢集](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "連接 Spark 叢集")

	您必須先在電腦上安裝 [Microsoft Spark ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229)，驗證下拉式清單中才會出現「Microsoft Azure HDInsight 服務」。

3. 在下一個畫面中，按一下 [結構描述] 下拉式清單的尋找圖示，然後按一下 [預設]。

	![尋找結構描述](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "尋找結構描述")

4. 對於 [資料表] 欄位，再次按一下尋找圖示以列出叢集中所有可用的 Hive 資料表。您應該會看到自己先前使用 Notebook 建立的 **hvac** 資料表。

	![尋找資料表](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "尋找資料表")

5. 將資料表拖放到右側頂端的方塊。Tableau 會匯入資料，並以紅色方塊反白顯示結構描述。

	![將資料表加入 Tableau](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "將資料表加入 Tableau")

6. 按一下左下方的 [Sheet1] 索引標籤。針對每個日期，製作出顯示所有建築物之平均目標溫度和實際溫度的視覺效果。請把 [日期] 和 [建築物識別碼] 拖放到 [資料行]中，然後把將 [實際溫度]/[**目標溫度**] 拖放到 [資料列] 中。請選取 [標記] 下方的 [區域]，以使用區域對應圖的視覺效果。

	 ![新增視覺效果的欄位](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "新增視覺效果的欄位")

7. 根據預設，溫度欄位以彙總形式顯示。如果您想要改為顯示平均溫度，可以從下拉式清單執行操作，如下所示。

	![採取平均溫度](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "採取平均溫度")

8. 您也可以將溫度對應加諸在其他對應之上，凸顯目標溫度和實際溫度之間的差異。將滑鼠游標移動到區域對應圖下半部的角落，直到控點出現為止 (如紅色圓圈中所示)。把對應圖拖曳到另一個對應圖的頂端，然後在您看到紅色圓圈中所示的形狀時，放開滑鼠按鍵。

	![合併對應](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "合併對應")

	 資料視覺效果應該出現如下所示的變更：

	![視覺效果](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "視覺效果")
	 
9. 按一下 [儲存] 以儲存工作表。您可以建立儀表板，並在儀表板中加入一個或多個工作表。

##<a name="seealso"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和機器學習：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0330_2016-->