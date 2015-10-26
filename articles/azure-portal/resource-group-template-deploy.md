<properties
   pageTitle="透過資源管理員範本部署資源 |Microsoft Azure"
   services="azure-resource-manager"
   description="使用 Azure 資源管理員將資源部署至 Azure。範本是 JSON 檔案，並可從入口網站、PowerShell、適用於 Mac、Linux 和 Windows 的 Azure 命令列介面，或 REST 來使用。"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="tomfitz"/>

# 使用 Azure 資源管理員範本部署應用程式

本主題說明如何使用 Azure 資源管理員範本，將您的應用程式部署至 Azure。它會說明如何使用 Azure PowerShell、Azure CLI、REST API 或 Microsoft Azure Preview 入口網站部署您的應用程式。

如需資源管理員的簡介，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。若要了解如何建立範本，請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

使用範本部署應用程式時，您可以提供參數值，以自訂建立資源的方式。您可以透過內嵌方式或在參數檔案中指定這些參數的值。


## 使用 PowerShell 部署

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]


1. 登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的帳戶的相關資訊。

    早於 Azure PowerShell 1.0 Preview：

        PS C:\> Switch-AzureMode AzureResourceManager
        ...
        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

    Azure PowerShell 1.0 Preview：

         PS C:\> Login-AzureRmAccount

         Evironment : AzureCloud
         Account    : someone@example.com
         ...


2. 如果您有多個訂用帳戶，請使用 **Select-AzureRmSubscription** 命令提供您想要用於部署的訂用帳戶識別碼。

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. 如果您沒有現有資源群組，請使用 **New-AzureRmResourceGroup** 命令建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。隨即傳回新資源群組的摘要。

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. 若要建立資源群組的新部署，請執行 **New-AzureRmResourceGroupDeployment** 命令，並提供必要的參數。參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL，以及您的案例所需的任何其他參數。
   
     您有下列選項可以用來提供參數值：
   
     - 使用內嵌參數。

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - 使用參數物件。

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - 使用參數檔案。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     部署資源群組之後，您會看到部署的摘要。

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. 取得部署失敗的相關資訊。

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment

        
### 影片

以下是使用 PowerShell 搭配資源管理員範本的影片示範。

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## 以適用於 Mac、Linux 和 Windows 的 Azure CLI 部署

如果您之前未曾搭配使用 Azure CLI 與資源管理員，請參閱[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](../xplat-cli-azure-resource-manager.md)。

1. 登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的登入的結果。

        azure login
  
        ...
        info:    login command OK

2. 如果您有多個訂用帳戶，請提供您想要用於部署的訂用帳戶識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換至 Azure 資源管理員模組。您會收到新模式的確認。

        azure config mode arm
   
        info:     New mode is arm

4. 如果您沒有現有資源群組，請建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。隨即傳回新資源群組的摘要。

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 若要建立資源群組的新部署，請執行下列命令，並提供必要的參數。參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL，以及您的案例所需的任何其他參數。
   
     您有下列選項可以用來提供參數值：

     - 使用內嵌參數和本機範本。每個參數皆為此格式：`"ParameterName": { "value": "ParameterValue" }`。以下範例顯示含逸出字元的參數。

             azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - 使用內嵌參數和範本的連結。

             azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - 使用參數檔案。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     部署資源群組之後，您會看到部署的摘要。
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 取得最新部署的相關資訊。

         azure group log show -l ExampleResourceGroup

7. 取得部署失敗的詳細資訊。
      
         azure group log show -l -v ExampleResourceGroup

## 使用 REST API 部署
1. 設定[一般參數和標頭](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) (包括驗證權杖)。
2. 如果您沒有現有資源群組，請建立新的資源群組。提供您的訂用帳戶識別碼、新資源群組的名稱，以及解決方案需要的位置。如需詳細資訊，請參閱[建立資源群組](https://msdn.microsoft.com/library/azure/dn790525.aspx)。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. 建立新的資源群組部署。提供您的訂用帳戶識別碼、要部署之資源群組的名稱、部署的名稱，以及範本的位置。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。如需以 REST API 建立資源群組的相關詳細資訊，請參閱 [建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)。
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. 取得範本部署的狀態。如需詳細資訊，請參閱 [取得範本部署的相關資訊](https://msdn.microsoft.com/library/azure/dn790565.aspx)。

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## 使用 Preview 入口網站部署

您知道嗎？ Azure 資源管理員範本支援所有經由[預覽入口網站](https://portal.azure.com/)建立的應用程式！ 只要透過入口網站建立虛擬機器、虛擬網路、儲存體帳戶、App Service 或資料庫，您就已經充分利用 Azure 資源管理員的優點，不多費心力。只要選取 [新增] 圖示，您即可進入透過 Azure 資源管理員部署應用程式的程序。

![新增](./media/resource-group-template-deploy/new.png)

如需關於搭配 Azure 資源管理員使用入口網站的詳細資訊，請參閱[使用 Azure Preview 入口網站來管理您的 Azure 資源](resource-group-portal.md)。


## 參數檔案

如果您在部署期間，使用參數檔案傳遞參數值到您的範本，您需要建立格式類似以下範例的 JSON 檔案。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

參數檔的大小不得超過 64 KB。

## 後續步驟
- 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱[使用 .NET 程式庫和範本部署資源](../arm-template-deployment.md)
- 如需部署應用程式的深入範例，請參閱[透過可預測方式在 Azure 中佈建和部署微服務](../app-service-web/app-service-deploy-complex-application-predictably.md)
- 如需將您的方案部署到不同的環境的指導，請參閱[在 Microsoft Azure 中開發和測試環境](../solution-dev-test-environments-preview-portal.md)。
- 若要了解 Azure 資源管理員範本的區段，請參閱[撰寫範本](../resource-group-authoring-templates.md)
- 如需可以在 Azure 資源管理員範本中使用的函式清單，請參閱[範本函式](../resource-group-template-functions.md)

 

<!---HONumber=Oct15_HO3-->