<properties 
	pageTitle="了解 HDInsight (hadoop) 中的 Apache Storm" 
	description="了解如何在 HDInsight (Hadoop) 中使用 Apache Storm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# HDInsight 上的 Apache Storm 概觀

## 什麼是 Storm？

[Apache Storm][apachestorm] 是一個分散式、容錯、開放原始碼的運算系統，可讓您即時處理資料。Storm 解決方案也能夠重播最初未成功處理的資料，保證一定會處理資料。

## HDInsight 上的 Storm 是什麼？

Storm 以受管理叢集的形式整合到 Azure 環境中，可納入更大的 Azure 解決方案中。例如，Storm 可能取用來自服務匯流排佇列或事件中心等服務的資料，然後使用網站或雲端服務將資料視覺化。Storm 叢集也可以設定在 Azure 虛擬網路上，以縮短與相同虛擬網路的其他資源之間的通訊，還能夠與私人資料中心內的資源進行安全的通訊。

若要開始使用 Storm，請參閱[開始使用 HDInsight 中的 Storm][gettingstarted]。

## 如何處理　HDInsight 上　Storm 中的資料？

Storm 叢集是處理**拓撲**，而不是您在 HDInsight 或 Hadoop 中可能熟悉的 MapReduce 工作。Storm 叢集包含兩種節點：執行 **Nimbus** 的前端節點和執行**監督員**的背景工作節點

* **Nimbus** - 類似於 Hadoop 中的 JobTracker，負責將程式碼散發到整個叢集、指派工作給機器及監控失敗。HDInsight 提供兩個 Nimbus 節點，所以 Storm 叢集不會有單一失敗點。

* **監督員** - 每一個背景工作節點的監督員負責啟動和停止節點上的**背景工作處理序**

* **背景工作處理序** - 背景工作處理序執行一部分的**拓撲**。執行中的拓撲分散在整個叢集的許多背景工作處理序上。

* **拓撲** - 定義一份可處理資料**串流**的運算圖。與 MapReduce 工作不同，拓撲會一直執行到您將它們停止為止

* **串流** - 無限制的 **Tuple** 集合。串流由 **spout** 和 **bolt** 產生，由 **bolt** 取用

* **Tuple** - 動態型別值的具名清單。

* **Spout** - 取用資料來源的資料，並發出一或多個**串流**

	> [AZURE.NOTE] 在許多情況下，資料是讀取自 Kafka、Azure 服務匯流排佇列或事件中心等佇列。佇列可確保中斷運作時資料仍持續存在。

* **Bolt** - 取用**串流**、在 **Tuple** 上執行處理，且可能發出**串流**。Bolt 也負責將資料寫入外部儲存體，例如佇列、HDInsight HBase、Blob 或其他資料存放區

* **Thrift** - Apache Thrift 是一套軟體架構，支援可擴充的跨語言服務開發。它可讓您建置 C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk 和其他語言之間通用的服務。

	* **Nimbus** 是一種 Thrift 服務，而**拓撲**是 Thrift 定義，因此可利用各種程式設計語言來開發拓撲。 

如需 Storm 元件的詳細資訊，請參閱 apache.org 上的[Storm 教學課程][apachetutorial] (英文)。

## 案例：Storm 的使用案例有哪些？

以下是可能使用 Apache storm 的常見案例。如需真實案例的相關資訊，請參閱[公司如何使用 Storm][poweredby]。

### 即時分析

Storm 可即時處理資料串流，很適合當資料串流送達時需要從中尋找特定事件或模式並作出反應的資料分析。例如，Storm 拓撲可能監視感應器資料來判斷系統健康情況，並於出現特定模式時產生簡訊來提醒您。

### 擷取轉換載入 (ETL)

ETL 幾乎可視為是 Storm 處理的副效應。例如，如果您透過即時分析進行詐騙偵測，您就已經擷取並轉換資料。您可能也想要有 bolt 將資料儲存在 HBase、Hive 或其他資料存放區中供未來分析。

### 分散式 RPC

分散式 RPC 是一種可利用 Storm 建立的模式。要求會先傳送至 Storm，接著 Storm 將運算工作分散至多個節點上，最後將結果串流傳回給等待的用戶端。

