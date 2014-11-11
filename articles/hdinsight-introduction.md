<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight 中 Hadoop 的簡介

## 概觀

Azure HDInsight 是在雲端部署和佈建 Apache™ Hadoop® 叢集的服務，提供管理、分析和報告巨量資料的軟體架構。

### 巨量資料

所謂的「巨量資料」是指提高容量、加速和擴大各種非結構化格式和可變語義內容時收集的資料。巨量資料集合本身對於企業不具有價值。若要讓巨量資料轉變為可化為行動的情報或認知，不僅必須提出正確的問題，並收集與問題相關的資料，而且必須可存取、整理、分析資料，然後以有用的形式呈現，這通常包含形成現在所謂「交互式」角度和內容的其他各種來源資料。

### Apache Hadoop

Apache Hadoop 是有助於巨量資料管理和分析的軟體架構。Apache Hadoop 核心提供 Hadoop Distributed File System (HDFS) 的可靠資料儲存，並提供簡單的 MapReduce 程式設計模型並行處理和分析這個分散式系統中儲存的資料。HDFS 使用資料複寫來處理部署這類高度分散式系統時發生的硬體故障問題。

### MapReduce 和 YARN

為了簡化分析不同來源非結構化資料的複雜度，MapReduce 程式設計模型提供對應和減少作業結束的核心抽象。MapReduce 程式設計模型將本身所有的工作視為對於包含機碼值組的資料集進行的運算。因此，輸入檔和輸出檔兩者必須包含僅由機碼值組組成的資料集。這項限制的主要優點是 MapReduce 工作最終將是可編輯的工作。

Pig 及 Hive 之類與 Hadoop 相關的其他專案均以 HDFS 及 MapReduce 架構為基礎。這類專案可提供比直接使用 MapReduce 程式更簡單的叢集管理方法。例如，Pig 能夠讓您使用稱為 Pig Latin 的程序語言撰寫程式，這些程式將編譯為叢集上的 MapReduce 程式。它也提供管理資料流程的流暢控制。Hive 是一種資料倉儲基礎結構，對於叢集中儲存的檔案所保留的資料提供資料表抽象，使用稱為 HiveQL 的宣告語言撰寫類似 SQL 的陳述式即可查詢這些資料。

### HDInsight

Azure HDInsight 讓 Apache Hadoop 成為雲端的服務。它使得 HDFS/MapReduce 軟體架構以及 Pig、Hive 和 Oozie 之類相關的專案可以在更簡單、更有擴充性且更符合成本效益的環境中發揮作用。

已在 HDInsight 所部署的 Hadoop 叢集中新增第二個前端節點，以提升服務的可用性。Hadoop 叢集的標準實作通常包含單一前端節點。HDInsight 會透過新增次要前端節點，來將這個單一失敗點移除。除非客戶使用超大型前端節點來佈建叢集，否則新的 HA 叢集組態開關並不會變更叢集價格。

HDInsight 的其中一個主要效益是管理和儲存資料的方式。HDInsight 使用 Azure Blob 儲存體做為預設檔案系統。Blob 儲存體和 HDFS 是不同的檔案系統，分別經過最佳化之後，均適用於儲存資料並對於這些資料進行運算。

-   對於將使用 HDInsight 處理的資料，Azure Blob 儲存體是高擴充性和可用性、低成本、長期和可共用的儲存選項。
-   HDInsight 在 HDFS 部署的 Hadoop 叢集適合對於資料執行 MapReduce 運算工作。

HDInsight 叢集部署在運算節點上的 Azure 中，用來執行 MapReduce 工作，這些工作完成後，使用者可以中止這些叢集。將計算之後的資料保留在 HDFS 叢集的成本較高。Blob 儲存體是一種健全、一般用途的 Azure 儲存體解決方案。將資料儲存於 Blob 儲存體中能夠安全刪除用於運算的叢集，而不遺失使用者資料。不過，Blob 儲存體不只是低成本的解決方案而已：它提供完整功能的 HDFS 檔案系統介面，所提供的 Hadoop 生態系統一整組元件可以直接針對它管理的資料直接運作 (預設)，讓客戶感覺相當便利。

HDInsight 使用 Azure PowerShell 設定、執行和後續處理 Hadoop 工作。HDInsight 也提供 Sqoop 連接器，可用來將 Azure SQL 資料庫的資料匯入到 HDFS，或將 HDFS 的資料匯出到 Azure SQL 資料庫。

HDInsight 另外還提供 YARN。這是一套新的、通用、分散式的應用程式管理架構，取代標準的 Apache Hadoop MapReduce 架構來處理 Hadoop 叢集的資料。實際上是當做 Hadoop 作業系統，可從批次處理的單一用途資料平台，將 Hadoop 移轉至多重用途平台上，以進行批次、互動式、線上和串流處理。這個新的管理架構可根據容量保證、公平性和服務等級協定等準則，提高延展性和叢集使用率。

