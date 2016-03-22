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
   	ms.date="03/16/2016"
   	ms.author="jgao"/>

# Hadoop 教學課程：開始在 HDInsight 中使用以 Linux 為基礎的 Hadoop

> [AZURE.SELECTOR]
- [以 Windows 為基礎](hdinsight-hadoop-tutorial-get-started-windows.md)
- [以 Linux 為基礎](hdinsight-hadoop-linux-tutorial-get-started.md)

了解如何在 HDInsight 中建立以 Linux 為基礎的 [Hadoop](http://hadoop.apache.org/) 叢集，以及如何在 HDInsight 中執行 Hive 作業。[Apache Hive](https://hive.apache.org/) 是 Hadoop 生態系統中最受歡迎的元件。HDInsight 隨附 4 種叢集類型︰[Hadoop](hdinsight-hadoop-introduction)、[Spark](hdinsight-apache-spark-overview.md)、[HBase](hdinsight-hbase-overview.md) 和 [Storm](hdinsight-storm-overview.md)。每種叢集類型都支援一組不同的元件。這 4 種叢集類型都支援 Hive。如需 HDInsight 中支援的元件清單，請參閱 [HDInsight 在 Hadoop 叢集版本中提供的新功能](hdinsight-component-versioning.md)

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### 必要條件

開始進行本教學課程之前，您必須具備：

- Azure 訂用帳戶︰若要建立一個月的免費試用帳戶，請瀏覽至 [azure.microsoft.com/free](https://azure.microsoft.com/free)。

## 建立叢集

大部分 Hadoop 作業都是批次作業。您會建立叢集、執行一些工作，然後刪除叢集。在本節中，您將在 HDInsight 中使用 [Azure ARM 範本](../resource-group-template-deploy.md)來建立以 Linux 為基礎的 Hadoop 叢集。您可以完全自訂 ARM 範本，讓您能夠輕鬆地建立 Azure 資源 (例如 HDInsight)。進行本教學課程並不需要具備 Azure ARM 範本經驗。如需其他叢集建立方法及了解本教學課程中使用的屬性，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。本教學課程中使用的 ARM 範本位於公用 Blob 容器中，[https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json)。

1. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 ARM 範本。 

	<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從 [參數] 刀鋒視窗，輸入下列資料：

    ![入口網站上 HDInsight Linux 開始使用的 arm 範本](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png)。

    - ClusterName：輸入您將建立的 Hadoop 叢集名稱。
    - 叢集登入名稱和密碼：預設登入名稱是 admin。
    - SSH 使用者名稱和密碼：預設使用者名稱是 sshuser。您可以將它重新命名。 
    
    其他參數都是進行本教學課程的選擇性參數。您可以將它們維持不變。
    
    每個叢集都具備 Azure Blob 儲存體帳戶相依性。通常稱為預設儲存體帳戶。HDInsight 叢集與其預設儲存體帳戶必須並存於相同的 Azure 區域。刪除叢集並不會刪除儲存體帳戶。在範本中，預設儲存體帳戶名稱已定義為附加 "store" 的叢集名稱。
    
3. 按一下 [確定] 儲存參數。
4. 在 [自訂部署] 刀鋒視窗中，按一下 [新增資源群組名稱] 來建立新的資源群組。資源群組是聚集叢集、相依儲存體帳戶和其他資源的容器。資源群組位置可以和叢集位置不同。
5. 按一下 [法律條款]，然後按一下 [建立]。
6. 確認已選取 [釘選到儀表板] 核取方塊，然後按一下 [建立]。您將會看到標題為 [進行範本部署] 的新圖格。大約需要 20 分鐘的時間來建立叢集。 
7.  建立叢集後，此圖格的標題會變更為您指定的資源群組名稱。而入口網站會自動開啟包含叢集和叢集設定的兩個刀鋒視窗。 

    ![HDInsight Linux 開始使用的叢集設定](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png)。

    其中列出兩項資源，即叢集和預設儲存體帳戶。

##執行 Hive 查詢

[Apache Hive](hdinsight-use-hive.md) 是 HDInsight 中使用的最受歡迎元件。有許多方法可在 HDInsight 上執行 Hive 工作。在本教學課程中，您將使用 Ambari Hive 檢視，從入口網站執行一些 Hive 工作。如需提交 Hive 工作的其他方法，請參閱[在 HDInsight 中使用 Hive](hdinsight-use-hive.md)。

1. 瀏覽至 **https://&lt;ClusterName>.azurehdinsight.net** 以開啟 Ambari，其中 &lt;ClusterName> 是您在上一節建立的叢集。
2. 輸入您在上一節指定的 Hadoop 使用者名稱和密碼。預設的使用者名稱為 **admin**。
3. 開啟 [Hive 檢視]，如下列螢幕擷取畫面所示：

    ![選取 ambari 檢視](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png)。
4. 在頁面的 [查詢編輯器] 區段中，將下列 HiveQL 陳述式貼到工作表中：

		SHOW TABLES;

    >[AZURE.NOTE] Hive 需要分號。
        
5. 按一下 [Execute (執行)]。[查詢程序結果] 區段應該會出現在 [查詢編輯器] 下方並顯示作業相關資訊。

    查詢完成後，[查詢程序結果] 區段會顯示作業的結果。您應該會看到一個名為 hivesampletable 的資料表。所有 HDInsight 叢集都提供此範例 Hive 資料表。

    ![HDInsight Hive 檢視](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png)。

6. 重複步驟 4 和 5，以執行下列查詢：

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] 請注意，[查詢程序結果] 區段右上方的 [儲存結果] 下拉式清單；您可以使用此下拉式清單來下載結果，或以 CSV 檔案形式將它們儲存到 HDInsight 儲存體。

7. 按一下 [歷程記錄] 以取得作業清單。

完成 Hive 工作之後，您可以[將結果匯出至 Azure SQL 資料庫或 SQL Server 資料庫](hdinsight-use-sqoop-mac-linux.md)，也可以[使用 Excel 將結果視覺化](hdinsight-connect-excel-power-query.md)。如需在 HDInsight 中使用 Hive 的詳細資訊，請參閱[搭配 HDInsight 中的 Hadoop 使用 Hive 和 HiveQL 來分析範例 Apache log4j 檔案](hdinsight-use-hive.md)。

##清除教學課程

完成本教學課程之後，您可以刪除叢集。利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。您也需支付 HDInsight 叢集的費用 (即使未使用)。由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

>[AZURE.NOTE] 使用 [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)，您可以視需要建立 HDInsight 叢集，並進行 TimeToLive 設定以自動刪除叢集。

刪除叢集和/或預設儲存體帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從入口網站儀表板，按一下具有您建立叢集時所用資源群組名稱的圖格。
3. 按一下資源刀鋒視窗上的 [刪除]，以刪除包含叢集和預設儲存體帳戶的資源群組，或按一下 [資源] 圖格上的叢集名稱，然後按一下叢集刀鋒視窗上的 [刪除]。請注意，刪除資源群組將會刪除儲存體帳戶。如果您想要保留儲存體帳戶，請選擇只刪除叢集。

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

<!---HONumber=AcomDC_0316_2016-->