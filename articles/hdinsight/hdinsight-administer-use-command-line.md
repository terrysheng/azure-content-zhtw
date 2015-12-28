<properties
	pageTitle="使用 Azure CLI 管理 Hadoop 叢集 | Microsoft Azure"
	description="如何使用 Azure CLI 管理 Azure HDInsight 上的 Hadoop 叢集"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="jgao"/>

# 使用 Azure CLI 管理 HDInsight 中的 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-portal-management-selector.md)]

了解如何使用 [Azure 命令列介面](xplat-cli-install.md)來管理 Azure HDInsight 中的 Hadoop 叢集。Azure CLI 會在 Node.js 中實作。此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。

本文只涵蓋搭配 HDInsight 使用 Azure CLI。如需如何使用 Azure CLI 的一般指南，請參閱[安裝及設定 Azure CLI][azure-command-line-tools]。

##必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure CLI** - 請參閱 [安裝及設定 Azure CLI](../xplat-cli-install.md) 以取得安裝和設定資訊。
- **連線到 Azure**，使用下列命令：

		azure login

	如需使用公司或學校帳戶驗證的詳細資訊，請參閱[從 Azure CLI 連線至 Azure 訂用帳戶](xplat-cli-connect.md)。
	
- 使用下列命令來**切換至 Azure 資源管理員模式**：

		azure config mode arm

若要取得說明，請使用 **-h** 參數。例如：

	azure hdinsight cluster create -h
	
##建立叢集

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

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

	> [AZURE.NOTE]儲存體帳戶必須與 HDInsight 並存於資料中心內。儲存體帳戶類型不能是 ZRS，因為 ZRS 不支援資料表。

	如需使用 Azure 入口網站建立 Azure 儲存體帳戶的相關資訊，請參閱[建立、管理或刪除儲存體帳戶][azure-create-storageaccount]。
	
	如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則可使用下列命令來擷取資訊：
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	如需使用 Azure 入口網站取得資訊的詳細資訊，請參閱[建立、管理或刪除儲存體帳戶][azure-create-storageaccount]的「檢視、複製及重新產生儲存體存取金鑰」一節。

- **(選擇性) 預設 Blob 容器**：如果容器不存在，**azure hdinsight cluster create** 命令會建立容器。如果您選擇預先建立容器，您可以使用下列命令：

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

在儲存體帳戶就緒後，您即可建立叢集：

	azure hdinsight cluster create --resource-group <Resource Group Name> --clusterName <Cluster Name> --location <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --storageAccountName <Default Storage Account Name> --storageAccountKey <Storage Account Key> --storageContainer <Default Storage Container> --username <HDInsight Cluster Username> --password <HDInsight Cluster Password> --sshUserName <SSH Username> --sshPassword <SSH User Password> --workerNodeCount <Number of Worker Nodes>


##使用組態檔建立叢集
一般而言，您會建立 HDInsight 叢集、在叢集上執行工作，然後就刪除叢集，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次建立叢集時皆可加以重複使用。

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

範例：建立一個組態檔，其中包含會在建立叢集時執行的指令碼動作。

	hdinsight config create "C:\myFiles\configFile.config"
	hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"

##使用指令碼動作來建立叢集

下列是一個範例：

	azure hdinsight cluster create -g mahirg001 -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
如需一般使用指令碼動作的資訊，請參閱[使用指令碼動作來自訂 HDInsight 叢集 (Linux)](hdinsight-hadoop-customize-cluster-linux.md)。

##列出和顯示叢集詳細資料
使用下列命令，以列出並顯示叢集詳細資料：

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##刪除叢集
使用下列命令刪除叢集：

	azure hdinsight cluster delete <Cluster Name>

##調整叢集

若要變更 Hadoop 叢集大小：

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## 啟用/停用叢集 HTTP 存取

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## 啟用/停用叢集 RDP 存取

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##後續步驟
本文中，您學到如何執行不同的 HDInsight 叢集管理工作。若要深入了解，請參閱下列文章：

* [使用 Azure 入口網站管理 HDInsight][hdinsight-admin-portal]
* [使用 Azure PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [開始使用 Azure HDInsight][hdinsight-get-started]
* [如何使用 Azure CLI][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "列出和顯示叢集"

<!---HONumber=AcomDC_1217_2015-->