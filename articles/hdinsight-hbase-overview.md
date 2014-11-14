<properties urlDisplayName="HDInsight HBase overview" pageTitle="HDInsight 中的 HBase 概觀 | Azure" metaKeywords="" description="HDInsight 中的 HBase 簡介、使用案例，以及與其他資料庫解決方案的比較。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight HBase 概觀" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev" />

# HDInsight HBase 概觀

## 什麼是 HBase？

HBase 是建置於 Hadoop 上的 Apache 開放原始碼 NoSQL 資料庫，可針對大量非結構化及半結構化資料，提供隨機存取功能和強大一致性。它模仿 Google 的 BigTable，並且是資料欄系列導向的資料庫。資料儲存在資料表的資料列中，而資料欄系列會對資料列中的資料進行分類。HBase 是無結構描述的資料庫，也就是說，在使用資料欄前，並不需要先定義資料欄和其中儲存之資料的類型。開放原始碼最先是由 Mike Cafarella 在 2007 年提出的，能以線性方式延展，以處理數千個節點上的數 PB 資料。它可依賴散佈在 Hadoop 生態系統中的應用程式所提供的資料備援、批次處理及其他功能。

## 什麼是 Azure HDInsight HBase？

HDInsight HBase 是以與 Azure 環境整合之受管理叢集的形式來提供。叢集依設定會將資料直接儲存在 Azure Blob 儲存體中，而在效能/成本的選擇中提供低延遲性與高度彈性。這讓客戶得以建置可使用大型資料集的互動式網站、建置可從數百萬個端點儲存感應器和遙測資料的服務，以及透過 Hadoop 工作分析這項資料。HBase 和 Hadoop 是在 Azure 中處理巨量資料專案的好起點，其尤其可以讓即時應用程式使用大型資料集。

HDInsight 實作運用 HBase 的向外延展架構，提供資料表自動分區功能、讀取和寫入的強大一致性，以及自動容錯移轉功能。透過在記憶體內部快取讀取和高輸送量的串流寫入，來提高效能。HDInsight HBase 也提供虛擬網路佈建功能。如需詳細資訊，請參閱[在 Azure 虛擬網路上佈建 HDInsight 叢集][在 Azure 虛擬網路上佈建 HDInsight 叢集] (英文)。

## 如何管理 HDInsight HBase 中的資料？

要管理 HBase 中的資料，可使用 HBase Shell 的 `create` `get`、`put` 和 `scan` 命令。要將資料寫入資料庫，需使用 `put`，而讀取則需使用 `get` 命令。`scan` 命令可用來取得資料表中多個資料列裡的資料。您也可以使用 HBase C# API 管理資料，其在 HBase REST API 之上提供用戶端程式庫。HBase 資料庫也可使用 Hive 進行查詢。如需這些程式設計模型的簡介，請參閱[開始在 HDInsight 中搭配使用 HBase 與 Hadoop][開始在 HDInsight 中搭配使用 HBase 與 Hadoop] (英文)。也提供副處理器，其可處理託管資料庫之節點中的資料。

## 案例：HBase 的使用案例有哪些？

建立 BigTable (以及由此延伸出的 HBase) 的正式使用案例是 Web 搜尋。搜尋引擎會建置索引，以將字詞對應到包含這些字詞的網頁。但除此之外，HBase 還有其他許多適用的使用案例，本節會列舉其中幾個。

### 使用案例 \#1：索引鍵-值存放區

HBase 可用來作為索引鍵-值存放區，並且適合用來管理訊息系統。Facebook 將 HBase 用在他們的訊息系統中，它十分適合用來儲存和管理網際網路通訊。WebTable 使用 HBase 搜尋和管理從網頁上擷取下來的資料表。

### 使用案例 \#2：感應器資料

HBase 適合用來擷取從多個來源不斷收集而來的資料。這包括社交分析、時間序列、讓互動式儀表板保有最新的趨勢與計數器，以及管理稽核記錄系統。範例包括彭博公司的股市資訊終端機
以及 Open Time Series Database (OpenTSDB)，後者可以儲存從伺服器系統之健康情況上收集到的數據，並讓使用者加以存取。

### 使用案例 \#3：即時查詢

[Phoenix][Phoenix] 是適用於 Apache HBase 的 SQL 查詢引擎。它是以 JDBC 驅動程式的形式進行存取，並可使用 SQL 查詢和管理 HBase 資料表。

### 使用案例 \#4：HBase 即平台

應用程式可以將 HBase 當作資料存放區，在此基礎上來執行。範例包括 Phoenix、OpenTSDB、Kiji 及 Titan。應用程式也可與 HBase 整合。範例包括 Hive、Pig、Solr、Storm、Flume、Impala、Spark、Ganglia 及 Drill。

## <a name="next-steps"></a>後續步驟

[開始在 HDInsight 中搭配使用 HBase 與 Hadoop (英文)][開始在 HDInsight 中搭配使用 HBase 與 Hadoop]

[在 Azure 虛擬網路上佈建 HDInsight 叢集 (英文)][在 Azure 虛擬網路上佈建 HDInsight 叢集]

[使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)][使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)]

[使用 Maven 建置搭配使用 HBase 和 HDInsight (Hadoop) 的 Java 應用程式 (英文)][使用 Maven 建置搭配使用 HBase 和 HDInsight (Hadoop) 的 Java 應用程式 (英文)]

[C# HBase SDK (英文)][C# HBase SDK (英文)]

## <a name="see-also"></a>另請參閱

[Apache HBase (英文)][Apache HBase (英文)]

[Bigtable：結構化資料的分散式儲存體系統][Bigtable：結構化資料的分散式儲存體系統] (英文)

  [在 Azure 虛擬網路上佈建 HDInsight 叢集]: ../hdinsight-hbase-provision-vnet/
  [開始在 HDInsight 中搭配使用 HBase 與 Hadoop]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-hbase-get-started/
  [Phoenix]: http://phoenix.apache.org/
  [使用 HDInsight 中的 HBase 分析 Twitter 情緒 (英文)]: ../hdinsight-hbase-analyze-twitter-sentiment/
  [使用 Maven 建置搭配使用 HBase 和 HDInsight (Hadoop) 的 Java 應用程式 (英文)]: ../hdinsight-hbase-build-java-maven/
  [C# HBase SDK (英文)]: https://github.com/hdinsight/hbase-sdk-for-net
  [Apache HBase (英文)]: https://hbase.apache.org/
  [Bigtable：結構化資料的分散式儲存體系統]: http://research.google.com/archive/bigtable.html
