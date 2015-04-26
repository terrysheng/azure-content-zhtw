<properties 
	pageTitle="HDInsight 版本資訊 | Azure" 
	description="HDInsight 版本資訊。" 
	services="hdinsight" 
	documentationCenter="" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2015" 
	ms.author="bradsev"/>


#Microsoft HDInsight 版本資訊

## HDInsight 2015/1/29 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 	2.1.10.455.1309616	(HDP 1.3.9.0-01351 - 保持不變)
* HDInsight 	3.0.6.455.1309616	(HDP 2.0.9.0-2097 -  保持不變)
* HDInsight 	3.1.2.455.1309616	(HDP 2.1.9.0-2196 -  保持不變)
* SDK			N/A

此版本包含下列更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>受影響的區域 
(例如：服務、OS 元件、SDK、PS、AUX)</p></th>
<th>受影響的叢集類型 (例如 Hadoop、HBase、Storm、全部)</th>
<th>JIRA (如果適用)</th>
</tr>


<tr>

<td> 錯誤修正</td>
<td>我們已進行幾個重要的錯誤修正，可在 Azure 升級期間改善 HDInsight 叢集的可靠性。</td>
<td>服務</td>
<td></td>
<td>N/A</td>
</tr>



</table>
<br>

## HDInsight 2015/1/5 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 	2.1.10.420.1246118	(HDP 1.3.9.0-01351 - 保持不變)
* HDInsight 	3.0.6.420.1246118	(HDP 2.0.9.0-2097 - 保持不變)
* HDInsight 	3.1.2.420.1246118	(HDP 2.1.9.0-2196 - 保持不變)


此版本包含下列更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>


<tr>
<td>Twitter 趨勢分析及以 Mahout 為基礎的電影推薦範例</td>
<td><p>在此版本中，HDInsight 查詢主控台會有兩個額外的範例：</p>

<p><b>Twitter 趨勢分析</b><br>
像 Twitter 之類的網站所提供的公開 API，是分析和了解流行趨勢的一項實用的資料來源。在本教學課程中，了解如何使用 Hive 來取得傳送包含特定單字的最多推文的 Twitter 使用者清單。 </p>

<p><b>Mahout 電影推薦</b><br>
Apache Mahout 是 Apache Hadoop 機器學習庫。Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。在此教學課程中，您將使用推薦引擎，根據朋友看過的電影來產生電影推薦。</p></td>
<td>查詢主控台</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>變更為 Hive 設定 hive.auto.convert.join.noconditionaltask.size 的預設值</td>
<td><p>此大小設定適用於自動轉換的對應聯結。此值代表可轉換成 hashmap 以適合放入記憶體的資料表大小的總和。在先前的版本中，這個值從 10 MB 的預設值增加為 128 MB。不過，新的 128 MB 值因為記憶體不足而導致工作失敗。這個版本會還原回為 10 MB 的預設值。客戶仍然可以選擇在叢集建立期間為其查詢和資料表大小覆寫此值。如需有關這項設定以及如何覆寫它的詳細資訊，請參閱 <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">最佳化自動聯結轉換</a> 在 Hortonworks 文件中。 </p></td>
<td>Hive</td>
<td>Hadoop、Hbase</td>
<td>N/A</td>
</tr>

</table>
<br>

## HDInsight 2014/12/23 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 	2.1.10.420.1246783	(HDP 版本保持不變)
* HDInsight 	3.0.6.420.1246783	(HDP 版本保持不變)
* HDInsight 	3.1.1.420.1246783	(HDP 版本保持不變)

此版本包含下列更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>


<tr>
<td>由於負載過大造成間歇性的叢集建立失敗</td>
<td><p>改良叢集建立期間用於下載 HDP 套件的演算法，可針對因負載過大的失敗提供更穩健的處理。預期在這個方面提供一些改良功能，對於與叢集建立相關的負載增加提供更好的調整。</p></td>
<td>服務</td>
<td>Hadoop、Hbase、Storm</td>
<td>N/A</td>
</tr>



</table>
<br>

## HDInsight 2014/12/18 版本的相關資訊 ##

