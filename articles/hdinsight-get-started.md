<properties 
	pageTitle="開始在 HDInsight 中使用 Hadoop 搭配 Hive | Azure" 
	description="開始使用 HDInsight 中的 Hadoop，這是雲端的巨量資料解決方案。了解如何佈建叢集、使用 Hive 查詢資料，以及輸出至 Excel 進行分析。" 
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
	ms.date="10/14/2014" 
	ms.author="nitinme"/>


# 開始在 HDInsight 中使用 Hadoop 搭配 Hive 以分析行動電話使用

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

為讓您快速使用 HDInsight，本教學課程將示範如何執行 Hive 查詢，以從 Hadoop 叢集的未結構化資料擷取有意義的資訊。之後，您將在 Microsoft Excel 中分析結果。


> [AZURE.NOTE] 如果您是 Hadoop 和巨量資料的新使用者，您可以進一步了解這些術語：[Apache Hadoop][apache-hadoop]、[MapReduce][apache-mapreduce]、[HDFS][apache-hdfs] 和 [Hive][apache-hive]。若要了解 HDInsight 如何在 Azure 中啟用 Hadoop，請參閱 [HDInsight 中 Hadoop 的簡介][hadoop-hdinsight-intro]。

除了全面供應 Azure HDInsight 之外，Microsoft 還提供 HDInsight Emulator for Azure，先前稱為  *Microsoft HDInsight Developer Preview*。模擬器非常適合開發人員使用，並只支援單一節點部署。關於使用 HDInsight Emulator，請參閱[開始使用 HDInsight Emulator][hdinsight-emulator]。

> [AZURE.NOTE] 如需如何佈建 HBase 叢集的指示，請參閱[在 HDInsight 上佈建 HBase 叢集][hdinsight-hbase-custom-provision]。請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Hadoop 和 HBase 之間有何不同？</a> 以了解為何您必須在兩者中擇一。   

## 本教學課程要達到什麼目標？ ##

假設您有一個大型的非結構化資料集，而且您想要在此資料集上執行查詢以擷取一些有用資訊。這正是我們要在此教學課程中進行的動作。以下是要達成此目標的作法：

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

您也可以觀看本教學課程的示範影片：

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：


- Azure 訂閱。如需有關如何取得訂閱的詳細資訊，請參閱[購買選項][azure-purchase-options]、[會員優惠][azure-member-offers]，或[免費試用版][azure-free-trial]。
- 安裝 Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus 的電腦。

**預估完成時間：**30 分鐘

##本教學課程內容

