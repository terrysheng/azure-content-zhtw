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
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="nitinme"/>


# 開始使用：在 Azure HDInsight (Linux) 上建立 Apache Spark 叢集，並使用 Spark SQL 執行互動式查詢

了解在 HDInsight 中建立 Apache Spark 叢集，然後使用 [Jupyter](https://jupyter.org) Notebook 對 Spark 叢集執行 Spark SQL 互動式查詢。

   ![開始使用 HDInsight 中的 Apache Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "開始使用 HDInsight 教學課程中的 Apache Spark說明的步驟：建立儲存體帳戶、建立叢集、執行 Spark SQL 陳述式")

>[AZURE.NOTE]如需目前版本的已知問題及限制清單，請參閱 [Azure HDInsight 中 Apache Spark 的已知問題及限制](hdinsight-apache-spark-jupyter-spark-sql.md)。

**必要條件：**

- **Azure 訂用帳戶**。開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **安全殼層 (SSH) 用戶端**：Linux、Unix 和 OS X 系統透過 `ssh` 命令提供 SSH 用戶端。如果是 Windows 系統，我們建議使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
    
- **安全殼層 (SSH) 金鑰 (選擇性)**：您可以使用密碼或公開金鑰，保護用來連線到叢集的 SSH 帳戶。使用密碼可以快速開始，如果您想快速建立叢集並執行一些測試作業，您應該使用這個選項。使用金鑰比較安全，但是需要額外的設定。建立生產叢集時，您可能會想使用此方法。在本文中，我們使用密碼方法。如需如何建立和使用 SSH 金鑰搭配 HDInsight 的相關指示，請參閱下列文章：

	-  從 Linux 電腦 - [從 Linux、Unix 或 OS X 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
    
	-  從 Windows 電腦 - [從 Windows 搭配使用 SSH 與以 Linux 為基礎的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-windows.md)。


## 在 HDInsight Linux 上建立 Spark 叢集

在本節中，您將建立採用 Spark 1.5.1 版的 HDInsight 3.3 版叢集。如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱〈[HDInsight 元件版本設定](hdinsight-component-versioning.md)〉。

>[AZURE.NOTE]本文章中的步驟能使用基本組態設定在 HDInsight 中建立 Apache Spark 叢集。如需其他叢集組態設定 (例如，使用其他儲存體、Azure 虛擬網路或 Hive 中繼存放區) 的相關資訊，請參閱[使用自訂選項建立 HDInsight Spark 叢集](hdinsight-hadoop-provision-linux-clusters.md)。


**建立 Spark 叢集**

1. 登入 [Azure Preview 入口網站](https://ms.portal.azure.com/)。

2. 依序按一下 [新增]、[資料 + 分析] 及 [HDInsight]。

    ![在 Azure Preview 入口網站中建立新的叢集](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.1.png "在 Azure Preview 入口網站中建立新的叢集")

3. 輸入 [叢集名稱]、針對 [叢集類型] 選取 [Hadoop]，然後從 [叢集作業系統] 下拉式功能表中選取 [Ubuntu]，再選取 Spark 的版本。如果該叢集可用，叢集名稱旁就會出現綠色勾號。

	![輸入叢集名稱和類型](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.2.png "輸入叢集名稱和類型")

4. 如果您有多個訂用帳戶，請按一下 [訂用帳戶] 項目，以選取要用於該叢集的 Azure 訂用帳戶。

5. 按一下 [資源群組] 來查看現有資源群組的清單，然後選取其中一個來建立叢集。或者按一下 [建立新項目]，然後輸入新資源群組的名稱。出現綠色勾號即表示新群組的名稱可供使用。

	> [AZURE.NOTE]如果有任何可用的資源群組，此項目即會預設為現有資源群組的其中一個群組。

6. 按一下 [認證]，然後輸入 admin 使用者的密碼。您也必須輸入 [SSH 使用者名稱]。針對 [SSH 驗證類型], ，按一下 [密碼] 並指定 SSH 使用者的密碼。按一下底部的 [選取] 以儲存認證組態。

	![提供叢集認證](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.3.png "提供叢集認證")

    > [AZURE.NOTE]SSH 可透過命令列遠端存取 HDInsight 叢集。您在此使用的使用者名稱和密碼會在透過 SSH 連接到叢集時使用。此外，SSH 使用者名稱必須是唯一的，因為該名稱會在所有 HDInsight 叢集節點上建立使用者帳戶。以下是一些保留給叢集上的服務使用的帳戶名稱，不能做為 SSH 使用者名稱︰
    >
    > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper。

	如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)


7. 按一下 [資料來源] 來選擇該叢集的現有資料來源，或建立一個新的資料來源。當您在 HDInsight 中建立 Hadoop 叢集時，您需要指定一個 Azure 儲存體帳戶。該帳戶特定的 Blob 儲存體容器將被指定為預設檔案系統，如同 Hadoop 分散式檔案系統 (HDFS)。依預設，系統會在與您指定儲存體帳戶的相同資料中心內建立 HDInsight 叢集。如需詳細資訊，請參閱＜[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]＞。

	![資料來源刀鋒視窗](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.4.png "提供資料來源組態")

	目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。使用下列資訊來了解 [資料來源] 刀鋒視窗上的項目。

	- **選取方法**：將此設為 [來自所有訂用帳戶]，即可瀏覽您所有訂用帳戶中的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。

	- **選取儲存體帳戶 / 建立新的帳戶**：按一下 [選取儲存體帳戶]，瀏覽並選取您要與叢集產生關聯的現有儲存體帳戶。或者按一下 [建立新項目] 來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可供使用，就會出現綠色勾號。

	- **選擇預設容器**：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

	- **位置**：儲存體帳戶所在或將建立的地理區域。

		> [AZURE.IMPORTANT]選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。

	按一下 [選取] 以儲存資料來源組態。

8. 按一下 [節點定價層]，來顯示將針對此叢集建立的節點相關資訊。設定該叢集所需的背景工作角色節點數目。該叢集的預估成本將會顯示在此刀鋒視窗內。

	![節點定價層刀鋒視窗](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.5.png "指定叢集節點的數目")

	按一下 [選取] 以儲存此節點定價組態。

9. 在 [新的 HDInsight 叢集] 刀鋒視窗中，確認已選取 [釘選到「開始面板」]，然後按一下 [建立]。這會建立叢集，並將該叢集磚加入到您 Azure 入口網站的開始面板。該圖示可表示該叢集正在建立，並將在建立完成後變更為 HDInsight 圖示。

	| 建立時 | 建立完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的建立指示器](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或頁面左邊的 [通知] 項目來以檢查建立進度。

10. 建立完成後，在開始面板上按一下 Spark 叢集的磚，以啟動叢集刀鋒視窗。



## <a name="jupyter"></a>使用 Jupyter Notebook 執行 Spark SQL 查詢

在本節中，您會使用 Jupyter Notebook 來針對 Spark 叢集執行 Spark SQL 查詢。

1. 在 [Azure Preview 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集磚 (如果您已將其釘選到開始面板)。您也可以按一下 [瀏覽全部] > [HDInsight 叢集]，瀏覽至您的叢集。   

2. 在 Spark 叢集刀鋒視窗中按一下 [快速連結] ，然後在 [叢集儀表板] 刀鋒視窗中按一下 [Jupyter Notebook]。出現提示時，輸入叢集的系統管理員認證。

	> [AZURE.NOTE]您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。使用您叢集的名稱取代 __CLUSTERNAME__：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 建立新的 Notebook。按一下 [新增]，然後按一下 [Python2]。

	![建立新的 Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "建立新的 Jupyter Notebook")

3. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

	![提供 Notebook 的名稱](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "提供 Notebook 的名稱")

4. 匯入所需的模組，然後建立 Spark 和 SQL 內容。將以下程式碼範例貼入空白儲存格，然後按下 **SHIFT + ENTER** 鍵。

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		# Create Spark and SQL contexts
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)

	每當您在 Jupyter 中執行作業時，網頁瀏覽器視窗標題將會顯示 Notebook 標題和 **(忙碌)** 狀態。您也會在右上角的 **Python 2** 文字旁看到實心圓。工作完成後，實心圓將變成空心圓。

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
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. 待工作順利完成後，即會顯示以下輸出。

		+----------+---------+------+
		|buildingID|temp_diff|  date|
		+----------+---------+------+
		|         4|        8|6/1/13|
		|         3|        2|6/1/13|
		|         7|      -10|6/1/13|
		|        12|        3|6/1/13|
		|         7|        9|6/1/13|
		|         7|        5|6/1/13|
		|         3|       11|6/1/13|
		|         8|       -7|6/1/13|
		|        17|       14|6/1/13|
		|        16|       -3|6/1/13|
		|         8|       -8|6/1/13|
		|         1|       -1|6/1/13|
		|        12|       11|6/1/13|
		|         3|       14|6/1/13|
		|         6|       -4|6/1/13|
		|         1|        4|6/1/13|
		|        19|        4|6/1/13|
		|        19|       12|6/1/13|
		|         9|       -9|6/1/13|
		|        15|      -10|6/1/13|
		+----------+---------+------+


6. 應用程式執行完畢之後，您應該要關閉 Notebook 來釋放資源。方法是從 Notebook 的 [檔案] 功能表上，按一下 [關閉並停止]。這樣就能夠結束並關閉 Notebook。


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

### 擴充功能

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0107_2016-->