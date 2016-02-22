<properties 
	pageTitle="在 HDInsight 中搭配使用事件中樞和 Apache Spark 來處理串流資料 | Microsoft Azure" 
	description="說明如何將資料流傳送到 Azure 事件中樞，接著再使用 Scala 應用程式於 Spark 中接收這些事件的逐步指示" 
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


# Spark Streaming：在 HDInsight 上使用 Apache Spark 處理來自 Azure 事件中樞的事件 (Linux)

Spark Streaming 能擴充核心的 Spark API，建置可調整、高輸送量、容錯的串流處理應用程式。資料能擷取自許多來源。在本文中，我們使用 Azure 事件中樞來擷取資料。事件中樞是可高度調整的擷取系統，每秒可以吸收數以百萬計的事件。

在本教學課程中，您將學習如何建立 Azure 事件中樞、使用以 Java 撰寫的主控台應用程式將訊息擷取到事件中樞，以及使用以 Scala 撰寫的 Spark 應用程式平行擷取它們。此應用程式會取用透過事件中樞串流處理的資料，並將其路由傳送至不同的輸出 (Azure 儲存體 Blob、Hive 資料表和 SQL 資料表)。

> [AZURE.NOTE] 若要遵循這篇文章中的指示，您必須使用兩種版本的 Azure 入口網站。若要建立事件中樞，您會用到 [Azure 入口網站](https://manage.windowsazure.com)。若要使用 HDInsight Spark 叢集，您會用到 [Azure Preview 入口網站](https://ms.portal.azure.com/)。

**必要條件：**

您必須滿足以下條件：

- Azure 訂用帳戶。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Apache Spark 叢集。如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
- Oracle Java Development Kit。您可以從[這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)加以安裝。
- Java IDE。本文使用 IntelliJ IDEA 15.0.1。您可以從[這裡](https://www.jetbrains.com/idea/download/)加以安裝。
- 適用於 SQL Server 4.1 版或更新版本的 Microsoft JDBC 驅動程式。要將事件資料寫入 SQL Server 資料庫中，必須要有此項目。您可以從[這裡](https://msdn.microsoft.com/sqlserver/aa937724.aspx)加以安裝。
- Azure SQL Database。如需指示，請參閱[快速建立 SQL 資料庫](sql-database/sql-database-get-started.md)

## 此解決方案有哪些功能？

以下是串流解決方案的運作流程：

1. 建立會接收事件串流的 Azure 事件中樞。

2. 執行會產生事件，並將其推送至 Azure 事件中樞的本機獨立應用程式。會執行此作業的範例應用程式發佈於：[https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)。

2. 在 Spark 叢集上，從遠端執行會從 Azure 事件中樞讀取串流事件，並將其推送至不同位置 (Azure Blob、Hive 資料表和 SQL 資料庫資料表) 的串流應用程式。

## 建立 Azure 事件中樞

1. 從 [Azure 入口網站](https://manage.windowsazure.com)選取 [新增] > [服務匯流排] > [事件中樞] > [自訂建立]。

2. 在 [加入新的事件中樞] 畫面中輸入 [事件中樞名稱]、選取要建立中樞的 [區域]，然後建立新的命名空間或選取現有的命名空間。按一下 [箭頭] 以繼續。

	![精靈頁面 1](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "建立 Azure 事件中樞")

	> [AZURE.NOTE] 您應該選取與 HDInsight 中 Apache Spark 叢集相同的**位置**，以便降低延遲的情況和成本。

3. 在 [設定事件中樞] 畫面中，輸入 [資料分割計數] 及 [訊息保留期] 的值，然後按一下核取記號。在此範例中，資料分割計數使用 10，訊息保留使用 1。請記下資料分割計數，因為您稍後會用到這個值。

	![精靈頁面 2](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "指定事件中樞的資料分割大小和保留天數")

4. 按一下您建立的事件中樞，再按一下 [設定]，然後為事件中樞建立兩個存取原則。

	<table>
<tr><th>名稱</th><th>權限</th></tr>
<tr><td>mysendpolicy</td><td>傳送</td></tr>
<tr><td>myreceivepolicy</td><td>接聽</td></tr>
</table>建立權限之後，在頁面底部選取**儲存**圖示。這會建立共用存取原則，可用來傳送 (**mysendpolicy**) 給及接聽 (**myreceivepolicy**) 此事件中樞。

	![原則](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "建立事件中樞原則")

	
5. 在相同頁面上，記下針對這兩個原則產生的原則金鑰。請儲存這些金鑰，因為稍後會用到。

	![原則金鑰](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "儲存原則金鑰")

6. 在 [儀表板] 頁面上，按一下底部的 [連接資訊]，以便使用兩個原則來擷取及儲存事件中樞的連接字串。

	![原則金鑰](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "儲存原則連接字串")

## 使用 Scala 應用程式將訊息傳送至事件中樞

在本節中，您會使用獨立的本機 Scala 應用程式，將事件串流傳送至您在先前的步驟中建立的 Azure 事件中樞。此應用程式可從 GitHub 取得，網址是：[https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)。以下步驟假設您已分接此 GitHub 儲存機制。

1. 在 IntelliJ IDEA 中，開啟應用程式 **EventhubsSampleEventProducer**。
	
2. 建置專案。在 [建置] 功能表中，按一下 [建立專案]。輸出 jar 會建立在 **\\out\\artifacts** 下。

>[AZURE.TIP] 您也可以使用 IntelliJ IDEA 提供的選項，直接從 GitHub 儲存機制建立專案。若要了解如何使用該方法，請參考下一節中的指示。請注意，下一節所說明的步驟，有許多並不適用於您在此步驟中建立的 Scala 應用程式。例如：

> * 您將無須更新 POM 以包含 Spark 版本。這是因為建立此應用程式時並不需要倚賴 Spark。
> * 您無須將某些相依性 jar 新增至專案程式庫。這是因為此專案並不需要這些 jar。

## 更新用來接收事件的 Scala 串流應用程式

會接收事件，並將其路由傳送至不同目的地的範例 Scala 應用程式，可在下列位置取得：[https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)。請遵循下列步驟來更新應用程式，並建立輸出 jar。

1. 啟動 IntelliJ IDEA，並在啟動畫面中選取 [從版本控制簽出]，然後按一下 [Git]。
		
	![從 Git 取得來源](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)

2. 在 [複製儲存機制] 對話方塊中，提供要從中複製之 Git 儲存機制的 URL、指定要複製到的目錄，然後按一下 [複製]。

	![從 Git 複製](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)

	
3. 依照提示操作，直到專案複製完成。按 **Alt + 1** 以開啟 [專案檢視]。其內容應如下所示。

	![專案檢視](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
	
4. 開啟 pom.xml，並確定 Spark 版本是正確的。在 <properties> 節點下尋找下列程式碼片段，並確認 Spark 版本。

		<scala.version>2.10.4</scala.version>
    	<scala.compat.version>2.10.4</scala.compat.version>
    	<scala.binary.version>2.10</scala.binary.version>
    	<spark.version>1.5.1</spark.version>

	請確定 **spark.version** 的值設為 **1.5.1**。

5. 應用程式需要兩個相依性 jar：

	* **EventHub 接收者 jar**。必須要有此項目，Spark 才能從事件中樞接收訊息。此 jar 位於您的 Spark Linux 叢集上：`/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.2.2.3.3.1-7-jar-with-dependencies.jar`。您可以使用 pscp 將 jar 複製到您的本機電腦。

			pscp sshuser@mysparkcluster-ssh.azurehdinsight.net:/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.2.2.3.3.1-7-jar-with-dependencies.jar C:/eventhubjar

		這會將 jar 檔案從 Spark 叢集複製到您的本機電腦上。

	* **JDBC 驅動程式 jar**。必須要有此項目，才能將接收自事件中樞的訊息寫入至 Azure SQL 資料庫。您可以從[這裡](https://msdn.microsoft.com/zh-TW/sqlserver/aa937724.aspx)下載此 jar 檔案的 4.1 版或更新版本。
	

		在專案程式庫中新增這些 jar 的參考。執行下列步驟：

		1. 在已開啟應用程式的 [IntelliJ IDEA] 視窗中，依序按一下 [檔案]、[專案結構] 和 [程式庫]。 

			![新增遺失的相依性](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "新增遺失的相依性 jar")

			按一下 [新增] 圖示 (![新增圖示](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png))，再按一下 [Java]，然後導覽至您下載 EventHub 接收者 jar 的位置。依照提示，將 jar 檔案新增至專案程式庫。

		1. 重複前述步驟，也將 JDBC jar 新增至專案程式庫。
	
			![新增遺失的相依性](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "新增遺失的相依性 jar")

		1. 按一下 [Apply (套用)]。

6. 建立輸出 jar 檔案。請執行下列步驟：
	1. 在 [專案結構] 對話方塊中，按一下 [構件]，然後按一下加號。在快顯對話方塊中按一下 [JAR]，然後按一下 [從具有相依性的模組]。

		![建立 JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)

	1. 在 [從模組建立 JAR] 對話方塊中，對 [主要類別] 按一下省略符號 (![省略符號](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png) )。

	1. 在 [選取主要類別] 對話方塊中，選取任何可用的類別，然後按一下 [確定]。

		![建立 JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)

	1. 在 [從模組建立 JAR] 對話方塊中，確定已選取 [擷取至目標 JAR]，然後按一下 [確定]。這會建立具有所有相依性的單一 JAR。

		![建立 JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)

	1. [輸出配置] 索引標籤會列出所有納入 Maven 專案中的 jar。您可以選取並刪除 Scala 應用程式未直接依存的 jar。對於我們在此處建立的應用程式，您可以移除最後一個 (**microsoft-spark-streaming-examples 編譯輸出**) 以外的所有 jar。選取要刪除的 jar，然後按一下 [刪除] 圖示 (![刪除圖示](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png))。

		![建立 JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)

		請確實選取 [在建置時建立] 方塊，以確保在每次建置或更新專案時都會建立 jar。依序按一下 [套用] 及 [確定]。

	1. 在 [輸出配置] 索引標籤中的 [可用的元素] 方塊右下方，會有兩個您先前新增至專案程式庫的相依性 jar。您必須將它們新增至 [輸出配置] 索引標籤。以滑鼠右鍵按一下每個 jar 檔案，然後按一下 [擷取到輸出根目錄中]。

		![擷取相依性 jar](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)

		針對其他相依性 jar，也重複執行此步驟。[輸出配置] 索引標籤此時應顯示如下。

		![最終輸出索引標籤](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)

		在 [專案結構] 對話方塊中，按一下 [套用]，然後按一下 [確定]。

	1. 在功能表列中按一下 [建置，然後按一下 [建立專案]。您也可以按一下 [建置構件]，以建立 jar。輸出 jar 會建立在 **\\out\\artifacts** 下。

		![建立 JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## 使用 Livy 在 Spark 叢集上遠端執行應用程式

我們將使用 Livy，在 Spark 叢集上從遠端執行串流應用程式。如需如何搭配使用 Livy 與 HDInsight Spark 叢集的詳細討論，請參閱[從遠端將作業提交到 Azure HDInsight 上的 Apache Spark 叢集](hdinsight-apache-spark-livy-rest-interface.md)。您必須先完成若干作業，才能開始執行遠端作業，使用 Spark 進行事件串流：

1. 啟動本機獨立應用程式以產生事件，並將其傳送至事件中樞。請使用下列命令來執行此動作：

		java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. 將串流 jar (**microsoft-spark-streaming-examples.jar**) 複製到與叢集相關聯的 Azure Blob 儲存體。如此，jar 即可供 Livy 存取。您可以使用命令列公用程式 [**AzCopy**](storage/storage-use-azcopy.md) 來執行此動作。此外也有很多用戶端可用來上傳資料。您可以在[在 HDInsight 上將 Hadoop 作業的資料上傳](hdinsight-upload-data.md)中找到其詳細資訊。

3. 將 CURL 安裝在您用來執行這些應用程式的電腦上。我們使用 CURL 來叫用 Livy 端點，以從遠端執行作業。

### 執行應用程式，以將事件以文字的形式接收到 Azure 儲存體 Blob 中

開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令 (取代使用者名稱/密碼與叢集名稱)：

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputBlob.txt** 中的參數定義如下：

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

我們要了解，輸入檔案中的參數為何：

* **file** 是與叢集相關聯的 Azure 儲存體帳戶上的應用程式 jar 檔案的路徑。
* **className** 是 jar 中的類別名稱。
* **args** 是類別所需的引數清單
* **numExecutors** 是 Spark 用來執行串流應用程式的核心數目。此數目一律至少應為事件中樞資料分割數目的兩倍。
* **executorMemory**、**executorCores**、**driverMemory** 是用來將必要資源指派給串流應用程式的參數。

>[AZURE.NOTE] 您不需要建立做為參數的輸出資料夾 (EventCheckpoint、EventCount/EventCount10)。串流應用程式會為您建立。
	
執行命令時，您應該會看到如下的輸出：

	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=UTF-8
	< Location: /18
	< Server: Microsoft-IIS/8.5
	< X-Powered-By: ARR/2.5
	< X-Powered-By: ASP.NET
	< Date: Tue, 01 Dec 2015 05:39:10 GMT
	< Content-Length: 37
	<
	{"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

請記下位於輸出中最後一行的批次識別碼 (在此範例中為 '1')。若要確認應用程式已成功執行，您可以查看與叢集相關聯的 Azure 儲存體帳戶，您應該會看到該處已建立 **/EventCount/EventCount10** 資料夾。此資料夾應包含相關 blob，其中擷取在為參數 **batch-interval-in-seconds** 指定的時段內所處理的事件數目。

應用程式會繼續執行，直到您加以刪除。若要這麼做，請使用下列命令：

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### 執行應用程式，以將事件以 JSON 的形式接收到 Azure 儲存體 Blob 中

開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令 (取代使用者名稱/密碼與叢集名稱)：

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputJSON.txt** 中的參數定義如下：

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

這些參數類似於您在先前的步驟中為文字輸出指定的參數。同樣地，您不需要建立做為參數的輸出資料夾 (EventCheckpoint、EventCount/EventCount10)。串流應用程式會為您建立。

 在執行命令之後，您可以查看與叢集相關聯的 Azure 儲存體帳戶，您應該會看到該處已建立 **/EventStore10** 資料夾。開啟任何以 **part-** 開頭的檔案，您應該會看到以 JSON 格式處理的事件。

### 執行應用程式，以將事件接收到 Hive 資料表中

若要執行會將事件串流至 Hive 資料表中的應用程式，您需要一些其他元件。它們是：

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

這些 **.Jar** 檔案位於您的 HDInsight Spark 叢集上：`/usr/hdp/current/spark-client/lib`。**hive-site.xml** 位於 `/usr/hdp/current/spark-client/conf`。

您可以使用 [WinScp](http://winscp.net/eng/download.php) 將這些檔案從叢集複製到您的本機電腦。接著，您可以使用工具，將這些檔案複製到與叢集相關聯的儲存體帳戶。如需關於如何將檔案上傳至儲存體帳戶的詳細資訊，請參閱[在 HDInsight 上將 Hadoop 作業的資料上傳](hdinsight-upload-data.md)。

將檔案複製到您的 Azure 儲存體帳戶之後，請開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令 (取代使用者名稱/密碼與叢集名稱)：

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputHive.txt** 中的參數定義如下：

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

這些參數類似於您在先前的步驟中為文字輸出指定的參數。同樣地，您不需要建立做為參數的輸出資料夾 (EventCheckpoint、EventCount/EventCount10) 或輸出 Hive 資料表 (EventHiveTable10)。串流應用程式會為您建立。請注意，**jars** 和 **files** 選項會包含您已複製到儲存體帳戶的 .jar 檔案和 hive-site.xml 的路徑。

若要確認 Hive 資料表已成功建立，您可以 SSH 到叢集中，並執行 Hive 查詢。如需指示，請參閱[利用 SSH 搭配使用 Hive 與 HDInsight 中的 Hadoop](hdinsight-hadoop-use-hive-ssh.md)。當您使用 SSH 連線之後，您可以執行下列命令，以確認 Hive 資料表 **EventHiveTable10** 已建立。

	show tables;

您應該會看到如下所示的輸出：

	OK
	eventhivetable10
	hivesampletable

您也可以執行 SELECT 查詢，以檢視資料表的內容。

	SELECT * FROM eventhivetable10 LIMIT 10;

您應該會看到如下的輸出：

	ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
	sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
	o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
	TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
	HKCpPlWFWAJILwR69MAq863nCWYzDEw6
	Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
	85dRppSBSbZgThLr1s0GMgKqynDUqudr
	5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
	ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
	vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
	Time taken: 4.434 seconds, Fetched: 10 row(s)


### 執行應用程式，以將事件接收到 Azure SQL Database 資料表中

執行此步驟之前，請先確定您已建立 Azure SQL Database。您的資料庫名稱、資料庫伺服器名稱和資料庫系統管理員認證都必須要有值，以做為參數。但您不需要建立資料庫資料表。串流應用程式會為您建立。

開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令：

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputSQL.txt** 中的參數定義如下：

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

若要驗證應用程式是否順利執行，您可以使用 SQL Server Management Studio 連接到 Azure SQL Database。如需如何執行該動作的指示，請參閱[使用 SQL Server Management Studio 連接到 SQL Database](sql-database/sql-database-connect-query-ssms)。連接到資料庫之後，您可以導覽至串流應用程式所建立的 **EventContent** 資料表。您可以執行快速查詢，以取得該資料表中的資料。請執行下列查詢：

	SELECT * FROM EventCount

您應該會看到如下所示的輸出：

	00046b0f-2552-4980-9c3f-8bba5647c8ee
	000b7530-12f9-4081-8e19-90acd26f9c0c
	000bc521-9c1b-4a42-ab08-dc1893b83f3b
	00123a2a-e00d-496a-9104-108920955718
	0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
	001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
	001vIZgOStka4DXtud0e3tX7XbfMnZrN
	00220586-3e1a-4d2d-a89b-05c5892e541a
	0029e309-9e54-4e1b-84be-cd04e6fce5ec
	003333cf-874f-4045-9da3-9f98c2b4ea49
	0043c07e-8d73-420a-9af7-1fcb94575356
	004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9

	
## <a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 案例

* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

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

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0211_2016-->