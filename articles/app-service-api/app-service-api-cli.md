<properties
	pageTitle="Azure CLI 與 API Apps | Microsoft Azure"
	description="如何使用適用於 Mac、Linux 和 Windows 的 Microsoft Azure 命令列介面 (CLI) 搭配 Azure API Apps。"
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Azure 命令列介面 (CLI) 與 API 應用程式

本文說明如何使用適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (CLI) 在 Azure App Service 中建立、管理及刪除 API 應用程式。

## 必要條件

本文假設您已安裝 Azure CLI 並且知道如何執行基本工作。如需 CLI 的簡介，請參閱[安裝和設定 Azure CLI](../xplat-cli-install.md)。

> [AZURE.NOTE] [連線到 Azure 訂用帳戶](../xplat-cli-connect.md)的指示提供兩個替代方案：使用工作帳戶或學校帳戶登入，然後下載 *.publishsettings* 檔案。如果是 API 應用程式，*.publishsettings* 檔案驗證方法將無法運作。這是因為您必須使用「資源管理」模式 (在下一節介紹) 才能處理 API 應用程式，*.publishsettings* 檔案驗證方法無法與資源管理員搭配運作。

## 啟用資源管理模式

您可以在[服務管理 (asm)](../virtual-machines/virtual-machines-command-line-tools.md)模式或[資源管理 (arm)](../xplat-cli-azure-resource-manager.md)模式中使用 CLI。如果是 API 應用程式，您必須使用「資源管理」模式。因為 `arm` 模式預設不啟用，所以請使用 `config mode arm` 命令予以啟用。

	azure config mode arm

## 列出可用來處理 API 應用程式的命令

若要查看目前可用來處理 API 應用程式的所有命令，請執行 `apiapp` 命令。

	azure apiapp

## 列出訂用帳戶或資源群組中所有的 API 應用程式

若要列出您的訂用帳戶中所有的 API 應用程式，請執行 `apiapp list` 命令不加任何參數。

	azure apiapp list

此清單會顯示每個 API 應用程式的資源群組、API 應用程式名稱、封裝識別碼和 URL。

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

若要將清單限制為指定的資源群組，請加入群組 (`-g`) 參數。

	azure apiapp list -g <resource group name>

例如：

	azure apiapp list -g mygroup

若要將 API 應用程式版本和存取層級資訊加入清單，請加入詳細資料 (`-d`) 參數。

	azure apiapp list -d

`list` 輸出與其他欄位看起來會像此範例：

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### 列出 API 應用程式的詳細資料

若要列出一個 API 應用程式的詳細資料，請使用 `apiapp show` 命令，加上群組 (`-g`) 和 API 應用程式名稱 (`-n`) 參數。

	azure apiapp show -g <resource group name> -n <API app name>

例如：

	azure apiapp show -g mygroup -n SimpleDropbox

輸出如下所示：

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## 建立 API 應用程式

有兩種方式可以建立 API 應用程式。您可以使用命令式 CLI 命令個別建立 Azure 資源，或者可以在範本中使用宣告式語法一起定義所有必要的資源，然後使用 CLI 命令部署該範本。如需宣告式方法，請參閱[後續步驟](#next-steps)。

若要使用命令式方式建立 API 應用程式，請執行下列步驟：

1. 選擇有效的位置
1. 建立或尋找要使用的資源群組
2. 建立或尋找要使用的 App Service 方案
4. 建立 API 應用程式

### 選擇有效的位置

建立資源群組時，必須指定位置。以下是一些適用於 API 應用程式的位置。

* 美國東部
* 美國西部
* 美國中南部
* 北歐
* 東亞
* 日本東部
* 西歐
* 東南亞
* 日本西部
* 美國中北部
* 美國中部
* 巴西南部
* 美國東部 2

若要取得完整且最新的位置清單，請使用 `location list` 命令，然後查看 `Microsoft.AppService/apiapps` 資源提供者列。

	azure location list

以下是 `location list` 命令的範例輸出。

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### 建立或尋找要使用的資源群組

若要建立資源群組，請使用 `group create` 命令，加上名稱 (`n`) 和位置 (`l`) 參數。

	azure group create -n <name> -l <location>

例如：

	azure group create -n "mygroup" -l "West US"

若要尋找現有的資源群組，請使用 `group list` 命令，然後在適用於 API 應用程式的位置中選擇資源群組。

	azure group list

### 建立或尋找要使用的 App Service 方案

若要建立 App Service 方案，請使用 `resource create` 命令，並且使用資源類型參數 (`-r`) 指定您想要建立的資源類型是 App Service 方案。

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
例如：

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

因為 REST API 最近有一些變更，所以必須有 `properties` (`-p`) 參數的 JSON 字串；未來 `-p` 將變成選用的參數。

範例命令指定了撰寫本文當日最新的 API 版本。若要檢查是否有更新的版本，請使用 `provider show` 命令並查看 `resourceTypes` 陣列中 `sites` 物件的 `apiVersions`陣列。

	azure provider show -n Microsoft.Web --json
   
以下是命令輸出中 `sites`物件的範例。

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

若要列出現有的 App Service 方案，請使用 `resource list` 命令並使用 `-r` 參數指定 App Service 方案為資源類型。

	azure resource list -r Microsoft.Web/serverfarms

輸出如下所示：

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### 建立空的 (自訂) API 應用程式

若要建立空的 API 應用程式 (您可以自行撰寫程式碼)，請使用 `apiapp create` 命令並指定 `Microsoft.ApiApp` Nuget 封裝 (`-u` 參數)。

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

例如：

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

輸出會在 API 應用程式建立時報告進度。

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### 從 Marketplace 建立 API 應用程式

若要取得 Marketplace 中可用的 API 應用程式封裝清單，請使用 `group template list` 命令並使用類別 (`-c`) 參數指定 API 應用程式。

	azure group template list -c apiapps

輸出看起來會像這個範例：

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

若要從 Marketplace 建立 API 應用程式，請使用 `apiapap create` 命令並使用 NuGet 封裝 (`-u`) 參數指定您想要建立的 API 應用程式名稱。

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

要用於 `-u` 參數的值位於 Marketplace 名稱的中間區段。例如，如果是 microsoft\_com.MicrosoftSqlConnector.0.2.2，命令看起來會像這樣：

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
如果需要任何 API 應用程式範本參數，例如伺服器名稱和 SQL 連接器的連接字串，系統將會提示您輸入必要的資料。您可以選擇使用 `--parameters` 或 `--parameters-file` 傳入參數值。如需有關參數檔案的詳細資訊，請參閱[以新的閘道佈建 API 應用程式](app-service-api-arm-new-gateway-provision.md)。

## 後續步驟

本篇文章已示範如何使用個別的 Azure CLI 來處理自訂的 API 應用程式或是您從 Marketplace 範本建立的 API 應用程式。如需如何使用自訂範本來自動化 API 應用程式建立的資訊，請參閱這些資源：

* [以現有閘道佈建 API 應用程式](app-service-api-arm-existing-gateway-provision.md)
* [以新的閘道佈建 API 應用程式](app-service-api-arm-new-gateway-provision.md)

如需如何使用 Azure 命令列公用程式搭配 Azure 資源管理員的詳細資訊，請參閱下列資源：
 
* [搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](../xplat-cli-azure-resource-manager.md)。
* [搭配使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)
 

<!---HONumber=AcomDC_0114_2016--->