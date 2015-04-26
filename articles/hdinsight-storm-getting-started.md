<properties 
	pageTitle="在 Microsoft Azure HDInsight (Hadoop) 上開始使用 Apache Storm" 
	description="了解如何使用  Apache Storm 來即時使用 HDInsight (Hadoop) 處理資料" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# 在 HDInsight (Hadoop) 上開始使用 Storm

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。在 Azure HDInsight 中，您可以建立雲端式 Hadoop 叢集，以利用 Storm 來執行即時的資料分析。

## 在本教學課程中，您將了解...

* [如何在　HDInsight　上佈建　Storm 叢集](#provision)

* [如何連接到叢集](#connect)

* [如何執行 Storm 拓撲](#run)

* [如何監視 Storm 拓撲](#monitor)

* [如何停止 Storm 拓撲](#stop)

* [後續步驟](#next)

## 開始之前

您必須具有下列先決條件，才能順利完成本教學課程。

* Azure 訂用帳戶

* Windows Azure PowerShell

* 如果不熟悉 Apache Storm，請先閱讀 [HDInsight Storm 概觀](hdinsight-storm-overview.md) 文章。

## <a id="provision"></a>在 Azure 入口網站上佈建 Storm 叢集

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. 登入 [Azure 管理入口網站][azureportal]

2. 按一下左邊的 [**HDInsight**]，然後按一下頁面左下角的 [**+新增**]。

3. 按一下第二欄的 HDInsight 圖示，然後選取 [**自訂**]。

4. 在 [**叢集詳細資料**] 頁面上，輸入新叢集的名稱，然後在 [**叢集類型**] 中選取 [**Storm**]。按一下箭頭以繼續。

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. 輸入要用於此叢集的 [**資料節點**] 數目，以及將建立此叢集的 [**區域/虛擬網路**]。按一下箭頭以繼續。

	> [AZURE.NOTE] 為了將本文使用的叢集成本降到最低，請將 [**叢集大小**] 降到 1，並於使用完畢之後刪除叢集。

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. 輸入系統管理員的 [**使用者名稱**] 和 [**密碼**]，然後按一下箭頭以繼續。

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. 在 [**儲存體帳戶**] 中，選取 [**建立新的儲存體**] 或選取現有的儲存體帳戶。選取或輸入 [**帳戶名稱**] 和要使用的 [**預設容器**]。

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. 在 [**指令碼動作**] 頁面上，按一下 [**加入指令碼動作**]，以提供自訂指令碼的詳細資料，您想要執行該指令碼以自訂叢集，做為正在建立的叢集。例如，您可以使用指令碼動作來自訂叢集以安裝 <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>。如需詳細資訊，請參閱[使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster.md)。 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>屬性</th><th>值</th></tr>
		<tr><td>名稱</td>
			<td>指定指令碼動作的名稱。</td></tr>
		<tr><td>指令碼 URI</td>
			<td>指定要叫用來自訂叢集的指令碼的 URI。</td></tr>
		<tr><td>節點類型</td>
			<td>指定執行自訂指定碼的節點。您可以選擇<b>所有節點</b>、<b>僅前端節點</b>或僅<b>背景工作節點</b>。
		<tr><td>參數</td>
			<td>如果指令碼要求，請指定參數。</td></tr>
	</table>

	您可以加入一個以上的指令碼動作，以在叢集上安裝多個元件。加入指令碼之後，按一下核取記號以開始佈建叢集。

## 在 HDInsight 上使用 Storm

在 HDInsight 上的 Storm 預覽版中，您必須使用遠端桌面連接到叢集，才能使用 Storm。請使用下列步驟連接到 HDInsight 叢集，並使用 Storm 命令。

###<a id="connect"></a>連接到叢集

1. 使用 [Azure 管理入口網站][management]，對 HDInsight 叢集啟用遠端桌面連線。在入口網站中，選取 HDInsight 叢集，然後在 [設定] 頁面底部選取 [啟用遠端]。

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    出現提示時，輸入要用於遠端工作階段的使用者名稱和密碼。您也必須選取遠端存取的到期日。

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. 啟用遠端存取之後，選取 [連接] 開始連線。這樣會下載 .rdp 檔案，此檔案可用來啟動遠端桌面工作階段。

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] 連接到遠端電腦時，可能出現許多提示要求您信任憑證。

3. 連線之後，使用桌面上的 [Hadoop 命令列] 圖示開啟 Hadoop 命令列。

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. 從 Hadoop 命令列，使用下列命令切換到含有 Storm 命令的目錄。

	cd %storm_home%\bin

5. 若要查看可用的命令清單，請輸入 `storm` 且不指定任何參數。

HDInsight 叢集隨附數個 Storm 拓撲範例。下列步驟中使用 **WordcountTopology** 範例。如需 HDInsight 上的 Storm 隨附的範例的詳細資訊，請參閱[後續步驟]。(#next)。

### <a id="run"></a>執行 Storm 拓撲

若要執行 **WordCountTopology**，請使用下列命令。

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

這會向 Storm 表示我們想要從 **storm.starter.WordCountTopology** 類別執行 **wordcount** 拓撲，此類別包含在 **storm-starter-&lt;version>-jar-with-dependencies.jar** 檔案中。此檔案位於 %storm_home% 目錄下的 contrib 資料夾中，且伴隨著其他 Storm 範例。

> [AZURE.NOTE] 含有範例的 JAR 檔案版本可能定期地變更。執行此命令時，請指定您的叢集隨附的檔案版本。

請注意，輸入命令時不會傳回任何資料。**Storm 拓撲一旦啟動，即會執行到您停止為止。**WordCountTopology 會產生隨機的句子，並持續計算每個單字出現的次數，直到您將它停止為止。

### <a id="monitor"></a>監視 Storm 拓撲的狀態

WordCountTopology 範例不會將輸出寫入目錄中，但我們可以使用 Storm UI 網頁來檢視拓撲的狀態及輸出。

1. 使用遠端桌面連接到 HDInsight 叢集。

2. 從桌面開啟 [**Storm UI**] 連結。這會顯示 Storm UI 網頁。  在 **Topology summary** 下，選取 **wordcount** 項目。

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	這將會顯示拓撲的統計資料，包括組成拓撲的元件，亦即 **spouts** 和 **bolts**。

3. 從頁面選取 [**spout**] 連結，然後選取 [**連接埠**] 號碼 (針對 [**執行者 (所有時間)**] 清單中，[**已發出**] 和 [**已轉移**] 欄中數字大於 0 的每個項目)。

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] 根據叢集中的節點數目和您執行的拓撲而定，可能需要經過幾分鐘，拓撲才會開始處理。請定期重新整理頁面，直到 [**已發出**] 和 [**已轉移**] 值開始增加為止。

4. 您應該會看到包含如下資訊的頁面。

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	在此片段中，您可以看到 spout 發出  'snow white and the seven dwarfs'，此子句已分割成個別單字。此外，也持續計算自從拓撲啟動之後每個單字已處理多少次。例如，在檢視上述輸出時， 'dwarfs' 這個字已由 spout 發出 8360 次。

### <a id="stop"></a>停止 Storm 拓撲

**WordCountTopology** 會持續執行到您將它停止為止。若要停止，請使用下列命令。

	storm kill wordcount

如果您在此命令之後立即檢視 Storm UI 網頁，您會發現 **Topology summary** 中的 **wordcount** 已變成 KILLED 狀態。經過幾秒鐘，拓撲就不再列於 **Topology summary** 區段中。

## <a id="next"></a>後續步驟

* **範例檔案** - Storm 叢集在 **%storm_home%\contrib** 目錄中提供數個範例。每一個範例應該會包含下列項目。

	* 原始程式碼 - 例如，storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Java 文件 - 例如，storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* 範例 - 例如，storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	請使用  'jar' 命令來解壓縮原始程式碼或 Java 文件。例如， 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'。

	> [AZURE.NOTE] Java 文件由網頁組成。解壓縮之後，請使用瀏覽器來檢視 **index.html** 檔案。

* [使用 Storm 和 HDInsight 分析感應器資料](hdinsight-storm-sensor-data-analysis.md)

* [在 HDInsight 中的 Storm 上使用 SCP.NET 和 C# 開發串流資料處理應用程式](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/

<!--HONumber=42-->
