<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Microsoft HDInsight 版本資訊

## 2014/8/21 版本的相關資訊

-   我們新增下列新的 WebHCat 組態 (HIVE-7155)，將 Templeton 控制器工作的預設記憶體限制設為 1GB (先前的預設值是 512MB)：

    -   templeton.mapper.memory.mb (=1024)
    -   此變更解決了因為記憶體限制太低而導致某些 Hive 查詢發生下列錯誤：「容器執行超出實體記憶體限制」。
    -   若要回復為舊的預設值，您可以在建立叢集時使用下列命令，透過 PowerShell SDK 將此組態值設為 512：

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   Zookeeper 角色的主機名稱已變更為 zookeeper。這會影響叢集內的名稱解析，但不影響外部 REST API。如果您有元件使用 zookeepernode 主機名稱，則必須更新為改用新的名稱。三個 zookeeper 節點的新名稱如下：

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   已更新 HBase 版本支援矩陣。只有 HDInsight 3.1 版 (HBase 0.98 版) 才支援生產 HBase 工作負載。將不再支援 3.0 預覽版。在轉移期間，客戶仍然可以建立 3.0 版的叢集。

## 2014/7/28 版本的相關資訊

-   **HDInsight 在新的區域上市**：在此版本中，我們將 HDInsight 的地理位置據點擴展到三個新的區域。HDInsight 客戶現在可以在這三個區域建立叢集。

    -   東亞、
    -   美國中北部及
    -   美國中南部。
-   在此版本中，HDInsight 1.6 版 (HDP1.1、Hadoop 1.0.3) 和 HDInsight 2.1 版 (HDP1.3、Hadoop 1.2) 正從 Azure 管理入口網站中移除。您可以繼續使用 HDInsight PowerShell Cmdlet ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) 或 [HDInsight SDK][HDInsight SDK] 來建立這些版本的 Hadoop 叢集。如需詳細資訊，請參閱 [HDInsight 所提供叢集版本的新功能][HDInsight 所提供叢集版本的新功能]頁面。
-   此版本中的 Hortonworks Data Platform (HDP) 變更：

| HDP               | 變更                                                         |
|-------------------|--------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | 沒有變更                                                     |
| HDP 2.0 / HDI 3.0 | 沒有變更                                                     |
| HDP 2.1 / HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## 2014/6/24 版本的相關資訊

此版本包含 HDInsight 服務的幾項新的增強功能：

-   **HDP 2.1 可用性**：HDInsight 3.1 (含 HDP 2.1) 現在已正式發行，也是新叢集的預設版本。
-   **HBase – Azure 管理入口網站改善**：我們提供預覽版的 HBase 叢集。您現在從入口網站中，按三下滑鼠就可以建立 HBase 叢集。

![][]

HBase 可讓您在 HDInsight 上建置各種即時工作負載，包括處理大型資料集的互動式網站，甚至是將數百萬個端點的感應器和遙測資料儲存起來的服務，都沒問題。下一步是使用 Hadoop 工作來分析這些工作負載中的資料，透過既有的體驗，像是 PowerShell 和 Hive 叢集儀表板，這立即就能在 HDInsight 中進行。

### Apache Mahout 現在預先安裝在 HDInsight 3.1

[Mahout][Mahout] 會預先安裝在 HDInsight 3.1 Hadoop 叢集上。以便您在無需任何其他叢集組態需求的情況下執行 Mahout 工作。例如，您可以使用遠端桌面通訊協定 (RDP) 從遠端進入 Hadoop 叢集，不需要額外的步驟就能執行 Hello world Mahout 命令：

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

如需此程序更完整的說明，請參閱 Apache Mahout 網站上關於 [Breiman 範例][Breiman 範例] (英文) 的文件。

### Hive 查詢可以在 HDinsight 3.1 中使用 Tez

Hive 0.13 現在可以在 HDInsight 3.1 中使用，且能夠使用 Tez 來執行查詢，妥善運用可大幅提升效能。
依預設不會對 Hive 查詢啟用 Tez。若要使用，您必須自行啟用。您可以執行下列程式碼片段來啟用 Tez：

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks 在標準效能評比中，已發表一份關於 Hive 查詢搭配 Tez 而提升效能的詳細數據。如需詳細資訊，請參閱 [Apache Hive 13 for Enterprise Hadoop 效能評比][Apache Hive 13 for Enterprise Hadoop 效能評比] (英文)。

