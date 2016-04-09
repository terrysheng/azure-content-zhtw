<properties
	pageTitle="在 HDInsight Linux 上建立 Spark 叢集，並使用 Spark SQL 從 Jupyter 執行互動式分析 | Microsoft Azure"
	description="關於如何在 HDInsight 中快速建立 Apache Spark 叢集，然後使用 Spark SQL 從 Jupyter Notebook 執行互動式查詢的逐步指示。"
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
	ms.topic="get-started-article"
	ms.date="03/21/2016"
	ms.author="nitinme"/>


# 開始使用：在 Azure HDInsight (Linux) 上建立 Apache Spark 叢集，並使用 Spark SQL 執行互動式查詢

了解在 HDInsight 中建立 Apache Spark 叢集，然後使用 [Jupyter](https://jupyter.org) Notebook 對 Spark 叢集執行 Spark SQL 互動式查詢。

   ![開始使用 HDInsight 中的 Apache Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "開始使用 HDInsight 教學課程中的 Apache Spark說明的步驟：建立儲存體帳戶、建立叢集、執行 Spark SQL 陳述式")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**必要條件：**

- **Azure 訂用帳戶**。開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **安全殼層 (SSH) 用戶端**：Linux、Unix 和 OS X 系統透過 `ssh` 命令提供 SSH 用戶端。如果是 Windows 系統，我們建議使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
    
- **安全殼層 (SSH) 金鑰 (選擇性)**：您可以使用密碼或公開金鑰，保護用來連線到叢集的 SSH 帳戶。使用密碼可以快速開始，如果您想快速建立叢集並執行一些測試作業，您應該使用這個選項。使用金鑰比較安全，但是需要額外的設定。建立生產叢集時，您可能會想使用此方法。在本文中，我們使用密碼方法。如需如何建立和使用 SSH 金鑰搭配 HDInsight 的相關指示，請參閱下列文章：

	-  從 Linux 電腦 - [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
    
	-  從 Windows 電腦 - [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-windows.md)。


## 建立 Spark 叢集

在本節中，您將使用 Azure ARM 範本建立 HDInsight 3.3 版叢集 (Spark 1.5.1 版)。如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱〈[HDInsight 元件版本設定](hdinsight-component-versioning.md)〉。如需其他叢集建立方法，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

1. 按一下以下影像，以在 Azure 入口網站中開啟 ARM 範本。         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/zh-TW/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    ARM 範本位於公用 Blob 容器中，**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*。
   
2. 從 [參數] 刀鋒視窗，輸入下列項目：

    - ClusterName：輸入您將建立的 Hadoop 叢集名稱。
    - 叢集登入名稱和密碼：預設登入名稱是 admin。
    - SSH 使用者名稱和密碼。
    
    請記下這些值。稍後在教學課程中需要這些資訊。

    > [AZURE.NOTE] SSH 可透過命令列遠端存取 HDInsight 叢集。您在此使用的使用者名稱和密碼會在透過 SSH 連接到叢集時使用。此外，SSH 使用者名稱必須是唯一的，因為該名稱會在所有 HDInsight 叢集節點上建立使用者帳戶。以下是一些保留給叢集上的服務使用的帳戶名稱，不能做為 SSH 使用者名稱︰
    >
    > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper。

	> 如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	> * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. 按一下 [確定] 儲存參數。

4\. 在 [自訂部署] 刀鋒視窗中，按一下 [資源群組] 下拉式方塊，然後按一下 [新增] 來建立新的資源群組。資源群組是聚集叢集、相依儲存體帳戶和其他已連結資源的容器。

5\. 按一下 [法律條款]，然後按一下 [建立]。

6\. 按一下 [建立]。您將會看到新的圖格，標題為「提交範本部署的部署」。大約需要 20 分鐘的時間來建立叢集和 SQL Database。



## 使用 Jupyter Notebook 執行 Spark SQL 查詢

在本節中，您會使用 Jupyter Notebook 來針對 Spark 叢集執行 Spark SQL 查詢。根據預設，Jupyter Notebook 附有 **Python2** 核心。HDInsight Spark 叢集另外還提供兩種核心，可讓您用於 Jupyter Notebook。它們是：

* **PySpark** (適用於以 Python 撰寫的應用程式)
* **Spark** (適用於以 Scala 撰寫的應用程式)

在本文中，您將使用 PySpark 核心。在[使用 Spark HDInsight 叢集之 Jupyter Notebook 上可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)一文中，您可以詳細閱讀使用 PySpark 核心的優點。但是，使用 PySpark 核心的幾個主要優點包括：

* 您不需要設定 Spark、SQL 和 Hive 的內容。這些內容會自動為您設定。
* 您可以使用不同的 cell magic (例如 %%sql 或 %%hive) 直接執行 SQL 或 Hive 查詢，而不需要任何前置的程式碼片段。
* SQL 或 Hive 查詢的輸出會自動視覺化。

### 使用 PySpark 核心建立 Jupyter Notebook 

1. 在 [Azure 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE] 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [新增]，然後按一下 [PySpark]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

4. 您使用 PySpark 核心建立 Notebook，因此不需要明確建立任何內容。當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark、SQL 和 Hive 內容。您可以從匯入這個案例所需的類型開始。方法是將下列程式碼片段貼到儲存格中，然後按下 SHIFT + ENTER。

		from pyspark.sql.types import *
		
	每當您在 Jupyter 中執行作業時，網頁瀏覽器視窗標題將會顯示 Notebook 標題和 **(忙碌)** 狀態。您也會在右上角的 PySpark 文字旁看到一個實心圓。工作完成後，實心圓將變成空心圓。

	 ![Jupyter Notebook 工作的狀態](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Jupyter Notebook 工作的狀態")

4. 將範例資料載入暫存資料表。當您在 HDInsight 中建立 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶中 (位於 **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac**)。

	在空白儲存格中，貼上以下程式碼範例，然後按下 **SHIFT + ENTER**。此程式碼範例會將資料註冊到名為 **hvac** 的暫存資料表。

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. 由於您使用的是 PySpark 核心，因此現在可直接在您剛才使用 `%%sql` magic 建立的暫存資料表 hvac 上執行 SQL 查詢。如需 `%%sql` magic 以及可搭配 PySpark 核心使用的其他 magic 的詳細資訊，請參閱[使用 Spark HDInsight 叢集之 Jupyter Notebook 上可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)。
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. 一旦工作順利完成後，預設會顯示下列表格式輸出。

 	![查詢結果的資料表輸出](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "查詢結果的資料表輸出")

	您也可以查看其他視覺效果中的結果。例如，相同輸出的區域圖看起來會如下所示。

	![查詢結果的區域圖](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "查詢結果的區域圖")


6. 應用程式執行完畢之後，您應該要關閉 Notebook 來釋放資源。方法是從 Notebook 的 [檔案] 功能表上，按一下 [關閉並停止]。這樣就能夠結束並關閉 Notebook。

##刪除叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## 另請參閱


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

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

### 已知問題

* [Azure HDInsight 中的 Apache Spark (Linux) 的已知問題](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0330_2016-->