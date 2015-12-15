<properties
	pageTitle="Apache Storm 教學課程：開始使用 Storm | Microsoft Azure"
	description="在 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。了解如何使用 Storm 即時處理資料。"
	keywords="apache storm,apache storm 教學課程,巨量資料分析,storm 入門"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# Apache Storm 教學課程：在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。在 Microsoft Azure HDInsight 的 Storm 中，您可以建立雲端式 Storm 叢集，來執行即時的巨量資料分析。

> [AZURE.NOTE]本文中的步驟會建立以 Windows 為基礎的 HDInsight 叢集。如需在 HDInsight 叢集上建立以 Linux 為基礎之 Storm 的步驟，請參閱 [Apache Storm 教學課程：在 HDInsight 上藉由資料分析開始使用 Storm Starter 範例](hdinsight-apache-storm-tutorial-get-started-linux.md)

## 必要條件

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## 建立 Storm 叢集

Storm on HDInsight 使用 Azure Blob 儲存體來儲存提交給叢集的記錄檔和拓撲。請使用以下步驟，來建立可與您的叢集搭配使用的 Azure 儲存體帳戶：

1. 登入 [Azure 入口網站][preview-portal]。

2. 依序選取 [新增]、[資料分析] 和 [HDInsight]。

	![在 Azure 入口網站中建立新的叢集](./media/hdinsight-apache-storm-tutorial-get-started/new-cluster.png)

3. 輸入 [叢集名稱]，然後在 [叢集類型] 選取 [Storm]。如果該 [叢集名稱] 可用，它旁邊就會出現綠色核取記號。

	![叢集名稱、叢集類型及 OS 類型](./media/hdinsight-apache-storm-tutorial-get-started/clustername.png)

4. 如果您有多個訂用帳戶，請選取 [訂用帳戶] 項目，以選取將用於該叢集的 Azure 訂用帳戶。

5. 對於 [資源群組]，您可以選取此項目來查看現有資源群組的清單，然後選取其中一個來建立叢集。或者選取 [建立新群組]，然後輸入新資源群組的名稱。出現綠色勾號即表示新群組的名稱可供使用。

	> [AZURE.NOTE]如果有任何可用的資源群組，此項目即會預設為現有資源群組的其中一個群組。

6. 選取 [認證]，然後輸入 [叢集登入使用者名稱] 和 [叢集登入密碼]。最後，使用 [選取] 按鈕來設定認證。本文件中不會使用遠端桌面，所以您可以將其停用。

	![叢集認證刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/clustercredentials.png)

6. 對於 [資料來源]，您可以選取此項目來選擇現有的資料來源，或建立一個新的資料來源。

	![資料來源刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/datasource.png)

	您目前可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。請使用下列步驟來了解 [資料來源] 刀鋒視窗上的項目。

	- __選取方法__：將此設為 [來自所有訂用帳戶]，即可瀏覽您訂用帳戶的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。

	- __建立新項目__：可用來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可供使用，就會出現綠色勾號。

	- __選擇預設容器__：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

	- __位置__：儲存體帳戶所在或將會建立在的地理區域。

		> [AZURE.IMPORTANT]選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。

	- __選取__：可用來儲存資料來源組態。

7. 選取 [節點定價層] 來顯示將針對此叢集建立之節點的相關資訊。根據預設，背景工作角色節點的數目會是 __4__。請將此值設為 __1__，因為這樣就足以供本教學課程使用，也能減少叢集成本。該叢集的預估成本將顯示於此刀鋒視窗的底部。

	![節點定價層刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/nodepricingtiers.png)

	請使用 [選取] 按鈕來儲存 [節點定價層] 資訊。

8. 選取 [選用組態]。此刀鋒視窗可讓您選取叢集的版本，以及設定其他選用的設定，例如聯結__虛擬網路__，或設定__外部中繼存放區__來保存 Hive 和 Oozie 的資料。

	![選用設定刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/optionalconfiguration.png)

