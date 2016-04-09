<properties
   pageTitle="使用 Azure Data Factory 在 HDInsight 中建立以 Linux 為基礎的隨選 Handooop 叢集 | Microsoft Azure"
   	description="了解如何使用 Azure Data Factory 建立隨選 HDInsight 叢集。"
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
   ms.date="03/16/2016"
   ms.author="jgao"/>

# 使用 Azure Data Factory 在 HDInsight 中建立以 Linux 為基礎的隨選 Handooop 叢集

[AZURE.INCLUDE [選取器](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。在本文中，您將學習如何使用 Azure Data Factory 建立 [Azure HDInsight 隨選連結服務](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)，並使用叢集來執行 Hive 作業。以下是高階流程︰

1. 建立隨選 HDInsight 叢集。
2. 執行 Hive 作業，以從來源 Blob 儲存體帳戶讀取原始 Web 記錄資料、轉換資料，並將輸出寫入至目的地 Blob 儲存體帳戶。 
3. 根據存留時間設定刪除叢集。

Data Factory 管線中定義的 Hive 活動會呼叫預先定義的 HiveQL 指令碼。指令碼會建立一個參照儲存在 Azure Blob 儲存體中的原始 Web 記錄資料的外部資料表，再依年份或月份分割原始資料。

這裡是輸入檔中每個月份的資料列範例。

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

此指令碼會根據先前的輸入建立三個輸出資料夾。每個資料夾都包含一個檔案，內含每個月的項目。

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

如需 Data Factory 資料轉換活動 (Hive 活動除外) 的清單，請參閱[使用 Azure Data Factory 進行轉換和分析](../data-factory/data-factory-data-transformation-activities.md)。

搭配使用 HDInsight 與 Data Factory 有許多優點：

- 不論使用與否，HDInsight 叢集皆會按小時計費。使用 Data Factory，依照需求建立叢集。而叢集會在作業完成時自動刪除。所以您只需對作業執行時間和短暫閒置時間 (存留時間) 付費。
- 您可以使用 Data Factory 管線建立工作流程。
- 您可以排程遞迴作業。  

##必要條件：

開始執行本文中的指示之前，您必須擁有以下項目：

- [Azure 訂用帳戶](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- [Azure CLI](../xplat-cli-install.md) 或 [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater)。 

##準備儲存體帳戶

在此案例中，您最多可以使用三個儲存體帳戶︰

- HDInsight 叢集的預設儲存體帳戶
- 輸入資料的儲存體帳戶
- 輸出資料的儲存體帳戶

為了簡化本教學課程，您會將一個儲存體帳戶用於 3 個用途。本節中找到的 Azure CLI 和 Azure PowerShell 範例指令碼會執行下列作業：

1. 登入 Azure。
2. 建立 Azure 資源群組。
3. 建立 Azure 儲存體帳戶。
4. 在儲存體帳戶上建立 Blob 容器
5. 將下列兩個檔案複製到 Blob 容器︰

    - 輸入資料檔：[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - HiveQL 指令碼：[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    這兩個檔案會儲存在公用 Blob 容器。

>[AZURE.IMPORTANT] 記下您的指令碼中使用的資源群組名稱、儲存體帳戶名稱和儲存體帳戶金鑰。您會在下一節中用到這些資料。

**使用 Azure CLI 準備處存體並複製檔案**

    azure login

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

容器名稱為 adfgetstarted。請讓它保持原狀。否則，您必須更新 ARM 範本。

如需有關此 CLI 指定碼的說明，請參閱[使用 Azure CLI 搭配 Azure 儲存體](../storage/storage-azure-cli.md)。

**使用 Azure PowerShell 準備處存體並複製檔案**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName |  %{ $_.Key1 }

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

如需有關此 PowerShell 指定碼的說明，請參閱[使用 Azure PowerShell 搭配 Azure 儲存體](../storage/storage-powershell-guide-full.md)。

**檢查儲存體帳戶和內容**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側面板上的 [資源群組]。
3. 按兩下您在 CLI 或 PowerShell 指令碼中建立的資源群組名稱。如果列出太多的資源群組，請使用篩選器。 
4. 除非您與其他專案共用資源群組，否則 [資源] 圖格應列出一個資源。這就是您先前指定名稱的儲存體帳戶。按一下儲存體帳戶名稱。
5. 按一下 [Blob] 圖格。
6. 按一下 [adfgetstarted] 容器。您會看到兩個資料夾︰[輸入資料] 和 [指令碼]。
7. 開啟資料夾並檢查資料夾中的檔案。
 
## 建立 Data Factory

備妥儲存體帳戶、輸入資料和 HiveQL 指令碼，您就準備好建立 Azure Data Factory。有數種方法可建立 Data Factory。在本教學課程中，您將使用 Azure 入口網站呼叫自訂 ARM 範本。您也可以從 [Azure CLI](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) 和 [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell) 呼叫 ARM 範本。如需其他 Data Factory 建立方法，請參閱[教學課程︰建立您的第一個 Data Factory](../data-factory/data-factory-build-your-first-pipeline.md)。

最上層的 ARM 範本包含︰

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

它包含一個稱為 hdinsight-hive-on-demand 的 Data Factory 資源 (此名稱不會顯示在螢幕擷取畫面上)。目前只有美國西部區域和北歐地區支援 Data Factory。

hdinsight-hive-on-demand 資源包含 4 個資源︰

- 將做為預設 HDInsight 儲存體帳戶、輸入資料儲存體和輸出資料儲存體之儲存體帳戶的連結服務。
- 即將建立之 HDInsight 叢集的連結服務：

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    即使尚未指定，也會在與儲存體帳戶相同的區域中建立叢集。
    
    請注意 timeToLive 設定。Data Factory 會在叢集閒置 30 分鐘後自動刪除叢集。
- 輸入資料的資料集。檔案名稱和資料夾名稱的定義如下︰

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- 輸出資料的資料集，然後是用於資料處理的管線。輸出路徑的定義如下︰
        
        "folderPath": "adfgetstarted/partitioneddata",

    [資料集可用性](../data-factory/data-factory-create-datasets.md#Availability)設定如下︰
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    在 Azure Data Factory 中，輸出資料集可用性會推動管線。這表示每個月會在當月的最後一天產生配量。如需詳細資訊，請參閱 [Data Factory 排程和執行](../data-factory/data-factory-scheduling-and-execution.md)。

    管線定義如下所示︰
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    它包含一個活動。活動的開始和結束都在過去的日期，這表示只會有一個配量。如果結束為未來日期，則 Data Factory 屆時會建立另一個配量。如需詳細資訊，請參閱 [Data Factory 排程和執行](../data-factory/data-factory-scheduling-and-execution.md)。

    活動定義如下︰
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    已定義輸入、輸出和指令碼路徑。
    
**建立 Data Factory**

1. 按一下以下影像，在 Azure 入口網站中登入 Azure 並開啟 ARM 範本。此範本位於 https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/zh-TW/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 為您在上一節中建立的帳戶輸入 [DATAFACTORYNAME]、[STORAGEACCOUNTNAME] 和 [STORAGEACCOUNTKEY]，然後按一下 [確定]。[Data Factory 名稱] 必須是全域唯一的。
3. 在 [資源群組] 中，選取您在上一節中使用的相同資源群組。
4. 按一下 [法律條款]，然後按一下 [建立]。
5. 按一下 [建立]。您將會在儀表板上看到名稱為 [進行範本部署] 的圖格。請等到圖格文字變更為資源群組名稱。建立 HDInsight 叢集通常需要大約 20 分鐘的時間。
6. 按一下圖格即可開啟資源群組。除了儲存體帳戶資源，您現在應該會看到另外列出一個 Data Factory 資源。
7. 按一下 [hdinsight-hive-on-demand]。
8. 按一下 [圖表] 圖格。此圖顯示一個具有輸入資料集與輸出資料集的活動︰

    ![Azure Data Factory HDInsight 隨選 Hive 活動管線圖](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    名稱已定義於 ARM 範本中。
9. 按兩下 [AzureBlobOutput]。
10. 在 [最近更新的配量] 上，您應該會看到一個配量。如果狀態為 [進行中]，請等到其變更為 [就緒]。

檢查 Data Factory 輸出

1. 使用最後一個工作階段中的相同程序來檢查 adfgetstarted 容器的內容。除了 adfgetsarted，有兩個新容器：

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx︰這是 HDInsight 叢集的預設容器。預設容器名稱的模式如下："adf>yourdatafactoryname>-linkedservicename-datetimestamp"。 
    - adfjobs︰這是 ADF 作業記錄檔的容器。
    
    如同您在 ARM 範本中所設定，Data Factory 輸出會儲存在 afgetstarted 中。 
2. 按一下 [adfgetstarted]。
3. 按兩下 [partitioneddata]。您會看到 year=2014 資料夾，因為所有 Web 記錄檔的日期皆為 2014 年。 

    ![Azure Data Factory HDInsight 隨選 Hive 活動管線輸出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    如果您向下鑽研清單，您會看到一月、二月和三月的 3 個資料夾。而且每個月都有記錄檔。

    ![Azure Data Factory HDInsight 隨選 Hive 活動管線輸出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##清除教學課程

使用 HDInsight 隨選連結服務時，除非有現有的即時叢集 (timeToLive)，否則每當需要處理配量時，就會建立 HDInsight 叢集，並在處理完成時刪除此叢集。對於每個叢集，Azure Data Factory 會建立 Azure Blob 儲存體以做為叢集的預設檔案系統。即使已刪除 HDInsight 叢集，但不會刪除預設 Blob 儲存體容器和相關聯的儲存體帳戶。原先的設計就是如此。隨著處理的配量越來越多，您會在 Azure Blob 儲存體中看到許多容器。如果在疑難排解作業時不需要這些容器，建議您加以刪除以降低儲存成本。這些容器的名稱遵循下列模式："adfyourdatafactoryname-linkedservicename-datetimestamp"。

[Azure Resource Manager](../resource-group-overview.md) 用來以群組方式部署、管理及監視您的解決方案。刪除資源群組將會刪除群組內的所有元件。

**刪除資源群組**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側面板上的 [資源群組]。
3. 按兩下您在 CLI 或 PowerShell 指令碼中建立的資源群組名稱。如果列出太多的資源群組，請使用篩選器。這會在新的刀鋒視窗中開啟資源群組。
4. 除非您與其他專案共用資源群組，否則 [資源] 圖格應列出預設儲存體帳戶和 Data Factory。
5. 按一下刀鋒視窗最上方的 [刪除]。這麼做，您也會刪除儲存體帳戶和此儲存體帳戶中儲存的資料。
6. 輸入資源群組名稱，然後按一下 [刪除]。

如果您不想在刪除資源群組時刪除儲存體帳戶，您可以考慮以下區隔商務資料與預設儲存體帳戶的架構設計。在此情況下，您會有一個資源群組用於包含商務資料的儲存體帳戶，而另一個資源群組則用於預設儲存體帳戶和 Data Factory。當您刪除第二個資源群組時，並不會影響商務資料儲存體帳戶。若要這樣做：

- 將下列程式碼以及 ARM 範本中的 Microsoft.DataFactory/datafactories 資源加入至最上層資源群組。這會建立新的儲存體帳戶：

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- 將新的連結服務點加入至新的儲存體帳戶︰

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- 使用其他 dependsOn 和 additionalLinkedServiceNames 設定 HDInsight 隨選連結服務︰

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##後續步驟
在本文中，您已學會如何使用 Azure Data Factory 來建立隨選 HDInsight 叢集來處理 Hive 作業。若要閱讀更多資訊︰

- [Hadoop 教學課程：開始在 HDInsight 中使用以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)
- [在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [HDInsight 文件](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Data Factory 文件](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0316_2016-->