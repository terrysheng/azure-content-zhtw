<properties
pageTitle="在以 Windows 為基礎的 HDInsight 中使用 Tez UI | Azure"
description="了解在以 Windows 為基礎的 HDInsight 上如何使用 Tez UI 偵錯 Tez 作業。"
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

# 在以 Windows 為基礎的 HDInsight 上使用 Tez UI 偵錯 Tez 作業

對於在以 Windows 為基礎的 HDInsight 叢集上使用 Tez 作為執行引擎的作業，Tez UI 是一個可用來了解和偵錯這些作業的網頁。Tez UI 可讓您把作業視覺化有已連接項目的圖表、深入每個項目、取得統計資料，以及記錄資訊。

> [AZURE.NOTE] 本文件的資訊是 以 Windows 為基礎之 HDInsight 叢集的特定資訊。如需在以 Windows 為基礎的 HDInsight 上檢視和偵錯 Tez 的相關資訊，請參閱[在 HDInsight 上使用 Ambari 檢視來為 Tez 作業偵錯](hdinsight-debug-ambari-tez-view.md)。

##必要條件

* 以 Windows 為基礎的 HDInsight 叢集。如需建立新叢集的步驟，請參閱[開始使用以 Windows 為基礎的 HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md)。

    > [AZURE.IMPORTANT] 只有在 2016 年 2 月 8 日之後建立的以 Windows 為基礎的 HDInsight 叢集上才能使用 Tez UI。

* 以 Windows 為基礎的遠端桌面用戶端。

##了解 Tez

Tez 是 Hadoop 中資料處理用的可延伸架構，資料處理的速度比傳統的 MapReduce 處理方式還要快。對於以 Windows 為基礎的 HDInsight 叢集，它是選擇性的引擎，只要在 Hive 查詢中使用下列命令，就可以對 Hive 啟用它：

    set hive.execution.engine=tez;

當工作提交到 Tez 時，Tez 會建立有向非循環圖 (DAG) 來說明該作業所需動作的執行順序。個別的動作稱為頂點，它會執行整體作業的一部分。由頂點所描述的實際工作 (Work) 執行程序稱為工作 (Task)，且可能會分散到叢集中的多個節點上。

###了解 Tez UI

針對使用 Tez 正在執行或先前執行的處理序，Tez UI 是一個提供相關資訊的網頁。它能讓您檢視由 Tez 所產生的 DAG、它如何分散到不同叢集上、計數器 (例如工作及頂點所使用的記憶體)，以及錯誤訊息。它可能會提供下列案例中的有用資訊：

* 監視長期執行的處理序、檢視對應進度，以及減少工作。

* 分析成功或失敗的處理序歷史資料，以便了解如何改進處理序，以及處理序失敗的原因。

##產生 DAG

Tez UI 只包含正在或曾經使用 Tez 引擎來執行之作業的資料。簡單的 Hive 查詢通常不用 Tez 就能解決，但更複雜的查詢 (會進行篩選、分組、排序、 聯結等) 通常需要使用 Tez。

請使用下列步驟，來執行會使用 Tez 來執行的 Hive 查詢。

1. 在網頁瀏覽器中，瀏覽至 https://CLUSTERNAME.azurehdinsight.net，其中 __CLUSTERNAME__ 是您 HDInsight 叢集的名稱。

2. 在頁面頂端的功能表中，選取 [Hive 編輯器]。這會顯示包含下列範例查詢的頁面。

        Select * from hivesampletable

    清除範例查詢並取代為下列內容。

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. 選取 [提交] 按鈕。頁面底部的 [作業工作階段] 會顯示查詢的狀態。當狀態變更為 [已完成] 後，請選取 [檢視詳細資料] 連結來檢視結果。[作業輸出] 應該類似如下：
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

##使用 Tez UI

> [AZURE.NOTE] 只有從叢集前端節點的桌面上才能使用 Tez UI，因此您必須使用遠端桌面連接到前端節點。

