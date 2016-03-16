<properties 
	pageTitle="使用 Livy 從遠端提交 Spark 作業 | Microsoft Azure" 
	description="了解如何使用 Livy 和 HDInsight 叢集從遠端提交 Spark 作業。" 
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


# 搭配 HDInsight (Linux) 上的 Spark 叢集利用 Livy 遠端提交 Spark 作業

Azure HDInsight 上的 Apache Spark 叢集包含 Livy，這是一個 REST 介面，可讓您從任何地方將作業遠端提交到 Spark 叢集。如需詳細文件，請參閱 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)。

您可以使用 Livy 執行互動式 Spark 殼層，或提交要在 Spark 上執行的批次作業。本文將討論如何使用 Livy 提交批次作業。下列語法會使用 Curl 對 Livy 端點發出 REST 呼叫。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- HDInsight Linux 上的 Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## 將批次作業提交至叢集

在提交批次作業之前，您必須將應用程式 jar 上傳至與叢集相關聯的叢集儲存體。您可以使用命令列公用程式 [**AzCopy**](storage/storage-use-azcopy.md) 來執行此動作。此外也有很多用戶端可用來上傳資料。您可以在[在 HDInsight 上將 Hadoop 作業的資料上傳](hdinsight-upload-data.md)中找到其詳細資訊。

	curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**範例**：

* 如果 jar 檔案位於叢集儲存體 (WASB) 上

		curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* 如果您想要在輸入檔案 (在此範例中為 input.txt) 中傳遞 jar 檔案名稱和類別名稱
		
		curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## 取得在叢集上執行之批次的相關資訊

	curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**範例**：

* 如果您想要擷取在叢集上執行的所有批次：

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* 如果您想要擷取具有指定批次識別碼的特定批次

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## 刪除批次作業

	curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**範例**：

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## 請舉例說明

在本節中，我們將透過範例了解如何使用 Livy 提交 Spark 應用程式、監視應用程式的進度，然後刪除作業。我們在此範例中使用的應用程式，就是[建立獨立 Scala 應用程式，並在 HDInsight Spark 叢集上執行](hdinsight-apache-spark-create-standalone-application.md)一文中所開發的應用程式。下列步驟假設：

* 您已將應用程式 jar 複製到與叢集相關聯的儲存體帳戶。
* 您已將 CuRL 安裝在要嘗試這些步驟的電腦上。

請執行下列步驟：

1. 我們先確認 Livy 正在叢集上執行。我們可以取得執行中的批次清單，加以確認。如果這是您第一次使用 Livy 執行作業，則應會傳回零。

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

	您應該會看到如下的輸出：

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:47:53 GMT
		< Content-Length: 34
		<
		{"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	請留意到輸出的最後一行顯示為 **total:0**，這表示沒有執行中的批次。

2. 現在，我們要提交批次作業。下列程式碼片段會使用輸入檔案 (input.txt) 傳遞 jar 名稱和類別名稱，做為參數。如果您要從 Windows 電腦執行這些步驟，建議您採用此方法。

		curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

	檔案 **input.txt** 中的參數定義如下：

		{ "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

	您應該會看到如下所示的輸出：

		< HTTP/1.1 201 Created
		< Content-Type: application/json; charset=UTF-8
		< Location: /0
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:51:30 GMT
		< Content-Length: 36
		<
		{"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	請留意到輸出的最後一行顯示為 **state:starting**。此外也顯示 **id:0**。這是批次識別碼。

3. 現在，您可以使用批次識別碼來擷取此批次的狀態。

		curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	您應該會看到如下所示的輸出：

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Fri, 20 Nov 2015 23:54:42 GMT
		< Content-Length: 509
		<
		{"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	輸出此時顯示 **state:success**，這表示作業已順利完成。

4. 現在，您可以視需要刪除批次。

		curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

	您應該會看到如下所示的輸出：

		< HTTP/1.1 200 OK
		< Content-Type: application/json; charset=UTF-8
		< Server: Microsoft-IIS/8.5
		< X-Powered-By: ARR/2.5
		< X-Powered-By: ASP.NET
		< Date: Sat, 21 Nov 2015 18:51:54 GMT
		< Content-Length: 17
		<
		{"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

	輸出的最後一行顯示批次已成功刪除。如果您刪除執行中的作業，該作業實際上就會被刪除。如果您刪除已完成的作業，無論成功與否，這將會完全刪除作業資訊。

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

### 工具和擴充功能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### 管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->