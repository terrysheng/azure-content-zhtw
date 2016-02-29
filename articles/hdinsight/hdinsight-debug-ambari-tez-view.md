<properties
pageTitle="搭配 HDInsight 來使用 Ambari Tez 檢視 | Azure"
description="了解如何在 HDInsight 上使用 Ambari Tez 檢視來為 Tez 作業偵錯。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

# 在 HDInsight 上使用 Ambari 檢視來為 Tez 作業偵錯

適用於 HDInsight 的 Ambari Web UI 包含 Tez 檢視，可用來了解以 Tez 為執行引擎的作業，以及為該類型的作業偵錯。Tez 檢視可讓您把作業視覺化有已連接項目的圖表、深入每個項目、取得統計資料，以及記錄資訊。

> [AZURE.NOTE] 本文件的資訊是以 Linux 為基礎的 HDInsight 叢集的特定資訊。如需如何使用 Windows 型 HDInsight 來為 Tez 作業偵錯的資訊，請參閱 [Use the Tez UI to debug Tez jobs on Windows-based HDInsight (在 Windows 型 HDInsight 上使用 Tez UI 來為 Tez 作業偵錯)](hdinsight-debug-tez-ui.md)。

##先決條件

* 以 Linux 為基礎的 HDInsight 叢集。如需建立新叢集的步驟，請參閱[開始使用 Linux 型 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)。

* 支援 HTML5 的新式網頁瀏覽器。

##了解 Tez

Tez 是 Hadoop 中資料處理用的可延伸架構，資料處理的速度比傳統的 MapReduce 處理方式還要快。而對於 Linux 型 HDInsight 叢集來說，Tez 是 Hive 的預設引擎。

當工作提交到 Tez 時，Tez 會建立有向非循環圖 (DAG) 來說明該作業所需動作的執行順序。個別的動作稱為頂點，它會執行整體作業的一部分。而由端點所描述的實際工作 (Work) 執行程序稱為工作 (Task)，且可能會分散到叢集中的多個節點上。

###了解 Tez 檢視

Tez 檢視提供正在或曾經使用 Tez 來執行的處理序的資訊。它能讓您檢視由 Tez 所產生的 DAG、它如何分散到不同叢集上、計數器 (例如工作及頂點所使用的記憶體)，以及錯誤訊息。它可能會提供下列案例中的有用資訊：

* 監視長期執行的處理序、檢視對應進度，以及減少工作。

* 分析成功或失敗的處理序歷史資料，以便了解如何改進處理序，或是處理序失敗的原因。

##產生 DAG

Tez 檢視只包含正在或曾經使用 Tez 引擎來執行之作業的資料。簡單的 Hive 查詢通常不用 Tez 就能解決，但更複雜的查詢 (會進行篩選、分組、排序、聯結等) 通常需要使用 Tez。

請使用下列步驟，來執行會使用 Tez 來執行的 Hive 查詢。

1. 在網頁瀏覽器中，瀏覽至 https://CLUSTERNAME.azurehdinsight.net，其中「CLUSTERNAME」是您 HDInsight 叢集的名稱。

2. 在頁面頂端的功能表中，選取檢視圖示。也就是看起來像一系列正方形的圖示。在隨後出現的下拉式清單中，選取 [Hive 檢視]。

    ![選取 [Hive 檢視]](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. 當 Hive 檢視載入之後，在查詢編輯器中貼上下列指令碼，然後按一下 [執行]。

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    當作業完成之後，您應該會在 [查詢處理程序結果] 區段看到輸出。結果應該會與下列內容類似
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. 選取 [記錄] 索引標籤。您會看到類似下列內容的資訊：
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    請儲存 __App id__ 的值，因為您將在下一節使用它。

##使用 Tez 檢視

1. 在頁面頂端的功能表中，選取檢視圖示。在隨後出現的下拉式清單中，選取 [Tez 檢視]。

    ![選取 [Tez 檢視]](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. 當 Tez 檢視載入時，您會看到正在或曾經在叢集上執行的 DAG 清單。預設的檢視包括 DAG 名稱、識別碼、傳送者、狀態、開始時間、結束時間、持續時間、應用程式識別碼及佇列。您可以利用頁面右側的齒輪圖示來新增更多資料行。

    ![所有 DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. 如果您只看到單一項目，它就是您在上一節所執行的查詢。如果您看到多筆項目，可以在 [應用程式識別碼] 欄位輸入應用程式識別碼，然後按下 Enter 鍵。

4. 選取 [Dag 名稱]。這會顯示 DAG 的相關資訊，以及用來下載包含 DAG 相關資訊的 JSON 壓縮檔的選項。

    ![DAG 詳細資料](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. [DAG 詳細資料] 上方有幾個連結，可用來顯示 DAG 的相關資訊。

    * [DAG 計數器] 會顯示此 DAG 的計數器資訊。
    
    * [圖形檢視] 會以圖形化的方式來顯示此 DAG。
    
    * [所有頂點] 會顯示此 DAG 中的頂點清單。
    
    * [所有工作] 會顯示此 DAG 中所有頂點的工作清單。
    
    * [所有工作嘗試] 會顯示針對此 DAG 來嘗試執行工作的相關資訊。
    
    > [AZURE.NOTE] 如果您捲動 [頂點]、[工作] 和 [工作嘗試] 的資料行顯示，就會看到可讓您檢視每個資料列的__計數器__，以及__檢視或下載每個資料列記錄檔__的連結。

    如果作業執行失敗，[DAG 詳細資料] 會顯示 [FAILED] 狀態，以及可前往失敗工作相關資訊的連結。而 [DAG 詳細資料] 下方會顯示診斷資訊。
    
    ![詳述某個失敗作業的 [DAG 詳細資料] 畫面](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. 選取 [圖形檢視]。這會以圖形化的方式來顯示 DAG。您可以將滑鼠游標移動到檢視中的每個頂點上，來顯示該頂點的相關資訊。

    ![圖形檢視](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. 只要按一下某個頂點，就會載入該項目的 [頂點詳細資料]。請按一下 [對應 1] 頂點來顯示此項目的詳細資料。

    ![頂點詳細資料](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. 請注意，現在頁面頂端有與頂點和工作相關的連結。

    > [AZURE.NOTE] 您也可以藉由回到 [DAG 詳細資料]、選取 [頂點詳細資料]，然後選取 [對應 1] 頂點，來進入此頁面。

    * [頂點計數器] 會顯示此頂點的計數器資訊。
    
    * [工作] 會顯示此頂點的工作。
    
    * [工作嘗試] 會顯示針對此頂點來嘗試執行工作的相關資訊。
    
    * [來源及接收器] 會顯示此頂點的資料來源和接收器。

    > [AZURE.NOTE] 跟前一個功能表一樣，您可以捲動 [工作]、[工作嘗試] 及 [來源與接收] 的資料行顯示，來顯示可前往每個項目詳細資訊的連結。

10. 選取 [工作]，然後選取名為 [00\_000000] 的項目。這會顯示此工作的 [工作詳細資料]。您可以從此畫面檢視 [工作計數器] 和 [工作嘗試]。

    ![作業詳細資料](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##後續步驟

現在您已經了解如何使用 Tez 檢視，請進一步了解[如何在 HDInsight 上使用 Hive](hdinsight-use-hive.md)。

如需 Tez 的詳細技術資訊，請參閱 [Hortonworks 的 Tez 頁面](http://hortonworks.com/hadoop/tez/)。

如需如何搭配 HDInsight 來使用 Ambari 的詳細資訊，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

<!---HONumber=AcomDC_0218_2016-->