<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in Hadoop cluster versions of HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight 所提供 Hadoop 叢集版本的新功能

## HDInsight 版本

HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。每一個版本選擇都會佈建特定版本的 Hortonworks Data Platform (HDP) 散發，以及該散發內包含的一組元件。下表列舉了與每個 HDInsight 叢集版本相關聯的元件版本。請注意，[Azure HDInsight][Azure HDInsight] 目前所使用的預設叢集版本為採用 HDP 2.1 的 3.1 版。

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">元件</th>
<th align="left">HDInsight 3.1 版 (預設)</th>
<th align="left">HDInsight 3.0 版</th>
<th align="left">HDInsight 2.1 版</th>
<th align="left">HDInsight 1.6 版</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Hortonworks Data Platform (HDP)</td>
<td align="left">2.1</td>
<td align="left">2.0</td>
<td align="left">1.3</td>
<td align="left">1.1</td>
</tr>
<tr class="even">
<td align="left">Apache Hadoop &amp; YARN</td>
<td align="left">2.4.0</td>
<td align="left">2.2.0</td>
<td align="left">1.2.0</td>
<td align="left">1.0.3</td>
</tr>
<tr class="odd">
<td align="left">Tez</td>
<td align="left">0.4.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Apache Pig</td>
<td align="left">0.12.1</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.3</td>
</tr>
<tr class="odd">
<td align="left">Apache Hive &amp; HCatalog</td>
<td align="left">0.13.0</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.0</td>
</tr>
<tr class="even">
<td align="left">HBase</td>
<td align="left">0.98.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Apache Sqoop</td>
<td align="left">1.4.4</td>
<td align="left">1.4.4</td>
<td align="left">1.4.3</td>
<td align="left">1.4.2</td>
</tr>
<tr class="even">
<td align="left">Apache Oozie</td>
<td align="left">4.0.0</td>
<td align="left">4.0.0</td>
<td align="left">3.3.2</td>
<td align="left">3.2.0</td>
</tr>
<tr class="odd">
<td align="left">Apache HCatalog</td>
<td align="left">與 Hive 合併</td>
<td align="left">與 Hive 合併</td>
<td align="left">與 Hive 合併</td>
<td align="left">0.4.1</td>
</tr>
<tr class="even">
<td align="left">Apache Templeton</td>
<td align="left">與 Hive 合併</td>
<td align="left">與 Hive 合併</td>
<td align="left">與 Hive 合併</td>
<td align="left">0.1.4</td>
</tr>
<tr class="odd">
<td align="left">Ambari</td>
<td align="left">1.5.1</td>
<td align="left">1.4.1</td>
<td align="left">API 1.0 版</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Zookeeper</td>
<td align="left">3.4.5</td>
<td align="left">3.4.5</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Mahout</td>
<td align="left">0.9.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

**取得目前的元件版本資訊**

在 HDInsight 的未來更新中，可能會變更 HDInsight 叢集版本相關的元件版本。若要判斷可用的元件和驗證叢集所使用的版本，有一個辦法就是使用 Ambari REST API。**GetComponentInformation** 命令可用來擷取服務元件的相關資訊。如需詳細資訊，請參閱＜[Ambari 文件][Ambari 文件]＞(英文)。另一種取得此資訊的作法就是使用遠端桌面登入叢集，然後直接檢查 "C:\\apps\\dist" 目錄的內容。

**版本資訊**

請參閱 [HDInsight 版本資訊][HDInsight 版本資訊]，以取得 HDInsight 最新版本的其他版本資訊。

### 佈建 HDInsight 叢集時選取版本

在透過 HDInsight PowerShell 指令程式或 HDInsight .NET SDK 建立叢集時，您可以使用 "Version" 參數來選擇 HDInsight Hadoop 叢集版本。

如果您使用 [快速建立] 選項，依預設您會取得可建立 Hadoop 叢集的 HDInsight 3.1 版。如果您在 Azure 入口網站中使用 [自訂建立] 選項，則可以從 [叢集詳細資料] 頁面的 [HDInsight 版本] 下拉式清單中，選擇您將部署的叢集版本。

## 功能要點