如需有關搭配 Tez 使用 Hive 的詳細資訊，請參閱 [Hive on Tez Wiki 頁面][Hive on Tez Wiki 頁面] (英文)。

### 全球可用性

隨著 Azure HDInsight on Hadoop 2.2 推出，Microsoft 已在所有主要的 Azure 地理區提供 HDInsight。尤其，西歐和東南亞資料中心已上線運作。這可讓客戶在附近的資料中心或法規要求相似的區域中尋找叢集。

### 重大變更

**前置詞語法**：
HDInsight 3.0 和 3.1 叢集僅支援 "wasb://" 語法。HDInsight 2.1 和 1.6 叢集支援舊的 "asv://" 語法，但在 HDInsight 3.0 叢集或更新版本中已不支援。這表示任何提交至 HDInsight 叢集 3.0 或 3.1 且明確使用 “asv://” 語法的工作都會失敗。請改用 wasb:// 語法。另外，如果工作提交至任何以現有 metastore 建立的 HDInsight 3.0 或 3.1 叢集，且 metastore 中使用 asv:// 語法來明確參考資源，則也會失敗。必須使用 wasb:// 來定址資源，以重新建立這些 metastore。

**連接埠**：HDInsight 服務使用的連接埠已變更。以前使用的連接埠號碼都在 Windows OS 暫時連接埠範圍內。對於短期的網際網路通訊協定通訊，自動會從預設定義的暫時範圍中配置連接埠。現在，新的一組允許的 Hortonworks Data Platform (HDP) 服務連接埠號碼不在此範圍內，可避免與前端節點上執行的服務所使用的連接埠發生衝突。新的連接埠號碼應該不會引起任何重大變更。現在使用的號碼如下：

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
名稱

</th>
<th>
值

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 和 3.1 (HDP 2.0 和 2.1)**

<table border="1">

<tr>
<th>
名稱

</th>
<th>
值

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### 相依項目

HDInsight 3.x (HDP2.x) 增加下列相依項目：

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

HDInsight 3.x (HDP2.x) 中已不存在下列相依項目：

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### 版本變更

HDInsight 2.x (HDP1.x) 與 HDInsight 3.x (HDP2.x) 之間有下列版本變更：

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus:['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper:['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp:['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### 驅動程式

SQL Server JDBC 驅動程式僅供 HDInsight 內部使用，不適用於外部作業。如果您想要使用 ODBC 連線至 HDInsight，請使用 Microsoft Hive ODBC 驅動程式。如需有關使用 Hive ODBC 的詳細資訊，請參閱 [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][connect-excel-with-hive-ODBC]。

### 錯誤修正

在此版本中，我們使用幾個錯誤修正來更新下列 HDInsight (Hortonworks Data Platform - HDP) 版本：

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Hortonworks 版本資訊

下列位置提供 HDInsight 叢集版本使用的 HDP 的版本資訊。

-   HDInsight 叢集 3.1 版採用以 [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1] 為基礎的 Hadoop 發佈 (這是使用 Azure HDInsight 入口網站時會建立的預設 Hadoop 叢集)。

-   HDInsight 叢集 3.0 版採用以 [Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0] 為基礎的 Hadoop 散發。

-   HDInsight 叢集 2.1 版採用以 [Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3] 為基礎的 Hadoop 散發。

-   HDInsight 叢集 1.6 版採用以 [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1] 為基礎的 Hadoop 散發。

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/zh-tw/library/dn593744.aspx
  [HDInsight SDK]: http://msdn.microsoft.com/zh-tw/library/azure/dn469975.aspx
  [HDInsight 所提供叢集版本的新功能]: http://azure.microsoft.com/zh-tw/documentation/articles/hdinsight-component-versioning/
  []: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Breiman 範例]: https://mahout.apache.org/users/classification/breiman-example.html
  [Apache Hive 13 for Enterprise Hadoop 效能評比]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [Hive on Tez Wiki 頁面]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
