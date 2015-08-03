<properties 
	pageTitle="在 HDInsight 中搭配使用事件中樞和 Apache Spark 來處理串流資料 | Azure" 
	description="說明如何將資料串流傳送到 Azure 事件中樞，接著再使用 Zeppelin Notebook 於 Spark 中接收這些事件的逐步指示" 
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
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# Spark Streaming：在 HDInsight 上使用 Apache Spark 處理來自 Azure 事件中樞的事件

Spark Streaming 能擴充核心的 Spark API，建置可調整、高輸送量、容錯的串流處理應用程式。資料能擷取自許多來源。在本文章中，我們使用事件中樞來擷取資料。事件中樞是可高度調整的擷取系統，每秒可以吸收數以百萬計的事件。

在本教學課程中，您將學習如何建立 Azure 事件中樞、使用以 C# 撰寫的主控台應用程式將訊息擷取到事件中樞，以及使用針對 HDInsight 中 Apache Spark 設定的 Zeppelin Notebook 平行擷取它們。

**必要條件：**

您必須滿足以下條件：

- Azure 訂閱。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Apache Spark 叢集。如需相關指示，請參閱[在 Azure HDInsight 中佈建 Apache Spark 叢集](hdinsight-apache-spark-provision-clusters.md)。
- [Azure 事件中樞](service-bus-event-hubs-csharp-ephcs-getstarted.md)。
- 安裝 Microsoft Visual Studio 2013 的工作站。如需指示，請參閱[安裝 Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx)。

##<a name="createeventhub"></a>建立 Azure 事件中樞

1. 從 [Azure 入口網站](https://manage.windowsazure.com)選取 [新增] > [服務匯流排] > [事件中樞] > [自訂建立]。

2. 在 [加入新的事件中樞] 畫面中輸入 [事件中樞名稱]、選取要建立中樞的 [區域]，然後建立新的命名空間或選取現有的命名空間。按一下 [箭頭] 以繼續。

	![精靈頁面 1](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "建立 Azure 事件中樞")

	> [AZURE.NOTE]您應該選取與 HDInsight 中 Apache Spark 叢集相同的 [**位置**]，以便降低延遲和成本。

3. 在 [**設定事件中樞**] 畫面中，輸入 [**資料分割計數**] 及 [**訊息保留**] 值，然後按一下核取記號。在此範例中，資料分割計數使用 10，訊息保留使用 1。請記下資料分割計數，因為您稍後會用到這個值。

	![精靈頁面 2](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "指定事件中樞的資料分割大小和保留天數")

4. 按一下您建立的事件中樞，再按一下 [**設定**]，然後為事件中樞建立兩個存取原則。

	<table>
<tr><th>名稱</th><th>權限</th></tr>
<tr><td>mysendpolicy</td><td>傳送</td></tr>
<tr><td>myreceivepolicy</td><td>接聽</td></tr>
</table>建立權限之後，在頁面底部選取**儲存**圖示。這樣會建立共用存取原則，可用來傳送 (**mysendpolicy**) 和接聽 (**myreceivepolicy**) 此事件中樞的訊息。

	![原則](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "建立事件中樞原則")

	
5. 在相同頁面上，記下針對這兩個原則產生的原則金鑰。請儲存這些金鑰，因為稍後會用到。

	![原則金鑰](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "儲存原則金鑰")

6. 在 [**儀表板**] 頁面上，按一下底部的 [**連接資訊**] 以使用兩個原則來擷取及儲存事件中樞的連接字串。

	![原則金鑰](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "儲存原則連接字串")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>使用 Zeppelin 在 HDInsight 上的 Spark 中接收訊息

在本節中，您會建立 [Zeppelin](https://zeppelin.incubator.apache.org) Notebook 以接收來自事件中樞的訊息，並傳遞到 HDInsight 中的 Spark 叢集。

1. 啟動 Zeppelin Notebook。在 Azure 入口網站選取 Spark 叢集，接著在底部的入口網站工作列按一下 [**Zeppelin Notebook**]。出現提示時，輸入 Spark 叢集的系統管理員認證。遵循頁面顯示的指示以啟動 Notebook。

2. 建立新的 Notebook。在標頭窗格中按一下 [**Notebook**]，然後在下拉式清單中按一下 [**建立新 Note**]。

	![建立新的 Zeppelin Notebook](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "建立新的 Zeppelin Notebook")

	在同一個頁面的 [**Notebook**] 標題下方，您應該會看到名稱開頭為 **Note XXXXXXXXX** 的新 Notebook。按一下新的 Notebook。

3. 在新 Notebook 的網頁上按一下標題，需要的話可以變更 Notebook 的名稱。按下 ENTER 以儲存名稱變更。此外，請確定 Notebook 標頭在右上角顯示 [**已連接**] 狀態。

	![Zeppelin Notebook 狀態](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Zeppelin Notebook 狀態")

4. 將以下程式碼片段貼入新 Notebook 中預設建立的空白段落中，並取代預留位置以使用您的事件中樞組態。在此程式碼片段中，您會接收來自事件中樞的串流，並將串流註冊到名為 **mytemptable** 的暫存資料表。在下一節中，我們會啟動傳送者應用程式。接著，您可以直接從資料表讀取資料。

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>執行應用程式

1. 從 Zeppelin Notebook 執行含有程式碼片段的段落。按下 **SHIFT + ENTER** 或右上角的 [**播放**] 按鈕。

	段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。輸出會顯示在同一個段落的底部。螢幕擷取畫面如下所示：

	![程式碼片段的輸出](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "程式碼片段的輸出")

2. 執行**傳送者**專案，並在主控台視窗中按 **Enter** 以開始將訊息傳送到事件中樞。

3. 在 Zeppelin Notebook 的新段落中，輸入以下程式碼片段以讀取在 Spark 中接收到的訊息。

		%sql select * from mytemptable limit 10

	下列螢幕擷取畫面顯示 **mytemptable** 中接收到的訊息。

	![在 Zeppelin 中接收訊息](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "在 Zeppelin Notebook 中接收訊息")

4. 重新啟動 Spark SQL 解譯器以結束應用程式。按一下頂端的 [**解譯器**] 索引標籤，然後針對 Spark 解譯器按一下 [**重新啟動**]。

	![重新啟動 Zeppelin 解譯器](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "重新啟動 Zeppelin 解譯器")

##<a name="sparkstreamingha"></a>以高可用性執行串流處理應用程式

使用 Zeppelin 來接收串流資料並傳遞到 HDInsight 上的 Spark 叢集是塑造應用程式原型的好方法。不過，若要以高可用性和恢復功能在實際執行設定中執行串流應用程式，您需要執行以下作業：

1. 將相依性 jar 複製到與叢集相關聯的儲存體帳戶。
2. 建置串流應用程式。
3. RDP 到叢集，並將應用程式 jar 複製到叢集的前端節點。
3. RDP 到叢集，並在叢集節點上執行應用程式。

如需執行這些步驟的指示和範例串流應用程式，請至 GitHub 下載：[https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples)。


##<a name="seealso"></a>另請參閱


* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [快速入門：在 HDInsight 上佈建 Apache Spark 並使用 Spark SQL 執行互動式查詢](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [在 HDInsight 中使用 Spark 建置機器學習應用程式](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO4-->