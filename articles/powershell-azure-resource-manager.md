<properties 
	pageTitle="Azure PowerShell 搭配資源管理員 | Microsoft Azure" 
	description="使用 Azure PowerShell 將多個資源做為資源群組部署至 Azure 的簡介。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tomfitz"/>

# 搭配使用 Azure PowerShell 與 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure 資源管理員介紹一種看待 Azure 資源的嶄新方式。與其建立並管理個別資源，您首先想像整個解決方案，例如，部落格、相片庫、SharePoint 入口網站或 Wiki。使用範本 (解決方案的宣告式呈現) 建立包含支援解決方案所需所有資源的資源群組。然後，以邏輯單元的方式來管理與部署該資源群組。

在本教學課程中，您將了解如何搭配使用 Azure PowerShell 與 Azure 資源管理員。它會逐步引導您為搭配 SQL 資料庫的 Azure 託管 web 應用程式，完成建立與部署資源群組的程序，並提供支援此群組所需的所有資源。

## 必要條件

若要完成本教學課程，您需要：

- 一個 Azure 帳戶
  + 您可以[免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。
  
  + 您可以[啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。
- Azure PowerShell 1.0。如需有關此版本以及如何安裝的資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

本教學課程是專為 PowerShell 初學者所設計的，但它會假設您已了解基本概念，例如模組、Cmdlet 和工作階段。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) (英文)。

## 部署內容

在本教學課程中，您將使用 Azure PowerShell 部署 Web 應用程式和 SQL Database。不過，此 Web 應用程式和 SQL Database 解決方案是由數個一起運作的資源類型組成。您將部署的實際資源是︰

- SQL Server - 主控資料庫
- SQL Database - 儲存資料
- 防火牆規則 - 允許 Web 應用程式連接到資料庫
- App Service 計劃 - 定義 Web 應用程式的功能和成本
- 網站 - 執行 Web 應用程式
- Web 組態 - 將連接字串儲存到資料庫 

## 取得 Cmdlet 的說明

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

	Get-Help <cmdlet-name> -Detailed

例如，如需取得 Get-AzureRmResource Cmdlet 的說明，請輸入：

	Get-Help Get-AzureRmResource -Detailed

若要取得資源模組中的 Cmdlet 清單及說明概要，請輸入：

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

輸出類似如下摘錄：

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

To get full help for a cmdlet, type a command with the format:

	Get-Help <cmdlet-name> -Full
  
## 登入您的 Azure 帳戶

在使用您的解決方案之前，您必須登入您的帳戶。

若要登入您的 Azure 帳戶，請使用 **Login-AzureRmAccount** Cmdlet。

    PS C:\> Login-AzureRmAccount

cmdlet 會提示您 Azure 帳戶的登入認證。登入之後，它會下載您的帳戶設定以供 Azure PowerShell 使用。

帳戶設定已過期，因此您必須偶爾重新整理這些設定。若要重新整理帳戶設定，請再次執行 **Login-AzureRmAccount**。

>[AZURE.NOTE]資源管理員模組需要 Login-AzureRmAccount。發佈設定檔案不符合需求。

## 取得資源類型位置

部署資源時您必須指定想要託管資源的位置。並非每個區域都支援每種資源類型。在部署您的 Web 應用程式和 SQL Database 之前，您必須了解哪些區域支援這些類型。資源群組可以包含位於不同區域的資源；不過，您應該盡可能在相同的位置建立資源以最佳化效能。尤其，您會想要確定您的資料庫與存取資料庫的應用程式位於相同的位置。

若要取得支援各種資源類型的位置，您必須使用 **Get-AzureRmResourceProvider** Cmdlet。首先，讓我們看看此命令傳回的項目：

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

ProviderNamespace 表示相關資源類型的集合。這些命名空間通常都能與您想要在 Azure 中建立的服務搭配得宜。如果您想要使用已列為 [**已取消註冊**] 的資源提供者，您可以透過執行 **Register-AzureRmResourceProvider** Cmdlet，並指定要註冊的提供者命名空間，為該資源提供者註冊。您將在本教學課程使用的資源提供者很有可能已經針對您的訂用帳戶註冊。

您可以藉由指定該命名空間以取得提供者的詳細資訊：

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

若要針對特定類型的資源將您的輸出限制為支援的位置，例如網站，請使用：

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
輸出將類似於：

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

您看到的位置可能會與先前的結果稍有不同。結果可能會不同，因為組織中的系統管理員已建立原則，限制哪些區域可用於您的訂用帳戶，或可能有與您的設籍國家/地區的稅捐原則相關的限制。

讓我們針對資料庫執行相同命令：

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

看來這些資源可用於許多區域。在此主題中，我們將使用**美國西部**，但您可以指定任何支援的區域。

## 建立資源群組

本教學課程的這個部分會逐步引導您建立資源群組的程序。資源群組會做為您的解決方案中共用相同生命週期的所有資源的容器。稍後在教學課程中，您會將 Web 應用程式和 SQL Database 部署至此資源群組。

若要建立資源群組，請使用 **New-AzureRmResourceGroup** Cmdlet。

此命令會使用 **Name** 參數來指定資源群組的名稱，並使用 **Location** 參數來指定其位置。根據我們在上一節中的發現，我們將使用「美國西部」做為位置。

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

已成功建立您的資源群組。


## 取得資源的可用 API 版本

當您部署範本時，您必須指定要用來建立資源的 API 版本。可用的 API 版本會對應至資源提供者所發行的 REST API 作業版本。當資源提供者啟用新功能時，它會發行新版本的 REST API。因此，您在範本中指定的 API 版本會影響您在建立範本時可用的屬性。一般而言，您會想要在建立新範本時選取最新的 API 版本。對於現有的範本，您可以決定要繼續使用您已知不會變更您的部署的舊 API 版本，或更新您的範本以便最新版本利用新功能。

這個步驟可能會產生混淆，但是探索可用於您的資源的 API 版本並不困難。您將會再次使用 **Get-AzureRmResourceProvider** 命令。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

如您所見，這個 API 經常更新。通常，相同的 API 版本號碼可用於資源提供者中的所有資源。唯一的例外狀況是在某個時間點新增或移除資源。我們會假設相同的 API 版本可用於 serverFarms 資源。不過，您可以仔細查看您認為可能會有不同的可用 API 版本清單的任何資源。

資料庫中，您會看到：

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## 建立您的範本

本主題不會顯示如何建立您的範本或討論範本的結構。如需詳細資訊，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。您將要部署的範本如下所示：請注意，範本會使用您在上一節擷取的 API 版本。為了確保所有資源都位於相同區域中，我們將使用範本運算式 **resourceGroup().location** 以使用資源群組的位置。

另請注意參數區段。這個區段會定義您在部署資源時可以提供的值。您會在本教學課程中稍後使用這些值。

您可以複製範本，並在本機將它儲存為 .json 檔案。在本教學課程中，我們假設它已儲存至 c:\\Azure\\Templates\\azuredeploy.json，但是您可以將它儲存在任何方便的位置，並且具有對於您的需求有意義的名稱。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "tags": {
                    "team": "webdev"
                },
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## 部署範本

您具有資源群組和範本，因此現在您已經準備好要將在您的範本中定義的基礎結構部署至資源群組。使用 **New-AzureRmResourceGroupDeployment** Cmdlet 部署資源。基本語法如下所示：

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

您指定資源群組和範本的位置。如果您的範本並非本機，可以使用 **-TemplateUri** 參數，並針對範本指定 URI。您可以將 **-Mode** 參數設為 **Incremental** 或 **Complete**。資源管理員預設會在部署期間執行累加式更新；因此，當您想要使用 **Incremental** 時不需要設定 **-Mode**。若要了解這些部署模式的差異，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

###動態範本參數

如果您熟悉 PowerShell，您知道您可以輸入減號 (-) 然後按下 TAB 鍵，循環 Cmdlet 的可用參數。相同的功能也適用於您在範本中定義的參數。在您輸入範本名稱之後，Cmdlet 便會立即擷取範本、剖析範本，並將範本參數動態新增至命令。這會讓指定範本參數值變得再容易不過了。另外，如果您忘記必要參數值，則 PowerShell 會出現此值的提示。

以下是包含參數的完整命令。您可以對資源名稱提供您自己的值。

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

當您輸入命令時，系統會提示您輸入遺漏的必要參數 **administratorLoginPassword**。並在您輸入密碼時，此安全字串值便會被遮住。如此，就不會有以純文字方式提供密碼的風險。

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

資源建立時，命令會執行並且傳回訊息。最後，您會看到您的部署結果。

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

只需幾個步驟，即可建立與部署複雜網站所需的資源。

## 取得資源群組的相關資訊

建立資源群組之後，您可以使用資源管理員模組中的 Cmdlet，來管理您的資源群組。

- 若要取得您訂用帳戶中的所有資源群組，請使用 **Get-AzureRmResourceGroup** Cmdlet：

		PS C:\> Get-AzureRmResourceGroup

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

      如果您只想取得特定資源群組，請提供 **Name** 參數。
      
          PS C:\> Get-AzureRmResourceGroup -Name TestRG1

- 若要取得您資源群組中的資源，請使用 **Find-AzureRmResource** Cmdlet 及其 **ResourceGroupNameContains** 參數。在沒有使用參數的情況下，Find-AzureRmResource 可取得您 Azure 訂用帳戶中的所有資源。

        PS C:\> Find-AzureRmResource -ResourceGroupNameContains TestRG1
		
        Name              : exampleserver
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
        ResourceName      : exampleserver
        ResourceType      : Microsoft.Sql/servers
        Kind              : v12.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
        ...
	        
- 上述範本包含一項資源上的標記。您可以使用標記，以邏輯方式組織訂用帳戶中的資源。使用 **Find-AzureRmResource** 和 **Find-AzureRmResourceGroup** 命令以標記查詢您的資源。

        PS C:\> Find-AzureRmResource -TagName team

        Name              : ExampleSiteuxq53xiz5etmq
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/ExampleSiteuxq53xiz5etmq
        ResourceName      : ExampleSiteuxq53xiz5etmq
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      還有更多功能可搭配標記使用。如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](resource-group-using-tags.md)。

