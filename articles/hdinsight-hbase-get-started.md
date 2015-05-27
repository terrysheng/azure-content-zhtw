<properties
	pageTitle="在 HDInsight 中使用 Hive 設定和查詢 HBase 資料表 | Azure"
	description="開始在 HDInsight 中搭配使用 HBase 與 Hadoop。了解如何使用 Hive 建立和查詢 HBase 表格。"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# 在 HDInsight 中開始使用 Apache HBase

了解如何在 HDInsight 中使用 Hive 建立 HBase 資料表和查詢 HBase 資料表。

HBase 是一種低延遲的 NoSQL 資料庫，可用於巨量資料的線上交易式處理。HBase 會以受管理叢集的形式提供，並整合到 Azure 環境中。叢集依設定會將資料直接儲存至 Azure Blob 儲存體中，使其在效能與成本的選擇中提供低延遲性與高度彈性。這可讓客戶建置使用大型資料集的互動式網站、建置可儲存數百萬端點上之感應器和遙測資料的服務，以及使用 Hadoop 工作分析此資料。如需 HBase 和其適用案例的詳細資訊，請參閱 [HDInsight HBase 概觀][hdinsight-hbase-overview]。

> [AZURE.NOTE]HBase (0.98.0 版) 只能與 HDInsight 上的 HDInsight 3.1 叢集一起使用 (以 Apache Hadoop 和 YARN 2.4.0 為基礎)。如需版本資訊，請參閱 [HDInsight 在 Hadoop 叢集版本中提供的新功能？][hdinsight-versions]

## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**：如需取得訂用帳戶的詳細資訊，請參閱[如何購買Azure][azure-purchase-options]、[成員優惠][azure-member-offers]或[免費試用][azure-free-trial]。
- **Azure 儲存體帳戶**：如需指示，請參閱[如何建立儲存體帳戶][azure-create-storageaccount]。
- 安裝 Visual Studio 2013 的**工作站**：如需指示，請參閱[安裝 Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx)。

## 佈建 HBase 叢集

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**使用 Azure 入口網站佈建 HBase 叢集**


1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下左下角的 [新增]****，再按 [資料服務]**** > [HDInsight]**** > [HBase]****。

	您也可以使用 [自訂建立] 選項。
3. 輸入 [叢集名稱]****、[叢集大小]****、[叢集使用者密碼] 和 [儲存體帳戶]****。

	![選擇 HBase 叢集類型並輸入叢集登入認證。][img-hdinsight-hbase-cluster-quick-create]

	預設 HTTP 使用者名稱為 admin。您可以使用 [自訂建立] 選項來自訂名稱。

	> [AZURE.WARNING]如需 HBase 服務的高可用性，您必須佈建包含至少**三個**節點的叢集。這可確保如果一個節點故障，仍可在其他節點上使用 HBase 資料區域。

4. 按一下位於右下角的核取記號圖示，以建立 HBase 叢集。

>[AZURE.NOTE]刪除 HBase 叢集之後，您可以使用相同的預設 Blob 建立另一個 HBase 叢集。這個新叢集將選取您在原始叢集中建立的 HBase 資料表。

## 從 HBase Shell 建立 HBase 範例資料表
本節說明如何使用 HBase Shell 來建立 HBase 資料表、加入資料列，以及列出資料列。若要存取 HBase Shell，您必須先啟用遠端桌面通訊協定 (RDP)，然後建立 HBase 叢集的 RDP 連接。如需指示，請參閱[使用 Azure 入口網站管理 HDInsight 上的 Hadoop 叢集][hdinsight-manage-portal]。


**使用 HBase Shell**

1. 在您的 RDP 工作階段內，按一下位於桌面上的 [Hadoop 命令列]**** 捷徑。
2. 將資料夾切換至 HBase 主目錄：

		cd %HBASE_HOME%\bin
3. 開啟 HBase Shell：

		hbase shell

4. 建立具有一個資料行系列的 HBase，並插入資料列：

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	如需 Hbase 資料表結構描述的詳細資訊，請參閱 [HBase 結構描述設計簡介][hbase-schema]。如需其他 HBase 命令，請參閱 [Apache HBase 參考指南][hbase-quick-start]。
5. 列出 HBase 資料表：

		list

**在 HBase WebUI 檢查叢集狀態**

HBase 同時也附有 WebUI，其可以協助您監視叢集。例如，您可能會要求關於區域的統計資料或資訊。在 HBase 叢集上，您可以在 zookeepernode 的位址下找到 WebUI：


	http://zookeepernode:60010/master-status

在高可用性叢集中，您會找到目前使用中之 HBase 主要節點 (其正在主控 WebUI) 的連結。

**大量載入範例資料表**

1. 從 HBase Shell 建立具有兩個資料行系列的 HBase 資料表：

		create 'Contacts', 'Personal', 'Office'


3. 建立包含下列資料的連絡人清單，並將其上傳至 Azure Blob 儲存體中名為 /tmp/contacts.txt 的資料夾。如需指示，請參閱[在 HDInsight 中將 Hadoop 工作的資料上傳][hdinsight-upload-data]。

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. 在 Hadoop 命令列中，將資料夾切換至 HBase 主目錄：

		cd %HBASE_HOME%\bin

