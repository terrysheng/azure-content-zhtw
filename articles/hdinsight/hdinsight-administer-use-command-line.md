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
	ms.date="11/03/2015"
	ms.author="jgao"/>

# 使用 Azure CLI 管理 HDInsight 中的 Hadoop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-portal-management-selector.md)]

了解如何使用 [Azure 命令列介面](xplat-cli-install.md)來管理 Azure HDInsight 中的 Hadoop 叢集。Azure CLI 會在 Node.js 中實作。此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。

Azure CLI 為開放原始碼。原始程式碼會在 GitHub 中進行管理 (<a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>)。

本文只涵蓋搭配 HDInsight 使用 Azure CLI。如需如何使用 Azure CLI 的一般指南，請參閱[安裝及設定 Azure CLI][azure-command-line-tools]。


##先決條件

開始閱讀本文之前，您必須符合下列必要條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **Azure CLI** - 請參閱 [安裝及設定 Azure CLI](../xplat-cli-install.md) 以取得安裝和設定資訊。
- **連線到 Azure**，使用下列命令：

		azure login

	如需使用公司或學校帳戶驗證的詳細資訊，請參閱[從 Azure CLI 連線至 Azure 訂用帳戶](xplat-cli-connect.md)。
	
- 使用下列命令來**切換至 Azure 資源管理員模式**：

		azure config mode arm


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

	如需使用 Azure Preview 入口網站建立 Azure 儲存體帳戶的相關資訊，請參閱[建立、管理或刪除儲存體帳戶][azure-create-storageaccount]。
	
	如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則可使用下列命令來擷取資訊：
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	如需使用 Azure Preview 入口網站取得資訊的詳細資料，請參閱[建立、管理或刪除儲存體帳戶][azure-create-storageaccount]的＜檢視、複製及重新產生儲存體存取金鑰＞一節。

- **(選擇性) 預設 Blob 容器**：如果容器不存在，**azure hdinsight cluster create** 命令會建立容器。如果您選擇預先建立容器，您可以使用下列命令：

	azure storage container create --account-name "<Storage Account Name>" --account-key <StorageAccountKey> [ContainerName]

在儲存體帳號和 Blob 容器準備就緒後，您即可建立叢集。

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<Storage Account Name>" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##使用組態檔建立叢集
一般而言，您會建立 HDInsight 叢集、在叢集上執行工作，然後就刪除叢集，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次建立叢集時皆可加以重複使用。

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName ""<Storage Account Name>".blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName ""<Storage Account Name>".blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##列出和顯示叢集詳細資料
使用下列命令，以列出並顯示叢集詳細資料：

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##刪除叢集
使用下列命令刪除叢集：

	azure hdinsight cluster delete <ClusterName>

##調整叢集

若要使用 Azure PowerShell 變更 Hadoop 叢集大小，請從用戶端電腦執行下列命令：

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>

##後續步驟
本文中，您學到如何執行不同的 HDInsight 叢集管理工作。若要深入了解，請參閱下列文章：

* [使用 Azure Preview 入口網站管理 HDInsight][hdinsight-admin-portal]
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

<!---HONumber=Nov15_HO2-->