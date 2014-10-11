<properties linkid="manage-services-hdinsight-provision-hadoop-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# 使用自訂選項在 HDInsight 上佈建 Hadoop 叢集

在本文中，您將了解在 Azure HDInsight 上自訂佈建 Hadoop 叢集的不同方式 - 使用 Azure 管理入口網站、PowerShell、命令列工具或 HDInsight .NET SDK。本文將針對佈建 Hadoop 叢集進行討論。如需如何佈建 HBase 叢集的指示，請參閱[在 HDInsight 上佈建 HBase 叢集][]。請參閱 [Hadoop 和 HBase 之間的差別？][]，以了解最優先的選擇理由。

## 什麼是 HDInsight 叢集？

您有沒有想過為什麼我們每次針對 Hadoop 或 BigData 進行討論時，都一定會提到叢集？那是因為 Hadoop 允許大型資料的分散式處理，散佈到叢集的不同節點中。叢集具有主/從架構，包含一個主要 (亦稱為前端節點或名稱節點) 和任意數目的從屬 (亦稱為資料節點)。如需詳細資訊，請參閱 [Apache Hadoop][]。

HDInsight 叢集摘要了 Hadoop 實作詳細資料，因此您無需擔心如何與叢集的不同節點通訊。佈建 HDInsight 叢集時，您可以佈建包含 Hadoop 及相關應用程式的 Azure 運算資源。如需詳細資訊，請參閱 [HDInsight 中 Hadoop 的簡介][]。

本文針對佈建叢集的不同方式提供指示。如果您想知道佈建叢集的快速入門方法，請參閱[開始使用 Azure HDInsight][]。

**必要條件：**

開始閱讀本文之前，您必須符合下列必要條件：

-   Azure 訂用帳戶。Azure 是訂用帳戶型平台。HDInsight PowerShell Cmdlet 會為您的訂用帳戶執行相關工作。如需取得訂用帳戶的詳細資訊，請參閱[購買選項][]、[成員優惠][]或[免費試用][]。

## 本文內容

-   [使用 Azure 管理入口網站][]
-   [使用 Azure PowerShell][]
-   [使用跨平台命令列][]
-   [使用 HDInsight .NET SDK][]
-   [後續步驟][]

## <span id="portal"></span></a>使用 Azure 管理入口網站

HDInsight 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。相同的資料中心上必須要有 Azure 儲存帳號，您才能建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][]。如需建立 Azure 儲存帳號的詳細資訊，請參閱[如何建立儲存帳號][]。

> [WACOM.NOTE] 目前只有「東亞」、「東南亞」、「北歐」、「西歐」、「美國東部」、「美國西部」、「美國中北部」和「美國中南部」等區域可以代管 HDInsight 叢集。

**使用自訂建立選項建立 HDInsight 叢集**

