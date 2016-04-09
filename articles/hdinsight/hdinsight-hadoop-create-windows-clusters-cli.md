<properties
   pageTitle="使用 Azure CLI 在 HDInsight 中建立 Windows 型 Hadoop 叢集"
   	description="了解如何使用 Azure CLI 建立 Azure HDInsight 的叢集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/08/2016"
   ms.author="jgao"/>

# 使用 Azure CLI 在 HDInsight 中建立 Windows 型 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-selector-create-clusters.md)]

了解如何使用 Azure CLI 建立 HDInsight 叢集。如需其他叢集建立工具和功能的資訊，請按一下此頁面頂端的索引標籤，或參閱[叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

##必要條件：

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


開始執行本文中的指示之前，您必須擁有以下項目：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure CLI** - 請參閱 [安裝及設定 Azure CLI](../xplat-cli-install.md) 以取得安裝和設定資訊。

##連接到 Azure

使用下列命令連接到 Azure：

	azure login

如需使用公司或學校帳戶驗證的詳細資訊，請參閱[從 Azure CLI 連線至 Azure 訂用帳戶](../xplat-cli-connect.md)。
	
使用下列命令切換至 ARM 模式：

	azure config mode arm

若要取得說明，請使用 **-h** 參數。例如：

	azure hdinsight cluster create -h
	
##建立叢集

您必須擁有 Azure 資源管理 (ARM) 與 Azure Blob 儲存體帳戶才能建立 HDInsight 叢集。若要建立 HDInsight 叢集，您必須指定下列項目:

- **Azure 資源群組**：資料湖分析帳戶必須建立在 Azure 資源群組內。Azure 資源管理員可讓您將應用程式中的資源做為群組使用。您可以透過單一、協調的作業來部署、更新或刪除應用程式的所有資源。 

	若要列出訂用帳戶中的資源群組：
	
		azure group list 
	
	若要建立新的資源群組：
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight 叢集名稱**

- **位置**：其中一個支援 HDInsight 叢集的 Azure 資料中心。如需支援的位置，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

- **預設儲存體帳戶**：HDInsight 使用 Azure Blob 儲存體容器做為預設檔案系統。必須要有 Azure 儲存體帳戶，才能夠建立 HDInsight 叢集。

	建立新的 Azure 儲存體帳戶：
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE] 儲存體帳戶必須與 HDInsight 並存於資料中心內。儲存體帳戶類型不能是 ZRS，因為 ZRS 不支援資料表。

	如需使用 Azure 入口網站建立 Azure 儲存體帳戶的相關資訊，請參閱 [建立、管理或刪除儲存體帳戶] [azure-create-storageaccount]。
	
	如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則可使用下列命令來擷取資訊：
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	如需使用 Azure 入口網站取得資訊的詳細資訊，請參閱 [建立、管理或刪除儲存體帳戶][azure-create-storageaccount] 的「檢視、複製及重新產生儲存體存取金鑰」一節。

- **(選擇性) 預設 Blob 容器**：如果容器不存在，**azure hdinsight cluster create** 命令會建立容器。如果您選擇預先建立容器，您可以使用下列命令：

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

在儲存體帳戶就緒後，您即可建立叢集：

    
    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##使用組態檔建立叢集
一般而言，您會建立 HDInsight 叢集、在叢集上執行工作，然後就刪除叢集，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次建立叢集時皆可加以重複使用。

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

範例：建立一個組態檔，其中包含會在建立叢集時執行的指令碼動作。

	azure hdinsight config create "C:\myFiles\configFile.config"
	azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
	azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##使用指令碼動作建立叢集

建立一個組態檔，其中包含會在建立叢集時執行的指令碼動作。

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

使用指令碼動作建立叢集

	azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
	
如需一般使用指令碼動作的資訊，請參閱[使用指令碼動作來自訂 HDInsight 叢集 (Linux)](hdinsight-hadoop-customize-cluster.md)。


## 使用 ARM 範本建立叢集

您可以使用 CLI 呼叫 ARM 範本以建立叢集。請參閱[使用 Azure CLI 進行部署](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli)。

## 另請參閱

- [開始使用 Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - 了解如何開始使用 HDInsight 叢集
- [以程式設計方式提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以程式設計方式提交工作至 HDInsight
- [使用 Azure CLI 管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-command-line.md)
- [使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 服務管理](../virtual-machines/virtual-machines-command-line-tools.md)

<!---HONumber=AcomDC_0309_2016-->