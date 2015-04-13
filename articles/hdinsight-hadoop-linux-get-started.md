<properties 
   pageTitle="在 Linux 上開始在 HDInsight 中搭配使用 Hadoop 與 Hive| Azure" 
   description="在 Linux 上開始在 HDInsight 中使用 Hadoop。了解如何佈建在 Linux 上執行的 HDInsight Hadoop 叢集以及使用 Hive 查詢資料" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# 在 Linux 上開始在 HDInsight 中搭配使用 Hadoop 與 Hive (預覽)

本教學課程將示範如何在 Linux 上佈建 HDInsight Hadoop 叢集並執行 Hive 查詢以從非結構化資料擷取有意義的資訊，讓您快速地在 Linux 上開始使用 HDInsight。然後，您將在商業智慧 (BI) 工具 (例如 Tableau) 中分析結果。


> [AZURE.NOTE] 如果您是 Hadoop 和巨量資料的新使用者，您可以進一步了解下列術語：<a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>、<a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>、<a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a> 和 <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>。若要了解 HDInsight 如何在 Azure 中啟用 Hadoop，請參閱 [HDInsight 中 Hadoop 的簡介](../hdinsight-hadoop-introduction/)。


## 本教學課程要達到什麼目標？ ##

假設您有一個大型的非結構化資料集，而且您想要在此資料集上執行查詢以擷取一些有用資訊。以下是要達成此目標的作法：

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：


- Azure 訂閱。如需取得訂閱的詳細資訊，請參閱<a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">購買選項</a>、<a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">會員優惠</a>或 <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">免費試用</a>。

**預估完成時間：**30 分鐘

## 本教學課程內容