1.  登入 [Azure 管理入口網站][]。
2.  按一下頁面底部的 [+新增]，然後依序按一下 [資料服務]、[HDInsight]、[自訂建立]。
3.  在 [叢集詳細資料] 頁面上，輸入或選擇下列值：

    ![HDI.CustomCreateCluster][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">屬性</th>
    <th align="left">值</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">叢集名稱</td>
    <td align="left"><p>為叢集命名。</p>
    <ul>
    <li>DNS 名稱的開頭與結尾都必須是英數字元，名稱中可包含連字號。</li>
    <li>欄位必須是 3 到 63 個字元的字串。</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">叢集類型</td>
    <td align="left">在叢集類型中選取 [Hadoop]。</td>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight 版本</td>
    <td align="left">選擇版本。若是 Hadoop，預設值為採用 Hadoop 2.4 的 HDInsight 3.1 版。</td>
    </tr>
    </tbody>
    </table>

    輸入或選取如資料表中所示的值，然後按一下向右箭頭。

4.  在 [設定叢集] 頁面上，輸入或選取下列值：

    | 名稱          | 值                                                                                                                                                                                                                                                                                      |
    |---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 資料節點      | 您要部署的資料節點數。請建立單一節點叢集，以供測試之用。                                                                                                                                                                                                                                
                     叢集大小限制會隨著 Azure 訂用帳戶而不同。若要提高限制，請與 Azure 帳務支援人員連絡。                                                                                                                                                                                                     |
    | 區域/虛擬網路 | 請選擇與您在前一個程序中建立的儲存體帳戶相同的區域。使用 HDInsight 時，儲存體帳戶必須位於相同的區域中。後續進行設定時，您只能選擇此處指定之相同區域中的儲存體帳戶。可用的區域如下：「東亞」、「東南亞」、「北歐」、「西歐」、「美國東部」、「美國西部」、「美國中北部」和「美國中南部」 |

5.  在 [Configure Cluster User] 頁面上，提供下列值：

    ![HDI.CustomCreateCluster.ClusterUser][]

    | 屬性                      | 值                                                                                                                                                                                             |
    |---------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 使用者名稱                | 指定 HDInsight 叢集使用者名稱。                                                                                                                                                                |
    | 密碼/確認密碼             | 指定 HDInsight 叢集使用者密碼。                                                                                                                                                                |
    | 輸入 Hive/Oozie Metastore | 勾選此核取方塊，在與叢集相同的資料中心上指定 SQL Database，以作為 Hive/Oozie metastore 使用。即使在將叢集刪除之後，如果您想要保留 Hive/Oozie 工作的相關中繼資料，此選項將會很有幫助。          |
    | Metastore 資料庫          | 指定要作為 Hive/OOzie metastore 的 Azure SQL 資料庫。此 SQL 資料庫必須與 HDInsight 叢集位於相同的資料中心內。清單方塊只會列出與您在 [叢集詳細資料] 頁面上指定的相同資料中心內的 SQL Database。 |
    | 資料庫使用者              | 指定將用來連接到資料庫的 SQL 資料庫使用者。                                                                                                                                                    |
    | 資料庫使用者密碼          | 指定 SQL 資料庫使用者密碼。                                                                                                                                                                    |

    > [WACOM.NOTE] 用於 metastore 的 Azure SQL Database 必須允許與其他 Azure 服務 (包括 Azure HDInsight) 的連線能力。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [設定]，然後在 [Windows Azure 服務] 中，依序按一下 [是] 和 [儲存]。

    按一下向右箭頭。

6.  在 [儲存體帳戶] 頁面上，提供下列值：

    ![HDI.CustomCreateCluster.StorageAccount][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">屬性</th>
    <th align="left">值</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">儲存體帳戶</td>
    <td align="left">指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存體帳戶。您可以從三個選項中擇一使用：
    <ul>
    <li>使用現有儲存體</li>
    <li>建立新的儲存體</li>
    <li>使用其他訂用帳戶的儲存體</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">帳戶名稱</td>
    <td align="left"><ul>
    <li>如果您選擇使用現有儲存體，請在 [帳戶名稱] 中選取現有儲存體帳戶。下拉式清單所列出的儲存體帳戶僅限位於與您選擇佈建叢集相同的資料中心。</li>
    <li>如果選擇 [Create new storage] 或 [Use storage from another subscription] 選項，則您必須提供儲存體帳戶名稱。</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">帳戶金鑰</td>
    <td align="left">如果選擇 [Use Storage From Another Subscription] 選項，請指定該儲存體帳戶的帳戶金鑰。</td>
    </tr>
    <tr class="even">
    <td align="left">預設容器</td>
    <td align="left"><p>指定儲存體帳戶上的預設容器作為 HDInsight 叢集的預設檔案系統使用。如果在 [儲存體帳戶] 欄位中選擇了 [Use Existing Storage]，但該帳戶中沒有現有容器，則系統便會依預設建立容器，其名稱將會與叢集名稱相同。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。例如 mycontainer1、mycontainer2，依此類推。然而，如果現有儲存體帳戶有容器存在，且其名稱與您指定的叢集名稱不同，則您也可以使用該容器。</p>
    <p>如果選擇建立新儲存體或使用其他 Azure 訂用帳戶的儲存體，您必須指定預設容器名稱</p></td>
    </tr>
    <tr class="odd">
    <td align="left">其他儲存體帳戶</td>
    <td align="left">HDInsight 支援多個儲存帳號。叢集可使用的其他儲存體帳戶並沒有數量上的限制。但如果您使用管理入口網站建立叢集，則會因為 UI 的限制而限定為七個帳戶。您所指定的每個其他儲存體帳戶都會在精靈上另外新增一個 [儲存體帳戶] 頁面，您可在此指定帳戶資訊。以上面的螢幕擷取畫面為例，在選取一個其他儲存體帳戶後，對話方塊因此新增了頁面 5。</td>
    </tr>
    </tbody>
    </table>

    按一下向右箭頭。

7.  在 [儲存體帳戶] 頁面上，輸入其他儲存體帳戶的帳戶資訊：

    ![HDI.CustomCreateCluster.AddOnStorage][]

    在這裡，您可以選擇現有儲存體、建立新儲存體或使用其他 Azure 訂用帳戶的儲存體。提供值的程序會與上個步驟類似。

    按一下核取記號以開始佈建叢集。佈建完成時，狀態欄便會顯示 [執行中]。

    > [WACOM.NOTE] 在您為 HDInsight 叢集選擇 Azure 儲存帳號後，您將無法刪除帳號，也無法將帳號變更為不同的帳號。

## <span id="powershell"></span></a>使用 Azure PowerShell

Azure PowerShell 是功能強大的指令碼環境，可讓您在 Azure 中控制和自動化工作量的部署與管理。本節提供如何佈建 HDInsight 叢集的指示，如需設定工作站以執行 HDInsight Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell][]。如需有關在 HDInsight 上使用 PowerShell 的詳細資訊，請參閱[使用 PowerShell 來管理 HDInsight][]。如需 HDInsight PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考文件][]。