* [建立 Azure 儲存體帳戶](#storage)
* [佈建 HDInsight 叢集](#provision)
* [從入口網站執行範例](#sample)
* [執行 HIVE 工作](#hivequery)
* [後續步驟](#nextsteps)

##<a name="storage"></a>建立 Azure 儲存體帳戶

HDInsight 會使用 Azure Blob 儲存體來儲存資料。稱為 *WASB* 或 *Azure Storage - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。

佈建 HDInsight 叢集時，您可以指定 Azure 儲存體帳戶。將該帳戶的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。依預設，系統會在與您指定儲存體帳戶的相同資料中心內佈建 HDInsight 叢集。

除了此儲存體帳戶以外，您可以在自訂設定 HDInsight 叢集時新增其他儲存體帳戶。這個其他儲存體帳戶可以來自相同的 Azure 訂用帳戶或不同的 Azure 訂用帳戶。如需相關指示，請參閱[使用自訂選項佈建 HDInsight 叢集][hdinsight-provision]。 

為了簡化本教學課程，將僅使用預設的 Blob 容器和預設的儲存體帳戶。實際上，資料檔案通常會儲存在指定的儲存體帳戶中。

**建立 Azure 儲存體帳戶**

1. 登入 [Azure 管理入口網站][azure-management-portal]。
2. 按一下左下角的 [**新增**]，並指向 [**資料服務**]，接著指向 [**儲存體**]，然後按一下 [**快速建立**]。

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. 輸入 [**URL**]、[**位置**] 和 [**複寫**]，然後按一下 [**建立儲存體帳戶**]。不支援同質群組。您將在儲存體清單中看見新的儲存體帳戶。

	>[AZURE.NOTE]  佈建 HDInsight 叢集的快速建立選項 (如在本教學課程中所使用的選項) 在佈建叢集時並不會詢問位置。相反地，它會依預設將叢集並存於與儲存體帳戶相同的資料中心內。因此，請務必在支援叢集的位置建立您的儲存體帳戶，這些位置包括：**東亞**、**東南亞**、**北歐**、**西歐**、**美國東部**、**西部**、**美國中北部**、**美國中南部**。

4. 等候新儲存體帳戶的 [**狀態**] 變更為 [**線上**]。
5. 從清單中選取新儲存體帳戶，並按一下頁面底部的 [**管理存取金鑰**]。
7. 請記下 [**儲存體帳戶名稱**] 和 [**主要存取金鑰**] (或 [**次要存取金鑰**]。其中任何一個金鑰皆可)。稍後在教學課程中需要這些資訊。


如需詳細資訊，請參閱
[如何建立儲存體帳戶][azure-create-storageaccount]和[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。
	
##<a name="provision"></a>佈建 HDInsight 叢集

佈建 HDInsight 叢集時，您可以佈建包含 Hadoop 及相關應用程式的 Azure 運算資源。在本節中，您將佈建採用 Hadoop 2.4 版的 HDInsight 叢集 3.1 版。您也可以使用 Azure 入口網站、HDInsight PowerShell Cmdlet 或 HDInsight .NET SDK 來建立其他版本的 Hadoop 叢集。如需相關指示，請參閱[使用自訂選項佈建 HDInsight 叢集][hdinsight-provision]。如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱 [HDInsight 元件版本設定](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-component-versioning/)頁面。

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**佈建 HDInsight 叢集** 

1. 登入 [Azure 管理入口網站][azure-management-portal]。 

2. 按一下左邊的 [**HDInsight**] 列出您帳戶中的叢集狀態。在下列螢幕擷取畫面中，沒有任何現有的 HDInsight 叢集。

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. 按一下左下角的 [**新增**]，然後依序按一下 [**資料服務**]、[**HDInsight**] 和 [**Hadoop**]。

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. 輸入或選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值</th></tr>
	<tr><td>叢集名稱</td><td>叢集的名稱</td></tr>
	<tr><td>叢集大小</td><td>您要部署的資料節點數。預設值為 4。但您也可以從下拉式清單中選擇使用 1 或 2 個資料節點。可以指定建立任何數目的叢集節點，方法是使用 <strong>自訂建立</strong> 選項。另外也提供各種叢集大小的費率定價詳細資料。按一下 <strong>?</strong> 符號 (下拉式方塊正上方)，並遵循快顯功能表上的連結。</td></tr>
	<tr><td>密碼</td><td>表示 <i>admin</i> 帳戶的密碼。系統會將叢集使用者名稱指定為 "admin"，前提是未使用 <strong>自訂建立</strong> 選項。請注意，這「不是」指佈建叢集所在 VM 的 Windows 系統管理員帳戶。可變更帳戶名稱，方法是使用 <strong>自訂建立</strong> 精靈。</td></tr>
	<tr><td>儲存體帳戶</td><td>從下拉式方塊中，選取您建立的儲存體帳戶。 <br/>

	一旦選擇儲存體帳戶之後，便無法變更。如果移除儲存體帳戶，則無法再使用叢集。

	HDInsight 叢集會並存於與儲存體帳戶相同的資料中心內。 
	</td></tr>
	</table>

	保留叢集名稱的複本。稍後在教學課程中將會需要這些資訊。

	
5. 按一下 [**建立 HDInsight 叢集**]。佈建完成時，狀態欄便會顯示 [**執行中**]。

	>[AZURE.NOTE] 以上程序會採用 HDInsight 叢集 3.1 版建立叢集。若要建立其他叢集版本，您可以在管理入口網站中使用自訂建立方法，或使用 Azure PowerShell。如需每個叢集版本之間的差異相關資訊，請參閱 [HDInsight 所提供叢集版本的新功能][hdinsight-versions]。如需使用 [**自訂建立**] 選項的相關資訊，請參閱[使用自訂選項佈建 HDInsight 叢集][hdinsight-provision]。


##<a name="sample"></a>從入口網站執行範例

佈建成功的 HDInsight 叢集會提供查詢主控台，可從入口網站直接執行範例。您可以利用範例來逐步完成一些基本案例，以了解如何使用 HDInsight。這些範例隨附所有必要的元件，例如要分析的資料及在資料上執行的查詢。 

**若要執行範例**，請從 Azure 管理入口網站按一下您要執行範例的叢集名稱，然後按一下頁面底部的 [**查詢主控台**]。從開啟的網頁中，按一下 [**開始使用組件庫**] 索引標籤，然後在 [**範例**] 類別下，按一下您要執行的範例。依照網頁上的指示完成範例。如需深入了解每個範例的用途，請按下面的連結。

範例 | 用途
------ | ---------------
[感應器資料分析][hdinsight-sensor-data-sample] | 了解如何使用 HDInsight 來處理暖氣、通風和空調 (HVAC) 系統所產生的歷史資料，以識別無法可靠地維持規定溫度的系統。
[網站記錄分析][hdinsight-weblogs-sample] | 了解如何使用 HDInsight 來分析網站記錄檔，以深入了解一天之中來自外部網站的造訪次數，以及使用者遭遇網站錯誤的摘要。


##<a name="hivequery"></a>從入口網站執行 HIVE 查詢
現在您已佈建一個 HDInsight 叢集，下一個步驟是執行 Hive 工作，以查詢 HDInsight 叢集隨附的範例 Hive 資料表  *hivesampletable*。此資料表會包含行動裝置製造商、平台及模型等資料。查詢這個資料表，以擷取特定製造商的行動裝置資料。

> [AZURE.NOTE] HDInsight Tools for Visual Studio 隨附於 Azure SDK for .NET 2.5 版或更新版本。  使用 Visual Studio 提供的工具，您可以連線至 HDInsight 叢集、建立 Hive 資料表，並執行 Hive 查詢。  如需詳細資訊，請參閱[開始使用 HDInsight Hadoop Tools for Visual Studio][1]。

**從叢集儀表板執行 Hive 工作**

1. 登入 [Azure 管理入口網站][azure-management-portal]。 
2. 按一下左窗格上的 [**HDInsight**]。您會看見已建立的叢集清單，其中包含您在上一節中建立的叢集。
3. 按一下您要執行 Hive 工作的叢集名稱，然後按一下頁面底部的 [**查詢主控台**]。 
4. 它會在不同瀏覽器索引標籤上開啟網頁。輸入 Hadoop 使用者帳戶和密碼。  預設使用者名稱為 **admin**；密碼是您在佈建叢集時所輸入的密碼。  儀表板顯示如下：

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	頂端會有數個索引標籤。  預設索引標籤為 [**Hive 編輯器**]，其他索引標籤包括 [**工作歷程記錄**] 和 [**檔案瀏覽器**]。  您可透過儀表板提交 Hive 查詢、檢查 Hadoop 工作記錄及瀏覽 WASB 檔案。

	> [AZURE.NOTE] 請注意，網頁的 URL 為 *&lt;ClusterName&gt;.azurehdinsight.net*。因此，與其從管理入口網站開啟儀表板，您也可以使用 URL 從網頁瀏覽器中開啟儀表板。

6. 在 [**Hive 編輯器**] 索引標籤的 [**查詢名稱**] 中，輸入 **HTC20**。  查詢名稱就是工作標題。

7. 在查詢窗格中，輸入下列查詢： 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. 按一下 [**提交**]。需要一點時間才會取回結果。螢幕每 30 秒會重新整理一次。您也可以按一下 [**重新整理**] 來重新整理螢幕。

    完成後，螢幕會如下所示：

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. 按一下螢幕上的查詢名稱以查看輸出。記下 [**工作開始時間 (UTC)**]。稍後您將會用到此資訊。 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    此頁面也會顯示 [**工作輸出**] 和 [**工作記錄**]。您還可以選擇下載輸出檔案 (\_stdout) 和記錄檔 \(_stderr)。


	> [AZURE.NOTE] 只要您停留在 [**Hive 編輯器**] 索引標籤上，該索引標籤上的 [**工作階段**] 資料表便會列出已完成或執行中的工作。如果您瀏覽至其他頁面，則此資料表不會列出任何工作。[**工作歷程記錄**] 索引標籤會維護所有工作、已完成或執行中等清單。
 

**瀏覽至輸出檔案**

1. 在叢集儀表板中，按一下頂端的 [**檔案瀏覽器**]。 
2. 依序按一下您的儲存體帳戶名稱、容器名稱 (會與您的叢集名稱相同)，然後按一下 [**使用者**]。
3. 按一下 [**admin**]，然後按一下其上次修改時間比您先前記下的工作開始時間稍晚的 GUID。記下此 GUID。您在下一節將會用到此號碼。


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>連接到 Microsoft 商業智慧工具 

您可以使用適用於 Microsoft Excel 的 Power Query 增益集，將工作輸出從 HDInsight 匯入 Excel，然後使用 Excel 的 Microsoft 商業智慧 (BI) 工具來進一步分析結果。 

您必須安裝 Excel 2010 或 2013，才能完成這個部分的教學課程。 

**下載 Microsoft Power Query for Excel**

- 從 [Microsoft 下載中心](http://www.microsoft.com/zh-tw/download/details.aspx?id=39379)下載並安裝 Microsoft Power Query for Microsoft Excel。

**匯入 HDInsight 資料**

1. 開啟 Excel，並建立新的空白活頁簿。
3. 依序按一下 [**Power Query**] 功能表、[**從其他來源**] 和 [**從 Azure HDInsight**]。

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. 輸入叢集相關 Azure Blob 儲存體帳戶的 [**帳戶名稱**]，然後按一下 [**確定**]。這是您在教學課程稍早建立的儲存體帳戶。
4. 輸入 Azure Blob 儲存體帳戶的 [**帳戶金鑰**]，然後按一下 [**儲存**]。 
5. 在右邊的瀏覽器窗格中，按兩下 Blob 儲存體容器名稱。依預設，容器名稱與叢集名稱相同。 

6. 在 [**名稱**] 欄中找到 **stdout**。驗證對應 [**資料夾路徑**] 欄中的 GUID 是否符合您先前記下的 GUID。相符就表示輸出資料對應於您提交的工作。按一下 **stdout** 左邊的 [**二進位**]。

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. 按一下左上角的 [**關閉並載入**] 以將 Hive 工作輸出匯入 Excel。


##<a name="nextsteps"></a>後續步驟
在本教學課程中，您已經了解如何使用 HDInsight 佈建叢集、在其中執行 MapReduce 工作，並且將結果匯入 Excel，以便使用 BI 工具進行後續處理並以圖形顯示。若要深入了解，請參閱下列文章：

- [開始使用 HDInsight Hadoop Tools for Visual Studio][1]
- [開始使用 HDInsight Emulator][hdinsight-emulator]
- [使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]
- [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
- [將資料上傳至 HDInsight][hdinsight-upload-data]
- [使用 MapReduce 搭配 HDInsight
- [使用 Hive 搭配 HDInsight][hdinsight-use-hive]
- [使用 Pig 搭配 HDInsight][hdinsight-use-pig]
- [使用 Oozie 搭配 HDInsight][hdinsight-use-oozie]
- [開發 HDInsight 的 C# Hadoop 串流程式][hdinsight-develop-streaming]
- [開發 HDInsight 的 Java MapReduce 程式][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=42-->
