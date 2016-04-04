<properties
	pageTitle="以 Linux 為基礎的 HDInsight (Hadoop) 高可用性功能 |Microsoft Azure"
	description="了解如何使用額外的前端節點，讓以 Linux 為基礎的 HDInsight 叢集可以提高可靠性和可用性。您將了解這會如何影響例如 Ambari 和 Hive 等 Hadoop 服務，以及如何使用 SSH 分別連線到每個前端節點。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="larryfr"/>

#HDInsight 上 Hadoop 叢集的可用性和可靠性

第二個前端節點會由 Azure HDInsight 部署之以 Linux 為基礎的 Hadoop 叢集使用。這可讓 Azure 中執行的 Hadoop 服務與工作更加可用和可靠。

> [AZURE.NOTE] 此文件中使用的步驟是針對以 Linux 為基礎的 HDInsight 叢集。如果您使用以 Windows 為主的叢集，如需 Windows 特定的資訊，請參閱 [HDInsight 上以 Windows 為主的 Hadoop 叢集可用性和可靠性](hdinsight-high-availability.md)。

##了解前端節點

Hadoop 的某些實作具有單一前端節點，其可裝載服務和元件，能順利管理資料 (背景工作角色) 節點的失敗。但任何在前端節點上執行的主要服務中斷都有可能導致叢集停止工作。

HDInsight 叢集提供次要的前端節點，在主要節點失敗時，可讓主要服務和元件繼續在次要節點上執行。

> [AZURE.IMPORTANT] 這兩個前端節點會同時為作用中和在叢集中執行。某些服務，例如 HDFS 或 YARN，在任何給定的時間僅能在其中一個前端節點上為「作用中」(而在其他前端節點上為「待命」)。HiveServer2 或 Hive MetaStore 等其他服務可同時在這兩個前端節點上作用。

