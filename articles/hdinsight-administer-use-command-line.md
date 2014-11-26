<properties linkid="manage-services-hdinsight-administer-hdinsight-hadoop-clusters-using-command-line" urlDisplayName="HDInsight Administration" pageTitle="Manage Hadoop clusters using Cross-Platform Command-Line | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hadoop, administration" description="Learn how to use the Cross-Platform Command-Line Interface to manage Hadoop clusters in HDIsight on any platform that supports Node.js, including Windows, Mac, and Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer Hadoop clusters using the Cross-platform Command-line Interface" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# 使用跨平台命令列介面管理 HDInsight 上的 Hadoop 叢集

本文中，您將學習如何使用跨平台命令列介面來管理 HDInsight 上的 Hadoop 叢集。命令列工具在 Node.js 中實作。在任何支援 Node.js 的平台上都可使用，包括 Windows、Mac 和 Linux。

此命令列工具採用開放原始碼。原始程式碼會在 GitHub 中受到管理 (<https://github.com/WindowsAzure/azure-sdk-tools-xplat>)。

本文僅提供從 Windows 使用此命令列介面的相關資訊。如需如何使用此命令列介面的一般指引，請參閱[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具][如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]。如需完整的參考文件，請參閱[適用於 Mac 和 Linux 的 Azure 命令列工具][適用於 Mac 和 Linux 的 Azure 命令列工具]。

**必要條件：**

開始閱讀本文之前，您必須符合下列必要條件：

-   **Azure 訂用帳戶**。Azure 是訂用帳戶型平台。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][購買選項]、[成員優惠][成員優惠]或[免費試用][免費試用]。

## 本文內容

-   [安裝][安裝]
-   [下載和匯入 Azure 帳戶 publishsettings][下載和匯入 Azure 帳戶 publishsettings]
-   [佈建叢集][佈建叢集]
-   [使用組態檔來佈建叢集][使用組態檔來佈建叢集]
-   [列出和顯示叢集][列出和顯示叢集]
-   [刪除叢集][刪除叢集]
-   [後續步驟][後續步驟]

## <span id="installation"></span></a>安裝

此命令列介面可使用 *Node.js 封裝管理員 (NPM)* 或 Windows Installer 進行安裝。

**使用 NPM 安裝命令列介面**

1.  瀏覽至 **www.nodejs.org**。
2.  按一下 \[安裝]，並依照指示使用預設設定操作。
3.  從您的工作站開啟 \[命令提示字元] (或是 *Azure 命令提示字元*或 *VS2012 開發人員命令提示字元*)。
4.  在命令提示字元視窗中執行下列命令。

        npm install -g azure-cli

    > [WACOM.NOTE] 如果出現「找不到 NPM 命令」的錯誤，請驗證下列路徑是否在 PATH 環境變數中：*C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm* 或 *C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm*

5.  執行下列命令以驗證安裝：

        azure hdinsight -h

    您可以在不同層級使用 *-h* 參數，以顯示說明資訊。例如：

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**使用 Windows Installer 安裝命令列介面**

1.  瀏覽至 **<http://azure.microsoft.com/zh-tw/downloads/>**。
2.  向下捲動至 [命令列工具] 區段，然後按一下 [跨平台命令列介面]，並依照 Web Platform Installer 精靈操作。

## <span id="importsettings"></span></a>下載和匯入 Azure 帳戶 publishsettings

使用命令列介面之前，您必須先設定工作站與 Azure 之間的連線。命令列介面會使用您的 Azure 訂用帳戶資訊連接到您的帳號。這項資訊可從 Azure 的 publishsettings 檔案取得。接著，publishsettings 檔案可匯入為持續性的本機組態設定，供命令列介面用於後續的作業。您的 publishsettings 只需匯入一次即可。

> [WACOM.NOTE] publishsettings 檔案包含敏感資訊。建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。在 Windows 中，修改資料夾屬性或使用 BitLocker。

**下載和匯入 publishsettings**