9. 確認已選取 [釘選到「開始面板」]，然後選取 [建立]。這會建立叢集，並將該叢集磚加入到您 Azure 入口網站的開始面板。該圖示表示該叢集正在佈建中，並會在佈建完成之後變更為顯示 HDInsight 圖示。

	| 佈建期間 | 佈建完成 |
	| ------------------ | --------------------- |
	| ![在開始面板上佈建指示器](./media/hdinsight-apache-storm-tutorial-get-started/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-storm-tutorial-get-started/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或頁面左邊的 [通知] 項目來檢查佈建進度。

## 在 HDInsight 上執行 Storm Starter 範例

本 Apache Storm 教學課程會向您介紹在 GitHub 上使用 Storm Starter 範例的巨量資料分析。

每個 Storm on HDInsight 叢集都會隨附一個 Storm 儀表板，讓您將 Storm 拓撲上傳到叢集並在其中執行 Storm 拓撲。每個叢集也會隨附可直接從 Storm 儀表板執行的範例拓撲。

### <a id="connect"></a>連接至儀表板

儀表板位於 **https://&lt;clustername>.azurehdinsight.net//**，其中 **clustername** 是叢集的名稱。您也可以尋找儀表板的連結，方法是從「開始面板」選取叢集，然後在刀鋒視窗頂端選取 [儀表板] 連結。

![含 Storm 儀表板連結的 Azure 入口網站](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [AZURE.NOTE]連線至儀表板時，系統會提示您輸入使用者名稱和密碼。這是您建立叢集時使用的系統管理員名稱 (**admin**) 和密碼。

載入 Storm 儀表板後，您會看到 [提交拓撲] (Submit Topology) 表單。

![利用 Storm 儀表板提交 Storm Starter 拓撲。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

[提交拓撲] 表單可用於上傳並執行含有 Storm 拓撲的 .jar 檔案。它也包含數個與叢集一併提供的基本範例。

### <a id="run">從 GitHub 的 Storm Starter 專案執行 word-count 範例

與叢集一併提供的範例包含 word-counting 拓撲的數個變體。這些範例包含會隨機產生句子的 **spout**，以及會將每個句子拆成個別單字，然後計算每個單字的出現次數的 **bolts**。這些範例均來自 [Storm Starter 範例](https://github.com/apache/storm/tree/master/examples/storm-starter) (Apache Storm 的一部分)。

請使用以下步驟執行 Storm Starter 範例：

1. 選取 [Jar 檔案] 下拉式清單中的 [StormStarter - WordCount]。此時 [類別名稱] 和 [其他參數] 欄位會填入此範例的參數。

	![在 Storm 儀表板上選取的 Storm Starter WordCount。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

	* **類別名稱**：提交拓撲的 .jar 檔案中的類別。
	* **其他參數**：拓撲需要的所有參數。在此範例中，此欄位用於讓提交的拓撲有易記名稱。

2. 按一下 [提交]。隨後，[結果] 欄位會顯示用來提交作業的命令，以及該命令的結果。[錯誤] 欄位會顯示提交拓撲時所發生的任何錯誤。

	![Storm Starter WordCount 的提交按鈕和結果。](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)

	> [AZURE.NOTE]顯示結果並不表示拓撲已完成，**Storm 拓撲一旦啟動，就會持續執行，直到您將拓撲停止為止。** Word-count 拓撲會產生隨機的句子，並持續計算每個單字出現的次數，直到您停止拓撲為止。

### <a id="monitor"></a>監視拓撲

您可以使用 Storm UI 監視拓撲。

1. 選取 Storm 儀表板頂端的 [Storm UI]。這會顯示叢集和所有執行中拓撲的摘要資訊。

	![顯示 Storm Starter WordCount 拓樸摘要的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)

	在上方的頁面中，您會看到拓撲作用中的時間，以及使用的背景工作數、執行程式數和工作數。

	> [AZURE.NOTE][名稱] 欄含有稍早透過 [其他參數] 欄位提供的易記名稱。

4. 在 [拓撲摘要] 下，選取 [名稱] 欄中的 [wordcount] 項目。這會顯示拓撲的詳細資訊。

	![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)

	此頁面提供以下資訊：

	* **拓撲統計資料 (Topology stats)**：拓撲效能的基本資訊，已整理為時間範圍。

		> [AZURE.NOTE]選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。

	* **Spouts**：spout 的基本資訊，包括每個 spout 傳回的最後一個錯誤。

	* **Bolts**：bolt 的基本資訊。

	* **拓撲組態 (Topology configuration)**：拓撲組態的詳細資訊。

	此頁面也提供可對拓撲採取的動作：

	* **啟用**：繼續處理已停用的拓撲。

	* **停用**：暫停執行中拓撲。

	* **重新平衡**：調整拓撲的平行處理原則。變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。這可讓拓撲調整平行處理原則，以彌補叢集中增加/減少的節點數目。如需詳細資訊，請參閱[了解 Storm 拓撲的平行處理原則](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

	* **終止 (Kill)**：在指定的逾時之後終止 Storm 拓撲。

5. 在此頁面中，選取 [Spouts] 或 [Bolts] 區段中的一個項目。這會顯示所選元件的相關資訊。

	![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)

	此頁面會顯示以下資訊：

	* **Spout/Bolt 統計資料 (Spout/Bolt stats)**：元件效能的基本資訊，已整理為時間範圍。

		> [AZURE.NOTE]選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。

	* **輸入統計資料 (Input stats)** (僅 bolt)：提供的資訊是關於產生 bolt 所使用之資料的元件。

	* **輸出統計資料 (Output stats)**：此 bolt 發出之資料的資訊。

	* **執行程式**：此元件之執行個體的資訊。

	* **錯誤**：此元件產生的錯誤。

5. 檢視 spout 或 bolt 的詳細資料時，請在 [執行程式] 區段的 [連接埠] 欄中選取一個項目，以檢視特定元件執行個體的詳細資料。

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	您可以在此資料中看到 **seven** 一字已出現 1,493,957 次。這就是啟動拓撲後，該字所出現的次數。

### 停止拓撲

請返回 word-count 拓撲的 [拓撲摘要] 頁面，然後選取 [拓撲動作] 區段中的 [終止] 按鈕。出現提示時，請先輸入要等候 10 秒，再停止拓撲。逾時期限過後，當您瀏覽儀表板的 [Storm UI] 區段時，就不會再看到拓撲。

## 摘要

您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。

## <a id="next"></a>接續步驟

* **HDInsight Tools for Visual Studio**：類似稍早提到的 Storm 儀表板，HDInsight Tools 可讓您運用 Visual Studio 提交、監視及管理 Storm 拓撲。HDInsight Tools 也能讓您建立 C# Storm 拓撲，並提供可讓您在叢集上部署和執行的範例拓撲。

	如需詳細資訊，請參閱[開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。

* **範例檔案**：HDInsight Storm 叢集在 **%STORM\_HOME%\\contrib** 目錄中提供數個範例。每個範例應該會包含下列項目：

	* 原始程式碼 - 例如，storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Java 文件 - 例如，storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* 範例 - 例如，storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	請使用 'jar' 命令來解壓縮原始程式碼或 Java 文件。例如，'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'。

	> [AZURE.NOTE]Java 文件是由網頁組成。解壓縮之後，請使用瀏覽器來檢視 **index.html** 檔案。

	若要存取這些範例，必須為 Storm on HDInsight 叢集啟用「遠端桌面」，然後從 **%STORM\_HOME%\\contrib** 複製檔案。

* 以下文件含有可和 Storm on HDInsight 搭配使用的其他範例清單：

	* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_1210_2015-->