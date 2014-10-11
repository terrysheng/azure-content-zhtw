<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitor Hadoop clusters in HDInsight using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# 使用 Ambari API 監視 HDInsight 上的 Hadoop 叢集

了解如何使用 Ambari API 監視 HDInsight 叢集 2.1 版。

**預估完成時間:** 15 分鐘

## 本文內容

-   [什麼是 Ambari？][]
-   [必要條件][]
-   [快速啟動][]
-   [Ambari 監視 API][]
-   [後續步驟][]

## <span id="whatisambari"></span></a>什麼是 Ambari？

[Apache Ambari][] 可用來佈建、管理及監視 Apache Hadoop 叢集。其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。如需這些 API 的詳細資訊，請參閱 [Ambari API 參考資料][]。

HDInsight 目前僅支援 Ambari 監視功能。HDInsight 叢集 2.1 和 3.0 版可支援 Ambari API v1.0。本文僅說明如何在 HDInsight 叢集 2.1 版上執行 Ambari API。

## <span id="prerequisites"></span></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

-   已安裝並設定 Azure PowerShell 的**工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][]。

    [Curl][] 是選用項目。您可以從[這裡][]加以安裝。

    > [WACOM.NOTE] 在 Windows 上使用 curl 命令時，對選項值請使用雙引號，而不要使用單引號。

-   **Azure HDInsight 叢集**。如需叢集佈建的指示，請參閱[開始使用 HDInsight][] 或[佈建 HDInsight 叢集][]。進行教學課程時，您將需要下列資料：

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">叢集屬性</th>
    <th align="left">PowerShell 變數名稱</th>
    <th align="left">值</th>
    <th align="left">說明</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight 叢集名稱</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">您的 HDInsight 叢集名稱。</td>
    </tr>
    <tr class="even">
    <td align="left">叢集使用者名稱</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">佈建時指定的叢集使用者名稱。</td>
    </tr>
    <tr class="odd">
    <td align="left">叢集密碼</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">叢集使用者密碼。</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] 將值填入資料表中。這將有助於本教學課程的執行。

## <span id="jumpstart"></span></a>快速啟動

您可以透過數種方式使用 Ambari 監視 HDInsight 叢集。

**使用 Azure PowerShell**

以下是用來取得 MapReduce Jobtracker 資訊的 PowerShell 指令碼：

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'mapred.JobTracker'

輸出如下：

![Jobtracker Output][]

**使用 curl**

以下是使用 Curl 取得叢集資訊的範例：

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

輸出如下：

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"workernode0"}}]}

## <span id="monitor"></span></a>Ambari 監視 API

下表列出部分最常用的 Ambari 監視 API 呼叫。如需 API 的詳細資訊，請參閱 [Ambari API 參考資料][]。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">監視 API 呼叫</th>
<th align="left">URI</th>
<th align="left">說明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">取得叢集</td>
<td align="left">/api/v1/clusters</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">取得叢集資訊。</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td>
<td align="left">叢集、服務、主機</td>
</tr>
<tr class="odd">
<td align="left">取得服務</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td>
<td align="left">服務包括：hdfs、mapreduce</td>
</tr>
<tr class="even">
<td align="left">取得服務資訊</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">取得服務元件</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td>
<td align="left">HDFS：namenode、datanode<br />MapReduce：jobtracker、tasktracker</td>
</tr>
<tr class="even">
<td align="left">取得元件資訊</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td>
<td align="left">ServiceComponentInfo、主機元件、度量</td>
</tr>
<tr class="odd">
<td align="left">取得主機</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td>
<td align="left">headnode0、workernode0</td>
</tr>
<tr class="even">
<td align="left">取得主機資訊</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">取得主機元件</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components</td>
<td align="left">namenode、resourcemanager</td>
</tr>
<tr class="even">
<td align="left">取得主機元件資訊</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;</td>
<td align="left">HostRoles、元件、主機、度量</td>
</tr>
<tr class="odd">
<td align="left">取得組態</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations</td>
<td align="left">組態類型：core-site、hdfs-site、mapred-site、hive-site</td>
</tr>
<tr class="even">
<td align="left">取得組態資訊</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt;</td>
<td align="left">組態類型：core-site、hdfs-site、mapred-site、hive-site</td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a>後續步驟

現在，您已了解如何使用 Ambari 監視 API。若要深入了解，請參閱：

-   [使用管理入口網站管理 HDInsight 叢集][]
-   [使用 Azure PowerShell 管理 HDInsight 叢集][]
-   [使用命令列介面管理 HDInsight 叢集][]
-   [HDInsight 文件][]
-   [開始使用 HDInsight][]

  [什麼是 Ambari？]: #whatisambari
  [必要條件]: #prerequisites
  [快速啟動]: #jumpstart
  [Ambari 監視 API]: #monitor
  [後續步驟]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Ambari API 參考資料]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [執行 Windows PowerShell 指令碼]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [這裡]: http://curl.haxx.se/download.html
  [開始使用 HDInsight]: ../hdinsight-get-started/
  [佈建 HDInsight 叢集]: ../hdinsight-provision-clusters/
  [Jobtracker Output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [使用管理入口網站管理 HDInsight 叢集]: ../hdinsight-administer-use-management-portal/
  [使用 Azure PowerShell 管理 HDInsight 叢集]: ../hdinsight-administer-use-powershell/
  [使用命令列介面管理 HDInsight 叢集]: ../hdinsight-administer-use-command-line/
  [HDInsight 文件]: /en-us/documentation/services/hdinsight/
