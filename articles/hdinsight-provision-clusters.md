<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision HDInsight clusters | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision HDInsight clusters" authors="jgao" />

佈建 HDInsight 叢集
===================

在本文中，您將了解佈建 HDInsight 叢集的不同方式。

**必要條件：**

開始進行本文內容之前，您必須具備下列項目：

-   Azure 訂閱。Azure 是訂閱型平台。HDInsight PowerShell Cmdlet 會為您的訂閱執行相關工作。如需取得訂閱的詳細資訊，請參閱[購買選項](https://www.windowsazure.com/en-us/pricing/purchase-options/)、[成員優惠](https://www.windowsazure.com/en-us/pricing/member-offers/)或[免費試用](https://www.windowsazure.com/en-us/pricing/free-trial/)。

本文內容
--------

-   [使用 Azure 管理入口網站](#portal)
-   [使用 Azure PowerShell](#powershell)
-   [使用跨平台命令列](#cli)
-   [使用 HDInsight .NET SDK](#sdk)
-   [後續步驟](#nextsteps)

使用 Azure 管理入口網站
-----------------------

HDInsight 叢集會使用 Azure Blob 儲存容器作為預設檔案系統。相同的資料中心上必須要有 Azure 儲存帳號，您才能建立 HDInsight 叢集。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體](/en-us/manage/services/hdinsight/howto-blob-store/)。如需建立 Azure 儲存帳號的詳細資訊，請參閱[如何建立儲存帳號](/en-us/manage/services/storage/how-to-create-a-storage-account/)。

> [WACOM.NOTE] 目前只有東南亞、北歐、西歐、美國東部和美國西部等地區可代管 HDInsight 叢集。

本文將討論使用自訂建立選項來建立 HDInsight 叢集的程序。如需使用快速建立選項的相關資訊，請參閱 [Azure HDInsight 使用者入門](/en-us/manage/services/hdinsight/get-started-hdinsight/)。

**使用自訂建立選項建立 HDInsight 叢集**

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2.  按一下頁面底部的 **[+新增]**，然後依序按一下 **[資料服務]**、**[HDInsight]**、**[自訂建立]**。
3.  在 [Cluster Details] 頁面上，輸入或選擇下列值：

     <table  border="1">
    <tr><th>屬性</th>
    <th>值</th>
    </tr>
    
    <tr><td>叢集名稱</td>
    
    <td><p>為叢集命名。 </p>
    
                 <ul>
    <li>DNS 名稱的開頭與結尾都必須是英數字元，名稱中可包含連字號。</li>
    
    <li>欄位必須是 3 到 63 個字元的字串。</li>
    
                 </ul>
    </td>
    </tr>
    
    <tr><td>資料節點</td>
    
    <td>指定叢集中的節點數。預設值為 4。</td>
    </tr>
    
    <tr><td>HDINSIGHT 版本</td>
    
    <td>選擇版本。預設值為執行 Hadoop 1.2 叢集的 2.0 版。3.0 版會使用 Hadoop 2.2 叢集。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/en-us/manage/services/hdinsight/versioning-in-hdinsight/">Azure HDInsight 提供 Hadoop 的什麼版本？</a>
     (英文)。</td>
    </tr>
    
    <tr><td>區域</td>
    
    <td>指定叢集安裝所在的資料中心。此位置必須與將作為預設檔案系統的 Azure Blob 儲存體相同。目前您只能選擇「東南亞」、「北歐」、「西歐」、「美國東部」或「美國西部」。</td>
    
         </tr>
    
     </table>

    ![HDI.CustomProvision.Page1](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png)

4.  按一下頁面右下角的向右箭頭。
5.  在 [Configure Cluster User] 頁面上，輸入或選擇下列值：

    <table  border="1">
    <tr><th>屬性</th>
    <th>值</th>
    </tr>
    
    <tr><td>使用者名稱</td>
    
    <td>指定 HDInsight 叢集使用者名稱。</td>
    </tr>
    
    <tr><td><p>密碼</p>
    <p>確認密碼</p>
    </td>
    
    <td>指定 HDInsight 叢集使用者密碼。</td>
    </tr>
    
    <tr><td>輸入 Hive/Oozie Metastore</td>
    
    <td>在相同的資料中心上指定要作為 Hive/Oozie metastore 的 SQL 資料庫。</td>
    </tr>
    
    <tr><td>HIVE META/OOZIESTORE 資料庫</td>
    
    <td>指定要作為 Hive/OOzie metastore 的 Azure SQL 資料庫。此 SQL 資料庫必須與 HDInsight 叢集位於相同的資料中心內。清單方塊只會列出您在 [Cluster Details] 頁面上指定之資料中心內的 SQL 資料庫。</td>
    </tr>
    
    <tr><td>資料庫使用者</td>
    
    <td>指定將用來連接到資料庫的 SQL 資料庫使用者。</td>
    </tr>
    
    <tr><td>資料庫使用者密碼</td>
    
    <td>指定 SQL 資料庫使用者密碼。</td>
    </tr>
    
     </table>
    
    就 2.0 版 HDInsight 叢集而言，此處提供的認證只能存取叢集上的服務。遠端桌面可在在叢集建立後開啟。
    

    ![HDI.CustomProvision.Page2](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

6.  按一下頁面右下角的向右箭頭。
7.  在 [儲存帳號] 頁面上，輸入或選擇下列值：

    <table  border="1">
    <tr><th>屬性</th>
    <th>值</th>
    </tr>
    
    <tr><td>儲存體帳戶</td>
    
    <td>指定將作為 HDInsight 叢集之預設檔案系統的 Azure 儲存帳號。您可以從三個選項中擇一使用：
             <ul>
    <li>使用現有儲存體</li>
    
    <li>建立新的儲存體</li>
    
    <li>使用其他訂閱的儲存體</li>
    
             </ul>
    
             </td>
    </tr>
    
    <tr><td>帳號名稱</td>
    
    <td>選擇 [Use Existing Storage]<b  />
     時，清單方塊只會列出位於相同資料中心上的儲存帳號。</td>
    </tr>
    
    <tr><td>帳號金鑰</td>
    
    <td>只有在 [儲存帳號] 欄位中選擇了 [Use Storage From Another Subscription]<strong  />
     時，才可使用此欄位。</td>
    </tr>
    	
    <tr><td>預設容器</td>
    
    <td>儲存帳號上的預設容器將會作為 HDInsight 叢集的預設檔案系統。如果在 [儲存帳號] 欄位中選擇了 [Use Existing Storage]<strong  />
    ，並在 [預設容器] 欄位中選擇了 [建立預設容器]<strong  />
    ，預設容器的名稱將會與叢集相同。如果已有使用叢集名稱的容器存在，則會為容器名稱加上序號。例如 mycontainer1、mycontainer2，依此類推。</td>
    </tr>
    
    <tr><td>附加儲存帳號</td>
    
    <td>HDInsight 支援多個儲存帳號。叢集可使用的附加儲存帳號沒有數量上的限制。但如果您使用管理入口網站建立叢集，則會因為 UI 的限制而限定為七個帳號。您在此欄位中指定的每個附加儲存帳號都會另外新增一個 [儲存帳號] 頁面，供您指定帳號資訊。以下列螢幕擷取畫面為例，在選取一個附加儲存帳號後，對話方塊中即新增了頁面 4。</td>
    </tr>
    		
     </table>

    ![HDI.CustomProvision.Page3](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

8.  按一下頁面右下角的向右箭頭。
9.  在 [儲存帳號] 頁面上，輸入附加儲存帳號的帳號資訊：

    ![HDI.CustomProvision.Page4](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

10. 按一下位於右下角的 [完成] 按鈕 (勾號圖示)，以啟動 HDInsight 叢集佈建程序。

佈建叢集可能需要幾分鐘的時間。佈建程序順利完成時，叢集的狀態欄將顯示為 **[執行中]**。

> [WACOM.NOTE] 在您為 HDInsight 叢集選擇 Azure 儲存帳號後，您將無法刪除帳號，也無法將帳號變更為不同的帳號。

使用 Azure PowerShell
---------------------

Azure PowerShell 是一個功能強大的指令碼環境，可用來控制及自動化您在 Azure 中的工作負載部署和管理。如需設定工作站以執行 HDInsight Powershell Cmdlet 的相關資訊，請參閱[安裝並設定 Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/)。如需搭配使用 PowerShell 與 HDInsight 的詳細資訊，請參閱[使用 PowerShell 管理 HDInsight](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)。如需 HDInsight PowerShell Cmdlet 的清單，請參閱 [HDInsight Cmdlet 參考文件](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx)。

以下是使用 PowerShell 佈建 HDInsight 叢集時所需執行的程序：

-   建立 Azure 儲存體帳戶
-   建立 Azure Blob 容器
-   建立 HDInsight 叢集

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存帳號和儲存容器，您才能建立 HDInsight 叢集。儲存帳號必須與 HDInsight 叢集位於相同的資料中心。

**建立 Azure 儲存帳號**

-   從 Azure PowerShell 主控台視窗執行下列命令：

          $storageAccountName = "<StorageAcccountName>"
        $location = "<MicrosoftDataCenter>"       # For example, "West US"
            
        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列 PowerShell 命令來擷取資訊：

          # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**建立 Azure 儲存容器**

-   從 Azure PowerShell 視窗執行下列命令：

          $storageAccountName = "<StorageAccountName>"
        $storageAccountKey = "<StorageAccountKey>"
        $containerName="<ContainerName>"

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
        -StorageAccountKey $storageAccountKey  
             
        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

在儲存帳號和 Blob 容器準備就緒後，您即可建立叢集。

**佈建 HDInsight 叢集**

-   從 Azure PowerShell 視窗執行下列命令：

          $subscriptionName = "<SubscriptionName>"     # The name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"  # The Azure storage account that hosts the default container.The default container will be used as the default file system.
        $containerName = "<ContainerName>"                # The Azure Blob storage container that will be used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"           # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"               # The location of the HDInsight cluster.It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    叢集佈建可能需要幾分鐘的時間。

    ![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

您也可以佈建叢集並加以設定，使其連接到多個 Azure Blob 儲存體或自訂的 Hive 和 Oozie metastore。這項進階功能可讓您區隔資料和中繼資料的存留期與叢集的存留期。

**使用組態佈建 HDInsight 叢集**

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
        #$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        #New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
            
        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
        Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
        Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
        Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
        Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
        New-AzureHDInsightCluster -Name $clusterName -Location $location

**列出 HDInsight 叢集**

-   從 Azure PowerShell 視窗執行下列命令：

          Get-AzureHDInsightCluster -Name <ClusterName>

使用跨平台命令列
----------------

另一個佈建 HDInsight 叢集的選項，是跨平台命令列介面。此命令列工具會在 Node.js 中實作。此工具可在任何支援 Node.js 的平台上使用，包括 Windows、Mac 和 Linux。此命令列工具採用開放原始碼。原始程式碼會在 GitHub 中受到管理 (<https://github.com/WindowsAzure/azure-sdk-tools-xplat>)。如需如何使用此命令列介面的一般指引，請參閱[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具](/en-us/develop/nodejs/how-to-guides/command-line-tools/)。如需完整的參考文件，請參閱[適用於 Mac 和 Linux 的 Azure 命令列工具](/en-us/manage/linux/other-resources/command-line-tools/)。本文僅提供從 Windows 使用此命令列介面的相關資訊。

以下是使用跨平台命令列佈建 HDInsight 叢集時所需執行的程序：

-   安裝跨平台命令列
-   下載及匯入 Azure 帳號發佈設定
-   建立 Azure 儲存體帳戶
-   佈建叢集

此命令列介面可使用 *Node.js 封裝管理員 (NPM)* 或 Windows Installer 進行安裝。

**使用 NPM 安裝命令列介面**

1.  瀏覽至 **www.nodejs.org**。
2.  按一下 **[安裝]**，並依照指示使用預設設定操作。
3.  從您的工作站開啟 **[命令提示字元]** (或是 *Azure 命令提示字元*或 *VS2012 開發人員命令提示字元*)。
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

1.  瀏覽至 **http://www.windowsazure.com/en-us/downloads/**。
2.  向下捲動至 **[命令列工具]** 區段，然後按一下 **[跨平台命令列介面]**，並依照 Web Platform Installer 精靈操作。

使用命令列介面之前，您必須先設定工作站與 Azure 之間的連線。命令列介面會使用您的 Azure 訂閱資訊連接到您的帳號。這項資訊可從 Azure 的發佈設定檔案取得。接著，發佈設定檔案可匯入為持續性的本機組態設定，供命令列介面用於後續的作業。您的發佈設定只需匯入一次即可。

> [WACOM.NOTE] 發佈設定檔案包含敏感資訊。建議您刪除此檔案，或另採相關步驟加密包含此檔案的使用者資料夾。在 Windows 中，修改資料夾屬性或使用 BitLocker。

**下載及匯入發佈設定**

1.  開啟 **[命令提示字元]**。
2.  執行下列命令以下載發佈設定檔案。

        azure account download

    ![HDI.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png)

    此命令會顯示下載檔案的指示，包括 URL。

3.  開啟 **Internet Explorer**，然後瀏覽至命令提示字元視窗中列出的 URL。
4.  按一下 **[儲存]**，將檔案儲存至工作站。
5.  在命令提示字元視窗中執行下列命令，以匯入發佈設定檔案：

        azure account import <file>

    在上一個螢幕擷取畫面中，發佈設定檔案已儲存至工作站上的 C:\\HDInsight 資料夾。

HDInsight 會使用 Azure Blob 儲存容器作為預設檔案系統。必須要有 Azure 儲存帳號，您才能建立 HDInsight 叢集。儲存帳號必須位於相同的資料中心內。

**建立 Azure 儲存帳號**

-   在命令提示字元視窗中執行下列命令：

          azure account storage create [options] <StorageAccountName>

如需使用 Azure 管理入口網站建立 Azure 儲存帳號的相關資訊，請參閱[如何建立儲存帳號](/en-us/manage/services/storage/how-to-create-a-storage-account/)。

如果您已有儲存帳號，但不知道帳號名稱和帳號金鑰，您可以使用下列命令來擷取資訊：

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

如需使用管理入口網站取得資訊的詳細資訊，請參閱[如何管理儲存帳號](/en-us/manage/services/storage/how-to-manage-a-storage-account/)的*作法：檢視、複製及重新產生儲存體存取金鑰*一節。

*azure hdinsight cluster create* 命令會建立容器 (如果不存在)。如果您選擇預先建立容器，您可以使用下列命令：

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

在儲存帳號和 Blob 容器準備就緒後，您即可建立叢集。

**建立 HDInsight 叢集**

-   在命令提示字元視窗中執行下列命令：

          azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation](./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png)

一般而言，您會佈建 HDInsight 叢集並執行其工作之後刪除叢集，以降低成本。此命令列介面可讓您選擇將組態儲存至檔案，以便您在每次佈建叢集時皆可加以重複使用。

**使用組態檔佈建 HDInsight 叢集**

-   在命令提示字元視窗中執行下列命令：

          azure hdinsight cluster config create <file>
             
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
             
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
        --storageAccountKey "<StorageAccountKey>"
             
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
        --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
             
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
        --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
             
        azure hdinsight cluster create --config <file>

    ![HDI.CLIClusterCreationConfig](./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png)

**列出並顯示叢集詳細資料**

-   使用下列命令，以列出並顯示叢集詳細資料：

          azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster](./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters")

**刪除叢集**

-   使用下列命令刪除叢集：

          azure hdinsight cluster delete <ClusterName>

使用 HDInsight .NET SDK
-----------------------

HDInsight .NET SDK 所提供的 .NET 用戶端程式庫，可讓您輕鬆地從 .NET 使用 HDInsight。

以下是使用 SDK 佈建 HDInsight 叢集時所需執行的程序：

-   安裝 HDInsight .NET SDK
-   建立主控台應用程式
-   執行應用程式

**安裝 HDInsight .NET SDK** 您可以從 [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) 安裝最新發佈的 SDK 組建。相關指示將說明於下列程序中。

**建立 Visual Studio 主控台應用程式**

1.  開啟 Visual Studio 2012。

2.  在 [檔案] 功能表中按一下 **[新增]**，然後按一下 **[專案]**。

3.  在 [新增專案] 中，輸入或選取下列值：

    <table  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
     <tr>
    <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">屬性</th>
    
    <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">值</th>
    </tr>
    
     <tr>
    <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">類別</td>
    
    <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">範本/Visual C#/Windows</td>
    </tr>
    
     <tr>
    <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">範本</td>
    
    <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">主控台應用程式</td>
    </tr>
    
     <tr>
    <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名稱</td>
    
    <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td>
    </tr>
    
     </table>

4.  按一下 **[確定]** 以建立專案。

5.  在 **[工具]** 功能表中按一下 **[Library Package Manager]**，然後按一下 **[Package Manager Console]**。

6.  在主控台中執行下列命令，以安裝封裝。

         Install-Package Microsoft.WindowsAzure.Management.HDInsight

    此命令會將 .NET 程式庫及其參考新增至目前的 Visual Studio 專案。

7.  在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。

8.  在檔案頂端新增下列 using 陳述式：

         using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  在 Main() 函數中，複製並貼上下列程式碼：

         string certfriendlyname = "<CertificateFriendlyName>";
        string subscriptionid = "<WindowsAzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
        string storageaccountkey = "<WindowsAzureStorageAccountKey>";
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

        Console.WriteLine("Created cluster:{0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. 取代 main() 函數開頭處的變數。

**執行應用程式**

在 Visual Studio 中開啟應用程式後，按 **F5** 以執行應用程式。主控台視窗應會開啟並顯示應用程式的狀態。建立 HDInsight 叢集可能需要幾分鐘的時間。

後續步驟
--------

在本文中，您已了解數種佈建 HDInsight 叢集的方式。若要深入了解，請參閱下列文章：

-   [Azure HDInsight 使用者入門](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [使用 PowerShell 管理 HDInsight](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [以程式設計方式提交 Hadoop 工作](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Azure HDInsight SDK 文件](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