此版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>

<tr>
<td><a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">叢集自訂 GA</a></td>
<td><p>自訂可讓您自訂Azure HDInsight 叢集，以搭配使用 Apache Hadoop 生態系統的專案。使用這項新功能，您現在可以試驗並部署 Hadoop 專案到 Azure HDInsight。這是透過啟用<b>指令碼動作</b>功能啟用，該功能可以使用自訂指令碼，以任意方式修改 Hadoop 叢集。此自訂可在所有類型的 HDInsight 叢集上使用，包括 Hadoop、HBase 和 Storm。為了示範這項功能的強大功能，我們記錄了程序以安裝受歡迎的 <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-spark-install/" target="_blank">Spark</a>、 <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-r-scripts/" target="_blank">R</a>、 <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-solr-install/" target="_blank">Solr</a>和 <a href = "http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-giraph-install/" target="_blank">Giraph</a> 模組。這個版本也加入讓客戶透過 Azure 管理入口網站指定其自訂指令碼動作的功能、提供如何使用 helper 方法建置自訂指令碼動作的指導方針最佳作法和最佳作法，並提供有關如何測試指令碼動作的指導方針。 </p></td>
<td>功能 GA</td>
<td>全部</td>
<td>N/A</td>
</tr>


</table>
<br>

## HDInsight 2014/12/5 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 	2.1.9.406.1221105	(HDP 1.3.9.0-01351)
* HDInsight 	3.0.5.406.1221105	(HDP 2.0.9.0-2097)
* HDInsight 	3.1.1.406.1221105	(HDP 2.1.9.0-2196)
* HDInsight SDK N/A

此版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>

<tr>
<td>錯誤修正：將大量磁碟分割新增至 Hive DDL 中的資料表時發生間歇性的錯誤。 </td>
<td><p>將大量磁碟分割新增至 Hive 資料表時，如果 Hive metastore 資料庫發生間歇性的連線錯誤，則 Hive DDL 可能會失敗。如果發生此錯誤，可在 Hive 錯誤記錄中看到下列陳述： </p><p>「錯誤 [main]: ql.Driver (SessionState.java:printError(547)) - 失敗：執行錯誤，從 org.apache.hadoop.hive.ql.exec.DDLTask 傳回代碼 1。MetaException(message:java.lang.RuntimeException: commitTransaction 已呼叫，但 openTransactionCalls = 0。這可能表示對 openTransaction/commitTransaction 進行不平衡的呼叫)"</p></td>
<td>Hive</td>
<td>Hadoop、Hbase</td>
<td>HIVE-482 (這是內部 JIRA，因此不可在外部加上引號。在此記錄供參考之用。)</td>
</tr>

<tr>
<td>錯誤修正：HDInsight 查詢偶爾當機  查詢主控台</td>
<td>發生此情況時，可在 WebHCat 啟動器工作的 WebHCat 記錄中看到下列陳述： <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException):無法載入歷程記錄檔 {歷程記錄檔的 wasb url}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (這是內部 JIRA，因此不可在外部加上引號。在此記錄供參考之用。)</td>
</tr>

<tr>
<td>錯誤修正：Hbase 查詢延遲偶爾激增</td>
<td>如果發生這種情況，使用者會發現 Hbase 查詢延遲偶爾會激增 3 秒。 </td>
<td>HDInsight 叢集閘道</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>HDP Jar 檔案名稱變更</td>
<td>若為 HDI 叢集 3.0 版，HDP 安裝的內部 jar 檔案會有一些變更。jetty-6.1.26.jar
 已取代為 jetty-6.1.26.hwx.jar。jetty-util-6.1.26.jar 已取代為 jetty-util-6.1.26.hwx.jar。這些變更適用於 Hadoop、Mahout、WebHCat 和 Oozie 專案。**</td>
<td>Hadoop、Mahout、WebHCat、Oozie</td>
<td>Hadoop、HBase</td>
<td>N/A</td>
</tr>

</table>
<br>


