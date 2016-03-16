<properties
	pageTitle="以程式設計方式存取 Hadoop YARN 應用程式記錄檔 | Microsoft Azure"
	description="在 HDInsight 中的 Hadoop 叢集上以程式設計方式存取應用程式記錄檔"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian" 
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="jgao"/>

# 存取以 Windows 為基礎之 HDInsight 上的 YARN 應用程式記錄

本主題說明如何存取已在 Azure HDInsight 的 Hadoop 叢集上完成的 YARN (Yet Another Resource Negotiator) 應用程式記錄。

> [AZURE.NOTE] 本文件中的資訊僅適用於以 Windows 為基礎的 HDInsight 叢集。如需存取 Linux 之 HDInsight 叢集上 YARN 記錄檔的相關資訊，請參閱 [在 HDInsight 中的 Linux 之 Hadoop 上存取 YARN 應用程式記錄檔](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### 必要條件

- 以 Windows 為基礎的 HDInsight 叢集。請參閱[在 HDInsight 中建立 Windows 型 Hadoop 叢集](hdinsight-provision-clusters.md)。


## YARN Timeline Server

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> 透過兩個不同的介面提供已完成之應用程式的相關泛型資訊，以及架構特定應用程式資訊。具體而言：

* 儲存及擷取 HDInsight 叢集上泛型應用程式資訊的功能已在版本 3.1.1.374 或更新版本上啟用。
* Timeline Server 的架構特定應用程式資訊元件目前在 HDInsight 叢集上並未提供。


應用程式的相關泛型資訊包含下列類型的資料：

* 應用程式識別碼，應用程式的唯一識別碼
* 啟動應用程式的使用者
* 為完成應用程式而進行之嘗試的相關資訊
* 任何指定之應用程式嘗試所使用的容器

在您的 HDInsight 叢集上，會由「Azure 資源管理員」將這項資訊儲存至您預設 Azure 儲存體帳戶之預設容器中的歷程記錄存放區。透過 REST API 即可抓取這項已完成之應用程式的相關泛型資料：

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## YARN 應用程式和記錄檔

YARN 藉由將資源管理從應用程式排程/監視分離，支援多種程式設計模型 (MapReduce 為其中之一)。這是透過全域「*資源管理員*」(RM)、每一背景工作節點「*節點管理員*」(NM) 及每一應用程式「*應用程式主機*」(AM) 來達成。每一應用程式 AM 會與 RM 交涉用來執行您應用程式的資源 (CPU、記憶體、磁碟、網路)。RM 會與 NM 合作來授與這些資源 (以「*容器*」的形式授與)。AM 則是負責追蹤 RM 指派給它之容器的進度。視應用程式的本質而定，一個應用程式可能會需要許多容器。

此外，每個應用程式可能包含多個「*應用程式嘗試*」，以在應用程式發生當機時，或因 AM 與 RM 之間通訊中斷時，完成應用程式。因此，容器是被授與應用程式的特定嘗試。就某方面來說，容器會提供 YARN 應用程式所執行之基本工作單位的內容，而在容器的內容中完成的所有工作，都是在配置給該容器的單一背景工作節點上執行。請參閱 [YARN 概念][YARN-concepts]，以取得進一步的參考資料。

應用程式記錄檔 (和關聯的容器記錄檔) 在對有問題的 Hadoop 應用程式進行偵錯上相當重要。YARN 以[記錄檔彙總][log-aggregation]功能提供一個良好的架構，以收集、彙總及儲存應用程式記錄檔。「記錄檔彙總」功能讓存取應用程式記錄檔更具確定性，因為它會彙總背景工作節點上所有容器的記錄檔，並在應用程式完成之後，將它們以每一背景工作節點一個彙總記錄檔方式儲存在預設檔案系統上。您的應用程式可能使用數百或數千個容器，但在單一工作者節點上執行之所有容器的記錄檔將一律彙總成單一檔案，也就是為您應用程式所使用的每一背景工作節點產生一個記錄檔。「記錄檔彙總」在 HDInsight 叢集 (3.0 版和更新版本) 上預設為啟用，而在您叢集的預設容器中，即可找到彙總的記錄檔，位置如下：

	wasb:///app-logs/<user>/logs/<applicationId>

在該位置中，*user* 是啟動應用程式之使用者的名稱，*applicationId* 是 YARN RM 所指派的應用程式唯一識別碼。

您無法直接閱讀彙總的記錄檔，因為它們是以 [TFile][T-file]、由容器編制索引的[二進位格式][binary-format]撰寫。YARN 提供 CLI 工具，可針對您感興趣的應用程式或容器，將這些記錄檔傾印成純文字。您可以直接在叢集節點上 (透過 RDP 連線到節點之後) 執行下列其中一個 YARN 命令，以純文字格式檢視這些記錄檔：

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## YARN ResourceManager UI

YARN ResourceManager UI 是在叢集前端節點上執行，並可透過 Azure 入口網站的儀表板進行存取：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 
2. 在左側功能表中，依序按一下 [瀏覽]、[HDInsight 叢集] 和您想要存取 YARN 應用程式記錄檔的以 Windows 為基礎的叢集。
3. 在頂端功能表上，按一下 [儀表板]。您會看到有頁面在新的瀏覽器索引標籤上開啟，其名稱為 **HDInsight 查詢主控台**。
4. 在 [HDInsight 查詢主控台] 上，按一下 [Yarn UI]。




[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=AcomDC_0204_2016-->