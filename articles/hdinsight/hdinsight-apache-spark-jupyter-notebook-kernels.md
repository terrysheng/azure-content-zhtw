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
	ms.date="02/17/2016" 
	ms.author="nitinme"/>


# HDInsight (Linux) 上的 Spark 叢集可供 Jupyter Notebook 使用的核心

在 HDInsight (Linux) 上的 Apache Spark 叢集包含可用來測試應用程式的 Jupyter Notebook。根據預設，Jupyter Notebook 附有 **Python2** 核心。核心是一個可執行並解譯程式碼的程式。HDInsight Spark 叢集另外還提供兩種核心，可讓您用於 Jupyter Notebook。它們是：

1. **PySpark** (適用於以 Python 撰寫的應用程式)
2. **Spark** (適用於以 Scala 撰寫的應用程式)

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

以下是使用新核心的幾項好處。

1. **預設內容**。使用可搭配 Jupyter Notebook 使用的預設 **Python2** 核心時，您必須先明確地設定 Spark、SQL 或 Hive 內容，才能開始使用您所開發的應用程式。如果您使用新的核心 (**PySpark** 或 **Spark**)，依預設會將這些內容提供給您使用。這些內容包括：

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
	
2. **Cell magic**。PySpark 核心提供一些預先定義的 “magics”，這是您可以使用 `%%` 呼叫的特殊命令 (例如 `%%MAGIC` <args>)。magic 命令必須是程式碼儲存格中的第一個字，而且允許多行的內容。magic 這個字應該是儲存格中的第一個字。在 magic 前面加入任何項目，甚至是註解，將會造成錯誤。如需有關 magic 的詳細資訊，請參閱[這裡](http://ipython.readthedocs.org/en/stable/interactive/magics.html)。

	下表列出可透過核心提供的不同 magic。

	| magic | 範例 | 說明 |
	|-----------|---------------------------------|--------------|
	| 說明 | `%%help` | 產生所有可用 magic 的表格，其中包含範例與說明 |
	| info | `%%info` | 輸出目前 Livy 端點的工作階段資訊 |
	| 設定 | `%%configure -f {"executorMemory": "1000M", "executorCores": 4`} | 設定用來建立工作階段的參數。如果已建立工作階段，而且將會卸除並重新建立該工作階段，則 force 旗標 (-f) 是必要的。如需有效參數的清單，請查看 [Livy 的 POST /sessions 要求本文](https://github.com/cloudera/livy#request-body)。參數必須以 JSON 字串傳入。 |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | 針對 sqlContext 執行 SQL 查詢。如果傳遞 `-o` 參數，則查詢的結果會當做 [Pandas](http://pandas.pydata.org/) 資料框架，保存在 %%local Python 內容中。 |
	| hive | `%%hive -o <variable name>`<br> `SHOW TABLES` | 針對 hivelContext 執行 Hive 查詢。如果傳遞 -o 參數，則查詢的結果會當做 [Pandas](http://pandas.pydata.org/) 資料框架，保存在 %%local Python 內容中。 |
	| local | `%%local`<br>`a=1` | 接下來幾行的所有程式碼將會在本機執行。程式碼必須是有效的 Python 程式碼。 |
	| 記錄檔 | `%%logs` | 輸出目前 Livy 工作階段的記錄檔。 |
	| delete | `%%delete -f -s <session number>` | 刪除目前 Livy 端點的特定工作階段。請注意，您無法刪除針對核心本身起始的工作階段。 |
	| cleanup | `%%cleanup -f` | 刪除目前 Livy 端點的所有工作階段，包括此 Notebook 的工作階段。force 旗標 -f 是必要的。 |

3. **自動視覺化**。**Pyspark** 核心會將 Hive 和 SQL 查詢的輸出自動視覺化。您可以選擇數種不同類型的視覺效果，包括資料表、圓形圖、線條、區域、長條圖。


## 使用新核心的考量

無論您使用何種核心 (Python2、PySpark 或 Spark)，讓 Notebook 持續執行，都將會耗用您的叢集資源。使用 Python2 Notebook 時，由於您會明確地建立內容，因此您也可以在結束應用程式時刪除這些內容。

但在使用 PySpark 和 Spark 核心時，由於內容會預先設定，因此您無法明確刪除內容。因此，在您剛結束 Notebook 之後，內容可能仍會執行，而使用您的叢集資源。使用 PySpark 和 Spark 核心時，理想的做法是使用 Notebook 的 [檔案] 功能表中的 [關閉並停止] 選項。這將會先刪除內容，再結束 Notebook。


## 請舉例說明

當您開啟 Jupyter Notebook 時，您會在根層級看到兩個可用資料夾。

* **PySpark** 資料夾含有使用新 **Python** 核心的範例 Notebook。
* **Scala** 資料夾含有使用新 **Spark** 核心的範例 Notebook。

您可以從 **PySpark** 或 **Spark** 資料夾開啟 **00 - [READ ME FIRST] Spark Magic Kernel Features** Notebook，以了解可用的不同 magic。您也可以使用這兩個資料夾底下提供的其他 Notebook 範例，以了解如何搭配 HDInsight Spark 叢集使用 Jupyter Notebook 完成不同的案例。

## 意見反應

新的核心已在發展階段，而且經過一段時間後將會成熟。這或許也意味著，API 可能會隨著這些核心的成熟而改變。您在使用這些新核心時如有任何意見，我們都非常樂於知道。這對於這些核心最終版本的定調，將很有幫助。您可以將您的評論/意見反應填寫在本文最後的**評論**一節底下。


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

<!---HONumber=AcomDC_0224_2016-->