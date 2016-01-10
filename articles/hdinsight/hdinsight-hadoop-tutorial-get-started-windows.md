<properties
   pageTitle="Hadoop 教學課程：開始在 Windows 上使用 Hadoop |Microsoft Azure"
   description="開始使用 HDInsight 中的 Hadoop。了解如何在 Windows 上建立 Hadoop 叢集、對資料執行 Hive 查詢，以及在 Excel 中分析輸出的資料。"
   keywords="hadoop 教學課程,windows 上的 hadoop,hadoop 叢集,了解 hadoop,hive 查詢"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/13/2015"
   ms.author="nitinme"/>


# Hadoop 教學課程：開始在 Windows 上的 HDInsight 中使用 Hadoop

> [AZURE.SELECTOR]
- [Windows](../hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](../hdinsight-hadoop-linux-tutorial-get-started.md)

為了協助您了解在 Windows 上的 Hadoop 並開始使用 HDInsight，本教學課程為您示範如何在 Hadoop 叢集上執行 Hive 查詢非結構化資料，並且接著在 Microsoft Excel 中分析結果。

假設您有一個大型的非結構化資料集，而且您想要在此資料集上執行查詢，以擷取一些有用資訊。這正是您要在這個教學課程中做的事。以下是要達成此目標的作法：

   ![Hadoop 教學課程：建立帳戶；建立 Hadoop 叢集；提交 Hive 查詢；在 Excel 中分析資料。][image-hdi-getstarted-flow]

觀看本教學課程示範影片以了解 HDInsight 上的 Hadoop：

![第一個 Hadoop 教學課程的影片：在 Hadoop 叢集上提交 Hive 查詢，並在 Excel 中分析結果。][img-hdi-getstarted-video]

**[在 YouTube 上觀看 HDInsight 的 Hadoop 教學課程](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**

除了全面供應 Azure HDInsight 之外，Microsoft 還提供 HDInsight Emulator for Azure，先前稱為 *Microsoft HDInsight Developer Preview*。模擬器非常適合開發人員使用，並只支援單一節點部署。如需使用 HDInsight Emulator 的詳細資訊，請參閱〈[開始使用 HDInsight Emulator][hdinsight-emulator]〉。

### 必要條件

開始進行在 Windows 上的 Hadoop 教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- 安裝 Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus 的**工作站電腦**。

##建立 Hadoop 叢集

當您建立叢集時，即建立了包含 Hadoop 及相關應用程式的 Azure 計算資源。在本節中，您將建立 HDInsight 3.2 版的叢集。您也可以建立其他版本的 Hadoop 叢集。如需相關指示，請參閱〈[使用自訂選項建立 HDInsight 叢集][hdinsight-provision]〉。如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱〈[HDInsight 元件版本設定](hdinsight-component-versioning.md)〉。


**如何建立 Hadoop 叢集**

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 依序按一下 [新增]、[資料分析] 及 [HDInsight]。此時入口網站會開啟 [新的 HDInsight 叢集] 刀鋒視窗。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.1.png "在 Azure 入口網站中建立新的叢集")

3. 輸入或選取下列欄位的值：

	![輸入叢集名稱和類型](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.2.png "輸入叢集名稱和類型")
	
	|欄位名稱| 值|
	|----------|------|
	|叢集名稱| 用來識別叢集的唯一名稱|
	|叢集類型| 請為本教學課程選取 [Hadoop]。 |
	|叢集作業系統| 請為本教學課程選取 [Windows Server 2012 R2 Datacenter]。|
	|HDInsight 版本| 請為本教學課程選取最新的版本。|
	|訂閱| 請選取將用於此叢集的 Azure 訂用帳戶。|
	|資源群組 | 請選取現有的 Azure 資源群組，或建立新的資源群組。基本的 HDInsight 叢集包含一個叢集，以及它的預設儲存體帳戶。您可以把兩個叢集組成一個資源群組來方便管理。|
	|認證| 請輸入叢集登入使用者名稱和密碼。以 Windows 為基礎的叢集可以有 2 個使用者帳戶。系統會利用叢集使用者 (或 HTTP 使用者) 來管理叢集及提交工作。(選用) 您可以建立遠端桌面 (RDP) 使用者帳戶，以便遠端連線到叢集。如果您選擇啟用遠端桌面，您將建立 RDP 使用者帳戶。|
	|資料來源| 請按一下 [建立新項目] 來建立新的預設 Azure 儲存體帳戶。請把叢集名稱當做預設的容器名稱。每個 HDinsight 叢集在 Azure 儲存體帳戶中都有一個預設的 Blob 容器。而預設 Azure 儲存體帳戶的位置，會決定 HDInsight 叢集的位置。|
	|節點定價層| 請為本教學課程搭配預設背景工作角色節點和前端節點訂價層來使用 1 或 2 個背景工作節點。|
	|選用組態| 請略過這部分。|

