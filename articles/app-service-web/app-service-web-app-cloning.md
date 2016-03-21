<properties
	pageTitle="使用 PowerShell 複製 Web 應用程式"
	description="了解如何使用 PowerShell，將您的 Web Apps 複製到新的 Web Apps。"
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# 使用 PowerShell 複製 Azure App Service App#

隨著 Microsoft Azure PowerShell 版本 1.1.0 的發行，為 New-AzureRMWebApp 加入了新選項，可讓使用者將現有的 Web 應用程式複製到在不同區域或在相同區域中新建立的應用程式。這可讓客戶輕鬆且快速地跨不同區域部署許多 app。

應用程式複製目前僅支援 Premium 層 App Service 方案。新的功能使用與 Web Apps 備份功能相同的限制，請參閱[在 Azure App Service 中備份 Web 應用程式](web-sites-backup.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]


## 複製現有的應用程式 ##

案例：使用者想要將位在美國中南部區域的現有 Web 應用程式內容複製到位在美國中北部區域的新 Web 應用程式中。使用 ARM 版本 PowerShell Cmdlet 利用 -SourceWebApp 選項來建立新的 Web 應用程式，即可實現此目的。

了解包含來源 Web 應用程式的資源群組名稱，我們可以使用下列 PowerShell 命令來取得來源 Web 應用程式的資訊 (在此情況下名為 source-webapp)：

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

若要建立新的 App Service 方案，我們可以使用 New-AzureRmAppServicePlan 命令，如下列範例所示

	New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

使用 New-AzureRmWebApp 命令，我們可以在美國中北部區域中建立新的 Web 應用程式，並將它連接至現有的 Premium 層 App Service 方案，而且我們可以使用相同的資源群組作為來源 Web 應用程式，或定義新的資源群組，範例如下：

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

若要複製現有的 Web 應用程式 (包括所有相關聯的部署位置)，使用者必須使用 IncludeSourceWebAppSlots 參數，以下 PowerShell 命令示範如何使用該參數搭配 New-AzureRmWebApp 命令：

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

若要在相同區域中複製現有的 Web 應用程式，使用者必須在相同區域中建立新資源群組和新的 App Service 方案，然後使用下列 PowerShell 命令來複製 Web 應用程式

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## 複製現有應用程式至 App Service 環境 ##

案例：使用者想要將位在美國中南部區域的現有 Web 應用程式的內容複製到現有的 App Service 環境 (ASE) 的新 Web 應用程式中。

了解包含來源 Web 應用程式的資源群組名稱，我們可以使用下列 PowerShell 命令來取得來源 Web 應用程式的資訊 (在此情況下名為 source-webapp)：

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

知道 ASE 的名稱和 ASE 所屬的資源群組名稱，使用者可以使用 New-AzureRmWebApp 命令在現有的 ASE 中建立新的 Web 應用程式，以下示範：

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

由於使用舊版，必須提供位置參數；但如果是在 ASE 中建立應用程式，則將會忽略它。

## 複製現有的應用程式位置 ##

案例：使用者想要將現有 Web 應用程式位置複製到新 Web 應用程式或新的 Web 應用程式位置。新的 Web 應用程式可以在與原始的 Web 應用程式位置位於相同區域或不同區域中。

了解包含來源 Web 應用程式的資源群組名稱，我們可以使用下列 PowerShell 命令來取得繫結至 Web App source-webapp 之來源 Web 應用程式的位置資訊 (在此情況下名為 source-webapp)：

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

以下示範建立來源 Web 應用程式的複製到新的 Web 應用程式：

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## 設定流量管理員同時複製應用程式 ##

建立多重區域 Web 應用程式，並設定 Azure 流量管理員，將流量路由到這些 Web 應用程式，重要的案例是確保客戶的應用程式為高可用性，當複製現有的 Web 應用程式時，您可以選擇將兩個 Web 應用程式連接到新的流量管理員設定檔或現有的設定檔 - 請注意，僅支援 ARM 版本的流量管理員。

### 建立新流量管理員設定檔同時複製應用程式 ###

案例：使用者想要將 Web 應用程式複製到另一個區域中，同時設定包含兩個 Web 應用程式的 ARM 流量管理員設定檔。以下示範建立來源 Web 應用程式的複製到新的 Web 應用程式，同時設定新流量管理員設定檔：

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### 加入新複製的 Web 應用程式至現有的流量管理員設定檔 ###

案例：使用者已經有他想要將兩個 Web 應用程式加入為端點的 ARM 流量管理員設定檔。若要這樣做，我們必須先組合將現有的流量管理員設定檔的識別碼，我們需要訂用帳戶訂用帳戶識別碼、資源群組名稱和現有的流量管理員設定檔名稱。

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

有了流量管理員識別碼之後，以下示範建立來源 Web 應用程式的複製到新的 Web 應用程式，同時將它們加入現有的流量管理員設定檔：

	$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## 目前的限制 ##

這項功能目前僅供預覽，我們正努力隨著時間加入新功能，以下是目前版本 App 複製已知的限制：

- 不會複製自動調整設定
- 不會複製備份排程設定
- 不會複製 VNET 設定
- 未自動在目的地 Web 應用程式上設定 App Insights
- 不會複製簡單驗證設定
- 不會複製 Kudu 延伸模組
- 不會複製 TiP 規則


### 參考 ###
- [使用 Azure 入口網站複製 Web 應用程式](app-service-web-app-cloning-portal.md)
- [在 Azure App Service 中備份 Web 應用程式](web-sites-backup.md)
- [Azure 資源管理員的 Azure 流量管理員支援預覽](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [App Service 環境簡介](app-service-app-service-environment-intro.md)
- [搭配使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0309_2016-->