## HDInsight 2014/11/21 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 2.1.9.382.1169709 (從 2014/11/14 後未變更)
* HDInsight 3.0.5.382.1169709 (從 2014/11/14 後未變更)
* HDInsight 3.1.1.382.1169709 (從 2014/11/14 後未變更)
* HDINsight SDK 1.4.0

此版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>

<tr>
<td>存取應用程式記錄</td>
<td>已在您的叢集上利用程式設計列舉應用程式，並下載相關的應用程式或容器特定記錄以協助偵錯有問題的應用程式。</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>在 IHdInsightClient.DeleteCluster 中指定地區名稱的能力 </td>
<td>Azure HDInsight SDK 現在提供在使用 **DeleteCluster** 時指定地區名稱的能力。這有助於解除封鎖在不同的地區擁有 2 個同名資源，而且已無法刪除任一資源的客戶。</td>
<td>SDK</td>
<td>全部</td>
<td>N/A</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>**ClusterDetails** 物件會傳回 **DeploymentID** 欄位來代表叢集的唯一識別碼。這可在使用相同名稱跨叢集嘗試建立時，保證有唯一的名稱。</td>
<td>SDK</td>
<td>全部</td>
<td>N/A</td>
</tr>
</table>
<br>

## HDInsight 2014/11/14 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

此版本包含下列新功能、元件更新和錯誤修正。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>

<tr>
<td>指令碼動作 (預覽)</td>
<td>叢集自訂功能的預覽，可以任意方式使用自訂指令碼來修改 Hadoop 叢集。利用這個新功能，使用者可透過 Apache Hadoop 生態系統試驗並部署專案至 Azure HDInsight 叢集。此自訂功能可在所有類型的 HDInsight 叢集上使用，包括 Hadoop、HBase 和 Storm。</td>
<td>新功能</td>
<td>全部</td>
<td>N/A</td>
</tr>

<tr>
<td>為 Azure 網站與儲存體記錄分析預先建立的工作</td>
<td>HDInsight 查詢主控台有一個開始使用組件庫 (Getting Started Gallery)，其支援處理您的資料或資料範例的解決方案。
<p>處理您資料的解決方案：<br>
我們已經為最常見的部分資料分析案例建立工作，做為您建立您自己的解決方案的起點。您可以執行工作來使用自己的資料，以查看其運作方式。接著在就緒時，使用已學得的知識來建立您在預先建立工作後所製作的解決方案模型。</p>
<p>處理資料範例的解決方案：<br>
逐步執行部分基本案例 (例如分析 Web 記錄和感測器資料) 來了解如何使用 HDInsight。您不只會學習如何使用 HDInsight 來分析此類資料，也會學習如何將其他應用程式和服務連線至此資料。藉由連線至 Microsoft Excel 來提供此強大方案範例，藉此視覺化資料。</p></td>
<td>查詢主控台</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Templeton 中的記憶體遺漏修正</td>
<td>已解決 Templeton 中的記憶體遺漏，該問題會影響已長期執行叢集的客戶，或每秒提交數百次工作要求的客戶。此問題會顯示為 Templeton 5xx 錯誤，且因應措施為重新啟動服務。不再需要此因應措施。</td>
<td>Templeton</td>
<td>全部</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


注意：為了示範叢集自訂所提供的新功能，此程序使用 [指令碼動作]，在已記錄的叢集上安裝 Spark 和 R 模組。如需進一步資訊，請參閱：

* [在 HDInsight 叢集上安裝和使用 Spark 1.0][hdinsight-install-spark]
* [在 HDInsight Hadoop 叢集上安裝和使用 R][hdinsight-r-scripts]




## HDInsight 2014/11/7 版本的相關資訊 ##

使用此版本部署的 HDInsight 叢集的完整版本號碼為：

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

