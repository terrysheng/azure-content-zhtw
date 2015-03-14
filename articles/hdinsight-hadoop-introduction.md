<properties 
	pageTitle="HDInsight 中 Hadoop 的簡介：雲端中的巨量資料分析 | Azure" 
	description="了解 Azure HDInsight 如何使用雲端中的 Apache Hadoop 叢集，以提供軟體架構來管理、分析和報告巨量資料。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# HDInsight 中 Hadoop 的簡介：雲端中的巨量資料處理和分析

取得 HDInsight 元件、一般術語和案例的概觀，並了解在 HDInsight 中使用 Hadoop 所需的資源。

Azure HDInsight 在雲端部署和佈建 Apache Hadoop 叢集，提供軟體架構來管理、分析和報告巨量資料。Hadoop 核心採用 Hadoop Distributed File System (HDFS) 來提供可靠的資料儲存，並提供簡單的 MapReduce 程式設計模型，可並行處理和分析這個分散式系統中儲存的資料。 

### 什麼是巨量資料？
巨量資料是指以越來越多數量、越來越快的速度及越來越多不同的非結構化格式和可變語義內容所收集的資料。 

巨量資料是指任何極為龐大的數位資訊，包括 Twitter 摘要中的文字、來自工業設備的感應器資訊，以及客戶在線上型錄瀏覽和購物的相關資訊。巨量資料可以屬於過去 (亦即儲存的資料) 或即時 (亦即直接從來源串流而來)。 

若要讓巨量資料轉變為可化為行動的情報或認知，不僅必須提出正確的問題，並收集與問題相關的資料，而且資料必須可存取、經過整理和分析，然後以實用的形式呈現。這就是 HDInsight 中 Hadoop 的用途。


## 本文內容

本文提供 HDInsight 上的 Hadoop 概觀，包括：

