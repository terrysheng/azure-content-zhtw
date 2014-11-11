<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 開始使用 HDInsight (預覽版) 中的 Hadoop 2.4

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="開始使用 HDInsight 中的 Hadoop 2.2">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="開始使用 HDInsight 中的 Hadoop 2.4" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="開始使用 HDInsight 中的 Hadoop 1.2">Hadoop 1.2</a>
</div>

HDInsight 讓 [Apache Hadoop][Apache Hadoop] 成為雲端的服務。它使得 MapReduce 軟體架構可以在更簡單、更有擴充性且更符合成本效益的 Azure 環境中發揮作用。HDInsight 也提供使用 Azure Blob 儲存來管理和儲存資料的高成本效益作法。

在這個教學課程中，您將使用 Azure 管理入口網站佈建 HDInsight 叢集，並使用 HDInsight 叢集儀表板提交 Hive 工作來查詢範例 Hive 資料表，然後將 Hive 工作輸出資料匯入 Excel 中進行檢查。

> [WACOM.NOTE] 本教學課程涵蓋在 HDInsight (預覽版) 使用 Hadoop 2.4 叢集。若為其他支援的版本，請按一下頁面頂端的選取器。如需版本資訊，請參閱 [HDInsight 所提供叢集版本的新功能][HDInsight 所提供叢集版本的新功能]

本文的現場示範：

<center>
[![HDI.getstarted.video][img-hdi-getstarted-video]][]

</center>
除了全面供應 Azure HDInsight 之外，Microsoft 也推出 HDInsight Emulator for Azure，也就是過去的 *Microsoft HDInsight Developer Preview*。本產品適合開發人員使用，因此僅支援單一節點部署。關於使用 HDInsight Emulator，請參閱[開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   Azure 訂閱。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][購買選項]、[成員優惠][成員優惠]或[免費試用][免費試用]。
-   安裝 Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus 的電腦。
-   如需 HDInsight 最新版本的說明，請參閱 [HDInsight 版本資訊][HDInsight 版本資訊] (英文)。

**預估完成時間：**30 分鐘

## 本教學課程內容

-   [佈建 HDInsight 叢集][佈建 HDInsight 叢集]
-   [執行 Hive 工作][執行 Hive 工作]
-   [連接到 Microsoft 商業智慧工具][連接到 Microsoft 商業智慧工具]
-   [後續步驟][後續步驟]

## <a name="provision"></a>佈建 HDInsight 叢集

HDInsight 會使用 Azure Blob 儲存體來儲存資料。我們稱之為 *WASB* 或 *Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

佈建 HDInsight 叢集時，會將一個 Azure 儲存體帳戶及其下的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。儲存體帳戶必須位在 HDInsight 運算資源所在的同一個資料中心。目前，您只能在下列資料中心佈建 HDInsight 叢集：

-   東南亞
-   北歐
-   西歐
-   美國東部
-   美國西部

除了此儲存體帳戶，您也可以從相同或不同的 Azure 訂用帳戶中新增其他儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][1]。

