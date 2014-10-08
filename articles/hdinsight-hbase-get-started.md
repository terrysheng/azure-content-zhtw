<properties linkid="manage-services-hdinsight-hbase-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using HBase with Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. learn how to created HBase tables and query them with Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using HBase with Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# 開始在 HDInsight 中搭配使用 HBase 與 Hadoop

HBase 是一種低延遲的 NoSQL 資料庫，可用於巨量資料的線上交易式處理。HBase 會以受管理叢集的形式提供，並整合到 Azure 環境中。叢集依設定會將資料直接儲存在 Azure Blob 儲存體中，而在效能/成本的選擇中提供低延遲性與高度彈性。這讓客戶得以建置可使用大型資料集的互動式網站、建置可從數百萬個端點儲存感應器和遙測資料的服務，以及透過 Hadoop 工作分析這項資料。

在本教學課程中，您將了解如何使用 HDInsight 來建立及查詢 HBase 資料表。本文將說明下列程序：

-   如何使用 Azure 入口網站佈建 HBase 叢集。
-   如何啟用及使用 RDP 以存取 HBase Shell，並使用 HBase Shell 建立 HBase 範例資料表、新增資料列，然後列出資料表中的資料列。
-   如何建立對應至現有 HBase 資料表的 Hive 資料表，並使用 HiveQL 查詢 HBase 資料表中的資料。
-   如何使用 .NET SDK 建立新的 HBase 資料表、列出您帳戶中的 HBase 資料表，以及如何從您的資料表中新增及擷取資料列。

> [WACOM.NOTE] HBase 目前只能在預覽本中用於 HDInsight 上的 HDInsight 3.0 叢集 (以 Hadoop 2.2.0 為基礎)。如需版本資訊，請參閱 [HDInsight 在 Hadoop 叢集版本中提供的新功能？][]

在預覽期間，建議您針對使用於 HBase 叢集中的任何資料，將其原始版本的備份複本儲存在該叢集以外。這是因為資料庫檔案的格式在未來的版本中可能會有變更，而預覽版中使用的現行資料檔案格式可能不受支援，或無法升級至後續版本。

**必要條件：**

開始進行本教學課程之前，您必須具備下列條件：

-   Azure 訂閱。如需取得訂閱的詳細資訊，請參閱[購買選項][]、[成員優惠][]或[免費試用][]。
-   一個 Azure 儲存體帳戶。如需相關指示，請參閱[如何建立儲存體帳戶][]。
-   Visual Studio 的複本。

**預估完成時間**：30 分鐘

## 本教學課程內容