3. 執行 ImportTsv。ImportTsv 是將資料以 TSV 格式載入至 HBase 的工具。其具有兩種不同的用法：將資料從 Hadoop 分散式檔案系統 (HDFS) 的 TSV 格式載入到 HBase，以及準備要載入的檔案。如需詳細資訊，請參閱 [Apache HBase 參考指南][hbase-reference]。

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. 將先前命令的輸出載入到 HBase：

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## 使用 Hive 查詢 HBase 資料表

既然您已佈建 HBase 叢集並建立 HBase 資料表，便可以使用 Hive 查詢資料表中的資料。本節將建立對應至 HBase 資料表的 Hive 資料表，並用以查詢您 HBase 資料表中的資料。

**開啟叢集儀表板**

1. 登入 [Azure 入口網站][azure-management-portal]。
2. 按一下左窗格中的 [HDInsight]****。您會看見叢集清單，包括您在上一節中建立的叢集。
3. 按一下要執行 Hive 工作的叢集名稱。
4. 按一下位於頁面底部的 [查詢主控台]****，以開啟叢集儀表板。其會在不同瀏覽器索引標籤中開啟網頁。
5. 輸入 Hadoop 使用者帳戶的使用者名稱和密碼。預設使用者名稱為 **admin**，密碼則是您在佈建程序中輸入的密碼。隨即開啟新的瀏覽器索引標籤。
6. 按一下頁面頂端的 [Hive 編輯器]****。Hive 編輯器外觀如下：

	![HDInsight 叢集儀表板。][img-hdinsight-hbase-hive-editor]





























**執行 Hive 查詢**

1. 將下列 HiveQL 指令碼輸入至 Hive 編輯器，然後按一下 [提交]****，以建立對應到 HBase 資料表的 Hive 資料表。在執行此陳述式前，請確定您已使用 HBase Shell 建立參考先前本教學課程的範例資料表。

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	請等到 [狀態]**** 更新為 [已完成]****。

2. 將下列 HiveQL 指令碼輸入至 Hive 編輯器，然後按一下 [提交]****。Hive 查詢會查詢 HBase 資料表中的資料：

     	SELECT count(*) FROM hbasecontactstable;

4. 若要擷取 Hive 查詢的結果，請在工作執行完成時，按一下 [工作階段]**** 視窗中的 [檢視詳細資料]**** 連結。只會有一個工作輸出檔案，因為您將一個記錄放置到 HBase 資料表。




**瀏覽輸出檔案**

1. 在查詢主控台中，按一下 [檔案瀏覽器]****。
2. 按一下做為 HBase 叢集之預設檔案系統的 Azure 儲存體帳戶。
3. 按一下 HBase 叢集名稱。預設 Azure 儲存體帳戶容器會使用叢集名稱。
4. 按一下 [使用者]****，然後按一下 [Admin]****。(此為 Hadoop 使用者名稱。)
6. 按一下工作名稱為符合 SELECT Hive 查詢執行時間的 [上次修改]**** 時間。
4. 按一下 [stdout]****。儲存檔案並以記事本開啟檔案。將會有一個輸出檔案。

	![HDInsight HBase Hive 編輯器檔案瀏覽器][img-hdinsight-hbase-file-browser]

## 使用適用於 .NET C# 的 HBase REST 用戶端程式庫建立 HBase 資料表，並從 HBase 資料表擷取資料

您必須從 GitHub 下載適用於 .NET 的 HBase REST API 用戶端程式庫並建置專案，才能使用 HBase .NET SDK。下列程序包括此工作的指示。

1. 建立新的 C# Visual Studio Windows 桌面主控台應用程式。
2. 按一下 [工具]**** 功能表 > [NuGet 套件管理器]**** > [套件管理器主控台]****，以開啟 NuGet  套件管理器主控台。
3. 在主控台中，執行下列 NuGet 命令：

		Install-Package Microsoft.HBase.Client

5. 在檔案頂端加入下列 **using** 陳述式：

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 使用下列程式碼來取代 **Main** 函數：

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. 設定 **Main** 函數中的前三個變數。
8. 按 **F5** 鍵執行應用程式。



## 後續步驟
在本教學課程中，您已了解如何佈建 HBase 叢集，以及如何建立資料表，並從 HBase Shell 檢視這些資料表中的資料。您同時也了解到如何使用 Hive 查詢 HBase 資料表中的資料，以及如何使用 HBase C# REST API 建立 HBase 資料表，並擷取其資料表中的資料。

若要深入了解，請參閱：

- [HDInsight HBase 概觀][hdinsight-hbase-overview]：HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。
- [在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]：由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。
- [設定 HDInsight 中的 HBase 複寫](hdinsight-hbase-geo-replication.md)：了解如何跨兩個 Azure 資料中心設定 HBase 複寫。 
- [利用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]：了解如何使用 HDInsight 之 Hadoop 叢集中的 HBase，執行巨量資料的即時[情緒分析](http://en.wikipedia.org/wiki/Sentiment_analysis)。

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->