* [建立 Azure 儲存體帳戶](#storage)
* [佈建 HDInsight Linux 叢集](#provision)
* [提交 Hive 工作](#hivequery)
* [後續步驟](#nextsteps)

## <a name="storage"></a>建立 Azure 儲存體帳戶

HDInsight 會使用 Azure Blob 儲存體來儲存資料。我們稱之為「 *WASB*」或「 *Azure 儲存體 - Blob*」。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage/)。

佈建 HDInsight 叢集時，您可以指定 Azure 儲存體帳戶。將該帳戶的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。依預設，系統會在與您指定儲存體帳戶的相同資料中心內佈建 HDInsight 叢集。

除了此儲存體帳戶以外，您可以在自訂設定 HDInsight 叢集時新增其他儲存體帳戶。這個其他儲存體帳戶可以來自相同的 Azure 訂用帳戶或不同的 Azure 訂用帳戶。如需相關指示，請參閱 [使用自訂選項佈建 HDInsight Linux 叢集](../hdinsight-hadoop-provision-linux-clusters)。 

為了簡化本教學課程，將僅使用預設的 Blob 容器和預設的儲存體帳戶。實際上，資料檔案通常會儲存在指定的儲存體帳戶中。

**建立 Azure 儲存體帳戶**

1. 登入 <a href="https://manage.windowsazure.com/" target="_blank">Azure 管理入口網站</a>。
2. 按一下左下角的 [**新增**]，指向 [**資料服務**] 及 [**儲存體**]，然後按一下 [**快速建立**]。

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. 輸入 **URL**、**位置**和**複寫**，然後按一下 [**建立儲存體帳戶**]。不支援同質群組。您將在儲存體清單中看見新的儲存體帳戶。

	>[AZURE.NOTE]  佈建 HDInsight Linux 叢集的快速建立選項 (如在本教學課程中所使用的選項) 在佈建叢集時並不會詢問位置。依預設，它會將叢集並存於與儲存體帳戶相同的資料中心內。因此，請務必在支援叢集的位置建立您的儲存體帳戶，這些位置包括：**東亞**、**東南亞**、**北歐**、**西歐**、**美國東部**、**美國西部**、**美國中北部**和**美國中南部**。

4. 等候新儲存體帳戶的 [**狀態**] 變更為 [**線上**]。
5. 從清單中選取新儲存體帳戶，並按一下頁面底部的 [**管理存取金鑰**]。
7. 記下 [**儲存體帳戶名稱**] 和 [**主要存取金鑰**] (或 [**次要存取金鑰**]。其中任何一個金鑰皆可)。稍後在教學課程中需要這些資訊。


如需詳細資訊，請參閱
[如何建立儲存體帳戶](../storage-create-storage-account/) 及 [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage/)。
	
## <a name="provision"></a>在 Linux 上佈建 HDInsight 叢集

佈建 HDInsight 叢集時，您可以佈建包含 Hadoop 及相關應用程式的 Azure 運算資源。在本節中，您將使用 quick-create 選項在 Linux 上佈建 HDInsight 叢集。此選項使用預設使用者名稱和 Azure 儲存體容器，並使用在 Ubuntu 12.04 LTS 上執行的 HDInsight 3.2 版 (Hadoop 2.5 版、HDP 2.2 版) 設定叢集。如需不同 HDInsight 版本及其 SLA 的相關資訊，請參閱 [HDInsight 元件版本設定](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)頁面。

>[AZURE.NOTE]  您也可以建立執行 Windows Server 作業系統的 Hadoop 叢集。如需相關指示，請參閱 [在 Windows 上開始使用 HDInsight](../hdinsight-get-started/)。


**佈建 HDInsight 叢集** 

1. 登入 <a href="https://manage.windowsazure.com/" target="_blank">Azure 管理入口網站</a>。 

2. 按一下左下角的 [**新增**]，然後依序按一下 [ **資料服務**、[**HDInsight**] 和 [**Linux 上的 Hadoop**]。

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. 輸入或選取下列值：

	<table border="1">
	<tr><th>名稱</th><th>值</th></tr>
	<tr><td>叢集名稱</td><td>叢集的名稱</td></tr>
	<tr><td>叢集大小</td><td>您要部署的資料節點數。預設值為 4。但您也可以從下拉式清單中選擇使用 1 或 2 個資料節點。使用 [<strong>自訂建立</strong>] 選項可以指定任何數目的叢集節點。另外也提供各種叢集大小的費率定價詳細資料。按一下下拉式方塊正上方的 [<strong>?</strong>] 符號，並點選快顯功能表上的連結。</td></tr>
	<tr><td>密碼</td><td><i>HTTP</i> 帳戶 (預設使用者名稱：admin) 和 <i>SSH</i> 帳戶 (預設使用者名稱：hdiuser) 的密碼。請注意，這些「不是」指佈建叢集所在 VM 的系統管理員帳戶。 </td></tr>
	
	<tr><td>儲存體帳戶</td><td>從下拉式方塊中，選取您建立的儲存體帳戶。 <br/>

	一旦選擇儲存體帳戶之後，便無法變更。如果移除儲存體帳戶，則無法再使用叢集。

	HDInsight 叢集會並存於與儲存體帳戶相同的資料中心內。 
	</td></tr>
	</table>

	保留叢集名稱的複本。稍後在教學課程中將會需要這些資訊。

	
5. 按一下 [**建立 HDInsight 叢集**]。佈建完成時，狀態欄便會顯示 [**執行中**]。

	>[AZURE.NOTE] 上述程序會使用 quick-create 選項建立 Linux 叢集，此選項使用預設的 SSH 使用者名稱和 Azure 儲存體容器。若要使用自訂選項建立叢集，例如使用 SSH 金鑰進行驗證或使用其他儲存體帳戶，請參閱 [使用自訂選項佈建 HDInsight Linux 叢集](../hdinsight-hadoop-provision-linux-clusters)。


## <a name="hivequery"></a>在叢集上提交 Hive 工作
現在您已佈建 HDInsight Linux 叢集，下一個步驟是執行範例 Hive 工作，以查詢 HDInsight 叢集隨附的範例資料 (sample.log)。範例資料包含各種記錄資訊，包括追蹤、警告、資訊、錯誤等。我們會查詢此資料來擷取具有特定嚴重性的所有錯誤記錄檔。您必須執行下列步驟以在 HDInsight Linux 叢集上執行 Hive 查詢。

- 連接到 Linux 叢集
- 執行 Hive 工作



### 連接到叢集

您可以從 Linux 電腦或 Windows 電腦使用 SSH 連線到 Linux 上的 HDInsight 叢集。

**從 Linux 電腦連線**

1. 開啟終端機並輸入下列命令：

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	因為您是使用快速建立選項佈建叢集，預設的 SSH 使用者名稱是 **hdiuser**。因此，命令必須是

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. 出現提示時，輸入您在佈建叢集時所提供的密碼。成功連線後，提示將變更如下：

		hdiuser@headnode-0:~$


**從 Windows 電腦連線**

1. 下載 Windows 用戶端的 **PuTTY**。您可以從下列位置取得：<a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. 開啟 **PuTTY**。在 [**類別**] 中，按一下 [**工作階段**]。在 [**PuTTY 工作階段的基本選項**] 畫面上，將您的 HDInsight 伺服器的 SSH 位址輸入到 [**主機名稱 (或 IP 位址)**] 欄位。SSH 位址是叢集名稱加上 **-ssh.azurehdinsight.net**。例如，**myhdinsightcluster-ssh.azurehdinsight.net**。

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. 若要儲存連線資訊供日後使用，請在 [**儲存的工作階段**] 底下輸入此連線的名稱，然後按一下 [**儲存**]。連線便會加入已儲存的工作階段清單。

4. 按一下 [**開啟**] 來連線到叢集。在提示輸入使用者名稱時，請輸入  *hdiuser*。至於密碼，請輸入您在佈建叢集時指定的密碼。成功連線後，提示將變更如下：

		hdiuser@headnode-0:~$

### 執行 Hive 工作

使用 SSH 連線到叢集後，請使用下列命令來執行 Hive 查詢。

1. 在提示中使用下列命令來啟動 Hive 命令列介面 (CLI)：

		hive

2. 在 CLI 中輸入下列陳述式，使用叢集上已有的範例資料建立新的資料表 log4jLogs。

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	這些陳述式將執行下列動作。

	- **DROP TABLE** - 當資料表已存在時，刪除資料表和資料檔。
	- **CREATE EXTERNAL TABLE** - 在 Hive 中建立新的「外部」資料表。外部資料表只會在 Hive 中儲存資料表定義；資料會保留在原始位置。
	- **ROW FORMAT** - 讓 Hive 知道資料的格式化方式。在此情況下，每個記錄中的欄位會以空格隔開。
	- **STORED AS TEXTFILE LOCATION** - 將資料的儲存位置告訴 Hive (範例/資料目錄)，且資料是儲存為文字。
	- **SELECT** - 選擇其資料欄 t4 包含值 [ERROR] 的所有資料列計數。

	>[WACOM.NOTE] 當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。捨棄外部資料表並「**不會**」刪除資料，只會刪除資料表定義。

	這會傳回下列輸出。

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched: 
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	請注意，輸出會包含 **[ERROR]  3**，這是因為有三個資料列包含此值。

3. 使用下列陳述式建立新的「內部」資料表 **errorLogs**。

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	這些陳述式將執行下列動作。

	- **CREATE TABLE IF NOT EXISTS** - 建立資料表 (如果不存在)。因為未使用 EXTERNAL 關鍵字，所以這是  「內部」資料表，內部資料表會儲存在 Hive 資料倉儲中，並完全受 Hive 所管理。與 **EXTERNAL** 資料表不同，捨棄內部資料表也會刪除基礎資料。
	- **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。這是高度最佳化且有效率的 Hive 資料儲存格式。
	- **INSERT OVERWRITE ...SELECT** - 從包含 [ERROR] 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表

4. 若要確認是否只有資料欄 t4 中包含 **[ERROR]** 的資料列會儲存至 **errorLogs** 資料表，請使用下列陳述式，從 errorLogs 傳回所有資料列。

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	The returned data should all correspond to [ERROR] logs.


## <a name="nextsteps"></a>後續步驟
在本教學課程中，您已經學會如何使用 HDInsight 佈建 Hadoop Linux 叢集並使用 SSH 在叢集上執行 Hive 查詢。若要深入了解，請參閱下列文章：

- [使用自訂選項在 Linux 上佈建 HDInsight](../hdinsight-hadoop-provision-linux-clusters)
- [在 Linux 上使用 HDInsight](../hdinsight-hadoop-linux-information)
- [使用 Ambari 管理 HDInsight 叢集](../hdinsight-hadoop-manage-ambari)
- [搭配 HDInsight 使用 MapReduce][hdinsight-use-mapreduce]
- [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
- [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
- [搭配 HDInsight 使用 Azure Blob 儲存體](../hdinsight-use-blob-storage)
- [將資料上傳至 HDInsight][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=47-->