以下是使用 PowerShell 佈建 HDInsight 叢集時所需執行的程序：

-   建立 Azure 儲存體帳戶
-   建立 Azure Blob 容器
-   建立 HDInsight 叢集

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存帳號和儲存容器，您才能建立 HDInsight 叢集。儲存帳號必須與 HDInsight 叢集位於相同的資料中心。

**建立 Azure 儲存體帳戶**

-   從 Azure PowerShell 主控台視窗執行下列命令：

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列 PowerShell 命令來擷取資訊：

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**建立 Azure Blob 儲存體容器**

-   從 Azure PowerShell 主控台視窗執行下列命令：

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

在儲存帳號和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

-   從 Azure PowerShell 主控台視窗執行下列命令：

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    出現提示時，請輸入叢集的認證。叢集佈建可能需要幾分鐘的時間。

    ![HDI.CLI.Provision][]

**使用自訂組態選項佈建 HDInsight 叢集**

佈建叢集時，您可以使用其他組態選項，例如連線到多個 Azure Blob 儲存體，或使用 Azure SQL Database 來處理 Hive 和 Oozie metastore。這可讓您區隔資料和中繼資料的存留期與叢集的存留期。

-   從 Windows PowerShell 視窗執行下列命令：

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location

    > [WACOM.NOTE] 用於 metastore 的 Azure SQL Database 必須允許與其他 Azure 服務 (包括 Azure HDInsight) 的連線能力。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [設定]，然後在 [Windows Azure 服務] 中，依序按一下 [是] 和 [儲存]。

**列出 HDInsight 叢集**

-   從 Azure PowerShell 主控台視窗執行下列命令，以列出 HDInsight 叢集並確認是否已成功佈建叢集：

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a>使用跨平台命令列

