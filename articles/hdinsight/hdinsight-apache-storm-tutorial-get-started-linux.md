<properties
	pageTitle="Apache Storm 教學課程：在 HDInsight 上開始使用 Linux 架構的 Storm | Microsoft Azure"
	description="在 Linux 架構的 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。了解如何使用 Storm 即時處理資料。"
	keywords="apache storm,apache storm 教學課程,巨量資料分析,storm 入門"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/12/2016"
   ms.author="larryfr"/>


# Apache Storm 教學課程：在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。在 Storm on Azure HDInsight 中，您可以建立雲端式 Storm 叢集，以執行即時的巨量資料分析。

> [AZURE.NOTE]本文中的步驟會建立以 Linux 為基礎的 HDInsight 叢集。如需在 HDInsight 叢集上建立 Windows 式 Storm 的步驟，請參閱 [Apache Storm 教學課程：在 HDInsight 上藉由資料分析開始使用 Storm Starter 範例](hdinsight-apache-storm-tutorial-get-started.md)

## 開始之前

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **熟悉 SSH 和 SCP**。如需搭配 HDInsight 使用 SSH 和 SCP 的詳細資訊，請參閱下列文章：

    - **Linux、Unix 或 OS X 用戶端**：請參閱[從 Linux、OS X 或 Unix 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Windows 用戶端**：請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

## 建立 Storm 叢集

Storm on HDInsight 使用 Azure Blob 儲存體來儲存提交給叢集的記錄檔和拓撲。請使用以下步驟建立和您的叢集搭配使用的 Azure 儲存體帳戶：

1. 登入 [Azure 入口網站][preview-portal]。

2. 依序選取 [新增]、[資料分析] 和 [HDInsight]。

	![在 Azure 入口網站中建立新的叢集](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. 輸入 [叢集名稱]，然後在 [叢集類型] 選取 [Storm]。如果該 [叢集名稱] 可用，它旁邊就會出現綠色核取記號。

	![叢集名稱、叢集類型及 OS 類型](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	請選取 [Ubuntu] 來建立以 Linux 為基礎的 HDInsight 叢集。
    
    > [AZURE.NOTE]針對本文件中的步驟保留 [版本] 欄位的預設值。
	
4. 如果您有多個訂用帳戶，請選取 [訂用帳戶] 項目，以選取將用於該叢集的 Azure 訂用帳戶。

5. 針對 [資源群組]，您可以選取此項目以查看現有資源群組的清單，然後選取其中一個用來建立叢集。或者選取 [建立新群組]，然後輸入新資源群組的名稱。出現綠色核取記號即表示新群組的名稱可用。

	> [AZURE.NOTE]如果有可用的資源群組，則此項目會預設為現有資源群組的其中一個群組。

6. 選取 [認證]，然後輸入 [叢集登入使用者名稱] 的 [叢集登入密碼]。您也必須輸入 [SSH 使用者名稱] 和 [密碼] 或 [公開金鑰]，這會用來驗證 SSH 使用者。最後，使用 [選取] 按鈕來設定認證。

	![叢集認證刀鋒視窗](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

6. 針對 [資料來源]，您可以選取此項目，藉此選擇現有資料來源，或建立一個新的資料來源。

	![資料來源刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。請使用下列資訊來了解 [資料來源] 刀鋒視窗上的項目。
	
	- __選取方法__：將此設為 [來自所有訂用帳戶]，即可瀏覽您訂用帳戶的儲存體帳戶。如果您想要輸入現有儲存體帳戶的 [儲存體名稱] 和 [存取金鑰]，請將此設為 [存取金鑰]。
    
    - __選取儲存體帳戶__：如果您的訂用帳戶已經有儲存體帳戶，使用此選項來選取要用於叢集的帳戶。
	
	- __建立新項目__：可用來建立新的儲存體帳戶。使用出現的欄位輸入儲存體帳戶名稱。如果該名稱可用，將會出現綠色核取記號。
	
	- __選擇預設容器__：使用此選項可輸入要用於該叢集的預設容器名稱。雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。
	
	- __位置__：儲存體帳戶所在地或將建立的地理區域。
	
		> [AZURE.IMPORTANT]選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。叢集和預設資料來源必須位於相同區域中。
    
    - __叢集 AAD 身分識別__：使用此選項以選取將由叢集用來存取 Azure 資料湖存放區的 Azure Active Directory 身分識別。
    
        > [AZURE.NOTE]這不會在這份文件中使用，並且可以保留預設設定。如需使用此項目和 Azure 資料湖存放區與 HDInsight 的詳細資訊，請參閱[建立使用 Azure 資料湖存放區的 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。
		
	- __選取__：用來儲存資料來源組態。
	
7. 選取 [節點定價層] 會顯示將針對此叢集建立之節點的相關資訊。背景工作節點數目預設會設為 __4__。該叢集的預估成本將會顯示在此刀鋒視窗的底部。

	![節點定價層刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
    您可以選取每個節點類型，以變更用於叢集中這些節點的 VM 類型。針對本文件中的步驟保留這些項目的預設設定。
    
	使用 [選取] 按鈕以儲存 [節點定價層] 資訊。

8. 選取 [選用設定]。此刀鋒視窗可讓您將叢集加入至__虛擬網路__、使用__指令碼動作__自訂叢集，或使用__自訂中繼存放區__以保留 Hive 和 Oozie 的資料。

	![選用設定刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)
    
    針對本文件中的步驟將這些設定保留為 [未設定]。

9. 請確定已選取 [釘選到「開始面板」]，然後選取 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

	| 佈建期間 | 佈建完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的佈建指示器](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或頁面左邊的 [通知] 項目來以檢查佈建進度。

##在 HDInsight 上執行 Storm Starter 範例

HDInsight 叢集已包含 [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) 範例。在下列步驟中，您將執行 WordCount 範例。

1. 使用 SSH 連線到 HDInsight 叢集

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	如果您已經使用密碼保護您 SSH 使用者帳戶的安全，系統會提示您輸入密碼。如果您使用的是公開金鑰，您可能必須使用 `-i` 參數來指定對應的私密金鑰。例如，`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`。
		
	如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：
	
	* [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)

2. 使用下列命令以啟動範例拓撲：

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE]因為 HDinsight 會隨著 Storm 的新版本而更新，所以檔案名稱的 `0.9.3.2.2.4.9-1` 部分可能會變更。

    這會在叢集上使用 'wordcount' 的易記名稱，啟動範例 WordCount 拓撲。命令會隨機產生句子，並計算句子中每個字詞的出現次數。

    > [AZURE.NOTE]將拓撲提交至叢集時，您必須先複製包含叢集的 jar 檔案，再使用 `storm` 命令。而您可以從檔案所在的用戶端使用 `scp` 命令來完成這個動作。例如，`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > WordCount 範例和其他 Storm 入門範例都已經包含在叢集中，位置是 `/usr/hdp/current/storm-client/contrib/storm-starter/`。

##監視拓撲

Storm UI 提供 Web 介面來處理執行中的拓撲，包含在您的 HDInsight 叢集中。

使用下列步驟以 Storm UI 監視拓撲。

1. 開啟網頁瀏覽器至 https://CLUSTERNAME.azurehdinsight.net/stormui，其中 __CLUSTERNAME__ 是叢集的名稱。這會開啟 Storm UI。

	> [AZURE.NOTE]如果要求您提供使用者名稱和密碼，請輸入叢集系統管理員 (admin) 和建立叢集時使用的密碼。

2. 在 [拓撲摘要] 下，選取 [名稱] 欄中的 [wordcount] 項目。這麼做會顯示拓撲的詳細資訊。

	![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

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

3. 在此頁面中，選取 [Spouts] 或 [Bolts] 區段中的一個項目。如此會顯示所選元件的相關資訊。

	![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	此頁面會顯示以下資訊：

	* **Spout/Bolt 統計資料 (Spout/Bolt stats)**：元件效能的基本資訊，已整理為時間範圍。

		> [AZURE.NOTE]選取特定的時間範圍，可以變更頁面中其他區段所顯示之資訊的時間範圍。

	* **輸入統計資料 (Input stats)** (僅 bolt)：提供的資訊是關於產生 bolt 所使用之資料的元件。

	* **輸出統計資料 (Output stats)**：此 bolt 發出之資料的資訊。

	* **執行程式**：此元件之執行個體的資訊。

	* **錯誤**：此元件產生的錯誤。

4. 檢視 spout 或 bolt 的詳細資料時，請在 [執行程式] 區段的 [連接埠] 欄中選取一個項目，以檢視特定元件執行個體的詳細資料。

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	您可以在此資料中看到 **seven** 一字已出現 1,493,957 次。這就是啟動拓撲後，該字所出現的次數。

##停止拓撲

返回 word-count 拓撲的 [拓撲摘要] \(Topology summary) 頁面，然後選取 [拓撲動作] \(Topology actions) 區段中的 [終止] \(Kill) 按鈕。出現提示時，請先輸入要等候 10 秒，再停止拓撲。逾時期限過後，當您瀏覽儀表板的 [Storm UI] 區段時，便不會再顯示拓撲。

##摘要

您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。

##<a id="next"></a>接續步驟

* 以下文件含有可和 Storm on HDInsight 搭配使用的其他範例清單：

	* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->