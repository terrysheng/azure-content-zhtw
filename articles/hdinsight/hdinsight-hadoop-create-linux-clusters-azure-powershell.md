<properties
   	pageTitle="在 HDInsight 中使用 Azure PowerShell 在 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
   	description="了解如何使用 Azure PowerShell 在適用於 HDInsight 的 Linux 上建立 Hadoop、HBase 或 Storm 叢集。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="11/16/2015"
   	ms.author="nitinme"/>

#使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。本文件提供有關如何使用 Azure PowerShell 以及範例指令碼佈建以 Linux 為基礎的 HDInsight 叢集資訊。

> [AZURE.NOTE]Azure PowerShell 僅適用於 Windows 用戶端。如果您使用 Linux、Unix 或 Mac OS X 用戶端，請參閱 [使用 Azure CLI 建立以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-create-linux-cluster-azure-cli.md)，以取得使用 Azure CLI 建立叢集的資訊。

###必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __Azure PowerSHell__。如需設定工作站以執行 HDInsight Windows Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。如需搭配使用 Azure PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)。如需 HDInsight Windows PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/dn858087.aspx)。


##建立叢集

以下是使用 Azure PowerShell 佈建 HDInsight 叢集時所需執行的程序：

- 建立 Azure 資源群組
- 建立 Azure 儲存體帳戶
- 建立 Azure Blob 容器
- 建立 HDInsight 叢集

佈建 Linux 叢集時所必須設定的兩個最重要的參數，是用來指定 OS 類型和 SSH 使用者詳細資料的參數：

- 請務必將 **-OSType** 參數指定為 **Linux**。
- 若要對叢集上的遠端工作階段使用 SSH，您可以指定 SSH 使用者密碼或 SSH 公開金鑰。如果您同時指定 SSH 使用者密碼或 SSH 公開金鑰，系統會忽略公開金鑰。如果您想要對遠端工作階段使用 SSH 金鑰，您必須在出現密碼提示時指定空白的 SSH 密碼。如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：
    
    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

下列指令碼示範如何建立新叢集：

    ###########################################
    # Create required items, if none exist
    ###########################################

    # Sign in
    Add-AzureRmAccount

    # Select the subscription to use
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    $resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
    $location = "<Location>"                        # For example, "West US"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account
    $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
    $destContext = New-AzureRmStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzureRmStorageContainer -Name $containerName -Context $destContext

    ###########################################
    # Create an HDInsight Cluster
    ###########################################

    # Skip these variables if you just created them
    $resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
    $storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
    $containerName = "<ContainerName>"              # Provide the container name
    $storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

    # Set these variables
    $clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
    $credentials = Get-Credential
    $sshCredentials = Get-Credential

    # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials

您對 **$clusterCredentials** 指定的值會用來建立叢集的 Hadoop 使用者帳戶。您將使用此帳戶來連線到叢集。您對 **$sshCredentials** 指定的值會用來建立叢集的 SSH 使用者。您會使用此帳戶在叢集上啟動遠端 SSH 工作階段並執行工作。

> [AZURE.IMPORTANT]在這個指令碼中，您必須指定將位於叢集中的背景工作節點數目。如果您在建立叢集時或在建立後調整叢集時規劃使用 32 個以上的背景工作角色節點，則您也必須指定具有至少 8 個核心和 14 GB RAM 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

完成佈建最多需要花費 15 分鐘。

##後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集：

###Hadoop 叢集

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

###HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-stared-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux)

###Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python.md)
* [使用 Storm on HDInsight 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_1203_2015-->