此版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>說明</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA (如果適用)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>此版本根據 Hortonworks Data Platform (HDP) 2.1.7。HDP 2.1.7 的版本資訊位於 Hortonworks 網站，網址為 http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html。</td>
<td>HDP</td>
<td>全部</td>
<td>N/A</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>YARN Timeline Server (也稱為 Generic Application History Server) 預設已啟用。Timeline Server 提供已完成應用程式的一般相關資訊，例如應用程式 ID、應用程式名稱、應用程式狀態、應用程式提交時間和應用程式完成時間。 <p>可存取 URI http://headnodehost:8188 或執行 YARN 命令：yarn application -list -appStates ALL，藉此從前端節點擷取此應用程式資訊。</p> 
<p>也可以透過 REST API (位於 https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/) 從遠端擷取此資訊。</p> 
<p>可在 http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html 上取得 Timeline Server 的更詳細資訊。</p></td>
<td>服務、YARN</td>
<td>Hadoop、HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>叢集部署 ID</td>
<td>從最新的 SDK 1.3.3.1.5426.29232 版開始，使用者可存取 HDInsight 為每個叢集所發行的唯一 ID。這能在跨建立/捨棄案例重複使用 dnsname 時，讓客戶理解叢集的唯一執行個體。</td>
<td>SDK</td>
<td>全部</td>
<td>N/A</td>
</tr>
</table>
<br>

* 請注意，這個版本已修正防止入口網站顯示、防止 SDK 或 PowerShell 傳回完整版本號碼的錯誤。 

## 2014/10/15 版本的相關資訊 ##

此 Hotfix 版本修復 Templeton 中影響 Templeton 重度使用者的記憶體遺漏。在某些情況下，Templeton 的重度使用者會看到以 500 錯誤碼表示的錯誤，因為要求沒有足夠的記憶體可執行。重新啟動 Templeton 服務就能解決此問題。現在已修正此問題。


## 2014/10/7 版本的相關資訊 ##

* 使用 Ambari 端點 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}" 時， *host_name* 欄位現在會傳回節點的完整網域名稱 (FQDN)，而不是只有主機名稱。例如，不是傳回 "**headnode0**"，而是傳回 FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**"。需要此變更，才能在一個虛擬網路 (VNET) 中部署多種叢集類型，例如 HBase 和 Hadoop。例如，使用 HBase 做為 Hadoop 的後端平台時就是這種情形。

* 我們為 HDInsight 叢集的預設部署提供新的記憶體設定。先前的預設記憶體設定並未充分考量到所部署的 CPU 核心數目。根據 Hortonworks 建議，這些新的記憶體設定應該提供更佳的預設值。若要變更，請參閱 SDK 參考文件來變更叢集組態。下表列舉預設 4 CPU 核心 (8 容器) HDInsight 叢集使用的新記憶體設定(括號中也附帶此版本之前使用的值)。 
 