Microsoft Power Query for Excel 可用來將 Azure HDInsight 或任何 HDFS 的資料匯入到 Excel。這項附加元件可簡化大範圍資料來源的資料探索和存取，使 Excel 的自助 BI 操作方式提升。除了 Power Query 之外，Microsoft Hive ODBC Driver 也可用來整合商業智慧 (BI) 工具，例如 Excel、SQL Server Analysis Services 和 Reporting Services，使得端對端資料分析更加順利且簡化。

### 大綱

本主題說明 HDInsight 支援的 Hadoop 生態系統、HDInsight 的主要使用案例，以及後續資源的指南。其中包含下列小節：

-   [HDInsight 的 Hadoop 生態系統][HDInsight 的 Hadoop 生態系統]：HDInsight 提供 Pig、Hive、Sqoop、Oozie 和 Ambari 的實作，並支援其他 BI 工具，例如 Excel、SQL Server Analysis Services 和 Reporting Services，這些工具與使用 Power Query 或 Microsoft Hive ODBC Driver 的 Blob 儲存體/HDFS 和 MapReduce 架構整合。本節說明 Hadoop 生態系統中的這些系統能夠處理哪些工作。

-   [HDInsight 的巨量資料案例][HDInsight 的巨量資料案例]：本節解說下列問題：HDInsight 是適合處理何種工作的技術？

-   [HDInsight 的資源][HDInsight 的資源]：本節說明尋找相關資源的詳細資訊。

## <a name="Ecosystem">Azure 的 Hadoop 生態系統 </a>

### 簡介

HDInsight 提供實作 Microsoft 的雲端解決方案處理巨量資料的架構。這個同盟生態系統可運用 MapReduce 程式設計模型的平行處理能力管理和分析大量資料。本節將舉例簡要說明可搭配 HDInsight 使用的 Apache 相容 Hadoop 技術。

HDInsight 提供 Hive 及 Pig 的實作，藉以整合資料處理和倉儲功能。Microsoft 的巨量資料解決方案整合 Microsoft 的 BI 工具，例如 SQL Server Analysis Services、Reporting Services、PowerPivot 和 Excel。這能夠讓您對於 HDInsight 在 Blob 儲存體中儲存和管理的資料執行直接的 BI。

使用 HDInsight 也可以下載屬於 Hadoop 生態系統且能夠在 Hadoop 叢集上執行的其他 Apache 相容技術和相關技術。這些包括 Sqoop 之類整合 HDFS 與關聯式資料儲存的開放原始碼技術。

### Pig

Pig 是處理 Hadoop 叢集巨量資料的高階平台。Pig 包含稱為 Pig Latin 的資料流程語言，支援對於大型資料集撰寫查詢，以及從主控台執行程式的執行環境。Pig Latin 程式包含轉換為 MapReduce 程式系列的資料集轉換系列。Pig Latin 抽象提供的資料結構比 MapReduce 豐富，而且對於 Hadoop 執行的工作與 SQL 對於關聯式資料庫管理系統 (RDBMS) 執行的工作相同。Pig Latin 可完全擴充。以 Java、Python、Ruby、C# 或 JavaScript 撰寫的使用者定義函數 (UDF) 可以呼叫來自訂撰寫分析時的各個處理路徑階段。如需詳細資訊，請參閱[歡迎使用 Apache Pig！][歡迎使用 Apache Pig！](英文)

### Hive

Hive 是儲存於 HDFS 的分散式資料倉儲管理資料。它是 Hadoop 查詢引擎。Hive 就像是具備高超 SQL 技術的分析師，提供類似 SQL 的介面和關聯式資料模型。Hive 使用稱為 HiveQL 的語言，這是 SQL 的方言。Hive 和 Pig 一樣，是以 MapReduce 為基礎的抽象，Hive 會在執行時將查詢轉換為一系列的 MapReduce 工作。Hive 的用途在概念上與 RDBMS 的用途相近，因此適合大多數結構化資料使用。對於非結構化資料，Pig 是較好的選擇。如需詳細資訊，請參閱[歡迎使用 Apache Hive！][歡迎使用 Apache Hive！](英文)

### Sqoop

Sqoop 是在 Hadoop 與 SQL 之類的關聯式資料庫或其他結構化資料儲存之間盡可能有效傳輸大量資料的工具。Sqoop 可用來將外部結構化資料儲存的資料匯入到 HDFS 或 Hive 之類的相關系統。Sqoop 也可以擷取 Hadoop 的資料，並且將擷取的資料匯出到外部關聯式資料庫、企業資料倉儲，或其他任何結構化資料儲存類型。如需詳細資訊，請參閱 [Apache Sqoop][Apache Sqoop] 網站 (英文)。