另一個佈建 HDInsight 叢集的選項，是跨平台命令列介面。命令列工具在 Node.js 中實作。在任何支援 Node.js 的平台上都可使用，包括 Windows、Mac 和 Linux。此命令列工具採用開放原始碼。原始程式碼會在 GitHub 中受到管理 (<https://github.com/Azure/azure-sdk-tools-xplat>)。如需如何使用此命令列介面的一般指引，請參閱[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具][]。如需完整的參考文件，請參閱[適用於 Mac 和 Linux 的 Azure 命令列工具][]。本文僅提供從 Windows 使用此命令列介面的相關資訊。

以下是使用跨平台命令列佈建 HDInsight 叢集時所需執行的程序：

-   安裝跨平台命令列
-   下載及匯入 Azure 帳號發佈設定
-   建立 Azure 儲存體帳戶
-   佈建叢集

此命令列介面可使用 *Node.js 封裝管理員 (NPM)* 或 Windows Installer 進行安裝。Microsoft 建議您僅使用下列兩個選項的其中一個選項進行安裝。

**使用 NPM 安裝命令列介面**

1.  瀏覽至 **www.nodejs.org**。
2.  按一下 [安裝]，並依照指示使用預設設定操作。
3.  從您的工作站開啟 [命令提示字元] \(或是 *Azure 命令提示字元*或 *VS2012 開發人員命令提示字元*)。
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

1.  瀏覽至 **<http://azure.microsoft.com/en-us/downloads/>**。
2.  向下捲動至 [命令列工具] 區段，然後按一下 [跨平台命令列介面]，並依照 Web Platform Installer 精靈操作。

**下載及匯入發佈設定**

使用命令列介面之前，您必須先設定工作站與 Azure 之間的連線。命令列介面會使用您的 Azure 訂用帳戶資訊連接到您的帳號。這項資訊可從 Azure 的發佈設定檔案取得。接著，發佈設定檔案可匯入為持續性的本機組態設定，供命令列介面用於後續的作業。您的發佈設定只需匯入一次即可。

> [WACOM.NOTE] 發佈設定檔案包含敏感資訊。Microsoft 建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。在 Windows 中，修改資料夾屬性或使用 BitLocker。

1.  開啟 [命令提示字元]。
2.  執行下列命令以下載發佈設定檔案。

        azure account download

    ![HDI.CLIAccountDownloadImport][]

    此命令會開啟可下載發行設定檔案的網頁。

3.  出現儲存檔案的提示時，請按一下 [儲存] 並提供檔案必須儲存的位置。
4.  在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

        azure account import <file>

    在上一個螢幕擷取畫面中，發佈設定檔案已儲存至工作站上的 C:\\HDInsight 資料夾。

**建立 Azure 儲存體帳戶**

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存帳號，您才能建立 HDInsight 叢集。儲存帳號必須位於相同的資料中心內。

-   在命令提示字元視窗中執行下列命令，以建立 Azure 儲存體帳戶：

        azure storage account create [options] <StorageAccountName>

    出現位置提示時，請選取可佈建 HDINsight 叢集的位置。儲存體必須與 HDInsight 叢集位於相同的位置。目前只有「東亞」、「東南亞」、「北歐」、「西歐」、「美國東部」、「美國西部」、「美國中北部」和「美國中南部」等區域可以代管 HDInsight 叢集。

如需使用 Azure 管理入口網站建立 Azure 儲存帳號的相關資訊，請參閱[如何建立儲存帳號][]。

如果您已經有儲存體帳戶，但不知道帳戶名稱和帳戶金鑰，則您可以使用下列命令來擷取資訊：

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

如需有關使用管理入口網站取得資訊的詳細資訊，請參閱＜*作法：檢視、複製及重新產生儲存體存取金鑰*一節 (位於[如何管理儲存體帳戶][]中)。

HDInsight 叢集還要求儲存體帳戶內要有容器。如果您所提供的儲存體帳戶沒有容器，則 *azure hdinsight cluster create* 會提示您輸入容器名稱並建立容器。不過，如果您想要預先建立容器，您可以使用下列命令：

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

在儲存帳號和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

-   在命令提示字元視窗中執行下列命令：

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][]

**使用組態檔佈建 HDInsight 叢集**

一般而言，您會佈建 HDInsight 叢集、執行工作，然後將此叢集刪除，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次佈建叢集時皆可加以重複使用。

-   在命令提示字元視窗中執行下列命令：

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] 用於 metastore 的 Azure SQL Database 必須允許與其他 Azure 服務 (包括 Azure HDInsight) 的連線能力。在 Azure SQL Database 儀表板中，按一下右側的伺服器名稱。這是指執行 SQL Database 執行個體的伺服器。一旦進入伺服器檢視後，按一下 [設定]，然後在 [Windows Azure 服務] 中，依序按一下 [是] 和 [儲存]。

    ![HDI.CLIClusterCreationConfig][]

**列出並顯示叢集詳細資料**

