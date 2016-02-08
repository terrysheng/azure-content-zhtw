<properties
   	pageTitle="在 HDInsight 中使用跨平台 Azure CLI 在 Linux 上建立 Hadoop、HBase 或 Storm 叢集 | Microsoft Azure"
   	description="了解如何使用跨平台 Azure CLI、Azure 資源管理員範本以及 Azure REST API 來建立以 Linux 為基礎的 HDInsight 叢集。您可以指定叢集類型 (Hadoop、HBase、或 Storm) 或使用指令碼來安裝自訂元件。"
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
   	ms.date="01/22/2016"
   	ms.author="larryfr"/>

#使用 Azure CLI 建立 HDInsight 上的 Linux 型叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure CLI 是可讓您管理 Azure 服務的跨平台命令列公用工具。它可搭配 Azure 資源管理範本用來建立 HDInsight 叢集，以及相關聯的儲存體帳戶和其他服務。

Azure 資源管理範本是描述__資源群組__與其中所有資源 (例如 HDInsight) 的 JSON 文件。 這個以範本為基礎的方法可讓您在一個範本中定義 HDInsight 所需的所有資源，並透過可將變更套用至群組的__部署__，整體管理群組的變更。

本文件中的步驟將逐步完成使用 Azure CLI 和範本建立新 HDInsight 叢集的程序。

> [AZURE.IMPORTANT] 本文件中的步驟使用 HDInsight 叢集的背景工作節點預設數目 (4)。如果您在建立叢集時或在建立後調整叢集時規劃有 32 個以上的背景工作節點，則您必須選取具有至少 8 個核心和 14 GB ram 的前端節點大小。
>
> 如需節點大小和相關成本的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

##必要條件

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __Azure CLI__。如需安裝 CLI 的相關資訊，請參閱[安裝 Azure CLI](../xplat-cli-install.md)。

##登入您的 Azure 訂用帳戶

依照[從 Azure 命令列介面 (Azure CLI) 連線到 Azure 訂用帳戶](../xplat-cli-connect.md)中記載的步驟，使用 __login__ 方法連線到您的訂用帳戶。

##建立叢集

在安裝和設定 Azure CLI 之後，應該從命令提示字元、殼層或終端機工作階段執行下列步驟。

1. 使用下列命令來驗證您的 Azure 訂用帳戶：

        azure login

    系統會提示您提供使用者名稱與密碼。如果您有多個 Azure 訂用帳戶，則可以使用 `azure account set <subscriptionname>` 設定 Azure CLI 命令將使用的訂用帳戶。

3. 使用下列命令來切換至 Azure 資源管理員模式︰

        azure config mode arm

4. 建立 HDInsight 叢集的範本。以下是一些基本範例範本：

    * [以 Linux 為基礎的叢集 (使用 SSH 公開金鑰)](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-publickey)
    * [以 Linux 為基礎的叢集 (使用 SSH 帳戶的密碼)](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)

    這兩個範本也會建立 HDInsight 所使用的預設 Azure 儲存體帳戶。

    您需要的檔案是 __azuredeploy.json__ 和 __azuredeploy.parameters.json__。在繼續之前，請在本機複製這些檔案。

5. 在編輯器中開啟 __azuredeploy.parameters.json__ 檔案，並在 `parameters` 區段中提供項目的值：

    * __location__︰將在其中建立資源的資料中心。您可以檢視 __azuredeploy.json__ 檔案中的 `location` 區段，以取得允許的位置清單。
    * __clusterName__︰HDInsight 叢集的名稱。這個名稱必須是唯一的，否則部署將會失敗。
    * __clusterStorageAccountName__︰要為 HDInsight 叢集建立的 Azure 儲存體帳戶名稱。這個名稱必須是唯一的，否則部署將會失敗。
    * __clusterLoginPassword__︰叢集系統管理員使用者的密碼。這應該是安全的密碼，因為它是用來存取網站和叢集上的 REST 服務。
    * __sshUserName__︰要為此叢集建立的第一個 SSH 使用者名稱。SSH 將利用此帳戶用來從遠端存取此叢集。
    * __sshPublicKey__︰如果您使用的範本需要 SSH 公開金鑰，您就必須在下面這行上新增您的公開金鑰。如需產生和使用公開金鑰的詳細資訊，請參閱下列文章：

        * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__︰如果您使用的範本需要 SSH 密碼，您就必須在下面這行上新增密碼。

    完成後，請儲存並關閉檔案。

5. 使用下列來建立空的資源群組。以您要用於此群組的名稱取代 __RESOURCEGROUPNAME__。以您要在其中建立 HDInsight 叢集的資料中心取代 __LOCATION__︰

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE] 如果位置名稱包含空格，請將它放在引號中。例如 "South Central US"。

6. 使用下列命令來建立此資源群組的初始部署。以 __azuredeploy.json__ 範本檔案的路徑取代 __PATHTOTEMPLATE__。以 __azuredeploy.parameters.json__ 檔案的路徑取代 __PATHTOPARAMETERSFILE__。以您在上一個步驟中建立的群組名稱取代 __RESOURCEGROUPNAME__︰

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    一旦接受部署，您應該會看到類似 `group deployment create command ok` 的訊息。

7. 可能需要一些時間 (大約 15 分鐘) 才能完成部署。您可以使用下列命令檢視有關部署的資訊。以上一個步驟中使用的資源群組名稱取代 __RESOURCEGROUPNAME__︰

        azure group log show -l RESOURCEGROUPNAME
    
    一旦部署完成，[狀態] 欄位會包含 [成功] 這個值。如果在部署期間發生失敗，您可以使用下列命令取得更多有關失敗的資訊

        azure group log show -l -v RESOURCEGROUPNAME

##後續步驟

既然您已成功建立 HDInsight 叢集，請使用下列內容來了解如何使用您的叢集：

###Hadoop 叢集

* [〈搭配 HDInsight 使用 Hivet〉](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [〈搭配 HDInsight 使用 MapReduce〉](hdinsight-use-mapreduce.md)

###HBase 叢集

* [開始在 HDInsight 上使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md)
* [在 HDInsight 上開發適用於 HBase 的 Java 應用程式](hdinsight-hbase-build-java-maven-linux.md)

###Storm 叢集

* [在 HDInsight 上開發適用於 Storm 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [在 HDInsight 上的 Storm 中使用 Python 元件](hdinsight-storm-develop-python-topology.md)
* [在 HDInsight 上使用 Storm 部署和監視拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0128_2016-->