## 新增至資源群組

若要將資源新增至資源群組，可以使用 **New-AzureRmResource** Cmdlet。不過，以這種方式新增資源可能會造成未來的混淆，因為新的資源不存在於您的範本。如果您重新部署舊範本，則會部署不完整的解決方案。如果您經常部署，您會發現將新的資源新增至您的範本並重新部署它更方便且更可靠。

## 移動資源

您可以將現有的資源移動到新的資源群組。如需範例，請參閱[將資源移至新的資源群組或訂用帳戶](resource-group-move-resources.md)。

## 刪除資源群組

- 若要從資源群組中刪除資源，請使用 **Remove-AzureRmResource** Cmdlet。此 Cmdlet 會刪除資源，但不會刪除資源群組。

	此命令會將 TestSite 網站從 TestRG 資源群組中移除。

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- 若要刪除資源群組，請使用 **Remove-AzureRmResourceGroup** Cmdlet。此 Cmdlet 會刪除資源群組及其資源。

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## 後續步驟

- 若要了解如何建立資源管理員範本，請參閱[撰寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。
- 若要了解部署範本的相關資訊，請參閱[使用 Azure 資源管理員範本部署應用程式](./resource-group-template-deploy.md)。
- 如需部署專案的詳細範例，請參閱[透過可預測方式在 Azure 中部署微服務](app-service-web/app-service-deploy-complex-application-predictably.md)。
- 若要了解如何疑難排解失敗的部署，請參閱[在 Azure 中疑難排解資源群組部署](./virtual-machines/resource-group-deploy-debug.md)。

<!---HONumber=AcomDC_0114_2016-->