為了簡化本教學課程，我們只使用預設的 Blob 容器和預設儲存體帳戶，且所有檔案都儲存在位於 */tutorials/getstarted/* 的預設檔案系統容器中。實際上，資料檔案通常會儲存在指定的儲存體帳戶中。

**建立 Azure 儲存體帳戶**

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下左下角的 [新增]，並指向 [資料服務]，接著指向 [儲存體]，然後按一下 [快速建立]。

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  輸入 [URL]、[位置] 和 [複寫]，然後按一下 [建立儲存體帳戶]。不支援同質群組。您將在儲存體清單中看見新的儲存體帳戶。
4.  等候新儲存體帳戶的 [狀態] 變更為 [線上]。
5.  按一下清單中的新儲存體帳戶加以選取。
6.  按一下頁面底部的 [MANAGE ACCESS KEYS]。
7.  請記下 [儲存體帳戶名稱] 和 [主要存取金鑰] (或 [次要存取金鑰]。這兩個金鑰都有效)。稍後在教學課程中需要這些資訊。

如需詳細資訊，請參閱
[如何建立儲存體帳戶][如何建立儲存體帳戶]和[在 HDInsight 上使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]。

**佈建 HDInsight 叢集**

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  按一下左邊的 [HDINSIGHT]，列出您的帳戶下的 HDInsight 叢集。在下列螢幕擷取畫面中，沒有任何現有的 HDInsight 叢集。

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  按一下左下角的 [新增]，然後依序按一下 [資料服務]、[HDINSIGHT]、[自訂建立]。

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4.  在 [叢集詳細資料] 索引標籤上，輸入或選取下列值：

    | 名稱               | 值                                                                                                                                                                  |
    |--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **叢集名稱**       | 叢集的名稱。                                                                                                                                                        |
    | **資料節點**       | 您要部署的資料節點數。請建立單一節點叢集，以供測試之用。                                                                                                            
                          叢集大小限制會隨著 Azure 訂用帳戶而不同。若要提高限制，請與 Azure 帳務支援人員連絡。                                                                                 |
    | **HDInsight 版本** | 請選擇 **3.1**，以在 HDInsight 上建立 Hadoop 2.4 叢集。                                                                                                             |
    | **區域**           | 請選擇與您在前一個程序中建立的儲存體帳戶相同的區域。使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。 |

5.  按一下位於右下角的向右箭頭，以設定叢集使用者。
6.  在 [設定叢集使用者] 索引標籤中，輸入 HDInsight 叢集使用者帳戶的 [使用者名稱] 和 [密碼]。除了此帳戶以外，您也可以在叢集佈建之後建立 RDP 使用者帳戶，以便從遠端桌面連接到叢集。如需指示，請參閱 [使用管理入口網站來管理 HDInsight][hdinsight-admin-portal]
7.  按一下位於右下角的向右箭頭，以設定儲存體帳戶。
8.  在 [儲存體帳戶] 標籤上，輸入或選取下列值：

    | 名稱           | 值                                                                                                   |
    |----------------|------------------------------------------------------------------------------------------------------|
    | 儲存體帳戶     | 選取 [使用現有儲存體]。如果您尚未建立新的儲存體帳戶，您也可以選擇使用管理入口網站來建立帳戶。        |
    | 帳號名稱       | 指定您在本教學課程的上一個程序中建立的儲存體帳戶。請注意，清單方塊中只會顯示相同區域中的儲存體帳戶。 |
    | 預設容器       | 選取 [建立預設容器]。選擇此選項時，預設容器名稱會與叢集名稱相同。                                    |
    | 附加儲存體帳戶 | 選取 **0**。您可以選擇將叢集連接到多達 7 個附加儲存體帳戶。                                          |

9.  按一下位於右下角的核取圖示，以建立叢集。佈建程序完成時，狀態欄將顯示 [執行中]。

## <a name="sample"></a>執行 Hive 工作

現在您已經佈建 HDInsight 叢集。下一個步驟是執行 Hive 工作，以查詢 HDInsight 叢集隨附的範例 Hive 資料表。資料表名稱為 *hivesampletable*。

**開啟 HDInsight 叢集儀表板**

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  按一下左窗格上的 [HDInsight]。您會看見已建立的叢集清單，其中包含您在上一節中建立的叢集。
3.  按一下要執行 Hive 工作的叢集名稱。
4.  按一下位於頁面底部的 [管理叢集]，以開啟 HDInsight 叢集儀表板。它會在不同瀏覽器索引標籤上開啟網頁。
5.  輸入 Hadoop 使用者帳戶的使用者名稱和密碼。預設使用者名稱為 **admin**，密碼是您在佈建程序中輸入的密碼。儀表板顯示如下：

    ![hdi.dashboard][hdi.dashboard]

    頂端會有數個索引標籤。預設索引標籤是 [Hive Editor]，其他索引標籤包括 [工作] 和 [檔案]。您可透過儀表板提交 Hive 查詢、檢查 Hadoop 工作記錄及瀏覽 WASB 檔案。

> [wacom.note] 請注意，URL 是 *\<ClusterName\>.azurehdinsight.net*。除了從管理入口網站開啟儀表板，您也可以使用 URL 從網頁瀏覽器中開啟儀表板。

**執行 Hive 查詢**

1.  在 HDInsight 叢集儀表板中，按一下位於頂端的 [Hive Editor]。
2.  在 [查詢名稱] 中，輸入 **HTC20**。查詢名稱就是工作標題。
3.  在查詢窗格中，輸入下列查詢：

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  按一下 [提交]。需要一點時間才會取回結果。螢幕每 30 秒會重新整理一次。您也可以按一下 [重新整理] 來重新整理螢幕。

    完成後，螢幕會如下所示：

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    記下 [工作開始時間 (UTC)]。稍後您將會用到此資訊。

    向下捲動一些會看到 [工作記錄]。工作輸出是 stdout，工作記錄是 stderr。

5.  如果未來要重新開啟記錄檔，您可以從畫面頂端按一下 [工作]，然後按一下工作標題 (查詢名稱)。例如，此案例中的 **HTC20**。

**瀏覽輸出檔案**

1.  在 HDInsight 叢集儀表板中，按一下位於頂端的 [檔案]。
2.  按一下 [Templeton-工作- 狀態]。
3.  按一下「前次修改時間」比您先前記下的「工作開始時間」稍晚的 GUID 號碼。記下此 GUID。您在下一節將會用到此號碼。
4.  **stdout** 檔案包含您在下一節需要使用的資料。如有需要，您可以按一下 **stdout** 以下載資料檔案的複本。

## <a name="powerquery"></a>連接到 Microsoft 商業智慧工具

Excel 的 Power Query 增益集可用來將 HDInsight 的輸出匯出到 Excel，以便使用 Microsoft 商業智慧 (BI) 工具進一步處理或顯示結果。

您必須安裝 Excel 2010 或 2013，才能完成這個部分的教學課程。

**下載 Microsoft Power Query for Excel**

-   從 [Microsoft 下載中心][Microsoft 下載中心]下載並安裝 Microsoft Power Query for Excel。

**匯入 HDInsight 資料**

1.  開啟 Excel，並建立新的空白活頁簿。
2.  依序按一下 [Power Query] 功能表、[From Other Sources]、[From Azure HDInsight]。

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  輸入叢集相關 Azure Blob 儲存體帳戶的 [帳戶名稱]，然後按一下 [確定]。這是您在教學課程稍早建立的儲存體帳戶。
4.  輸入 Azure Blob 儲存體帳戶的 [帳戶金鑰]，然後按一下 [儲存]。
5.  在右邊的瀏覽器窗格中，按兩下 Blob 儲存體容器名稱。依預設，容器名稱與叢集名稱相同。

6.  在 [名稱] 欄位中找出 **stdout** (路徑為 *.../Templeton-Job-Status/<guid>*)，然後按一下 **stdout** 左側的[二進位]。<guid> 必須符合您在上一節記下的識別碼。

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  按一下左上角的 [Apply & Close]。查詢隨即將 Hive 工作輸出匯入 Excel 中。

## <a name="nextsteps"></a>後續步驟

在本教學課程中，您已經了解如何使用 HDInsight 佈建叢集、在其中執行 MapReduce 工作，並且將結果匯入 Excel，以便使用 BI 工具進行後續處理並以圖形顯示。若要深入了解，請參閱下列文章：

-   [開始使用 HDInsight Emulator][開始使用 HDInsight Emulator]
-   [在 HDInsight 上使用 Azure Blob 儲存體][搭配 HDInsight 使用 Azure Blob 儲存體]
-   [使用 PowerShell 管理 HDInsight][使用 PowerShell 管理 HDInsight]
-   [將資料上傳到 HDInsight][將資料上傳到 HDInsight]
-   [將 HDInsight 與 MapReduce 搭配使用][將 HDInsight 與 MapReduce 搭配使用]
-   [搭配 HDInsight 使用 Hive][搭配 HDInsight 使用 Hive]
-   [搭配 HDInsight 使用 Pig][搭配 HDInsight 使用 Pig]
-   [在 HDInsight 上使用 Oozie][在 HDInsight 上使用 Oozie]
-   [開發 HDInsight 的 C# Hadoop 串流程式][開發 HDInsight 的 C# Hadoop 串流程式]
-   [開發 HDInsight 的 Java MapReduce 程式][開發 HDInsight 的 Java MapReduce 程式]

  [Apache Hadoop]: http://hadoop.apache.org/
  [HDInsight 所提供叢集版本的新功能]: ../hdinsight-component-versioning/
  [![HDI.getstarted.video][img-hdi-getstarted-video]]: https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS
  [開始使用 HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [購買選項]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [HDInsight 版本資訊]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-release-notes/
  [佈建 HDInsight 叢集]: #provision
  [執行 Hive 工作]: #sample
  [連接到 Microsoft 商業智慧工具]: #powerquery
  [後續步驟]: #nextsteps
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [1]: ../hdinsight-provision-clusters/
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [如何建立儲存體帳戶]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Microsoft 下載中心]: http://www.microsoft.com/zh-tw/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [使用 PowerShell 管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [將資料上傳到 HDInsight]: ../hdinsight-upload-data/
  [將 HDInsight 與 MapReduce 搭配使用]: ../hdinsight-use-mapreduce
  [搭配 HDInsight 使用 Hive]: ../hdinsight-use-hive/
  [搭配 HDInsight 使用 Pig]: ../hdinsight-use-pig/
  [在 HDInsight 上使用 Oozie]: ../hdinsight-use-oozie/
  [開發 HDInsight 的 C# Hadoop 串流程式]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [開發 HDInsight 的 Java MapReduce 程式]: ../hdinsight-develop-deploy-java-mapreduce/