如需 Storm 隨附的分散式 RPC 和 DRPCClient 的詳細資訊，請參閱[分散式 RPC](https://storm.incubator.apache.org/documentation/Distributed-RPC.html) (英文)。

### 線上機器學習

Storm 可以與事先以批次處理訓練過的機器學習解決方案一起使用，例如以 Mahout 為基礎的解決方案。不過，其通用、分散式的運算模型也接納以串流為基礎的機器學習解決方案。例如，[Scalable Advanced Massive Online Analysis (SAMOA) 專案][samoa]是一個採用串流處理的機器學習庫，可以與 Storm 一起使用。

## 我可以使用哪些程式設計語言？

HDInsight 上的　Storm 叢集內建就支援 .NET、Java 和 Python。雖然 Storm [支援其他語言](https://storm.incubator.apache.org/about/multi-language.html)，但其中有許多語言除了需要您進行其他組態變更，還需要您在 HDInsight 叢集上安裝額外的程式設計語言。 

### .NET

SCP 是一個可讓 .NET 開發人員設計和實作拓撲 (包括 spout 和 bolt) 的專案。Storm 叢集預設提供 SCP 的支援。

如需有關使用 SCP 進行開發的相關資訊，請參閱[在 HDInsight 中的 Storm 上使用 SCP.NET 和 C# 開發串流資料處理應用程式](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)。

### Java

您遇到的大多數 Java 範例都是單純 Java 或 Trident。Trident 是一種高階抽象概念，可讓工作變得更簡單，例如聯結、彙總、分組和篩選。不過，Trident 以 Tuple 批次為對象，而原始 Java 解決方案是以一次一個 Tuple 的方式來處理串流。

如需 Trident 的詳細資訊，請參閱 apache.org 上的 [Trident 教學課程](https://storm.incubator.apache.org/documentation/Trident-tutorial.html)。

如需取得原始 Java 與 Trident 拓撲的範例，請移至 HDInsight Storm 叢集的 **%storm_home%\contrib\storm-starter** 目錄。

## 有哪些常見的開發模式？

### 保證處理訊息

Storm 可以提供不同程度的訊息處理保證。例如，基本的 Storm 應用程式可以保證至少處理一次，而 Trident 可以保證剛好處理一次。

如需詳細資訊，請參閱 apache.org 上的[保證處理資料](https://storm.apache.org/about/guarantees-data-processing.html) (英文)。

### BasicBolt

讀取輸入 Tuple、發出零個以上的 Tuple，然後在執行方法結束時立即認可輸入 Tuple，由於這種模式實在太常見，所以 Storm 提供 [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) 介面將此模式自動化。

### 聯結

聯結兩個資料串流的方式隨著應用程式而有所不同。例如，您可能將多個串流的每個 Tuple 聯結成一個新的串流，或只聯結特定時段的幾批 Tuple。無論何者，都可利用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 來完成聯結，這是一種定義 Tuple 如何路由傳送至 bolt 的方法。

在下列 Java 範例中，使用 fieldsGrouping 將來自元件 "1"、"2" 和 "3" 的 Tuple 路由傳送至 **MyJoiner** bolt。

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### 批次處理

批次處理有多種方式。在基本的 Storm Java 拓撲中，您可能使用簡單的計數器來批次處理 X 個 Tuple 再發出它們，或使用稱為計時 Tuple 的內部計時機制，每 X 秒發出一批。

有關使用計時 Tuple 的範例，請參閱[使用 Storm 和 HDInsight 分析感應器資料](hdinsight-storm-sensor-data-analysis.md.md)

如果您使用 Trident，則是以 Tuple 批次處理為基礎。

### 快取處理

通常以記憶體內部快取當做加速處理的機制，將常用的資產保留在記憶體內。因為拓撲分散於多個節點上，以及每個節點內的多個程序上，您應該考慮使用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29)，以確保含有快取查閱欄位的 Tuple，一定會路由傳送至相同的程序。這樣可避免快取項目在程序之間重複。

### 串流前 N 個

當拓撲依賴計算 'top' N 值時，例如 Twitter 的前 5 個趨勢，您應該平行地計算前 N 值，然後將這些計算的輸出合併成一個全域值。這可利用 [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) 來進行，依欄位路由傳送至平行 bolt，經過依欄位值分割資料後，最後路由傳送至 bolt 來整體決定前 N 值。

如需相關範例，請參閱 [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) 範例。

## 後續步驟

* [開始使用 HDInsight 中的 Storm][gettingstarted]

* [使用 Storm 和 HDInsight 分析感應器資料](hdinsight-storm-sensor-data-analysis.md)

* [在 HDInsight 中的 Storm 上使用 SCP.NET 和 C# 開發串流資料處理應用程式](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /zh-tw/documentation/articles/hdinsight-storm-getting-started

<!--HONumber=42-->