### Oozie

Apache Oozie 是可管理 Hadoop 工作的工作流程/協調系統。它可與 Hadoop 堆疊相整合，並支援 Apache MapReduce、Apache Pig、Apache Hive 和 Apache Sqoop 的 Hadoop 工作。它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。

### Ambari

Apache Ambari 可用來佈建、管理及監視 Apache Hadoop 叢集。其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。如需這些 API 的詳細資訊，請參閱 [Ambari API 參考資料][Ambari API 參考資料]。HDInsight 目前僅支援 Ambari 監視功能。HDInsight 叢集 2.1 和 3.0 版支援 Ambari API 1.0 版。如需 Ambari 的詳細資訊，請參閱 [Apache Ambari][Apache Ambari] 網站。

### Microsoft Avro Library

Microsoft Avro Library 可在 Microsoft.NET 環境中實作 Apache Avro 資料序列化系統。Apache Avro 提供一個可用於序列化的壓縮二進位資料交換格式。它會使用 [JSON][JSON] 來定義不限語言的結構描述，以負責提供語言互通性。以某個語言序列化的資料可以使用另一個語言讀取。目前支援 C、C++、C#、Java、PHP、Python 和 Ruby。如需此格式的詳細資訊，請參閱 [Apache Avro 規格][Apache Avro 規格]。請注意，Microsoft Avro Library 的目前版本不支援此規格的遠端程序呼叫 (RPC) 部分。

Azure HDInsight 和其他 Apache Hadoop 環境中廣泛採用了 Apache Avro 序列化格式。Avro 提供一個便利方式來呈現 Hadoop MapReduce 工作的複雜資料結構。Avro 檔案格式已針對支援分散式 MapReduce 程式設計模型進行設計。啟用配送的主要功能是指檔案「可分割」，因此使用者可搜尋檔案中的任何位置，並從特定區塊開始讀取。如需詳細資訊，請參閱[使用 Microsoft Avro Library 將資料序列化][使用 Microsoft Avro Library 將資料序列化]。

### 商業智慧工具及連接器

熟悉的商業智慧 (BI) 工具 (例如 Excel、PowerPivot、SQL Server Analysis Services 及 Reporting Services) 可整合使用 Power Query 增益集或 Microsoft Hive ODBC Driver 的 HDInsight 來擷取、分析和報告資料。

-   從 [Microsoft 下載中心][Microsoft 下載中心]可以下載 Microsoft Power Query for Excel。

-   從這個[下載中心][下載中心] (英文) 可以下載 Microsoft Hive ODBC Driver。

-   如需 Analysis Services 的資訊，請參閱 [SQL Server 2012 Analysis Services][SQL Server 2012 Analysis Services]。

-   如需 Reporting Services 的資訊，請參閱 [SQL Server 2012 Reporting][SQL Server 2012 Reporting]。

## <a name="Scenarios"></a>HDInsight 的巨量資料案例

HDInsight 用途的其中一個範例是對於 Azure 節點上儲存的整個非結構化資料集進行批次的臨機操作分析，這並不需要經常更新。

這些條件適用於商業、科學和治理的各種活動。例如，可能包括監視零售的供應鏈、金融的可疑交易模式、公共事業和服務的需求模式、環境感應器陣列的空氣品質和水質，或都會區的犯罪模式。

HDInsight (以及一般的 Hadoop 技術) 最適合用來處理大量的記錄或封存資料，這種資料一旦寫入之後就不需要經常更新，而且經常讀取，一般是為了進行全面分析。這類資料可補足最適合需要較少資料 (僅達到 GB 程度而非 PB 程度) 的 RDBMS 所處理的資料，後面這種資料需要針對整個資料集內特定的資料點持續更新或查詢。RDBMS 最適合依據固定結構描述來組織和儲存的結構化資料。MapReduce 最適合沒有預先定義結構描述的非結構化資料，因為它能夠在這類資料進行處理時解譯該資料。

## <a name="Resources"></a>後續步驟：HDInsight 的資源

**Microsoft：HDInsight**

-   [HDInsight 文件][HDInsight 文件]：Azure HDInsight 的文件頁面，其中包含文章、視訊和其他資源的連結。

-   [HDInsight 版本資訊][HDInsight 版本資訊]：最新版本的注意事項。

-   [開始使用 Azure HDInsight][開始使用 Azure HDInsight]：提供使用 HDInsight 快速入門的教學課程。