依預設，Azure HDInsight 現可支援搭配 HDInsight 叢集 3.1 版 (採用 Hortonworks Data Platform 2.1) 的 Hadoop 2.4。它充分利用此平台向客戶提供了許多各種好處。其中最明顯的包括：

-   **HBase**：HBase 是一種低延遲的 NoSQL 資料庫，可用於巨量資料的線上交易式處理。HBase 會以受管理叢集的形式提供，並整合到 Azure 環境中。叢集依設定會將資料直接儲存在 Azure Blob 儲存體中，而在效能/成本的選擇中提供低延遲性與高度彈性。這讓客戶得以建置可使用大型資料集的互動式網站、建置可從數百萬個端點儲存感應器和遙測資料的服務，以及透過 Hadoop 工作分析這項資料。

-   **叢集儀表板**：部署到 HDInsight 叢集的新 Web 應用程式。您可透過此功能來執行 Hive 查詢、檢查工作記錄，以及瀏覽 Azure Blob 儲存體。用來存取 Web 應用程式的 URL 為 \<*ClusterName*\>.azurehdinsight.net。

-   **Microsoft Avro Library**：此程式庫可在 Microsoft.NET 環境中實作 Apache Avro 資料序列化系統。Apache Avro 提供一個可用於序列化的壓縮二進位資料交換格式。它會使用 JSON 來定義不限語言的結構描述，以負責提供語言互通性。以某個語言序列化的資料可以使用另一個語言讀取。目前支援 C、C++、C#、Java、PHP、Python 和 Ruby。Azure HDInsight 廣泛採用 Apache Avro 序列化格式，以呈現 Hadoop MapReduce 工作中的複雜資料結構。

-   **YARN**：這是一套新的、通用、分散式的應用程式管理架構，取代標準的 Apache Hadoop MapReduce 架構來處理 Hadoop 叢集的資料。實際上是當做 Hadoop 作業系統，可從批次處理的單一用途資料平台，將 Hadoop 移轉至多重用途平台上，以進行批次、互動式、線上和串流處理。這個新的管理架構可根據容量保證、公平性和服務等級協定等準則，提高延展性和叢集使用率。

-   **Tez (僅限 HDInsight 3.1)**：這是一套通用且可自訂的架構，可在 Hadoop 的大小規模工作負載中建立簡化的資料處理工作。它可讓您執行單一工作的複雜定向非循環圖 (DAG) 工作，以便 Apache Hadoop 生態系統 (例如 Apache Hive 和 Apache Pig) 中的專案可滿足人類互動回應時間和極高輸送量 (以 PB 為單位) 的需求。請注意，Hive 0.13 允許在 Tez (而不是 MapReduce) 上執行 Hive 查詢。

-   **高可用性**：已在 HDInsight 所部署的 Hadoop 叢集中新增第二個前端節點，以提升服務的可用性。Hadoop 叢集的標準實作通常包含單一前端節點。HDInsight 會透過新增次要前端節點，來將這個單一失敗點移除。除非客戶使用超大型前端節點 (而不是預設的大型節點) 來佈建叢集，否則新的 HA 叢集組態開關並不會變更叢集價格。

-   **Hive 效能**：採用**最佳化資料列單欄式 (Optimized Row Columnar, ORC)** 格式，大幅改善 Hive 查詢回應時間 (最快達到 40x) 和資料壓縮 (最高達到 80%)。

-   **Pig、Sqoop、Qozie、Ambari**：HDinsight 叢集 3.0 版 (HDP 2.0/Hadoop 2.2) 的元件版本升級，與 HDinsight 叢集 2.1 版 (HDP 1.3/Hadoop 1.2) 的地位同等。詳情請參閱下列版本表格。

-   **Mahout**：HDInsight 3.1 Hadoop 叢集會預先安裝這個可調整的機器學習演算法程式庫。以便您在無需任何其他叢集組態需求的情況下執行 Mahout 工作。

## 支援的版本

下表列出目前可用的 HDInsight 版本、它們使用的相對應 Hortonworks Data Platform (HDP) 版本及其發行日期。另外也會提供其支援到期日和淘汰日期 (已知道的話)。請注意：

