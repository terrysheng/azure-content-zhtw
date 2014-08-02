<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in the cluster versions provided by HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

HDInsight 所提供叢集版本的新功能
================================

Azure HDInsight 現在支援 Hadoop 2.2 及 HDinsight 叢集 3.0 版，且充分利用此平台來提供許多各種好處給客戶。其中最明顯的包括：

-   Hive：採用最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式，大幅改善 Hive 查詢回應時間 (最快達到 40x) 和資料壓縮 (最高達到 80%)。

-   YARN：這是一套新的、通用、分散式的應用程式管理架構，取代標準的 Apache Hadoop MapReduce 架構來處理 Hadoop 叢集的資料。實際上是當做 Hadoop 作業系統，可從批次處理的單一用途資料平台，將 Hadoop 移轉至多重用途平台上，以進行批次、互動式、線上和串流處理。這個新的管理架構可根據容量保證、公平性和服務等級協定等準則，提高延展性和叢集使用率。

-   Pig、Sqoop、Qozie、Ambari：HDinsight 叢集 3.0 版 (HDP 2.0) 的元件版本升級，與 HDinsight 叢集 2.1 版 (HDP 1.3) 的地位同等。詳情請參閱下列版本表格。請注意，不含 Hbase、Mahout、Flume。

**部署** 支援以 Azure 入口網站、HDinsight SDK 和 Azure PowerShell 在 Hadoop 2.2 上建立 HDInsight 3.0 叢集。

**全域可用性** 隨著 Azure HDInsight on Hadoop 2.2 推出，Microsoft 在所有主要 Azure 地理區中都提供 HDInsight，只有大中華地區除外。尤其，西歐和東南亞資料中心已上線運作。這可讓客戶在附近的資料中心或法規要求相似的區域中尋找叢集。

**重大變更** HDInsight 3.0 叢集僅支援 "wasb://" 語法。HDInsight 2.1 和 1.6 支援舊的 "asv://" 語法，但在 HDInsight 3.0 叢集中已不支援，未來的版本中也不再支援。這表示任何提交至 HDInsight 3.0 叢集且明確使用 “asv://” 語法的工作都會失敗。請改用 wasb:// 語法。另外，如果工作提交至任何以現有 metastore 建立的 HDInsight 3.0 叢集，且 metastore 中使用 asv:// 語法來明確參考資源，則也會失敗。必須使用 wasb:// 來定址資源，以重新建立這些 metastore。

HDInsight 版本
--------------

HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。每一個版本選擇都會佈建特定版本的 HortonWorks Data Platform (HDP) 散發，以及該散發內包含的一組元件。

### 叢集 3.0 版

Azure HDInsight 現在支援 Hadoop 2.2。它以 Hortonworks Data Platform 2.0 版為基礎，提供下表列舉的元件版本給 Hadoop 服務：

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">元件</th><th data-morhtml="true">版本</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">2.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.12.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.12</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">4.0.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">與 Hive 合併</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">與 Hive 合併</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API 1.0 版</td></tr>
</table>

### 叢集 2.1 版

[Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) 使用的預設叢集版本是 2.1。它以 Hortonworks Data Platform 1.3.0 版為基礎，提供下表列舉的元件版本給 Hadoop 服務：

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">元件</th><th data-morhtml="true">版本</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.11.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.11</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">與 Hive 合併</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">與 Hive 合併</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API 1.0 版</td></tr>
</table>

### 叢集 1.6 版

也有 [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) 叢集 1.6 版可用。它以 Hortonworks Data Platform 1.1.0 版為基礎，提供下表列舉的元件版本給 Hadoop 服務：

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">元件</th><th data-morhtml="true">版本</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.0.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.9.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.9.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">0.4.1</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">0.1.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">SQL Server JDBC 驅動程式</td><td data-morhtml="true">3.0</td></tr>
</table>

### 佈建 HDInsight 叢集時選取版本

在透過 HDInsight PowerShell 指令程式或 HDInsight .NET SDK 建立叢集時，您可以使用 "Version" 參數來選擇 HDInsight Hadoop 叢集版本。

如果您使用 **[快速建立]** 選項，依預設會取得 HDInsight Hadoop 2.1 版叢集。如果您在 Azure 入口網站中使用 **[自訂建立]** 選項，則可以從 **[叢集詳細資料]** 頁面的 **[HDInsight 版本]** 下拉式清單中，選擇您將部署的叢集版本。只有在 **[自訂建立]** 精靈中，才會出現 HDInsight Hadoop 3.0 版叢集選項。

![HDI.Versioning.VersionScreen](./media/hdinsight-component-versioning/hdi-versioning-version-screen.png)

支援的版本
----------

下表列出目前可用的 HDInsight 版本、它們使用的相對應 Hortonworks Data Platform (HDP) 版本及其發行日期。另外也會提供其淘汰日期 (已知道的話)。

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">HDInsight 版本</th><th data-morhtml="true">HDP 版本</th><th data-morhtml="true">發行日期</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 3.0</td><td data-morhtml="true">HDP 2.0</td><td data-morhtml="true">02/11/2014</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 2.1</td><td data-morhtml="true">HDP 1.3</td><td data-morhtml="true">10/28/2013</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 1.6</td><td data-morhtml="true">HDP 1.1</td><td data-morhtml="true">10/28/2013</td></tr>
</table>
### HDInsight 叢集版本的服務等級協定 (SLA)

SLA 是根據「支援期間」來定義。「支援期間」是指 Microsoft 客戶支援中心支援 HDInsight 叢集版本的一段時間。如果 HDInsight 叢集的版本「支援到期日」超過目前日期，就表示叢集超出「支援期間」。上表中可找到支援的 HDInsight 叢集版本清單。特定 HDInsight 版本 (以 X 版表示) 的「支援到期日」計算以時間最晚者為準：

-   公式 1：將 HDInsight 叢集 X 版發行日期加上 180 天
-   公式 2：將 HDInsight 叢集 X+1 版 (X 之後的後續版本) 出現在 Azure 管理入口網站中的日期加上 90 天。

> [WACOM.NOTE] HDInsight 2.1 和 3.0 都使用 Windows 2008 R2 SP1 64 位元版本搭配 .NET Framework 4.0。

**關於版本的其他注意事項和資訊**

-   SQL Server JDBC 驅動程式僅供 HDInsight 內部使用，不適用於外部作業。如果您想要使用 ODBC 連線至 HDInsight，請使用 Microsoft Hive ODBC 驅動程式。如需有關使用 Hive ODBC 的詳細資訊，請參閱＜[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight](/en-us/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver)＞。

-   HDInsight 叢集 3.0 版採用以 [Hortonworks Data Platform 2.0](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html) 為基礎的 Hadoop 散發。

-   HDInsight 叢集 2.1 版採用以 [Hortonworks Data Platform 1.3](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html) 為基礎的 Hadoop 散發。這是使用 Azure HDInsight 入口網站時所建立的預設 Hadoop 叢集。

-   HDInsight 叢集 1.6 版採用以 [Hortonworks Data Platform 1.1](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html) 為基礎的 Hadoop 散發。

-   在 HDInsight 的未來更新中，可能會變更 HDInsight 叢集版本相關的元件版本。若要判斷可用的元件和驗證叢集所使用的版本，有一個辦法就是使用 Ambari REST API。GetComponentInformation 命令可用來擷取服務元件的相關資訊。如需詳細資訊，請參閱＜[Ambari 文件](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)＞(英文)。另一種取得此資訊的作法就是使用遠端桌面登入叢集，然後直接檢查 "C:\\apps\\dist" 目錄的內容。