-   [執行 HDInsight 範例][執行 HDInsight 範例]：對於如何執行 HDInsight 提供的範例進行解說的教學課程。

-   [巨量資料與 Azure][巨量資料與 Azure]：說明使用 Azure 能夠建立的巨量資料案例。

-   [Azure HDInsight SDK][Azure HDInsight SDK]：HDinsight SDK 的參考文件。

**Microsoft：Windows 與 SQL Database**

-   [Azure 首頁][Azure 首頁]：開始建立應用程式所需的案例、免費試用註冊、開發工具和文件。

-   [Azure SQL Database][Azure SQL Database]：SQL Database 的 MSDN 文件。

-   [SQL Database 管理入口網站][SQL Database 管理入口網站]：在雲端管理 SQL Database 的輕量型使用簡便的資料庫管理工具。

-   [SQL Database 的大膽嘗試][SQL Database 的大膽嘗試] (英文)：SQL Database 範例資料庫的下載頁面。

**Microsoft：商業智慧**

-   [使用 Power Query 將 Excel 連接到 HDInsight][使用 Power Query 將 Excel 連接到 HDInsight]：了解如何使用 Microsoft Power Query for Excel，將 Excel 連接到儲存 HDInsight 叢集相關資料的 Azure 儲存體帳戶。

-   [使用 Microsoft Hive ODBC Driver 將 Excel 連接到 HDInsight][使用 Microsoft Hive ODBC Driver 將 Excel 連接到 HDInsight]：了解如何使用 Microsoft Hive ODBC Driver 從 Azure HDInsight 匯入資料。

-   [Microsoft BI PowerPivot][Microsoft BI PowerPivot]：下載強大的資料交互式和探勘工具並取得相關資訊。

-   [SQL Server 2012 Analysis Services][SQL Server 2012 Analysis Services]：下載 SQL Server 2012 試用版，並了解如何建立有助於將認知化為行動的全面性企業級分析解決方案。

-   [SQL Server 2012 Reporting][1]：下載 SQL Server 2012 試用版，並了解如何建立有助於企業即時決策的全面性高擴充性解決方案。

**Apache Hadoop**：

-   [Apache Hadoop][Apache Hadoop]：深入了解 Apache Hadoop 軟體程式庫，這是能夠進行電腦叢集大型資料集分散式處理的架構。

-   [HDFS][HDFS]：深入了解 Hadoop Distributed File System (HDFS) 的架構和設計，這是 Hadoop 應用程式使用的主要儲存系統。

-   [MapReduce][MapReduce]：深入了解撰寫 Hadoop 應用程式的程式設計架構，這能夠對於大型運算節點叢集快速平行處理大量資料。

  [HDInsight 的 Hadoop 生態系統]: #Ecosystem
  [HDInsight 的巨量資料案例]: #Scenarios
  [HDInsight 的資源]: #Resources
  [歡迎使用 Apache Pig！]: http://pig.apache.org/
  [歡迎使用 Apache Hive！]: http://hive.apache.org/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Ambari API 參考資料]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Apache Ambari]: http://ambari.apache.org/
  [JSON]: http://www.json.org
  [Apache Avro 規格]: http://avro.apache.org/docs/current/spec.html
  [使用 Microsoft Avro Library 將資料序列化]: ../hdinsight-dotnet-avro-serialization/
  [Microsoft 下載中心]: http://go.microsoft.com/fwlink/?LinkID=286689
  [下載中心]: http://go.microsoft.com/fwlink/?LinkID=286698
  [SQL Server 2012 Analysis Services]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx
  [SQL Server 2012 Reporting]: http://www.microsoft.com/zh-tw/sqlserver/solutions-technologies/business-intelligence/reporting.aspx
  [HDInsight 文件]: http://go.microsoft.com/fwlink/?LinkID=285601
  [HDInsight 版本資訊]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-release-notes/
  [開始使用 Azure HDInsight]: ../hdinsight-get-started/
  [執行 HDInsight 範例]: ../hdinsight-run-samples/
  [巨量資料與 Azure]: http://azure.microsoft.com/zh-tw/solutions/big-data/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx
  [Azure 首頁]: http://azure.microsoft.com/zh-tw/
  [Azure SQL Database]: http://msdn.microsoft.com/zh-tw/library/windowsazure/ee336279.aspx
  [SQL Database 管理入口網站]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg442309.aspx
  [SQL Database 的大膽嘗試]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [使用 Power Query 將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-power-query/
  [使用 Microsoft Hive ODBC Driver 將 Excel 連接到 HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft BI PowerPivot]: http://www.microsoft.com/zh-tw/bi/PowerPivot.aspx
  [1]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [HDFS]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [MapReduce]: http://mapreduce.org/