9. 在 [新的 HDInsight 叢集] 刀鋒視窗中，確認已選取 [釘選到「開始面板」]，然後按一下 [建立]。這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。該圖示會顯示叢集正在建立中，並會在叢集建立完成時變更為 HDInsight 圖示。

	| 在建立期間 | 建立完成 |
	| ------------------ | --------------------- |
	| ![「開始面板」上的建立指示器](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioning.png) | ![已建立叢集磚](./media/hdinsight-hadoop-tutorial-get-started-windows/provisioned.png) |

	> [AZURE.NOTE]建立叢集需要一些時間，通常約 15 分鐘左右。請使用「開始面板」上的磚，或頁面左邊的 [通知] 項目來以檢查建立進度。

10. 建立完成後，在「開始面板」按一下該叢集磚，以啟動叢集刀鋒視窗。


## 從入口網站執行 Hive 查詢
您已經建立 HDInsight 叢集，下一個步驟是執行 Hive 工作來查詢範例 Hive 資料表。我們會使用 HDInsight 叢集隨附的 *hivesampletable* 。此資料表會包含行動裝置製造商、平台及模型等資料。使用 Hive 查詢資料表會擷取特定製造商的行動裝置的資料。

> [AZURE.NOTE]HDInsight Tools for Visual Studio 隨附 Azure SDK for .NET 2.5 版或更新版本。您可以使用 Visual Studio 的工具，連線至 HDInsight 叢集、建立 Hive 資料表及執行 Hive 查詢。如需詳細資訊，請參閱〈[開始使用 HDInsight Hadoop Tools for Visual Studio][1]〉。

**從叢集儀表板執行 Hive 工作**

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。
2. 按一下 [瀏覽全部]，然後按一下 [HDInsight 叢集] 以檢視叢集清單，包括您在上一節中建立的叢集。
3. 按一下您要用來執行 Hive 工作的叢集名稱，然後按一下刀鋒視窗頂端的 [儀表板]。
4. 網頁會在不同瀏覽器索引標籤中開啟。輸入 Hadoop 使用者帳戶和密碼。預設使用者名稱為 **admin**，而密碼是您在建立叢集時所輸入的密碼。
5. 在儀表板中，按一下 [Hive 編輯器] 索引標籤。以下網頁隨即開啟。

	![HDInsight 叢集儀表板中的 [Hive 編輯器] 索引標籤。][img-hdi-dashboard]

	頁面頂端會有數個索引標籤。預設索引標籤為 [**Hive 編輯器**]，其他索引標籤包括 [**工作歷程記錄**] 和 [**檔案瀏覽器**]。您可以使用儀表板提交 Hive 查詢、檢查 Hadoop 工作記錄及瀏覽儲存體中的檔案。

	> [AZURE.NOTE]請注意，網頁的 URL 為 *&lt;ClusterName&gt;.azurehdinsight.net* 。因此，與其從入口網站開啟儀表板，您也可以使用 URL 從網頁瀏覽器中開啟儀表板。

6. 在 [Hive Editor] 索引標籤的 [查詢名稱] 中，輸入 **HTC20**。查詢名稱就是工作標題。在查詢窗格中，如圖所示輸入 Hive 查詢：

	![[Hive 編輯器] 查詢窗格中輸入的 Hive 查詢。][img-hdi-dashboard-query-select]

4. 按一下 [提交]。需要一點時間才會取回結果。螢幕每 30 秒會重新整理一次。您也可以按一下 [重新整理] 來重新整理螢幕。

    ![Hive 查詢的結果會列在叢集儀表板底部。][img-hdi-dashboard-query-select-result]

5. 在狀態顯示已完成工作之後，按一下螢幕上的查詢名稱以查看輸出。記下 [工作開始時間 (UTC)]。稍後您將會用到此資訊。

    ![HDInsight 叢集儀表板的 [工作歷程記錄] 索引標籤中列出的 [工作開始時間]。][img-hdi-dashboard-query-select-result-output]

    此頁面也會顯示 [工作輸出] 和 [工作記錄]。您還可以選擇下載輸出檔案 (\_stdout) 和記錄檔 (\_stderr)。


**瀏覽至輸出檔案**

1. 在叢集儀表板中，按一下 [**檔案瀏覽器**]。
2. 依序按一下您的儲存體帳戶名稱、容器名稱 (會與您的叢集名稱相同)，然後按一下 [使用者]。
3. 按一下 [**admin**]，然後按一下其上次修改時間比您先前記下的工作開始時間稍晚的 GUID。複製此 GUID。您在下一節將會用到此號碼。


   	![[檔案瀏覽器] 索引標籤中列出的 Hive 查詢輸出檔案 GUID。][img-hdi-dashboard-query-browse-output]


##連線到適用於 Excel 的 Microsoft Business Intelligence工具

