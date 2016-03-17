<properties
   	pageTitle="Linux 教學課程：開始使用 Hadoop 和 Hive |Microsoft Azure"
   	description="遵循本 Linux 教學課程，開始在 HDInsight 中使用 Hadoop。了解如何佈建 Linux 叢集，以及使用 Hive 查詢資料。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/03/2016"
   	ms.author="jgao"/>

# Hadoop 教學課程：開始在 HDInsight 中使用以 Linux 為基礎的 Hadoop

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

了解如何在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集，以及如何使用 Ambari Hive 檢視來執行 Hive 工作。

如果您不熟悉 Hadoop 和巨量資料，您可以進一步了解這些術語：[Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084)、[MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086)、[Hadoop 分散式檔案系統 (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) 及 [Hive](http://go.microsoft.com/fwlink/?LinkId=510085)。若要了解 HDInsight 如何在 Azure 中啟用 Hadoop，請參閱 [HDInsight 中 Hadoop 的簡介](hdinsight-hadoop-introduction.md)。

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### 必要條件

開始進行本教學課程之前，您必須具備：

- **Azure 訂用帳戶**︰若要建立免費試用帳戶，請瀏覽至 [azure.microsoft.com/free](https://azure.microsoft.com/free)。

## 建立叢集

大部分 Hadoop 作業都是批次作業。您會建立叢集、執行一些工作，然後刪除叢集。在本節中，您將在 HDInsight 中使用 [Azure ARM 範本](../resource-group-template-deploy.md)來建立以 Linux 為基礎的 Hadoop 叢集。您可以完全自訂 ARM 範本，讓您能夠輕鬆地建立 Azure 資源 (例如 HDInsight)。進行本教學課程並不需要具備 Azure ARM 範本經驗。如需其他叢集建立方法及了解本教學課程中使用的屬性，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。如需使用 ARM 範本在 HDInsight 中建立 Hadoop 叢集的詳細資訊，請參閱[使用 ARM 範本在 HDInsight 中建立 Hadoop 叢集](hdinsight-hadoop-create-windows-clusters-arm-templates.md)。本教學課程中使用的 ARM 範本位於公用 Blob 容器 (**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json*) 中。

1. 按一下以下影像，在 Azure 入口網站中開啟 ARM 範本。 

	<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從 [參數] 刀鋒視窗，輸入下列項目：

    - **ClusterName**：輸入您將建立的 Hadoop 叢集名稱。
    - **叢集登入名稱和密碼**：預設登入名稱是 **admin**。
    - **SSH 使用者名稱和密碼**：預設使用者名稱是 **sshuser**。您可以將它重新命名。 
    
    其他參數都是選擇性的。您可以將它們維持不變。
    
    每個叢集都具備 Azure Blob 儲存體帳戶相依性。刪除叢集之後，資料會保留在儲存體帳戶中。叢集預設儲存體帳戶名稱是附加 "store" 的叢集名稱。它會硬式編碼在範本變數區段中。
    
3. 按一下 [確定] 儲存參數。
4. 在 [自訂部署] 刀鋒視窗中，按一下 [資源群組] 下拉式方塊，然後按一下 [新增] 來建立新的資源群組。資源群組是聚集叢集、相依儲存體帳戶和其他已連結資源的容器。資源群組位置可以和叢集位置不同。
5. 按一下 [法律條款]，然後按一下 [建立]。
6. 確認已選取 [釘選到儀表板] 核取方塊，然後按一下 [建立]。您將會看到新的磚，標題為**提交範本部署的部署**。大約需要 20 分鐘的時間來建立叢集。一旦建立叢集後，您可以在入口網站按一下 [叢集] 刀鋒視窗來開啟它。

完成本教學課程之後，您可以刪除叢集。利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。您也需支付 HDInsight 叢集的費用 (即使未使用)。由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。如需刪除叢集的相關指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-management-portal.md#delete-clusters)。

**瀏覽叢集**

1. 從[入口網站](http://portal.azure.com)，按一下左上角的 [Microsoft Azure] 以開啟儀表板。 
2. 按兩下包含叢集名稱的磚，在刀鋒視窗中開啟該叢集。因為您選取了 [釘選到儀表板]，所以隨即會建立這個磚。
3. [程式集] 窗格會列出一些基本資訊。按一下 [設定]，以查看叢集的多個屬性。

##執行 Hive 查詢

[Apache Hive](hdinsight-use-hive.md) 是 HDInsight 中使用的最受歡迎的工具。有許多方法可在 HDInsight 上執行 Hive 工作。在本教學課程中，您將使用 Ambari Hive 檢視，從入口網站執行一些 Hive 工作。如需提交 Hive 查詢的其他方法，請參閱[在 HDInsight 中使用 Hive](hdinsight-use-hive.md)。

1. 瀏覽至 **https://&lt;ClusterName>.azurehdinsight.net** 以開啟 Ambari，其中 &lt;ClusterName> 是您在上一節建立的叢集。
2. 輸入您在上一節指定的 Hadoop 使用者名稱和密碼。預設的使用者名稱為 **admin**。
3. 開啟 [Hive 檢視]，如下列螢幕擷取畫面所示：

    ![選取 ambari 檢視](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png)。
4. 在頁面的 [查詢編輯器] 區段中，將下列 HiveQL 陳述式貼到工作表中：

		SHOW TABLES;
5. 按一下 [Execute (執行)]。[查詢程序結果] 區段應該會出現在 [查詢編輯器] 下方並顯示作業相關資訊。 

    查詢完成後，[查詢程序結果] 區段會顯示作業的結果。您應該會看到一個名為 **hivesampletable** 的資料表。所有 HDInsight 叢集都提供此範例 Hive 資料表。

    ![HDInsight Hive 檢視](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png)。

    
    > [AZURE.TIP] 請注意，[查詢程序結果] 區段右上方的 [儲存結果] 下拉式清單；您可以使用此下拉式清單來下載結果，或以 CSV 檔案形式將它們儲存到 HDInsight 儲存體。
6. 重複步驟 4 和 5，以執行下列查詢：

        SELECT * FROM hivesampletable;

完成 Hive 工作之後，您可以[將結果匯出至 Azure SQL 資料庫或 SQL Server 資料庫](hdinsight-use-sqoop-mac-linux.md)，也可以[使用 Excel 將結果視覺化](hdinsight-connect-excel-power-query.md)。如需在 HDInsight 中使用 Hive 的詳細資訊，請參閱[搭配 HDInsight 中的 Hadoop 使用 Hive 和 HiveQL 來分析範例 Apache log4j 檔案](hdinsight-use-hive.md)。

##刪除叢集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 後續步驟

在本教學課程中，您已經學會如何使用 ARM 範本建立以 Linux 為基礎的 HDInsight 叢集，以及如何執行基本的 Hive 查詢。

若要深入了解如何使用 HDInsight 分析資料，請參閱下列主題：

- 若要深入了解如何搭配 HDInsight 使用 Hive，包括如何從 Visual Studio 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 Hive][hdinsight-use-hive]。

- 若要了解用來轉換資料的 Pig 語言，請參閱[搭配 HDInsight 使用 Pig][hdinsight-use-pig]。

- 若要了解 MapReduce (一種撰寫程式以處理 Hadoop 資料的方式)，請參閱[搭配 HDInsight 使用 MapReduce][hdinsight-use-mapreduce]。

- 若要了解如何使用適用於 Visual Studio 的 HDInsight 工具來分析 HDInsight 資料，請參閱[開始使用 Visual Studio Hadoop tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md)。

如果您準備好開始使用您自己的資料，並需要進一步了解 HDInsight 儲存資料的方式或如何將資料匯入 HDInsight，請參閱下列主題：

- 如需有關 HDInsight 如何使用 Azure Blob 儲存體的資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。

- 如需如何上傳資料到 HDInsight 的資訊，請參閱[將資料上傳到 HDInsight][hdinsight-upload-data]。

如果您想要深入了解如何建立或管理 HDInsight 叢集，請參閱下列主題：

- 若要了解如何管理以 Linux 為基礎的 HDInsight 叢集，請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)。

- 若要深入了解建立 HDInsight 叢集時可選取的選項，請參閱[使用自訂選項在 Linux 上建立 HDInsight](hdinsight-hadoop-provision-linux-clusters.md)。

- 如果您已熟悉 Linux 和 Hadoop，但想要知道在 HDInsight 上有關 Hadoop 的特定資訊，請參閱[在 Linux 上使用 HDInsight](hdinsight-hadoop-linux-information.md)。提供如下資訊：

	* 裝載於叢集上的服務 (例如 Ambari 和 WebHCat) URL
	* Hadoop 檔案和範例在本機檔案系統上的位置
	* Azure 儲存體 (WASB) (而非 HDFS) 做為預設資料儲存體的使用方式


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!----HONumber=AcomDC_0309_2016-->