1. 從 [Azure 入口網站](https://portal.azure.com)，選取您的 HDInsight 叢集。從 HDInsight 刀鋒視窗的頂端，選取 [遠端桌面] 圖示。這會顯示遠端桌面刀鋒視窗

    ![遠端桌面圖示](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. 從 [遠端桌面] 刀鋒視窗中，選取 [連接] 來連線到叢集前端節點。出現提示時，使用遠端桌面使用者名稱和密碼來驗證連線。

    ![遠端桌面連線圖示](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] 如果您未啟用遠端桌面連線功能，請提供使用者名稱、密碼和到期日，然後選取 [啟用] 來啟用遠端桌面。啟用之後，請使用先前的步驟來連接。

3. 連線之後，請在遠端桌面開啟 Internet Explorer，選取瀏覽器右上方的齒輪圖示，然後選取 [相容性檢視設定]。

4. 從 [相容性檢視設定] 的底部，清除 [在相容性檢視下顯示內部網路網站] 核取方塊和 [使用 Microsoft 相容性清單]，然後選取 [關閉]。

5. 在 Internet Explorer 中，瀏覽至 http://headnodehost:8188/tezui/#/。這會顯示 Tez UI

    ![Tez UI](./media/hdinsight-debug-tez-ui/tezui.png)

    當 Tez UI 載入時，您會看到叢集上目前正在執行或已執行的一份 DAG 清單。預設檢視包括 Dag 名稱、識別碼、傳送者、狀態、開始時間、結束時間、持續時間、應用程式識別碼和佇列。使用頁面右邊的齒輪圖示可以加入更多資料行。

    如果您只有一個項目，則會是關於您在上一節執行的查詢。如果您有多個項目，可以在 DAG 上方的欄位中輸入搜尋準則來搜尋，然後按 __Enter__。

4. 選取最新 DAG 項目的 [Dag 名稱]。這會顯示 DAG 的相關資訊，以及用來下載 JSON 壓縮檔 (包含 DAG 的相關資訊) 的選項。

    ![DAG 詳細資料](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. [DAG 詳細資料] 上方有幾個連結可用來顯示 DAG 的相關資訊。

    * [DAG 計數器] 顯示此 DAG 的計數器資訊。
    
    * [圖形檢視] 顯示此 DAG 的圖形表示。
    
    * [所有頂點] 顯示此 DAG 中的頂點清單。
    
    * [所有工作] 顯示此 DAG 中所有頂點的工作清單。
    
    * [所有工作嘗試] 顯示嘗試對此 DAG 執行工作的相關資訊。
    
    > [AZURE.NOTE] 如果您捲動 [頂點]、[工作] 和 [工作嘗試] 的資料行顯示，請注意有連結可以檢視每個資料列的__計數器__和__檢視或下載記錄檔__。

    如果作業失敗，[DAG 詳細資料] 會顯示 [失敗] 狀態，以及失敗工作相關資訊的連結。DAG 詳細資料下方會顯示診斷資訊。

7. 選取 [圖形檢視]。這會顯示 DAG 的圖形表示。您可以將滑鼠移至檢視中的每個頂點上來顯示相關資訊。

    ![圖形檢視](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. 按一下頂點會載入該項目的 [頂點詳細資料]。按一下 [對應 1] 頂點來顯示此項目的詳細資料。選取 [確認] 來確認瀏覽。

    ![頂點詳細資料](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. 請注意，頁面頂端現在顯示頂點和工作相關的連結。

    > [AZURE.NOTE] 您也可以回到 [DAG 詳細資料]、選取 [頂點詳細資料]，然後選取 [對應 1] 頂點，就可進入此頁面。

    * [頂點計數器] 顯示此頂點的計數器資訊。
    
    * [工作] 顯示此頂點的工作。
    
    * [工作嘗試] 顯示嘗試對此頂端執行工作的相關資訊。
    
    * [來源與接收] 顯示此頂點的資料來源和接收。

    > [AZURE.NOTE] 與上一個功能表一樣，您可以捲動 [工作]、[工作嘗試] 及 [來源與接收] 的資料行顯示，以顯示每個項目的詳細資訊連結。

10. 選取 [工作]，然後選取名為 __00\_000000__ 的項目。這會顯示這項工作的 [工作詳細資料]。在此畫面中，您可以檢視 [工作計數器] 和 [工作嘗試]。

    ![作業詳細資料](./media/hdinsight-debug-tez-ui/taskdetails.png)

##後續步驟

既然您已了解如何使用 Tez 檢視，請深入了解[在 HDInsight 上使用 Hive](hdinsight-use-hive.md)。

如需 Tez 的詳細技術資訊，請參閱 [Hortonworks 的 Tez 頁面](http://hortonworks.com/hadoop/tez/)。

<!---HONumber=AcomDC_0218_2016-->