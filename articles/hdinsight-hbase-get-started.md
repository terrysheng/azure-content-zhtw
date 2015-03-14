<properties 
	pageTitle="在 HDInsight 中使用 Hive 設定和查詢 HBase 資料表 | Azure" 
	description="開始在 HDInsight 中搭配使用 HBase 與 Hadoop。了解如何使用 Hive 建立和查詢 HBase 表格。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



# 在 HDInsight 中於 Hadoop 上使用 Hive 設定 HBase 叢集和加以查詢

了解如何在 HDInsight 中於 Hadoop 上使用 Hive 建立和查詢 HBase 資料表。 

##本文內容

* [什麼是 HBase？](#hbaseintro)
* [必要條件](#prerequisites)
* [使用 Azure 管理入口網站佈建 HBase 叢集](#create-hbase-cluster)
* [使用 HBase Shell 管理 HBase 資料表](#create-sample-table)
* [使用 HiveQL 查詢 HBase 資料表](#hive-query)
* [使用 Microsoft HBase REST 用戶端程式庫來管理 HBase 資料表](#hbase-powershell)
* [另請參閱](#seealso)

##<a name="hbaseintro"></a>什麼是 HBase？

HBase 是一種低延遲的 NoSQL 資料庫，可用於巨量資料的線上交易式處理。HBase 會以受管理叢集的形式提供，並整合到 Azure 環境中。叢集依設定會將資料直接儲存在 Azure Blob 儲存體中，而在效能/成本的選擇中提供低延遲性與高度彈性。這讓客戶得以建置可使用大型資料集的互動式網站、建置可從數百萬個端點儲存感應器和遙測資料的服務，以及透過 Hadoop 工作分析這項資料。如需 HBase 和其適用案例的詳細資訊，請參閱 [HDInsight HBase 概觀][hdinsight-hbase-overview]

> [AZURE.NOTE] HBase (0.98.0 版) 只能與 HDInsight 上的 HDInsight 3.1 叢集一起使用 (以 Apache Hadoop 和 YARN 2.4.0 為基礎)。如需版本資訊，請參閱 [HDInsight 在 Hadoop 叢集版本中提供的新功能？][hdinsight-versions]

##<a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂閱**如需有關如何取得訂閱的詳細資訊，請參閱[購買選項][azure-purchase-options]、[會員優惠][azure-member-offers]，或[免費試用版][azure-free-trial]。
- **Azure 儲存體帳戶** 如需指示，請參閱[如何建立儲存體帳戶][azure-create-storageaccount]。
- 安裝 Visual Studio 2013 的**工作站**。如需指示，請參閱[安裝 Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx)。

##<a name="create-hbase-cluster"></a>在 Azure 入口網站上佈建 HBase 叢集

本節說明如何使用 Azure 管理入口網站佈建 HBase 叢集。


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**在 Azure 入口網站中佈建 HDInsight 叢集** 


1. 登入 [Azure 管理入口網站][azure-management-portal]。 
2. 按一下左下角的 [**新增**]，然後依序按一下 [**資料服務**]、[**HDInsight**] 和 [**HBASE**]。 
3. 輸入**叢集名稱**、**叢集大小**、叢集使用者密碼，以及**儲存體帳戶**。
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. 按一下位於左下角的勾號圖示，以建立 HBase 叢集。


##<a name="create-sample-table"></a>從 HBase Shell 建立 HBase 範例資料表
本節說明如何啟用及使用遠端桌面通訊協定 (RDP) 存取 HBase Shell，然後用它來建立 HBase 範例資料表、新增資料列，然後列出資料表中的資料列。

本節內容假設您已完成第一節中列出的程序，因此已成功建立 HBase 叢集。

**啟用 HBase 叢集的 RDP 連線**

1. 從管理入口網站中，按一下左側的 **HDInsight**，以檢視現有叢集的清單。
2. 按一下您要開啟 HBase 殼層的 HBase 叢集。
3. 按一下頂端的 [**設定**]。
4. 按一下底端的 [**啟用遠端**]。
5. 輸入 RDP 使用者名稱和密碼。  使用者名稱必須與您佈建叢集時所使用的叢集使用者名稱不同。**到期日**資料可以是從今天開始的七日。
6. 按一下右下角的勾號來啟用遠端桌面。
7. RPD 啟用後，按一下 [**組態**] 索引標籤底部的 [**連接**]，然後遵循指示進行。

 
**若要開啟 HBase 殼層**

1. 在您的 RDP 工作階段內，按一下位於桌面上的 [**Hadoop 命令列**] 捷徑。

2. 將資料夾切換至 HBase 主目錄：
		
		cd %HBASE_HOME%\bin

3. 開啟 HBase Shell：

		hbase shell


**建立範例資料表、新增資料，並擷取資料**

1. 建立範例資料表：

		create 'sampletable', 'cf1'

2. 將資料列新增至範例資料表：

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. 列出範例資料表中的資料列：
	
		scan 'sampletable'

**在 HBase WebUI 中檢查叢集狀態**
	
HBase 也隨附 WebUI，可協助監視您的叢集，例如藉由要求統計資料或區域的相關資訊。在 HBase 叢集上，您可以在 zookeepernode 的位址下找到 WebUI。


	http://zookeepernode:60010/master-status
	
在 HighAvailability (HA) 叢集中，您會找到主控 WebUI 的目前作用 HBase 主要節點的連結。

**大量載入範例資料表**

1. 建立包含下列資料的 samplefile1.txt，並上傳至 Azure Blob 儲存體至 /tmp/samplefile1.txt：

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. 將資料夾切換至 HBase 主目錄：
		
		cd %HBASE_HOME%\bin

3. Execute ImportTsv：

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. 從先前的命令載入輸出至 HBase：

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>使用 Hive 查詢 HBase 資料表

現在，您已佈建 HBase 叢集並建立資料表，您可以使用 Hive 加以查詢。本節將建立對應至 HBase 資料表的 Hive 資料表，並用它來查詢您 HBase 資料表中的資料。

**開啟叢集儀表板**

1. 登入 [Azure 管理入口網站][azure-management-portal]。 
2. 按一下左窗格上的 [**HDInsight**]。您會看見已建立的叢集清單，其中包含您在上一節中建立的叢集。
3. 按一下要執行 Hive 工作的叢集名稱。
4. 按一下位於頁面底部的 [**管理叢集**]，以開啟叢集儀表板。它會在不同瀏覽器索引標籤上開啟網頁。   
5. 輸入 Hadoop 使用者帳戶的使用者名稱和密碼。預設使用者名稱為 **admin**，密碼是您在佈建程序中輸入的密碼。隨即開啟新的瀏覽器索引標籤。 
6. 按一下頂端的 [**Hive 編輯器**]。Hive 編輯器看起來如下：

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**執行 Hive 查詢**

1. 輸入下列 HiveQL 指令碼至 Hive 編輯器，然後按一下 [**提交**] 來建立與 HBase 資料表對應的 Hive 資料表。在執行此陳述式前，請確定您已使用 HBase Shell 在 HBase 中建立此處參考的範例資料表。
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	等到 [**狀態**] 更新為 [**已完成**]。
 
2. 在 Hive 編輯器中輸入下列 HiveQL 指令碼，然後按一下 [**提交**] 按鈕。Hive 查詢會查詢 HBase 資料表中的資料：

     	SELECT count(*) FROM hbasesampletable;
 
4. 若要擷取 Hive 查詢的結果，請在工作執行完成時，按一下 [**工作階段**] 視窗中的 [**檢視詳細資料**] 連結。工作輸出應該是 1，因為只放了一筆記錄到 HBase 資料表。




**瀏覽輸出檔案**

1. 從查詢主控台中，從頂端按一下 [**檔案瀏覽器**]。
2. 按一下用作 HBase 叢集預設檔案系統的 Azure 儲存體帳戶。
3. 按一下 HBase 叢集名稱。預設 Azure 儲存體帳戶容器會使用叢集名稱。
4. 按一下 [**使用者**]。
5. 按一下 [**admin**]。這是 Hadoop 使用者名稱。
6. 按一下 [**上次修改**] 時間與 SELECT Hive 查詢執行時間相符的工作名稱。
4. 按一下 [**stdout**]。儲存檔案並使用記事本開啟檔案。輸出應該是 1。

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>使用 HBase REST Client Library for .NET C# API 建立 HBase 資料表，並從資料表擷取資料

Microsoft HBase REST Client Library for .NET 專案必須從 GitHub 下載，且建置的專案須使用 HBase .NET SDK。下列程序包括此工作的指示。

1. 建立新的 C# Visual Studio Windows 桌面主控台應用程式。
2. 依序按一下 [**工具**] 功能表、[**NuGet 封裝管理員**] 和 [**封裝管理員主控台**] 來開啟 NuGet 封裝管理員主控台。
3. 在主控台中執行下列 NuGet 命令：

	Install-Package Microsoft.HBase.Client

5. 在檔案頂端新增下列 using 陳述式：
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 以下列取代 Main 函式：

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

7. 在 Main 函式中設定前三個變數。
8. 按 **F5** 執行應用程式。



##<a name="next"></a> 後續步驟
在本教學課程中，您已了解到如何佈建 HBase 叢集、如何建立資料表，以及如何從 HBase Shell 檢視這些資料表中的資料。您也已了解如何使用 Hive 查詢 HBase 資料表中的資料，以及使用 HBase C# API 建立 HBase 資料表，和擷取資料表中的資料。 

若要深入了解，請參閱：

- [HDInsight HBase 概觀][hdinsight-hbase-overview]：
HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。
- [在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]：
由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。
- [使用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]：
了解如何在 HDInsight 的 Hadoop 中使用 HBase 針對巨量資料進行即時[情緒分析](http://en.wikipedia.org/wiki/Sentiment_analysis) (英文)。

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png








<!--HONumber=42-->