-   使用下列命令，以列出並顯示叢集詳細資料：

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][]

**刪除叢集**

-   使用下列命令刪除叢集：

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a>使用 HDInsight .NET SDK

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 應用程式使用 HDInsight。

以下是使用 SDK 佈建 HDInsight 叢集時必須執行的程序：

-   安裝 HDInsight .NET SDK
-   建立自我簽署憑證
-   建立主控台應用程式
-   執行應用程式

**安裝 HDInsight .NET SDK**

您可以從 [NuGet][] 安裝最新發佈的 SDK 組建。下一個程序會顯示相關指示。

**建立自我簽署憑證**

1.  建立用來驗證要求的自我簽署憑證。您可以使用 IIS 或 [makecert][] 來建立憑證。

2.  瀏覽至憑證位置，以滑鼠右鍵按一下此憑證，按一下 [安裝憑證] 將憑證安裝到電腦的個人存放區。編輯憑證屬性，並為它指派一個更容易記住的名稱。

3.  將憑證匯入 Azure 管理入口網站。在入口網站中，按一下頁面左下角的 [設定]，然後按一下 [管理憑證]。按一下頁面底部的 [上傳]，並遵照指示將您在前一個步驟中所建立的 .cer 檔案上傳。

    ![HDI.ClusterCreate.UploadCert][]

**建立 Visual Studio 主控台應用程式**

1.  開啟 Visual Studio 2013。

2.  在 [檔案] 功能表中按一下 [新增]，然後按一下 [專案]。

3.  在 [新增專案] 中，輸入或選取下列值：

    | 屬性 | 值                      |
    |------|-------------------------|
    | 類別 | 範本/Visual C#/Windows |
    | 範本 | 主控台應用程式          |
    | 名稱 | CreateHDICluster        |

4.  按一下 [確定] 以建立專案。

5.  在 [工具] 功能表中按一下 [Nuget 套件管理員]，然後按一下 [Package Manager Console]。

6.  在主控台中執行下列命令，以安裝封裝。

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    此命令會將 .NET 程式庫及其參考加入至目前的 Visual Studio 專案。

7.  在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8.  在檔案頂端新增下列 using 陳述式：

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  在 Main() 函數中，複製並貼上下列程式碼：

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. 取代 main() 函數開頭處的變數。

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗會開啟並顯示應用程式的狀態。建立 HDInsight 叢集可能需要幾分鐘的時間。

## <span id="nextsteps"></span></a>後續步驟

在本文中，您學到幾種佈建 HDInsight 叢集的方法。若要深入了解，請參閱下列文章：

-   [Azure HDInsight 使用者入門][開始使用 Azure HDInsight]
-   [使用 PowerShell 管理 HDInsight][使用 PowerShell 來管理 HDInsight]
-   [以程式設計方式提交 Hadoop 工作][]
-   [Azure HDInsight SDK 文件][]

  [在 HDInsight 上佈建 HBase 叢集]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [Hadoop 和 HBase 之間的差別？]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [HDInsight 中 Hadoop 的簡介]: ../hdinsight-introduction/
  [開始使用 Azure HDInsight]: ../hdinsight-get-started/
  [購買選項]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [成員優惠]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [免費試用]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [使用 Azure 管理入口網站]: #portal
  [使用 Azure PowerShell]: #powershell
  [使用跨平台命令列]: #cli
  [使用 HDInsight .NET SDK]: #sdk
  [後續步驟]: #nextsteps
  [搭配 HDInsight 使用 Azure Blob 儲存體]: ../hdinsight-use-blob-storage/
  [如何建立儲存帳號]: ../storage-create-storage-account/
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [安裝並設定 Azure PowerShell]: ../install-configure-powershell/
  [使用 PowerShell 來管理 HDInsight]: ../hdinsight-administer-use-powershell/
  [HDInsight Cmdlet 參考文件]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: ../xplat-cli/
  [適用於 Mac 和 Linux 的 Azure 命令列工具]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [如何管理儲存體帳戶]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [makecert]: http://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-get-started/HDI.ClusterCreate.UploadCert.png
  [以程式設計方式提交 Hadoop 工作]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight SDK 文件]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
