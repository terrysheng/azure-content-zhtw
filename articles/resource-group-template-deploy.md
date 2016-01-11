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
   ms.date="12/23/2015"
   ms.author="tomfitz"/>

# 使用 Azure 資源管理員範本部署應用程式

本主題說明如何使用 Azure 資源管理員範本，將您的應用程式部署至 Azure。它會說明如何使用 Azure PowerShell、Azure CLI、REST API 或 Azure 入口網站部署您的應用程式。

如需資源管理員的簡介，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。若要了解如何建立範本，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

使用範本部署應用程式時，您可以提供參數值，以自訂建立資源的方式。您可以透過內嵌方式或在參數檔案中指定這些參數的值。

## 累加部署與完整部署

資源管理員預設會將部署處理為資源群組的累加式更新。使用累加部署，資源管理員將：

- **保留且不變更**現存於資源群組中但未在範本中指定的資源
- **加入**在範本中指定但未存在於資源群組中的資源 
- **不重新佈建**現存於資源群組，並在範本中以相同條件定義的資源

透過 Azure PowerShell 或 REST API，您可以指定對資源群組的完整更新。Azure CLI 目前不支援完整部署。使用完整部署，資源管理員將：

- **刪除**現存於資源群組中但未在範本中指定的資源
- **加入**在範本中指定但未存在於資源群組中的資源 
- **不重新佈建**現存於資源群組，並在範本中以相同條件定義的資源
 
您可以透過 **Mode** 屬性，來為 PowerShell 和 REST API 指定部署類型，如下列範例所示。

## 使用 PowerShell 部署

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


1. 登入您的 Azure 帳戶。提供您的認證之後，命令會傳回您的帳戶的相關資訊。

    Azure PowerShell 1.0：

         PS C:\> Login-AzureRmAccount

         Evironment : AzureCloud
         Account    : someone@example.com
         ...


2. 如果您有多個訂用帳戶，請使用 **Select-AzureRmSubscription** 命令提供您想要用於部署的訂用帳戶識別碼。

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. 如果您沒有現有的資源群組，請使用 **New-AzureRmResourceGroup** 命令建立新的資源群組。提供您的解決方案所需的資源群組名稱和位置。隨即傳回新資源群組的摘要。

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

5. 若要建立資源群組的新部署，請執行 **New-AzureRmResourceGroupDeployment** 命令，並提供必要的參數。參數會包含您部署的名稱、資源群組的名稱、您建立之範本的路徑或 URL，以及您的案例所需的任何其他參數。沒有指定 **Mode** 參數，表示使用預設值 **Incremental**。
   
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

     若要執行完整部署，將 **Mode** 設為 **Complete**。請注意，系統會要求您確認，您的確想要使用可能會刪除資源的 Complete 模式。

          PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
          Confirm
          Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
          included in the template will be deleted.
          [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

6. 取得部署失敗的相關資訊。

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment
        
        
### 影片

以下是使用 PowerShell 搭配資源管理員範本的影片示範。

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## 以適用於 Mac、Linux 和 Windows 的 Azure CLI 部署

如果您之前未曾搭配使用 Azure CLI 與資源管理員，請參閱[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](xplat-cli-azure-resource-manager.md)。

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
   
3. 建立新的資源群組部署。提供您的訂用帳戶識別碼、要部署之資源群組的名稱、部署的名稱，以及範本的位置。如需範本檔案的相關資訊，請參閱[參數檔案](./#parameter-file)。如需以 REST API 建立資源群組的相關詳細資訊，請參閱 [建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)。請注意，**mode** 是設為 **Incremental**。若要執行完整部署，請將 **mode** 設為 **Complete**。
    
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

## 透過 Visual Studio 部署

有了 Visual Studio，您可以透過其使用者介面建立資源群組專案，並將其部署至 Azure。選取要包含在您專案中的資源類型後，這些資源會自動新增至資源管理員範本中。該專案也提供 PowerShell 指令碼來部署範本。

如需搭配資源群組使用 Visual Studio 的簡介，請參閱[透過 Visual Studio 建立和部署 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

## 使用入口網站部署

您知道嗎？ 您透過[入口網站](https://portal.azure.com/)所建立的每個應用程式，都受到一個 Azure 資源管理員範本的支援！ 只要透過入口網站建立虛擬機器、虛擬網路、儲存體帳戶、App Service 或資料庫，您就已經充分利用 Azure 資源管理員的優點，不多費心力。只要選取**新增**圖示，即可進入透過 Azure 資源管理員部署應用程式的程序。

![新增](./media/resource-group-template-deploy/new.png)

如需關於搭配 Azure 資源管理員使用入口網站的詳細資訊，請參閱[使用 Azure 入口網站來管理您的 Azure 資源](azure-portal/resource-group-portal.md)。


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
- 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱[使用 .NET 程式庫和範本部署資源](arm-template-deployment.md)
- 如需部署應用程式的深入範例，請參閱[透過可預測方式在 Azure 中佈建和部署微服務](app-service-web/app-service-deploy-complex-application-predictably.md)
- 如需如何將解決方案部署到不同環境的指南，請參閱[Microsoft Azure 中開發和測試環境](solution-dev-test-environments-preview-portal.md)。
- 如要了解 Azure 資源管理員範本的區段，請參閱[編寫範本](resource-group-authoring-templates.md)
- 如需可以在 Azure 資源管理員範本中使用的函式清單，請參閱[範本函式](resource-group-template-functions.md)

 

<!---HONumber=AcomDC_1223_2015-->