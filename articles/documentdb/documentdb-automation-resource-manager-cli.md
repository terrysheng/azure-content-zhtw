<properties
	pageTitle="DocumentDB 自動化 - 資源管理員 - CLI |Microsoft Azure"
	description="使用 Azure 資源管理員範本或 CLI 來部署 DocumentDB 資料庫帳戶。DocumentDB 是適用於 JSON 資料的雲端式 NoSQL 資料庫。"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="mimig"/>

# 使用 Azure 資源管理員範本和 Azure CLI 自動建立 DocumentDB 帳戶

> [AZURE.SELECTOR]
- [Azure 入口網站](documentdb-create-account.md)
- [Azure CLI 和 ARM](documentdb-automation-resource-manager-cli.md)

本文將說明如何使用 Azure 資源管理員範本或 Azure 命令列介面 (CLI) 來建立 DocumentDB 帳戶。若要使用 Azure 入口網站建立 DocumentDB 帳戶，請參閱[使用 Azure 入口網站建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)。

- [使用 CLI 建立 DocumentDB 帳戶](#quick-create-documentdb-account)
- [使用 ARM 範本建立 DocumentDB 帳戶](#deploy-documentdb-from-a-template)

DocumentDB 資料庫帳戶是目前唯一可以使用 ARM 範本和 Azure CLI 建立的 DocumentDB 資源。

## 準備就緒

在您能夠搭配 Azure 資源群組使用 Azure CLI 之前，必須備妥正確的 Azure CLI 版本以及 Azure 帳戶。如果您沒有 Azure CLI，請[安裝它](../xplat-cli-install.md)。

### 更新 Azure CLI 版本

在命令提示字元中輸入 `azure --version`，即可以查看您是否已經安裝 0.9.11 版或更新版本。

	azure --version
    0.9.11 (node: 0.12.7)

如果您的版本不是 0.9.11 或更新版本，就必須[安裝 Azure CLI](../xplat-cli-install.md)，或者使用其中一個原生安裝程式來更新，或是透過 **npm** 輸入 `npm update -g azure-cli` 來更新或輸入 `npm install -g azure-cli` 來安裝。

### 設定 Azure 帳戶和訂用帳戶

如果您還沒有 Azure 訂用帳戶，但是有 Visual Studio 訂用帳戶，請啟用您的 [Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。或者申請[免費試用](https://azure.microsoft.com/pricing/free-trial/)。

您需要有公司帳戶或學校帳戶或是 Microsoft 帳戶識別碼 ，才能使用 Azure 資源管理範本。如果您有這其中一個帳戶，請輸入下列命令。

	azure login

這會產生下列輸出：

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate. If you're signing in as an Azure
    AD application, use the --username and --password parameters.

> [AZURE.NOTE] 如果您沒有 Azure 帳戶，就會看到錯誤訊息，指出您需要不同類型的帳戶。若要從目前的 Azure 帳戶建立一個帳戶，請參閱[在 Azure Active Directory 中建立工作或學校身分識別](../virtual-machines/virtual-machines-windows-create-aad-work-id.md)。

在瀏覽器中開啟 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然後輸入命令輸出中提供的代碼。

![顯示 Microsoft Azure CLI 之裝置登入畫面的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

一旦輸入代碼之後，請選取您想要在瀏覽器中使用的身分識別，並視需要提供您的使用者名稱和密碼。

![顯示可在其中選取與您想要使用之 Azure 訂用帳戶相關聯的 Microsoft 身分識別帳戶的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

當您成功登入之後，將會收到下列確認畫面，接著就能關閉瀏覽器視窗。

![顯示確認登入 Microsoft Azure 跨平台命令列介面的螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

命令殼層也會提供下列輸出。

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OKK

除了此處所述的互動式登入方法之外，還有一些其他的 Azure CLI 登入方法可供使用。如需其他方法的詳細資訊以及處理多個訂用帳戶的相關資訊，請參閱[從 Azure 命令列介面 (Azure CLI) 連接到 Azure 訂用帳戶](../xplat-cli-connect.md)。

### 切換至 Azure CLI 資源群組模式

根據預設，Azure CLI 會在服務管理模式 (**asm** 模式) 下啟動。輸入下列內容以切換至資源群組模式。

	azure config mode arm

這會提供下列輸出：

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

要切換回預設的命令，可以輸入 `azure config mode asm`。

## <a id="quick-create-documentdb-account"></a>工作：使用 Azure CLI 建立 DocumentDB 帳戶

利用本節中的指示，使用 Azure CLI 來建立 DocumentDB 帳戶。

### 步驟 1：建立或擷取您的資源群組

若要建立 DocumentDB 帳戶，您首先需要資源群組。如果您已經知道想要使用的資源群組名稱，則跳至[步驟 2](#create-documentdb-account-cli)。

若要檢閱您目前所有的資源群組清單，請執行下列命令，並記下您想要使用的資源群組名稱：

    azure group list

若要建立新的資源群組，請執行下列命令、指定要建立的新資源群組名稱，以及要在其中建立資源群組的區域：

	azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。 
 - `<resourcegrouplocation>` 必須是已正式推出 DocumentDB 的其中一個區域。[Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

範例輸入：

	azure group create new_res_group westus

這會產生下列輸出：

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

如果您遇到錯誤，請參閱[疑難排解](#troubleshooting)。

### <a id="create-documentdb-account-cli"></a>步驟 2：使用 CLI 建立 DocumentDB 帳戶

在命令提示字元中輸入下列命令，於新的或現有的資源群組中建立 DocumentDB 帳戶：

> [AZURE.TIP] 如果您在 Azure PowerShell 或 Windows PowerShell 中執行此命令，將會收到關於未預期之權杖的錯誤。請改為在 Windows 命令提示字元中執行此命令。

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

 - `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。 
 - `<databaseaccountname>` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。
 - `<databaseaccountlocation>` 必須是已正式推出 DocumentDB 的其中一個區域。[Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

範例輸入：

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

若已佈建新的帳戶，這將會產生下列輸出：

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

如果您遇到錯誤，請參閱[疑難排解](#troubleshooting)。

在此命令傳回之後，在帳戶變更為**線上**狀態以準備好可供使用之前，該帳戶將會進入**正在建立**狀態數分鐘的時間。您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上檢查帳戶的狀態。

## <a id="deploy-documentdb-from-a-template"></a>工作：使用 ARM 範本建立 DocumentDB 帳戶

使用本節中的指示，利用 Azure 資源管理員 (ARM) 範本和選擇性參數檔案 (這兩者都是 JSON 檔案) 來建立 DocumentDB 帳戶。使用範本可讓您確實說明所需的資訊，並可重複使用而不會出現任何錯誤。

### 了解 ARM 範本和資源群組

大部分的應用程式在建置時會使用不同資源類型的組合 (例如，一或多個 DocumentDB 帳戶、儲存體帳戶、虛擬網路或內容傳遞網路)。預設 Azure 服務管理 API 和 Azure 入口網站可使用 service-by-service 方法代表這些項目。這個方法會要求您部署和個別管理個別服務 (或尋找執行這項作業的其他工具)，而不是做為部署的單一邏輯單元。

您可以利用「Azure 資源管理員範本」，將這些不同的資源宣告為一個邏輯部署單元，然後就能進行部署和管理。請不要以命令方式告訴 Azure 逐一部署命令，您應該在 JSON 檔案描述整個部署過程 -- 所有資源和相關設定以及部署參數 -- 然後告訴 Azure 將這些資源視為一個群組加以部署。

如需深入了解 Azure 資源群組及其功能，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。如果您有興趣了解如何編寫範本，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

### 步驟 1：建立範本和參數檔案

建立含有下列內容的本機範本檔案。將檔案命名為 azuredeploy.json。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

這個範本只需要一個參數：要建立的資料庫帳戶名稱。新資料庫帳戶的位置會設定為與資源群組相同的位置。

由於範本只採用一個參數，因此，您可以在命令列中輸入值，或建立參數檔案來指定值。

若要建立參數檔案，請將下列內容複製到新檔案，然後將檔案命名為 azuredeploy.parameters.json。如果您計劃在命令提示字元中指定資料庫帳戶名稱，就可以繼續執行而不需建立此檔案。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

在 azuredeploy.parameters.json 檔案中，將值 "samplearmacct" 更新為您想要使用的資料庫名稱，然後儲存檔案。`<databaseAccountName>` 只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。

### 步驟 2：建立或擷取您的資源群組

若要建立 DocumentDB 帳戶，您首先需要資源群組。如果您已經知道想要使用的資源群組名稱，請確定位置是[已正式推出 DocumentDB 的區域](https://azure.microsoft.com/regions/#services)，然後跳至[步驟 3](#create-account-from-template)。在範本中，帳戶的位置會建立在與資源群組相同的區域中，因此嘗試在無法使用 DocumentDB 的區域中建立帳戶將會導致部署錯誤。

若要檢閱您目前所有的資源群組清單，請執行下列命令，並記下您想要使用的資源群組名稱：

    azure group list

若要建立新的資源群組，請執行下列命令、指定要建立的新資源群組名稱，以及要在其中建立資源群組的區域：

	azure group create <resourcegroupname> <databaseaccountlocation>

 - `<resourcegroupname>` 只能使用英數字元、句號、底線、'-' 字元和括號，且不能以句號結尾。 
 - `<databaseaccountlocation>` 必須是已正式推出 DocumentDB 的其中一個區域。[Azure 區域頁面](https://azure.microsoft.com/regions/#services)會提供目前的區域清單。

範例輸入：

	azure group create new_res_group westus

這會產生下列輸出：

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

如果您遇到錯誤，請參閱[疑難排解](#troubleshooting)。

### <a id="create-account-from-template"></a>步驟 3：使用 ARM 範本建立 DocumentDB 帳戶

若要在資源群組中建立 DocumentDB 帳戶，請執行下列命令，並提供範本檔案的路徑、參數檔案的路徑或參數值、要部署於其中的資源群組名稱，以及部署名稱 (-n 是選擇性的)。

使用參數檔案：

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` 是步驟 1 中建立的 azuredeploy.json 檔案的路徑。如果您的路徑名稱含有空格，請使用雙引號括住此參數。
 - `<PathToParameterFile>` 是步驟 1 中建立的 azuredeploy.parameters.json 檔案的路徑。如果您的路徑名稱含有空格，請使用雙引號括住此參數。
 - `<resourcegroupname>` 是要在其中加入 DocumentDB 資料庫帳戶的現有資源群組名稱。 
 - `<deploymentname>` 是部署的選擇性名稱。

範例輸入：

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

或者，若要指定資料庫帳戶名稱參數而不使用參數檔案，並且改為取得值的提示，請執行下列命令：

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

範例輸入，其中顯示提示以及名為 new\_db\_acct 的資料庫帳戶項目：

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

佈建帳戶時，您將會收到下列資訊：

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    location             String  West US
    info:    group deployment create command OK

如果您遇到錯誤，請參閱[疑難排解](#troubleshooting)。

在此命令傳回之後，在帳戶變更為**線上**狀態以準備好可供使用之前，該帳戶將會進入**正在建立**狀態數分鐘的時間。您可以在 [Azure 入口網站](https://portal.azure.com)中的 [DocumentDB 帳戶] 刀鋒視窗上檢查帳戶的狀態。

## 疑難排解

如果您在建立資源群組或資料庫帳戶時收到錯誤 (例如 `Deployment provisioning state was not successful`)，您有一些疑難排解選項可用。

> [AZURE.NOTE] 在資料庫帳戶名稱中提供不正確的字元，或提供無法使用 DocumentDB 的位置將會導致部署錯誤。資料庫帳戶名稱只能使用小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。所有有效的資料庫帳戶位置都會列在 [Azure 區域頁面](https://azure.microsoft.com/regions/#services)上。

- 如果您的輸出包含下列 `Error information has been recorded to C:\Users\wendy\.azure\azure.err`，則檢閱 azure.err 檔案中的錯誤資訊。

- 您可在資源群組的記錄檔中找到有用的資訊。若要檢視記錄檔，請執行下列命令：

    	azure group log show <resourcegroupname> --last-deployment

    範例輸入：

    	azure group log show new_res_group --last-deployment

    如需詳細資訊，則請參閱[在 Azure 中疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-cli.md)。

- Azure 入口網站中也會提供錯誤資訊，如下列螢幕擷取畫面所示。若要瀏覽至錯誤資訊：按一下動態工具列中的 [資源群組]、選取發生錯誤的資源群組，接著在 [資源群組] 刀鋒視窗的 [基本功能] 區域中按一下 [上次部署] 的日期，然後在 [部署記錄] 刀鋒視窗中選取失敗的部署，之後在 [部署] 刀鋒視窗中按一下有紅色驚嘆號的 [作業詳細資料]。失敗部署的狀態訊息會顯示在 [作業詳細資料] 刀鋒視窗中。

    ![顯示如何瀏覽至部署錯誤訊息的 Azure 入口網站螢幕擷取畫面](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## 後續步驟

您已有了 DocumentDB 帳戶，下一步是建立 DocumentDB 資料庫。您可以使用下列其中一個動作來建立資料庫：

- Azure 入口網站，如[使用 Azure 入口網站建立 DocumentDB 資料庫](documentdb-create-database.md)中所述。
- GitHub 上 [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) 儲存機制之 [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) 專案中的 C# .NET 範例。
- [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。DocumentDB 有.NET、Java、Python、Node.js 和 JavaScript API SDK。 

建立您的資料庫之後, 您必須[加入一或多個集合](documentdb-create-collection.md)至資料庫，然後[加入文件](documentdb-view-json-document-explorer.md)至集合。

當集合中有文件之後，您就可以利用入口網站中的[查詢總管](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)，或其中一個 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)，來針對文件使用 [DocumentDB SQL](documentdb-sql-query.md) 來[執行查詢](documentdb-sql-query.md#executing-queries)。

若要深入了解 DocumentDB，請探索以下資源：

-	[DocumentDB 的學習途徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[DocumentDB 資源模型和概念](documentdb-resources.md)

如需您可以使用的其他範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。

<!---HONumber=AcomDC_0330_2016-->