-   [在 Azure 入口網站中佈建 HBase 叢集][]
-   [從 HBase Shell 建立 HBase 範例資料表][]
-   [使用 Hive 查詢 HBase 資料表][]
-   [使用 HBase C# API 建立 HBase 資料表，並從資料表擷取資料][]
-   [摘要][]

## <a name="create-hbase-cluster"></a>在 Azure 入口網站上佈建 HBase 叢集

本節說明如何使用 Azure 入口網站佈建 HBase 叢集。

**在 Azure 入口網站中佈建 HDInsight 叢集**

1.  登入 [Azure 管理入口網站][]。

2.  按一下左側的 [HDInsight] 列出您帳戶中的叢集狀態，然後按位於左下角的 [+新增]。

    ![][]

3.  按一下左邊算起第二欄中的 HDInsight 圖示，然後按位於下一欄中的 HBase 選項。指定 [叢集名稱] 和 [叢集大小] 的值、儲存體帳戶的名稱，以及新 HBase 叢集的密碼。

    ![][1]

4.  按一下位於左下角的勾號圖示，以建立 HBase 叢集。

## <a name="create-sample-table"></a>從 HBase Shell 建立 HBase 範例資料表

本節說明如何啟用及使用遠端桌面通訊協定 (RDP) 存取 HBase Shell，然後用它來建立 HBase 範例資料表、新增資料列，然後列出資料表中的資料列。

本節內容假設您已完成第一節中列出的程序，因此已成功建立 HBase 叢集。

**啟用 HBase 叢集的 RDP 連線**

1.  若要啟用 HDInsight 叢集的遠端桌面連線，請選取您已建立的 HBase 叢集，然後按一下 [組態] 索引標籤。按一下頁面底部的 [啟用遠端] 按鈕，以啟用叢集的 RDP 連線。
2.  在 [設定遠端桌面] 精靈上提供認證和到期日，然後按一下位於右下角的勾選圓圈。(作業可能需要數分鐘才能完成。)
3.  若要連接到 HDInsight 叢集，請按一下 [組態] 索引標籤底部的 [連接] 按鈕。

**開啟 HBase Shell**

1.  在您的 RDP 工作階段內，按一下位於桌面上的 [Hadoop 命令提示字元] 捷徑。

2.  將資料夾切換至 HBase 主目錄：

        cd %HBASE_HOME%\bin

3.  開啟 HBase Shell：

        hbase shell

**建立範例資料表、新增資料，並擷取資料**

1.  建立範例資料表：

        create 'sampletable', 'cf1'

2.  將資料列新增至範例資料表：

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  列出範例資料表中的資料列：

        scan 'sampletable'

## <a name="hive-query"></a>使用 Hive 查詢 HBase 資料表

現在，您已佈建 HBase 叢集並建立資料表，您可以使用 Hive 加以查詢。本節將建立對應至 HBase 資料表的 Hive 資料表，並用它來查詢您 HBase 資料表中的資料。

**開啟叢集儀表板**

1.  登入 [Azure 管理入口網站][]。
2.  按一下左窗格上的 [HDInsight]。您會看見已建立的叢集清單，其中包含您在上一節中建立的叢集。
3.  按一下要執行 Hive 工作的叢集名稱。
4.  按一下位於頁面底部的 [管理叢集]，以開啟叢集儀表板。它會在不同瀏覽器索引標籤上開啟網頁。
5.  輸入 Hadoop 使用者帳戶的使用者名稱和密碼。預設使用者名稱為 **admin**，密碼是您在佈建程序中輸入的密碼。儀表板顯示如下：

    ![][2]

**執行 Hive 查詢**

1.  若要建立對應至 HBase 資料表的 Hive 資料表，請將下方的 HiveQL 指令碼輸入 Hive 主控台視窗中，然後按一下 [提交] 按鈕。在執行此陳述式前，請確定您已使用 HBase Shell 在 HBase 中建立此處參考的範例資料表。

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;  
        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  若要對 HBase 中的資料執行 Hive 查詢，請將下方的 HiveQL 指令碼輸入 Hive 主控台視窗中，然後按一下 [提交] 按鈕。

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;
        SET hive.aux.jars.path=file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hive-hbase-handler-0.12.0.2.0.9.0-1677.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-server-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-protocol-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/htrace-core-2.01.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-client-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/guava-12.0.1.jar;
        SELECT count(*) FROM hbasesampletable;

3.  若要擷取 Hive 查詢的結果，請在工作執行完成時，按一下 [工作階段] 視窗中的 [檢視詳細資料] 連結。

注意：HBase Shell 連結會將索引標籤切換至 [HBase Shell]。

**瀏覽輸出檔案**

1.  在叢集儀表板中，按一下位於頂端的 [檔案]。
2.  按一下 [Templeton-工作- 狀態]。
3.  按一下「前次修改時間」比您先前記下的「工作開始時間」稍晚的 GUID 號碼。記下此 GUID。您在下一節將會用到此號碼。
4.  **stdout** 檔案包含您在下一節需要使用的資料。如有需要，您可以按一下 **stdout** 以下載資料檔案的複本。

## <a name="hbase-powershell"></a>使用 HBase C# API 建立 HBase 資料表，並從資料表擷取資料

Marlin 是 REST API 上方的一個薄層，用以處理在 C# 中使用 ProtoBuf 與 HBase 進行的互動。Marlin 專案必須從 github 下載，且建置的專案須使用 HBase .NET SDK。

1.  遵循 [Marlin 的專案頁面][] (英文) 中的「下載 Marlin 專案」所說明的建置步驟。將其解壓縮至本機目錄。

2.  在 Visual Studio 中開啟專案。移至 [工具] 功能表 -\> [Library Package Manager]，然後選取 [Manage NuGet Packages for Solution...]，以開啟 [管理 NuGet 封裝管理員] 精靈。

    ![][3]

3.  在右上方的線上方塊中搜尋 protobuf-net，然後安裝 v2.0.0.68。在 [方案總管] 的 [Marlin] 專案上按一下滑鼠右鍵，然後選取 [建置]，以建置 Marlin 專案。

4.  擷取已建置的 marlin.dll，並將其新增至您的 C# 專案。

5.  使用叢集認證建立新的 Marlin 執行個體，並擷取叢集版本：

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  若要列出叢集中的資料表，您可以使用下列程式碼：

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  若要建立新的 HBase 資料表，請使用此程式碼：

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  若要依索引鍵擷取資料列，請指定資料表名稱和資料列索引鍵，以擷取資料列值。

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  若要儲存資料的新資料列，您可以使用下列程式碼：

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>摘要

在本教學課程中，您已了解到如何佈建 HBase 叢集、如何建立資料表，以及如何從 HBase Shell 檢視這些資料表中的資料。您也已了解如何使用 Hive 查詢 HBase 資料表中的資料，以及使用 HBase C# API 建立 HBase 資料表，和擷取資料表中的資料。

  [HDInsight 在 Hadoop 叢集版本中提供的新功能？]: ../hdinsight-component-versioning/
  [購買選項]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [如何建立儲存體帳戶]: http://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
  [在 Azure 入口網站中佈建 HBase 叢集]: #create-hbase-cluster
  [從 HBase Shell 建立 HBase 範例資料表]: #create-sample-table
  [使用 Hive 查詢 HBase 資料表]: #hive-query
  [使用 HBase C# API 建立 HBase 資料表，並從資料表擷取資料]: #hbase-powershell
  [摘要]: #summary
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [Marlin 的專案頁面]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