<table border="1">
<tr><th>元件</th><th>記憶體配置</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (先前是 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB (不變)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB (不變)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (先前是 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (先前是 -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (先前是 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (先前是 -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (先前是 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (先前是 -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256MB (先前是 200MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536MB (不變)</td></tr>

</table><br>

如需 HDInsight 使用的 Hortonworks Data Platform 上供 YARN 和 MapReduce 使用的記憶體組態設定的詳細資訊，請參閱[決定 HDP 記憶體組態設定](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html) (英文)。Hortonworks 也提供工具來計算適當的記憶體設定。

HDInsight PowerShell/SDK 錯誤：「*叢集未設定 Http 服務存取*」：

* 此錯誤稱為[相容性問題](https://social.msdn.microsoft.com/Forums/azure/zh-tw/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight)，起因於 SDK/PowerShell 版本和叢集版本的差異。在 8/15 或之後建立的叢集支援佈建到虛擬網路的這項新功能。但舊版的 SDK/PowerShell 無法正確解譯此功能。結果造成某些工作提交作業失敗。如果您使用 SDK API 或 PowerShell Cmdlet 來提交工作 (**Use-AzureHDInsightCluster**、**Invoke-Hive**)，這些作業可能失敗並傳回錯誤訊息「*叢集 <clustername> 未設定 Http 服務存取*」，或者，根據作業而定，傳回其他錯誤訊息，例如「*無法連接到叢集*」。

* 最新版的 HDInsight SDK 和 Azure PowerShell 中已解決這些相容性問題。建議將 HDInsight SDK 更新到 1.3.1.6 版或更新版本，並將 Azure PowerShell Tools 更新到 0.8.8 版或更新版本。您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 取得最新的 HDInsight SDK，從[如何安裝並設定 Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)取得 Azure PowerShell Tools。

* 只要叢集的版本維持相同，SDK 和 PowerShell 就能繼續搭配叢集的新更新來運作。例如，叢集 3.1 版一定與目前版本的 SDK/PowerShell 1.3.1.6 和 0.8.8 相容。


## HDInsight 3.1 2014/9/12 版本的注意事項##

* 此版本根據 Hortonworks Data Platform (HDP) 2.1.5。如需此版本中修正的錯誤清單，請參閱 Hortonworks 網站的[此版本修正的項目](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html)頁面 (英文)。
* 在 pig 程式庫資料夾中，檔案 "avro-mapred-1.7.4.jar" 已變更為 avro-mapred-1.7.4-hadoop2.jar。這些檔案的內容包含非重大的次要錯誤修正。建議客戶不要直接依賴 JAR 檔案本身的名稱，以避免檔案重新命名時中斷。


## 2014/8/21 版本的相關資訊 ##

* 我們新增下列新的 WebHCat 組態 (HIVE-7155)，將 Templeton 控制器工作的預設記憶體限制設為 1GB (先前的預設值是 512MB)：
	
	* templeton.mapper.memory.mb (=1024)
	* 此變更解決了因為記憶體限制太低而導致某些 Hive 查詢發生下列錯誤：「容器執行超出實體記憶體限制」。
	* 若要回復為舊的預設值，您可以在建立叢集時使用下列命令，透過 PowerShell SDK 將此組態值設為 512：
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* Zookeeper 角色的主機名稱已變更為 zookeeper。這會影響叢集內的名稱解析，但不影響外部 REST API。如果您有元件使用 zookeepernode 主機名稱，則必須更新為改用新的名稱。三個 zookeeper 節點的新名稱如下： 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* 已更新 HBase 版本支援矩陣。只有 HDInsight 3.1 版 (HBase 0.98 版) 才支援生產 HBase 工作負載。將不再支援 3.0 預覽版。在轉移期間，客戶仍然可以建立 3.0 版的叢集。 

## 2014/8/15 之前建立的叢集的相關資訊 ##

可能發生 HDInsight PowerShell/SDK 錯誤，訊息為「叢集 <clustername> 未設定 Http 服務存取」(或者，依作業傳回其他訊息，例如：「無法連接到叢集」)，起因於 SDK/PowerShell 和叢集之間的版本差異。在 8/15 或之後建立的叢集支援佈建到虛擬網路的這項新功能。舊版的 SDK/PowerShell 無法正確解譯此功能，導致工作提交作業失敗。如果您使用 SDK API 或 PowerShell Cmdlet 來提交工作，例如 Use-AzureHDInsightCluster 或 Invoke-AzureHDInsightHiveJob，這些作業可能失敗並傳回上述其中一個錯誤訊息。

最新版的 SDK 和 Azure PowerShell 中已解決這些相容性問題。建議將 HDInsight SDK 更新到 1.3.1.6 版或更新版本，並將 Azure PowerShell Tools 更新到 0.8.8 版或更新版本。您可以從 [nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) 取得最新的 HDInsight SDK，使用 [Microsoft Web PI](http://go.microsoft.com/?linkid=9811175&clcid=0x409) 取得 Azure PowerShell Tools。

只要叢集的版本維持相同，SDK 和 PowerShell 就能繼續搭配叢集的新更新來運作。例如，叢集 3.1 版一定與目前版本的 SDK/PowerShell 1.3.1.6 和 0.8.8 相容。


## 2014/7/28 版本的相關資訊 ##

* **HDInsight 在新的區域上市**：在此版本中，我們將 HDInsight 的地理位置據點擴展到三個新的區域。HDInsight 客戶現在可以在這三個區域建立叢集。 
	* 東亞、 
	* 美國中北部及 
	* 美國中南部。 
* 在此版本中，HDInsight 1.6 版 (HDP1.1、Hadoop 1.0.3) 和 HDInsight 2.1 版 (HDP1.3、Hadoop 1.2) 正從 Azure 管理入口網站中移除。您可以繼續使用 HDInsight PowerShell Cmdlet ([New-AzureHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx)) 或 [HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx) 來建立這些版本的 Hadoop 叢集。如需詳細資訊，請參閱 [HDInsight 元件版本設定](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/)頁面。
* 此版本中的 Hortonworks Data Platform (HDP) 變更： 

<table border="1">
<tr><th>HDP</th><th>變更</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>沒有變更</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>沒有變更</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper:['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## 2014/6/24 版本的相關資訊 ##

此版本包含 HDInsight 服務的幾項新的增強功能： 

* **HDP 2.1 可用性**：HDInsight 3.1 (含 HDP 2.1) 現在已正式發行，也是新叢集的預設版本。
* **HBase - Azure 管理入口網站改善**：我們提供預覽版的 HBase 叢集。您現在從入口網站中，按三下滑鼠就可以建立 HBase 叢集。

![](http://i.imgur.com/cmOl5fM.png)

HBase 可讓您在 HDInsight 上建置各種即時工作負載，包括處理大型資料集的互動式網站，甚至是將數百萬個端點的感應器和遙測資料儲存起來的服務，都沒問題。下一步是使用 Hadoop 工作來分析這些工作負載中的資料，透過既有的體驗，像是 PowerShell 和 Hive 叢集儀表板，這立即就能在 HDInsight 中進行。

### Apache Mahout 現在預先安裝在 HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) 會預先安裝在 HDInsight 3.1 Hadoop 叢集上。以便您在無需任何其他叢集組態需求的情況下執行 Mahout 工作。例如，您可以使用遠端桌面通訊協定 (RDP) 從遠端進入 Hadoop 叢集，不需要額外的步驟就能執行 Hello world Mahout 命令：

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

如需此程序更完整的說明，請參閱 Apache Mahout 網站上關於 [Breiman 範例](https://mahout.apache.org/users/classification/breiman-example.html) (英文) 的文件。 


### Hive 查詢可以在 HDInsight 3.1 中使用 Tez ###

Hive 0.13 現在可以在 HDInsight 3.1 中使用，且能夠使用 Tez 來執行查詢，妥善運用可大幅提升效能。 
依預設不會對 Hive 查詢啟用 Tez。若要使用，您必須自行啟用。您可以執行下列程式碼片段來啟用 Tez：

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks 在標準效能評比中，已發表一份關於 Hive 查詢搭配 Tez 而提升效能的詳細數據。如需詳細資訊，請參閱 [Apache Hive 13 for Enterprise Hadoop 效能評比](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/) (英文)。 

如需有關搭配 Tez 使用 Hive 的詳細資訊，請參閱 [Hive on Tez Wiki 頁面](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) (英文)。

###全球可用性
隨著 Azure HDInsight on Hadoop 2.2 推出，Microsoft 已在所有主要的 Azure 地理區提供 HDInsight。尤其，西歐和東南亞資料中心已上線運作。這可讓客戶在附近的資料中心或法規要求相似的區域中尋找叢集。 


###叢集版本之間的注意事項

**HDInsight 3.1 叢集使用的 Oozie metastore 與舊版的 HDInsight 2.1 叢集不相容，無法與此舊版本一起使用**

與 HDInsight 3.1 叢集一起部署的自訂 Oozie metastore 資料庫，無法在 HDInsight 2.1 叢集上重複使用。即使是源自 2.1 叢集的 metastore 也是如此。不支援此案例，因為 metastore 結構描述與 3.1 叢集一起使用時會升級，所以與 2.1 叢集所需的 metastore 就不再相容。嘗試重複使用已在 HDInsight 3.1 叢集上使用過的 Oozie metastore，將會使 2.1 叢集變得沒有用。 

**叢集之間無法共用 Oozie metastore**
以更整體和稍微不同的觀點來說，Oozie metastore 會連接到特定叢集，無法在叢集之間共用。

###重大變更

**前置詞語法**：
HDInsight 3.0 和 3.1 叢集僅支援 "wasb://" 語法。HDInsight 2.1 和 1.6 叢集支援舊的 "asv://" 語法，但在 HDInsight 3.0 叢集或更新版本中已不支援。這表示任何工作提交至 HDInsight 3.0  或 3.1 叢集且明確使用 "asv://" 語法將會失敗。請改用 wasb:// 語法。另外，如果工作提交至任何以現有 metastore 建立的 HDInsight 3.0 或 3.1 叢集，且 metastore 中使用 asv:// 語法來明確參考資源，則也會失敗。必須使用 wasb:// 來定址資源，以重新建立這些 metastore。 


**連接埠**：HDInsight 服務使用的連接埠已變更。以前使用的連接埠號碼都在 Windows OS 暫時連接埠範圍內。對於短期的網際網路通訊協定通訊，自動會從預設定義的暫時範圍中配置連接埠。現在，新的一組允許的 Hortonworks Data Platform (HDP) 服務連接埠號碼不在此範圍內，可避免與前端節點上執行的服務所使用的連接埠發生衝突。新的連接埠號碼應該不會引起任何重大變更。現在使用的號碼如下：

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>名稱</th><th>值</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 和 3.1 (HDP 2.0 和 2.1)**
<table border="1">
<tr><th>名稱</th><th>值</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###相依項目 

HDInsight 3.x (HDP2.x) 增加下列相依項目：

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

HDInsight 3.x (HDP2.x) 中已不存在下列相依項目：

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###版本變更 

HDInsight 2.x (HDP1.x) 與 HDInsight 3.x (HDP2.x) 之間有下列版本變更：

* metrics-core:['2.1.2'] -> ['3.0.0']
* derbynet:['10.4.2.0'] -> ['10.10.1.1']
* datanucleus:['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler:['5.5.12'] -> ['5.5.23']
* log4j:['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient:['10.4.2.0'] -> ['10.10.1.1']
* httpcore:['4.2.4'] -> ['4.2.5']
* hsqldb:['1.8.0.10'] -> ['2.0.0']
* jets3t:['0.6.1'] -> ['0.9.0']
* protobuf-java:['2.4.1'] -> ['2.5.0']
* derby:['10.4.2.0'] -> ['10.10.1.1']
* jasper:['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon:['1.0.1'] -> ['1.0.13']
* datanucleus-core:['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo:['3.0.7'] -> ['3.2.6']
* zookeeper:['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp:['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###驅動程式
SQL Server JDBC 驅動程式僅供 HDInsight 內部使用，不適用於外部作業。如果您想要使用 ODBC 連線至 HDInsight，請使用 Microsoft Hive ODBC 驅動程式。如需有關使用 Hive ODBC 的詳細資訊，請參閱[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][connect-excel-with-hive-ODBC]。


### 錯誤修正 ###

在此版本中，我們已更新下列 HDInsight  (Hortonworks Data Platform - HDP) 版本的數個錯誤修正：

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Hortonworks 版本資訊 ##

下列位置提供 HDInsight 叢集版本使用的 HDP 的版本資訊。

* HDInsight 叢集 3.1 版採用以 [Hortonworks Data Platform 2.1.7][hdp-2-1-7] 為基礎的 Hadoop 發佈。這是使用 11/7/2014 之後的 Azure HDInsight 入口網站時會建立的預設 Hadoop 叢集。建立於 11/7/2014 之前的 HDInsight 3.1 叢集是基於 [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 

* HDInsight 叢集 3.0 版採用以 [Hortonworks Data Platform 2.0][hdp-2-0-8] 為基礎的 Hadoop 散發。

* HDInsight 叢集 2.1 版採用以 [Hortonworks Data Platform 1.3][hdp-1-3-0] 為基礎的 Hadoop 散發。 

* HDInsight 叢集 1.6 版採用以 [Hortonworks Data Platform 1.1][hdp-1-1-0] 為基礎的 Hadoop 散發。 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html


[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/




<!--HONumber=42-->