1.  開啟 [命令提示字元]。
2.  執行下列命令以下載 publishsettings 檔案。

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    此命令會顯示下載檔案的指示，包括 URL。
3.  開啟 **Internet Explorer**，然後瀏覽至命令提示字元視窗中列出的 URL。
4.  按一下 [儲存]，將檔案儲存至工作站。
5.  在命令提示字元視窗中執行下列命令，以匯入 publishsettings 檔案：

        azure account import <file>

    在上一個螢幕擷取畫面中，publishsettings 檔案已儲存至工作站的 C:\\HDInsight 資料夾。

## <span id="provision"></span></a>佈建 HDInsight 叢集

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存帳號，您才能建立 HDInsight 叢集。

匯入 publishsettings 檔案之後，您可以使用下列命令來建立儲存體帳戶：

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] 儲存體帳戶必須並存於相同的資料中心內。目前，您只能在下列資料中心佈建 HDInsight 叢集：

> -   東南亞
> -   北歐
> -   西歐
> -   美國東部
> -   美國西部

如需使用 Azure 管理入口網站建立 Azure 儲存帳號的相關資訊，請參閱[如何建立儲存帳號][如何建立儲存帳號]。

如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列命令來擷取資訊：

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

如需有關使用管理入口網站取得資訊的詳細資訊，請參閱＜*作法：檢視、複製及重新產生儲存體存取金鑰*一節 (位於[如何管理儲存體帳戶][如何管理儲存體帳戶]中)。

*azure hdinsight cluster create* 命令會建立容器 (如果不存在)。如果您選擇預先建立容器，您可以使用下列命令：

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

在儲存體帳戶和 Blob 容器準備就緒後，您即可建立叢集：

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

## <span id="provisionconfigfile"></span></a>使用組態檔佈建 HDInsight 叢集

一般而言，您會佈建 HDInsight 叢集、在叢集上執行工作，然後就刪除叢集，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次佈建叢集時皆可加以重複使用。

    azure hdinsight cluster config create <file>
     
    azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
     
    azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
           --storageAccountKey "<StorageAccountKey>"
     
    azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
           --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
     
    azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
           --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
     
    azure hdinsight cluster create --config <file>
         

![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

## <span id="listshow"></span></a> 列出和顯示叢集詳細資料

使用下列命令，以列出並顯示叢集詳細資料：

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][HDI.CLIListCluster]

## <span id="delete"></span></a> 刪除叢集

使用下列命令刪除叢集：

    azure hdinsight cluster delete <ClusterName>

## <span id="nextsteps"></span></a>後續步驟

本文中，您學到如何執行不同的 HDInsight 叢集管理工作。若要深入了解，請參閱下列文章：

-   [使用管理入口網站管理 HDInsight][使用管理入口網站管理 HDInsight]
-   [使用 PowerShell 管理 HDInsight][使用 PowerShell 管理 HDInsight]
-   [Azure HDInsight 使用者入門][Azure HDInsight 使用者入門]
-   [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具][如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]
-   [適用於 Mac 和 Linux 的 Azure 命令列工具][適用於 Mac 和 Linux 的 Azure 命令列工具]

  [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: ../xplat-cli/
  [適用於 Mac 和 Linux 的 Azure 命令列工具]: ../command-line-tools/
  [購買選項]: http://azure.microsoft.com/zh-tw/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/zh-tw/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [安裝]: #installation
  [下載和匯入 Azure 帳戶 publishsettings]: #importsettings
  [佈建叢集]: #provision
  [使用組態檔來佈建叢集]: #provisionconfigfile
  [列出和顯示叢集]: #listshow
  [刪除叢集]: #delete
  [後續步驟]: #nextsteps
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
  [如何建立儲存帳號]: ../storage-create-storage-account/
  [如何管理儲存體帳戶]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "List and show clusters"
  [使用管理入口網站管理 HDInsight]: ../hdinsight-administer-use-management-portal/
  [使用 PowerShell 管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [Azure HDInsight 使用者入門]: ../hdinsight-get-started/