[ZooKeeper](http://zookeeper.apache.org/) 節點 (ZK) 用於前端節點上主要服務的前置選擇，並可確保服務、資料 (背景工作角色) 節點和閘道知道主要服務在哪一個前端節點上為作用中。

## 存取前端節點

一般而言，擁有多個前端節點並不會影響透過公用閘道 (Ambari Web 和 REST API) 對叢集的所有存取。此要求會依適當情況，路由傳送至作用中的前端節點和服務。

使用 SSH 存取叢集時，透過連接埠 22 (SSH 的預設值) 的連接將會連接到前端節點 0；透過連接埠 23 的連接將會連接到前端節點 1。

### 內部完整網域名稱 (FQDN)

HDInsight 叢集中的節點具有內部 IP 位址和 FQDN，這些只能自叢集存取 (例如連接到前端節點的 SSH 工作階段或叢集上執行的作業。) 使用內部 FQDN 或 IP 位址存取叢集上的服務時，您應該使用 Ambari 來驗證要存取服務時所使用的 IP 或 FQDN。

例如 Oozie 服務只能在一個前端節點上執行，而且從 SSH 工作階段使用 `oozie` 命令需要有該服務的 URL。這個可以使用下列命令從 Ambari 擷取：

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

這會傳回如下的值，其中包含要使用 `oozie` 命令的內部 URL：

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

## 如何檢查服務狀態

Ambari Web UI 或 Ambari REST API 可用來檢查在前端節點執行的服務狀態。

###Ambari REST API

您可以使用下列命令透過 Ambari REST API 來檢查服務的狀態：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* 將 **PASSWORD** 取代為 HTTP 使用者 (系統管理員) 帳戶密碼。

* 將 **CLUSTERNAME** 取代為叢集的名稱。

* 將 **SERVICENAME** 取代為要檢查其狀態的服務名稱

例如，若要檢查在名為 **mycluster** 的叢集上的 **HDFS** 服務狀態，其中密碼是 **password**，您可使用下列命令：

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

回應將如下所示：

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

該 URL 會告訴我們**前端節點 0** 上目前執行的服務。

該狀態會告訴我們此服務目前正在執行，或**已啟動**。

如果您不知道有哪些服務安裝在該叢集上，您可以使用下列命令擷取清單：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####服務元件

服務可能包含您想要個別檢查狀態的元件。例如，HDFS 包含 NameNode 元件。若要檢視在元件上的資訊，該命令為：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

如果您不知道服務提供了哪些元件，您可以使用下列命令擷取清單：

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Ambari Web UI

Ambari Web UI 可在 https://CLUSTERNAME.azurehdinsight.net 檢視。將 **CLUSTERNAME** 取代為您叢集的名稱。如果出現提示，請輸入叢集的 HTTP 使用者認證。預設 HTTP 使用者名稱為 **admin** ，密碼是您在建立叢集時輸入的密碼。

當您來到 [Ambari] 頁面上時，會在該頁面的左邊列出已安裝的服務。

![已安裝的服務](./media/hdinsight-high-availability-linux/services.png)

服務旁可能會出現一系列圖示以表示狀態。可以使用在頁面頂端的 [警示] 連結，檢視與服務相關的任何警示。您可以選取每個服務來檢視其詳細資訊。

雖然 [服務] 頁面會提供資訊之狀態和每個服務的組態，但並不提供該服務正在哪些前端節點上執行的資訊。若要檢視這項資訊，請使用在頁面頂端的 [主機] 連結。這會顯示叢集內的主機，包括前端節點在內。

![主機清單](./media/hdinsight-high-availability-linux/hosts.png)

選取其中一個前端節點的連結將會顯示該節點上執行的服務與元件。

![元件狀態](./media/hdinsight-high-availability-linux/nodeservices.png)

## 如何存取次要前端節點上的記錄檔

###SSH

透過 SSH 連接至前端節點時，可以在 **/var/log** 找到記錄檔。例如，**/var/log/hadoop-yarn/yarn** 包含 YARN 的記錄檔。

每個前端節點可以有唯一的記錄項目，所以您應該檢查兩者的記錄檔。

###Ambari

> [AZURE.NOTE] 因為個別服務的網站不會在網際網路上公開，所以透過 Ambari 存取記錄檔需要 SSH 通道。如需使用 SSH 通道的相關資訊，請參閱[使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

從 Ambari Web UI 中，選取您想要檢視記錄檔的服務 (例如 YARN)，然後使用 [快速連結] 選取要檢視哪一個前端節點的記錄檔。

![使用快速連結檢視記錄檔](./media/hdinsight-high-availability-linux/viewlogs.png)

## 如何設定前端節點的大小 ##

只能在叢集建立期間選取前端節點的大小。您可以在 [HDInsight 定價頁面](https://azure.microsoft.com/pricing/details/hdinsight/)找到 HDInsight 可用之不同 VM 大小的清單，包括個別的核心、記憶體和本機儲存體。

建立新的叢集時，您可以指定該節點的大小。下列提供如何使用 [Azure 入口網站][preview-portal]、[Azure PowerShell][azure-powershell] 和 [Azure CLI][azure-cli] 指定大小的相關資訊：

* **Azure 入口網站**：建立新叢集時，會提供您設定該叢集前端和資料 (背景工作角色) 節點大小的選項 (定價層)：

	![可選取節點大小的 [叢集映像建立精靈]](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure CLI**：使用 `azure hdinsight cluster create` 命令時，您可以使用 `--headNodeSize` 參數設定前端節點的大小。

* **Azure PowerShell**：使用 `New-AzureRmHDInsightCluster` Cmdlet 時，您可以使用 `-HeadNodeVMSize` 參數設定前端節點的大小。

##後續步驟

在本文件中，您已了解到 Azure HDInsight 如何讓 Hadoop 更加可用。若要深入了解這份文件中所敘述的項目，請使用下列連結。

- [Ambari REST 參考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [安裝和設定 Azure CLI](../xplat-cli-install.md)

- [安裝並設定 Azure PowerShell](../powershell-install-configure.md)

- [使用 Ambari 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)

- [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0323_2016-->