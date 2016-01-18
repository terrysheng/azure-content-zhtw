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
	ms.date="01/04/2016"
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

請參閱[使用 Azure CLI 在 HDInsight 中建立 Windows 型 Hadoop 叢集](hdinsight-hadoop-create-windows-clusters-cli.md)。

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

<!---HONumber=AcomDC_0107_2016-->