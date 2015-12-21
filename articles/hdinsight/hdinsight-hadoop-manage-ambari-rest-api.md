<properties
   pageTitle="使用 Apache Ambari REST API 監視和管理 HDInsight 叢集 | Microsoft Azure"
   description="了解如何使用 Ambari 來監視和管理以 Linux 為基礎的 HDInsight 叢集。在本文件中，您將學習如何使用 HDInsight 叢集隨附的 Ambari REST API。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#使用 Ambari REST API 管理 HDInsight 叢集

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari 提供容易使用的 Web UI 和 REST API，可簡化 Hadoop 叢集的管理和監視。以 Linux 為基礎的 HDInsight 叢集上有 Ambari，用來監視叢集並進行組態變更。在這份文件中，您將透過執行一般工作，例如尋找叢集節點的完整格式的網域名稱或尋找叢集所使用的預設儲存體帳戶，學習使用 Ambari REST API 的基本概念。

> [AZURE.NOTE]本文的資訊僅適用於以 Linux 為基礎的 HDInsight 叢集。針對 Windows 型 HDInsight 叢集，只能透過 Ambari REST API 取得一部分的監視功能。請參閱[在 HDInsight 使用 Ambari API 監視以 Windows 為基礎的 Hadoop](hdinsight-monitor-use-ambari-api.md)。

##必要條件

* [cURL](http://curl.haxx.se/)：cURL 是跨平台公用程式，可以用來從命令列使用 REST API。在本文件中，它用來與 Ambari REST API 進行通訊。
* [jq](https://stedolan.github.io/jq/)：jq 是跨平台命令列公用程式，可與 JSON 文件搭配使用。在這份文件中，它用來剖析從 Ambari REST API 傳回的 JSON 文件。

##<a id="whatis"></a>什麼是 Ambari？

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> 提供簡單易用的 Web UI，以供用來佈建、管理及監視 Hadoop 叢集，讓 Hadoop 管理起來更為簡單。開發人員可以使用 <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a> 將這些功能整合到應用程式。

以 Linux 為基礎的 HDInsight 叢集預設會提供 Ambari。

##REST API

在 HDInsight 上 Ambari REST API 的基礎 URI 是 https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME，其中 __CLUSTERNAME__ 是叢集的名稱。

> [AZURE.IMPORTANT]連線到 HDInsight 上的 Ambari 需要 HTTPS。您也必須使用系統管理員帳戶名稱 (預設值是 __admin__) 和建立叢集時所提供的密碼來驗證 Ambari。

以下是使用 cURL 來對 REST API 執行 GET 要求的範例：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
如果您執行此動作，請將 __PASSWORD__ 取代為您的叢集的管理員密碼，並將 __CLUSTERNAME__ 取代為叢集名稱，您會收到一份 JSON 文件，開頭類似下列資訊：

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

由於這是 JSON，使用 JSON 剖析器來擷取資料通常較容易。例如，如果您想要擷取警示的計數 (包含在 __"Host/host\_status/ALERT"__ 元素中) 您可以使用下列指令直接存取值：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
這會擷取 JSON 文件，然後將輸出用管道輸送至 jq。`'.Clusters.health_report."Host/host_status/ALERT"'` 指出您要擷取的 JSON 文件內的元素。

> [AZURE.NOTE]__Host/host\_status/ALERT__ 元素含括在引號中，以表示 '/' 是元素名稱的一部分。如需使用 jq 的詳細資訊，請參閱 [jq website](https://stedolan.github.io/jq/)。

##範例：取得叢集節點的 FQDN

使用 HDInsight 時，您可能需要知道叢集節點的完整網域名稱 (FQDN)。您可以使用下列項目，輕鬆地擷取叢集中不同節點的 FQDN：

* __前端節點__：`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __背景工作節點__：`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper 節點__：`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

請注意，每一項均遵循查詢我們知道在這些節點上執行之元件的相同模式，然後擷取 `host_name` 元素，其中包含這些節點的 FQDN。

傳回文件的 `host_components` 元素包含多個項目。使用 `.host_components[]`，然後指定元素內的路徑，將對每個項目執行迴圈並從特定路徑提取值。如果您只想要一個值，例如第一個 FQDN 項目，您可以以集合形式傳回項目，然後選取特定項目：

    jq '[.host_components[].HostRoles.host_name][0]'

這會傳回來自集合的第一個 FQDN。

##範例：取得預設儲存體帳戶和容器

建立 HDInsight 叢集時，您必須使用 Azure 儲存體帳戶和 Blob 容器做為叢集的預設儲存體。在建立叢集之後，您可以使用 Ambari 來擷取這項資訊。例如，如果您想要以程式設計方式將資料直接寫入容器。

以下將擷取叢集預設儲存體的 WASB URI：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]這個方法會傳回套用至伺服器的第一個組態 (`service_config_version=1`) 其中會包含這項資訊。如果是擷取在叢集建立後修改的值，您可能需要列出組態版本並擷取最新的版本。

這會傳回值類似下列的值，其中 __CONTAINER__ 為預設容器和 __ACCOUNTNAME__ 是 Azure 儲存體帳戶名稱：

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

之後您可以使用這項資訊搭配 [Azure CLI](../xplat-cli-install.md) 來上傳或從容器下載資料。例如：

1. 取得儲存體帳戶的資源群組。將 __ACCOUNTNAME__ 取代為從 Ambari 擷取的儲存體帳戶名稱：

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    這會傳回帳戶的資源群組名稱。
    
2. 取得儲存體帳戶的金鑰。將 __GROUPNAME__ 取代為上一個步驟的資源群組。將 __ACCOUNTNAME__ 取代為儲存體帳戶名稱：

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    這會傳回帳戶的主要金鑰。
    
3. 使用上傳命令來將檔案儲存在容器：

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    將 __ACCOUNTNAME__ 取代為儲存體帳戶名稱。將 __ACCOUNTKEY__ 取代為先前擷取的索引鍵。__FILEPATH__ 是您想要上傳的檔案的路徑，而 __BLOBPATH__ 是在容器中的路徑。

    例如，如果您想要檔案出現在 HDInsight 中的 wasb://example/data/filename.txt，則 __BLOBPATH__ 會是 `example/data/filename.txt`。

##後續步驟

如需 REST API 的完整參考，請參閱 [Ambari API 參考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

> [AZURE.NOTE]某些 Ambari 功能已停用，因為這些功能是由 HDInsight 雲端服務所管理；例如，在叢集中新增或移除主機，或新增服務。

<!---HONumber=AcomDC_1210_2015-->