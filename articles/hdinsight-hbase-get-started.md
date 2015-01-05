<properties urlDisplayName="Get Started" pageTitle="在 HDInsight 中使用 Hive 設定和查詢 HBase 資料表 | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. Learn how to create HBase tables and query them using Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



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

HBase 是一種低延遲的 NoSQL 資料庫，可用於巨量資料的線上交易式處理。HBase 會以受管理叢集的形式提供，並整合到 Azure 環境中。叢集依設定會將資料直接儲存在 Azure Blob 儲存體中，而在效能/成本的選擇中提供低延遲性與高度彈性。這讓客戶得以建置可使用大型資料集的互動式網站、建置可從數百萬個端點儲存感應器和遙測資料的服務，以及透過 Hadoop 工作分析這項資料。如需 HBase 和其適用案例的詳細資訊，請參閱 [HDInsight HBase 概觀](http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-hbase-overview/)。

> [WACOM.NOTE] HBase (0.98.0 版) 只能與 HDInsight 上的 HDInsight 3.1 叢集一起使用 (以 Apache Hadoop 和 YARN 2.4.0 為基礎)。如需版本資訊，請參閱 [HDInsight 在 Hadoop 叢集版本中提供的新功能？][hdinsight-versions]

##<a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**如需取得訂用帳戶的詳細資訊，請參閱[購買選項][azure-purchase-options]、[成員優惠][azure-member-offers]或[免費試用][azure-free-trial]。
- **Azure 儲存體帳戶** 如需指示，請參閱[如何建立儲存體帳戶][azure-create-storageaccount]。
- 安裝 Visual Studio 2013 的**工作站**。如需指示，請參閱[安裝 Visual Studio](http://msdn.microsoft.com/zh-tw/library/e2h7fzkw.aspx)。
- 下載 [Microsoft HBase REST Client Library for .NET](https://github.com/hdinsight/hbase-sdk-for-net)。 


##<a name="create-hbase-cluster"></a>在 Azure 入口網站上佈建 HBase 叢集

本節說明如何使用 Azure 入口網站佈建 HBase 叢集。


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**在 Azure 入口網站中佈建 HDInsight 叢集** 


1. 登入 [Azure 管理入口網站][azure-management-portal]。 

2. 按一下左側的 [**HDInsight**] 列出您帳戶中的叢集狀態，然後按位於左下角的 [**+新增**]。 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. 按一下左邊算起第二欄中的 HDInsight 圖示，然後按位於下一欄中的 HBase 選項。指定 [叢集名稱] 和 [叢集大小] 的值、儲存體帳戶的名稱，以及新 HBase 叢集的密碼。
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. 按一下位於左下角的勾號圖示，以建立 HBase 叢集。


##<a name="create-sample-table"></a>從 HBase Shell 建立 HBase 範例資料表
本節說明如何啟用及使用遠端桌面通訊協定 (RDP) 存取 HBase Shell，然後用它來建立 HBase 範例資料表、新增資料列，然後列出資料表中的資料列。

本節內容假設您已完成第一節中列出的程序，因此已成功建立 HBase 叢集。

**啟用 HBase 叢集的 RDP 連線**

1. 若要啟用 HDInsight 叢集的遠端桌面連線，請選取您已建立的 HBase 叢集，然後按一下 [**組態**] 索引標籤。按一下頁面底部的 [**啟用遠端**] 按鈕，以啟用叢集的 RDP 連線。
2. 在 [**設定遠端桌面**] 精靈上提供認證和到期日，然後按一下位於右下角的勾選圓圈。(作業可能需要數分鐘才能完成。)
3. 若要連接到 HDInsight 叢集，請按一下 [**組態**] 索引標籤底部的 [**連接**] 按鈕。

 
**開啟 HBase Shell**

1. 在您的 RDP 工作階段內，按一下位於桌面上的 [**Hadoop 命令提示字元**] 捷徑。

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

##<a name="hive-query"></a>使用 Hive 查詢 HBase 資料表

現在，您已佈建 HBase 叢集並建立資料表，您可以使用 Hive 加以查詢。本節將建立對應至 HBase 資料表的 Hive 資料表，並用它來查詢您 HBase 資料表中的資料。

**開啟叢集儀表板**

1. 登入 [Azure 管理入口網站][azure-management-portal]。 
2. 按一下左窗格上的 [**HDInsight**]。您會看見已建立的叢集清單，其中包含您在上一節中建立的叢集。
3. 按一下要執行 Hive 工作的叢集名稱。
4. 按一下位於頁面底部的 [**管理叢集**]，以開啟叢集儀表板。它會在不同瀏覽器索引標籤上開啟網頁。   
5. 輸入 Hadoop 使用者帳戶的使用者名稱和密碼。預設使用者名稱為 **admin**，密碼是您在佈建程序中輸入的密碼。儀表板顯示如下：

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**執行 Hive 查詢**

1. 若要建立對應至 HBase 資料表的 Hive 資料表，請將下方的 HiveQL 指令碼輸入 Hive 主控台視窗中，然後按一下 [**提交**] 按鈕。在執行此陳述式前，請確定您已使用 HBase Shell 在 HBase 中建立此處參考的範例資料表。
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. 若要對 HBase 中的資料執行 Hive 查詢，請將下方的 HiveQL 指令碼輸入 Hive 主控台視窗中，然後按一下 [**提交**] 按鈕。

     	SELECT count(*) FROM hbasesampletable;
 
4. 若要擷取 Hive 查詢的結果，請在工作執行完成時，按一下 [**工作階段**] 視窗中的 [**檢視詳細資料**] 連結。


注意：HBase Shell 連結會將索引標籤切換至 [**HBase Shell**]。



**瀏覽輸出檔案**

1. 在叢集儀表板中，按一下位於頂端的 [**檔案**]。
2. 按一下 [**Templeton-工作- 狀態**]。
3. 按一下「前次修改時間」比您先前記下的「工作開始時間」稍晚的 GUID 號碼。記下此 GUID。您在下一節將會用到此號碼。
4.  **stdout** 檔案包含您在下一節需要使用的資料。如有需要，您可以按一下 **stdout** 以下載資料檔案的複本。

	
##<a name="hbase-powershell"></a>使用 HBase REST Client Library for .NET C# API 建立 HBase 資料表，並從資料表擷取資料

Microsoft HBase REST Client Library for .NET 專案必須從 GitHub 下載，且建置的專案須使用 HBase .NET SDK。下列程序包括此工作的指示。

1. 下載 [Microsoft HBase REST Client Library for .NET](https://github.com/hdinsight/hbase-sdk-for-net) ，如果您尚未滿足此必要條件的話。

2. 在 Visual Studio 中從其下載位置開啟 Marlin.sln ([**檔案**] -> [**開啟**] -> [**專案/方案...**])。選取 [**檢視**] -> [**方案總管**] 以查看 'Marlin' 方案和其 Microsoft.HBase.Client 專案。在 [**方案總管**] 的 **Marlin** 專案上按一下滑鼠右鍵，然後選取 [**建置方案**]。 

4. 建立新的 Visual C# 主控台應用程式。擷取產生的 Microsoft.HBase.Client.dll 和 protobuf.dll 組建 (從 ...\bin\debug\Microsoft.HBase.Client 目錄)，並將它們新增至您的 C# 專案：在 [**參考**] 上按一下滑鼠右鍵，選取 [**加入參考...**]，瀏覽至這兩個組件，然後加以上傳。[protobuf-net](http://code.google.com/p/protobuf-net/) 是 Google 的通訊協定緩衝二進位序列化程式的 .NET 實作，用於資料通訊。

5. 在檔案頂端新增下列 using 陳述式：
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. 使用叢集認證建立新的 HBase 用戶端執行個體，並擷取叢集版本：

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. 若要建立新的 HBase 資料表，請使用此程式碼：

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. 將資料插入具有此程式碼的資料表：

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. 若要擷取具有其金鑰的資料格，請使用此程式碼。 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. 使用下列程式碼掃描資料表中的資料列：

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>摘要
在本教學課程中，您已了解到如何佈建 HBase 叢集、如何建立資料表，以及如何從 HBase Shell 檢視這些資料表中的資料。您也已了解如何使用 Hive 查詢 HBase 資料表中的資料，以及使用 HBase C# API 建立 HBase 資料表，和擷取資料表中的資料。

##<a name="next"></a> 後續步驟

[HDInsight HBase 概觀][hdinsight-hbase-overview]：
HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。

[在 Azure 虛擬網路上佈建 HBase 叢集][hdinsight-hbase-provision-vnet]：
由於 HBase 叢集已與虛擬網路整合，因此能夠部署到和應用程式相同的虛擬網路，讓應用程式得以和 HBase 直接通訊。

[使用 HDInsight 中的 HBase 分析 Twitter 情緒][hbase-twitter-sentiment]：
了解如何執行巨量資料的即時[情緒分析](http://en.wikipedia.org/wiki/Sentiment_analysis) (在 HDInsight 的 Hadoop 叢集中使用 HBase)。

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