* **[HDInsight 上的 Hadoop 生態系統概觀：](#overview)**  HDInsight 是 Azure 上的 Hadoop 解決方案，提供 Storm、HBase、Pig、Hive、Sqoop、Oozie、Ambari 等的實作。HDInsight 也與商業智慧 (BI) 工具整合，例如 Excel、SQL Server Analysis Services 和 SQL Server Reporting Services。
* **[Hadoop 在雲端中的優點：](#advantage)**採用 HDInsight 的 Hadoop 雲端實作的理由。
* **[HDInsight 的巨量資料分析解決方案：](#solutions)**HDInsight 為組織提供解答的一些實際用途，包括分析 Twitter 情緒和分析 HVAC 系統效率。
* **[深入了解巨量資料分析、Hadoop 和 HDInsight 的實用資源：](#resources)**其他資訊的連結。


## <a name="overview"></a>HDInsight 上的 Hadoop 生態系統概觀

Hadoop 是快速發展的技術領域，是巨量資料分析的解決方案。HDInsight 是 Hadoop 的 Microsoft Azure 雲端實作架構。
 
HDInsight 中的 Hadoop 叢集採用多種 Hortonworks Data Platform (HDP) 散發版本，以及該散發內的 Hadoop 元件集。如需 HDInsight 叢集內的元件及其版本的相關資訊，請參閱 [HDInsight 所提供 Hadoop 叢集版本的新功能][component-versioning]

Apache Hadoop 是巨量資料管理和分析的軟體架構。Apache Hadoop 核心提供 Hadoop Distributed File System (HDFS) 的可靠資料儲存，並提供簡單的 MapReduce 程式設計模型並行處理和分析這個分散式系統中儲存的資料。HDFS 使用資料複寫來處理部署這類高度分散式系統時發生的硬體故障問題。

以下是 HDInsight 中的 Hadoop 技術：

* **[Ambari](#ambari)：**叢集佈建、管理和監視 
* **[Avro](#avro)** (Microsoft .NET Library for Avro)：Microsoft .NET 環境的資料序列化 
* **[HBase](#hbase)：**超大型資料表的非關聯式資料庫
* **[HDFS](#hdfs)：**Hadoop Distributed File System
* **[Hive](#hive)：**SQL 式查詢
* **[Mahout](#mahout)：**機器學習
* **[MapReduce 和 YARN](#mapreduce)：**分散式處理和資源管理
* **[Oozie](#oozie)：**工作流程管理
* **[Pig](#pig)：**簡單撰寫 MapReduce 轉換的指令碼
* **[Sqoop](#sqoop)：**資料匯入和匯出 
* **[Storm](#storm)：**即時處理快速、大量的資料串流
* **[Zookeeper](#zookeeper)：**協調分散式系統中的程序
 
此外，了解**[商業智慧工具](#bi)**您可以與 HDInsight 搭配使用。

###<a name="ambari"></a>Ambari

Apache Ambari 可用來佈建、管理及監視 Apache Hadoop 叢集。其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。請參閱  [使用 Ambari API 監視 HDInsight 中的 Hadoop 叢集](../hdinsight-monitor-use-ambari-api/)和 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 參考</a>。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

Microsoft .NET Library for Avro 針對 Microsoft .NET 環境的序列化，實作 Apache Avro 壓縮二進位資料交換格式。它會使用 <a target="_blank" href="http://www.json.org/">JSON</a> 來定義不限語言的結構描述，支援語言互通性，這表示以某種語言序列化的資料可供另一種語言讀取。如需此格式的詳細資訊，請參閱 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 規格</a>。 
Avro 檔案格式可支援分散式 MapReduce 程式設計模型。檔案「可分割」，這表示您可以從檔案中的任何一點開始讀取特定區塊。如需了解作法，請參閱[使用 Microsoft .NET Library for Avro 將資料序列化](../hdinsight-dotnet-avro-serialization/)。

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">Apache HBase (英文)</a> 是以 Hadoop 為基礎的非關聯式資料庫，針對大量的非結構化和半結構化資料而設計 - 可能是數十億的資料列乘以數十億的資料行。HDInsight 上的 HBase 叢集設定為直接將資料儲存在 Azure Blob 儲存體，延遲性低，彈性又高。請參閱 [HDInsight 上的 HBase 概觀](../hdinsight-hbase-overview/)。

### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) 是分散式檔案系統，加上 MapReduce 和 YARN，成為 Hadoop 生態系統的核心。HDFS 是 HDInsight 上的 Hadoop 叢集的標準檔案系統。

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是以 Hadoop 為基礎的資料倉儲軟體，可讓您使用一種稱為 HiveQL 的 SQL 式語言，以查詢和管理分散式儲存體中的大型資料集。Hive 和 Pig 一樣，是以 MapReduce 為基礎的抽象，Hive 會在執行時將查詢轉換為一系列的 MapReduce 工作。Hive 在概念上比 Pig 更接近關聯式資料庫管理系統，因此，適用於更具結構化的資料。對於非結構化資料，Pig 是較好的選擇。請參閱[在 HDInsight 上將 Hive 與 Hadoop 搭配使用](../hdinsight-use-hive/)。

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是 Hadoop 上執行的機器學習演算法的可擴充程式庫。機器學習應用程式採用統計學的原理，教導系統從資料中學習，根據過去的結果來研判未來的行為。請參閱[在 Hadoop 上使用 Mahout 來產生電影推薦](../hdinsight-mahout/)。

### <a name="mapreduce"></a>MapReduce 和 YARN
Hadoop MapReduce 是一套軟體架構，可讓您撰寫應用程式來平行處理巨量資料集。MapReduce 工作可分割大型資料集，並將資料組織成機碼值組以便於處理。 

Apache YARN 是新一代的 MapReduce (MapReduce 2.0，簡稱為 MRv2)，可將 JobTracker 的兩項主要工作 (資源管理和工作排程/監視) 分割成個別實體。

如需 MapReduce 的詳細資訊，請參閱 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> 位於 Hadoop Wiki。若要深入了解 YARN，請參閱 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是可管理 Hadoop 工作的工作流程協調系統。它可與 Hadoop 堆疊相整合，並支援 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 工作。它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。請參閱[在 HDInsight 上將以時間為基礎的 Oozie 協調器與 Hadoop 搭配使用](../hdinsight-use-oozie-coordinator-time/)。

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一個高階平台，可讓您在非常大的資料集上，使用稱為 Pig Latin 的簡單指令碼語言來執行複雜的 MapReduce 轉換。Pig 會將 Pig Latin 指令碼轉換成可在 Hadoop 內執行。您可以建立使用者定義函數 (UDF) 來延伸 Pig Latin。請參閱[使用 Pig 和 Hadoop 來分析 Apache 記錄檔](../hdinsight-use-pig/)。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是在 Hadoop 與 SQL 之類的關聯式資料庫或其他結構化資料儲存之間盡可能有效傳輸大量資料的工具。請參閱[使用 Sqoop 與 Hadoop](../hdinsight-use-sqoop/)。

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a> 是分散式、即時的運算系統，可快速處理大型的資料串流。Storm 以受管理叢集的形式在 HDInsight 中提供。請參閱[使用 Storm 和 Hadoop 來分析即時感應器資料](../hdinsight-storm-sensor-data-analysis/)。

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> 利用資料暫存器 (znode) 的共用階層式命名空間，協調大型分散式系統中的程序。Znodes 包含協調程序所需的少量中繼資訊：狀態、位置、組態等。 

### <a name="bi"></a>商業智慧工具 
熟悉的商業智慧 (BI) 工具 (例如 Excel、PowerPivot、SQL Server Analysis Services 及 Reporting Services) 可整合使用 Power Query 增益集或 Microsoft Hive ODBC Driver 的 HDInsight 來擷取、分析和報告資料。 

這些 BI 工具可協助您分析巨量資料：

* <a target="_blank" href="http://www.microsoft.com/zh-tw/download/details.aspx?id=39379">下載 Microsoft Power Query for Excel</a> 
* <a target="_blank" href="http://www.microsoft.com/zh-tw/download/details.aspx?id=40886">下載 Microsoft Hive ODBC 驅動程式</a>
* <a target="_blank" href="http://www.microsoft.com/zh-tw/server-cloud/solutions/business-intelligence/analysis.aspx">深入了解 SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">了解 SQL Server Reporting Services</a>

## <a name="advantage"></a>Hadoop 在雲端中的優點

在 Azure 雲端生態系統內，HDInsight 中的 Hadoop 提供許多優點，包括：

* 最先進的 Hadoop 元件。如需詳細資訊，請參閱[HDInsight 所提供 Hadoop 叢集版本的新功能？][component-versioning]
* 叢集的高可用性和可靠性。如需詳細資訊，請參閱　[HDInsight 中 Hadoop 叢集的可用性和可靠性](../hdinsight-high-availability/)。
* 有效率又符合經濟效益的資料存放區，採用 Azure Blob 儲存體，是一個 Hadoop 相容選項。如需詳細資訊，請參閱[在 HDInsight 中將 Azure Blob 儲存體與 Hadoop 搭配使用](../hdinsight-use-blob-storage/)。
* 與其他 Azure 服務整合，包括[網站](../documentation/services/websites/)和 [SQL Database](../documentation/services/sql-database/)。
* 進入成本低。開始[免費試用](/pricing/free-trial/)，或請參閱　[HDInsight 定價詳細資料](../pricing/details/hdinsight/)。

如需深入了解 HDInsight 中的 Hadoop 優點，請參閱　[HDInsight 的 Azure 功能頁面][marketing-page]。

## <a name="solutions"></a>試用 HDInsight 的巨量資料分析解決方案

分析組織中的資料以深入了解您的業務。這裡有一些範例： 

* [分析 HVAC 感應器資料](../hdinsight-hive-analyze-sensor-data/)：了解如何在 HDInsight (Hadoop) 上使用 Hive 來分析感應器資料，然後在 Microsoft Excel 中將資料視覺化。在此範例中，您將使用 Hive 來處理 HVAC 系統所產生的歷史資料，以查明哪些系統無法可靠地維持規定的溫度。
* [使用 Hive 搭配 HDInsight 來分析網站記錄](../hdinsight-hive-analyze-website-log/)：了解如何使用 HDInsight 中的 HiveQL 來分析網站記錄，以深入了解一天之中來自外部網站的造訪次數，以及使用者遭遇網站錯誤的摘要。
* [在 HDInsight (Hadoop) 中使用 Storm 和 HBase 來即時分析感應器資料](../hdinsight-storm-sensor-data-analysis/)：了解如何建置解決方案，以使用 HDInsight 中的 Storm 叢集處理來自 Azure 事件中心的感應器資料，然後將處理過的感應器資料以近乎即時的資訊顯示在 Web 型儀表板上。

若要試用 HDInsight 上的 Hadoop，請參閱　[HDInsight 文件頁面](../documentation/services/hdinsight/)的＜探索＞一節中的「開始使用」。若要嘗試更進階的範例，請向下捲動到＜分析＞一節。



## <a name="resources"></a>深入了解巨量資料分析、Hadoop 和 HDInsight 的實用資源 

### HDInsight 中的 Hadoop	

* [HDInsight 文件](../documentation/services/hdinsight/)Azure HDInsight 的文件頁面，其中包含文章、視訊和其他資源的連結。

* [HDInsight 中的 Hadoop 學習圖](../hdinsight-learn-map)：HDInsight 的 Hadoop 文件導覽說明。

* [開始使用 Azure HDInsight](../hdinsight-get-started/)：在 HDInsight 中使用 Hadoop 的快速入門教學課程。

* [執行 HDInsight 範例](../hdinsight-run-samples/)對於如何執行 HDInsight 提供的範例進行解說的教學課程。
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx)：HDinsight SDK 的參考文件。


### Azure 上的 SQL Database	
		
* [Azure SQL Database](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx)：SQL Database 的 MSDN 文件。
	
* [SQL Database 管理入口網站](http://msdn.microsoft.com/library/windowsazure/gg442309.aspx)：在雲端管理 SQL Database 的輕量型使用簡便的資料庫管理工具。

* [SQL Database 的大膽嘗試](http://msftdbprodsamples.codeplex.com/releases/view/37304)：SQL Database 範例資料庫的下載頁面。	

### Microsoft 商業智慧		

* [使用 Power Query 將 Excel 連接到 Hadoop](../hdinsight-connect-excel-power-query/)：了解如何使用 Microsoft Power Query for Excel，將 Excel 連接到儲存 HDInsight 叢集相關資料的 Azure 儲存體帳戶。 

* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop](../hdinsight-connect-excel-hive-ODBC-driver/)：了解如何使用 Microsoft Hive ODBC 驅動程式從 HDInsight 匯入資料。

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/zh-tw/server-cloud/solutions/business-intelligence/default.aspx)：了解 Power BI for Office 365、下載 SQL Server 試用版，以及設定 SharePoint Server 2013 和 SQL Server BI。
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>：深入了解 Apache Hadoop 軟體程式庫，這是能夠進行電腦叢集大型資料集分散式處理的架構。	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>：深入了解 Hadoop Distributed File System (HDFS) 的架構和設計，這是 Hadoop 應用程式使用的主要儲存系統。		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>：深入了解撰寫 Hadoop 應用程式的程式設計架構，這能夠對於大型運算節點叢集快速平行處理大量資料。	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 
<!--HONumber=42-->
