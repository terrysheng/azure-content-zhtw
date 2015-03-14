<properties 
	pageTitle="使用 Ambari API 監視 HDInsight 上的 Hadoop 叢集 | Azure" 
	description="使用 Apache Ambari API 來佈建、管理和監視 Hadoop 叢集。Ambari 的直覺式操作工具和 API 可消除 Hadoop 的複雜性。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	editor="cgronlun" 
	manager="paulettm"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>

# 使用 Ambari API 監視 HDInsight 上的 Hadoop 叢集
 
了解如何使用 Ambari API 監視 HDInsight 叢集 3.1 和 2.1 版。

##本文內容

- [什麼是 Ambari？](#whatisambari)
- [必要條件](#prerequisites)
- [快速啟動](#jumpstart)
- [Ambari 監視 API](#monitor)
- [後續步驟](#nextsteps)


## <a id="whatisambari"></a> 什麼是 Ambari？

[Apache Ambari][ambari-home] 可用來佈建、管理及監視 Apache Hadoop 叢集。其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。如需這些 API 的詳細資訊，請參閱 [Ambari API 參考資料][ambari-api-reference]。


HDInsight 目前僅支援 Ambari 監視功能。HDInsight 叢集 2.1 和 3.0 版可支援 Ambari API v1.0。本文涵蓋在 HDInsight 叢集 3.1 和 2.1 版上存取 Ambari API。兩者的主要差別在於某些元件已隨著新功能引進而變更 (例如工作歷程伺服器)。


##<a id="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- 已安裝並設定 Azure PowerShell 的**工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][powershell-script]。

	[Curl][curl] 是選用項目。您可以從[這裡][curl-download]加以安裝。

	>[AZURE.NOTE] 在 Windows 上使用 curl 命令時，對選項值請使用雙引號，而不要使用單引號。

- **Azure HDInsight 叢集**。如需叢集佈建的指示，請參閱[開始使用 HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。進行教學課程時，您將需要下列資料：

	<table border="1">
	<tr><th>叢集屬性</th><th>PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
	<tr><td>HDInsight 叢集名稱</td><td>$clusterName</td><td></td><td>您的 HDInsight 叢集名稱。</td></tr>
	<tr><td>叢集使用者名稱</td><td>$clusterUsername</td><td></td><td>佈建時指定的叢集使用者名稱。</td></tr>
	<tr><td>叢集密碼</td><td>$clusterPassword</td><td></td><td>叢集使用者密碼。</td></tr>
	</table>

	> [AZURE.NOTE] 將值填入資料表中。  這將有助於本教學課程的執行。



##<a id="jumpstart"></a>快速啟動

您可以透過數種方式使用 Ambari 監視 HDInsight 叢集。

**使用 Azure PowerShell**

以下是用來取得 MapReduce Jobtracker 資訊的 PowerShell 指令碼 *on a 3.1 cluster.*  此處的主要差別在於我們現在要從 YARN 服務 (而非 Map Reduce) 提取這些詳細資料。

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'yarn.queueMetrics'

以下是用來取得 MapReduce Jobtracker 資訊的 PowerShell 指令碼 *on a 2.1 cluster*：

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

![Jobtracker Output][img-jobtracker-output]

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
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

10/8/2014 版本的相關資訊：
使用 Ambari 端點 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" 時， *host_name* 欄位現在會傳回節點的完整網域名稱 (FQDN)，而不是主機名稱。在 2014/10/8 版本之前，此範例只會傳回 "**headnode0**"。在 2014/10/8 之後，您會取得 FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**"，如以上範例所示。需要此變更，才能在一個虛擬網路 (VNET) 中部署多種叢集類型，例如 HBase 和 Hadoop。例如，使用 HBase 做為 Hadoop 的後端平台時就是這種情形。

##<a id="monitor"></a>Ambari 監視 API

下表列出部分最常用的 Ambari 監視 API 呼叫。如需 API 的詳細資訊，請參閱 [Ambari API 參考資料][ambari-api-reference]。

<table border="1">
<tr><th>監視 API 呼叫</th><th>URI</th><th>說明</th></tr>
<tr><td>取得叢集</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>取得叢集資訊。</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</tt></td><td>叢集、服務、主機</td></tr>
<tr><td>取得服務</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</tt></td><td>服務包括：hdfs、mapreduce</td></tr>
<tr><td>取得服務資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</tt></td><td></td></tr>
<tr><td>取得服務元件</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</tt></td><td>HDFS：namenode、datanode<br/>MapReduce：jobtracker；tasktracker</td></tr>
<tr><td>取得元件資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</tt></td><td>ServiceComponentInfo、主機元件、度量</td></tr>
<tr><td>取得主機</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</tt></td><td>headnode0、workernode0</td></tr>
<tr><td>取得主機資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td></td></tr>
<tr><td>取得主機元件</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</tt></td><td>namenode、resourcemanager</td></tr>
<tr><td>取得主機元件資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</tt></td><td>HostRoles、元件、主機、度量</td></tr>
<tr><td>取得組態</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</tt></td><td>組態類型：core-site、hdfs-site、mapred-site、hive-site</td></tr>
<tr><td>取得組態資訊</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&tag=&lt;VersionName&gt; 
</tt></td><td>組態類型：core-site、hdfs-site、mapred-site、hive-site</td></tr>
</table>


##<a id="nextsteps"></a>後續步驟 

現在，您已了解如何使用 Ambari 監視 API。若要深入了解，請參閱：

- [使用管理入口網站管理 HDInsight 叢集][hdinsight-admin-portal]
- [使用 Azure PowerShell 管理 HDInsight 叢集][hdinsight-admin-powershell]
- [使用命令列介面管理 HDInsight 叢集][hdinsight-admin-cli]
- [HDInsight 文件][hdinsight-documentation]
- [開始使用 HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: ../install-configure-powershell/
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx 

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-documentation]: /zh-tw/documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png

<!--HONumber=42-->