-   依預設，系統會為 HDInsight 2.1、3.0 和 3.1 叢集部署搭配兩個前端節點的高可用性叢集。HDInsight 1.6 叢集並不適用。
-   在特定版本的支援到期後，您可能無法透過 Azure 管理入口網站取得。下表指出可在 Azure 管理入口網站上取得的版本。您可透過 PowerShell [New-AzureHDInsightCluster][New-AzureHDInsightCluster] 命令中的 `Version` 參數和 .NET SDK 持續取得叢集版本，直到其淘汰日期為止。

<table border="1">
<tr>
<th>
HDInsight 版本

</th>
<th>
HDP 版本</a>

<th>
高可用性

</th>
</th>
<th>
發行日期

</th>
<th>
可在 Azure 管理入口網站上取得

</th>
<th>
支援到期日

</th>
<th>
淘汰日期

</th>
</tr>
<tr>
<td>
HDI 3.1

</td>
<td>
HDP 2.1

</td>
<td>
是

</td>
<td>
</td>
<td>
是

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3.0

</td>
<td>
HDP 2.0

</td>
<td>
是

</td>
<td>
02/11/2014

</td>
<td>
是

</td>
<td>
09/17/2014

</td>
<td>
06/30/2015

</td>
</tr>
<tr>
<td>
HDI 2.1

</td>
<td>
HDP 1.3

</td>
<td>
是

</td>
<td>
10/28/2013

</td>
<td>
否

</td>
<td>
05/12/2014

</td>
<td>
05/31/2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
否

</td>
<td>
10/28/2013

</td>
<td>
否

</td>
<td>
04/26/2014

</td>
<td>
05/31/2015

</td>
</tr>
</table>
**非預設叢集的部署**

Azure 入口網站、HDInsight SDK 和 Azure PowerShell 支援在 Hadoop 2.4 上建立 HDInsight 3.1 叢集。請注意，依預設，系統會在 Hadoop 2.4 上建立 HDInsight 3.1 叢集，因此如果必須建立其他 HDInsight 叢集版本，使用者必須使用入口網站中的 [自訂建立] 選項進行指定。

### HDInsight 叢集版本的服務等級協定 (SLA)

SLA 是根據「支援期間」來定義。「支援期間」是指 Microsoft 客戶支援中心支援 HDInsight 叢集版本的一段時間。如果 HDInsight 叢集版本的 [支援到期日] 超過目前日期，則表示該叢集不在「支援期間」。上表中可找到支援的 HDInsight 叢集版本清單。特定 HDInsight X (在較新的 X+1 版本推出後) 版本的支援到期日計算方式會以下列較晚的時間為準：

-   公式 1：將 HDInsight 叢集 X 版發行日期加上 180 天
-   公式 2：將 HDInsight 叢集 X+1 版 (X 之後的後續版本) 出現在 Azure 管理入口網站中的日期加上 90 天。

[淘汰日期] 是指在此日期之後便無法在 HDInsight 上建立叢集版本。

> [WACOM.NOTE] HDInsight 2.1 和 3.0 叢集可在使用 Windows Server 2012 R2 64 位元版本並支援 .NET Framework 4.0、4.5 和 4.5.1 的 Azure 客體 OS [Family 4][Family 4] 上執行。

## 與 HDInsight 版本相關聯的 Hortonworks 版本資訊

-   HDInsight 叢集 3.1 版採用以 [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1] 為基礎的 Hadoop 發佈。這是使用 Azure HDInsight 入口網站時會建立的預設 Hadoop 叢集。

-   HDInsight 叢集 3.0 版採用以 [Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0] 為基礎的 Hadoop 散發。

-   HDInsight 叢集 2.1 版採用以 [Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3] 為基礎的 Hadoop 散發。

-   HDInsight 叢集 1.6 版採用以 [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1] 為基礎的 Hadoop 散發。

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [Ambari 文件]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [HDInsight 版本資訊]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/zh-tw/library/dn593744.aspx
  [Family 4]: http://msdn.microsoft.com/zh-tw/library/azure/ee924680.aspx#explanation
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