您可以使用適用於 Microsoft Excel 的 Power Query 增益集，將工作輸出從 HDInsight 匯入 Excel，然後使用 Microsoft 商業智慧工具來進一步分析結果。

您必須安裝 Excel 2013 或 2010，才能完成這個部分的教學課程。

**下載 Microsoft Power Query for Excel**

- 從 [Microsoft 下載中心](http://www.microsoft.com/download/details.aspx?id=39379)下載並安裝 Microsoft Power Query for Microsoft Excel。

**匯入 HDInsight 資料**

1. 開啟 Excel，並建立新的活頁簿。
3. 依序按一下 [Power Query] 功能表、[From Other Sources]、[From Azure HDInsight]。

	![[Excel PowerQuery 匯入] 功能表針對 Azure HDInsight 開啟。][image-hdi-gettingstarted-powerquery-importdata]

3. 輸入叢集相關 Azure Blob 儲存體帳戶的 [**帳戶名稱**]，然後按一下 [**確定**]。(這是您在教學課程稍早建立的儲存體帳戶。)
4. 輸入 Azure Blob 儲存體帳戶的 [**帳戶金鑰**]，然後按一下 [**儲存**]。
5. 在右窗格中，按兩下 Blob 名稱。依預設，Blob 名稱與叢集名稱相同。

6. 在 [名稱] 欄中找到 **stdout**。驗證對應 [**資料夾路徑**] 欄中的 GUID 是否符合您先前複製的 GUID。相符就表示輸出資料對應於您提交的工作。按一下 **stdout** 左邊欄的 [**二進位**]。

	![在內容清單中依據 GUID 尋找資料輸出。][image-hdi-gettingstarted-powerquery-importdata2]

9. 按一下左上角的 [**關閉及載入**] 以將 Hive 工作輸出匯入 Excel。

##執行範例

HDInsight 叢集會提供查詢主控台，其中包括快速入門組件庫，可讓您直接從入口網站執行範例。您可以利用範例來逐步完成一些基本案例，以了解如何使用 HDInsight。這些範例隨附所有必要的元件，例如要分析的資料及在資料上執行的查詢。若要深入了解 [開始使用] 資源庫中的範例，請參閱〈[使用 HDInsight 開始使用資源庫了解 HDInsight 中的 Hadoop](hdinsight-learn-hadoop-use-sample-gallery.md)〉。

**執行範例**

1. 在 Azure 入口網站的「開始面板」按一下您剛才所建立叢集的磚。
 
2. 在新的叢集刀鋒視窗中，按一下 [儀表板]。出現提示時，輸入該叢集的管理員使用者名稱和密碼。

	![啟動叢集儀表板](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.Cluster.Dashboard.png "啟動叢集儀表板")
 
3. 在開啟的網頁中，按一下 [快速入門組件庫] 索引標籤，然後在 [使用範例資料的解決方案] 類別下，按一下您要執行的範例。請遵循網頁上的指示完成範例。下表列出一些範例，並且提供關於每個範例之用途的詳細資訊。

範例 | 用途
------ | ---------------
[感應器資料分析][hdinsight-sensor-data-sample] | 了解如何使用 HDInsight 來處理暖氣、通風和空調 (HVAC) 系統所產生的歷史資料，以識別無法可靠地維持規定溫度的系統。
[網站記錄分析][hdinsight-weblogs-sample] | 了解如何使用 HDInsight 來分析網站記錄檔，以深入了解一天之中來自外部網站的造訪次數，以及使用者遭遇網站錯誤的摘要。
[Twitter 趨勢分析](hdinsight-analyze-twitter-data.md) | 了解如何使用 HDInsight 來分析 Twitter 的趨勢。



##後續步驟
在本 Hardoop 教學課程中，您已經了解如何在 Windows 上使用 HDInsight 建立叢集、對資料執行 Hive 查詢，並且將結果匯入 Excel，以便使用商業智慧工具進行後續處理及圖形顯示。若要深入了解，請參閱下列教學課程：

- [〈開始使用 HDInsight Hadoop Tools for Visual Studio〉][1]
- [〈開始使用 HDInsight Emulator〉][hdinsight-emulator]
- [搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]
- [〈使用 PowerShell 管理 HDInsight〉][hdinsight-admin-powershell]
- [〈將資料上傳到 HDInsight〉][hdinsight-upload-data]
- [〈搭配 HDInsight 使用 MapReduce〉][hdinsight-use-mapreduce]
- [〈搭配 HDInsight 使用 Hivet〉][hdinsight-use-hive]
- [〈搭配 HDInsight 使用 Pig〉][hdinsight-use-pig]
- [〈在 HDInsight 上使用 Oozie〉][hdinsight-use-oozie]
- [〈開發 HDInsight 的 C# Hadoop 串流程式〉][hdinsight-develop-streaming]
- [〈開發 HDInsight 的 Java MapReduce 程式〉][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://ms.portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!----HONumber=AcomDC_1203_2015-->