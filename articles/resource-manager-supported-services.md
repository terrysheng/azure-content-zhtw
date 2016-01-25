<properties
   pageTitle="資源管理員支援的服務、區域、結構描速及版本 |Microsoft Azure"
   description="說明支援資源管理員、其結構描述及可用 API 版本的資源提供者，以及可裝載資源的區域。"
   services="azure-resource-manager"
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
   ms.date="01/12/2016"
   ms.author="tomfitz"/>

# 資源管理員提供者、區域、API 版本及結構描述

Azure 資源管理員提供一種新方式來部署和管理組成應用程式的服務。
大部分但並非所有的服務都支援資源管理員，有些服務僅部分支援資源管理員。Microsoft 會針對每個服務啟用資源管理員，這對於未來的解決方案而言很重要，但在支援一致化之前，您需要了解每個服務的目前狀態。本主題提供支援 Azure 資源管理員的資源提供者清單。

部署資源時，您也需要知道哪些區域支援這些資源，以及哪些 API 版本適用於資源。[支援區域](#supported-regions)一節說明如何找出哪些區域適用於您的訂用帳戶和資源。[支援的 API 版本](#supported-api-versions)一節說明如何判斷您可以使用哪些 API 版本。

如要 Azure 入口網站和傳統入口網站支援哪些服務，請參閱[Azure 入口網站的可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。

下表列出哪些服務可透過資源管理員支援部署和管理，哪些則否。標題為「移動資源」的資料行，代表此類型的資源是否能移動到新的資源群組和新的訂用帳戶。標題為「快速入門範本」的資料行中的連結，會為特定資源提供者傳送查詢給 Azure 快速入門範本儲存機制。快速入門範本會頻繁地新增及更新。即使特定服務有連結，也並不一定代表查詢將會從儲存機制傳回範本。


## 計算

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------------------------ | -------------- |-------- | ------ | ------ |
| 虛擬機器 | 是 | 否 | [VM REST](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| 批次 | 是 | 是 | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics 週期服務 | 是 | | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (Preview) | 是 | | [Service Fabric Rest](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| 虛擬機器 (傳統) | 有限 | 部分 (請參閱下文) | - | - | 
| 遠端應用程式 | 否 | - | - | - | 
| 雲端服務 (傳統) | 是 (請參閱下文) | 部分 (請參閱下文) | - | - | - |

虛擬機器 (傳統) 是指已透過傳統部署模型部署的資源，而不是透過資源管理員部署模型部署的資源。一般而言，這些資源不支援資源管理員作業，但已啟用某些作業。如需這些部署模型的詳細資訊，請參閱[了解資源管理員部署和傳統部署](resource-manager-deployment-model.md)。

已為雲端服務啟用資源管理員，以搭配其他傳統資源使用；不過，傳統資源不會利用所有的資源管理員功能，也不是未來解決方案的好選項。請改為考慮變更您的應用程式基礎結構，以從 Microsoft.Compute、Microsoft.Storage，和 Microsoft.Network 命名空間使用資源。

虛擬機器 (傳統) 和雲端服務可以移至新的資源群組，但不能移至新的訂用帳戶。

## 網路

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| 應用程式閘道 | 是 | | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | 是 | | [DNS REST](https://msdn.microsoft.com/library/azure/mt163862.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| 負載平衡器 | 是 | | [負載平衡器 REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| 虛擬網路 | 是 | 部分 (請參閱下文) | [虛擬網路 REST](https://msdn.microsoft.com/zh-TW/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| 流量管理員 | 是 | | [流量管理員 REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| ExpressRoute | 是 | 否 | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |

虛擬網路可以移至新的資源群組，但不能移至新的訂用帳戶。

## 資料與儲存體

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------- | ------ |
| DocumentDB | 是 | 是 | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| 儲存體 | 是 | 否 | [儲存體 REST](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [儲存體帳戶](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Redis 快取 | 是 | 是 | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| SQL Database | 是 | 是 | [SQL Database REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| 搜尋 | 是 | 是 | [搜尋 REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| SQL 資料倉儲 | 是 | | | |
| StorSimple | 否 | - | - | - | - |

## Web 與行動

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| API 管理 | 是 | 是 | [API 管理 REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| API 應用程式 | 是 | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [API 應用程式](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Web Apps | 是 | 是，但有限制 (請參閱下文) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |
| 行動應用程式 | 是 | | | | |
| 通知中樞 | 是 | 是 | [通知中樞 REST](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |
| Logic Apps | 是 | 是 | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Mobile Engagement | 是 | 是 | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |

使用 Web 應用程式時，您無法只移動 App Service 方案。若要移動 Web 應用程式，您的選項如下：

- 如果目的地資源群組還沒有 Microsoft.Web 資源，將所有的資源從某個資源群組移動到不同的資源群組。
- 將 Web 應用程式移動到不同的資源群組，但在原始的資源群組中保留 App Service 方案。

## Analytics

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | -------  | -------------- | -------- | ------ | ------ |
| 事件中心 | 是 | | [事件中樞 REST](https://msdn.microsoft.com/library/azure/dn790674.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| 串流分析 | 是 | | [串流分析 REST](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| HDInsights | 是 | 是 | [HDInsights REST](https://msdn.microsoft.com/library/azure/mt622197.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Data Factory | 是 | 是 | [Data Factory REST](https://msdn.microsoft.com/library/azure/dn906738.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| 機器學習 | 否 | - | - | - | 
| 資料目錄 | 否 | - | - | - |

## 媒體與 CDN

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| CDN | 是 | | [CDN REST](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| 媒體服務 | 否 | | | |


## 混合式整合

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| BizTalk 服務 | 是 | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| 服務匯流排 | 是 | | | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| 備份 | 否 | - | - | - | 
| Site Recovery | 否 | - | - | - |

## 身分識別與存取管理 

Azure Active Directory 可搭配資源管理員使用，以針對您的訂用帳戶啟用角色型存取控制。若要深入了解使用角色型存取控制和 Active Directory，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

## 開發人員服務 

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Application Insights | 是 | 否 | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Bing 地圖 | 是 | | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| DevTest Labs (Preview) | 是 | | | [2015-05-21-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Visual Studio 帳戶 | 是 | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## 管理和安全性

| 服務 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| 自動化 | 是 | 是 | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| 金鑰保存庫 | 是 | 是 | [金鑰保存庫 REST](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [金鑰保存庫](resource-manager-template-keyvault.md)<br />[金鑰保存庫密碼](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| 排程器 | 是 | | [排程器 REST](https://msdn.microsoft.com/library/azure/mt629143.aspx) | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Operational Insights | 是 | 是 | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| IoTHubs | 是 | | [IoT 中心 REST (英文)](https://msdn.microsoft.com/library/azure/mt589014.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| 安全性 (Preview) | 是 | | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## 資源管理員

| 功能 | 已啟用資源管理員 | 移動資源 | REST API | 結構描述 | 快速入門範本 |
| ------- | ------- | -------- | -------------- | -------- | ------ | ------ |
| Authorization | 是 | N/A | [管理鎖定](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[角色型存取控制](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [資源鎖定](resource-manager-template-lock.md)<br />[角色指派](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| 資源 | 是 | N/A | [Linked resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [資源連結](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## 資源提供者和類型

部署資源時，您經常需要擷取有關資源提供者和類型的資訊。您可以透過 REST API、Azure PowerShell 或 Azure CLI 擷取此資訊。

### REST API

若要取得所有可用的資源提供者，包括其類型、位置、API 版本及登錄狀態，請使用[列出所有資源提供者](https://msdn.microsoft.com/library/azure/dn790524.aspx)作業。

### PowerShell

下列範例示範如何取得所有可用的資源提供者。

    PS C:\> Get-AzureRmResourceProvider -ListAvailable
    
輸出將類似於：

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    ...

下一個範例示範如何取得特定資源提供者的資源類型。

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
輸出將類似於：

    ResourceTypeName                Locations                                         ApiVersions
    ----------------                ---------                                         ------
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} {20...
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} {20...
    ...
    
### Azure CLI

您可以使用下列命令，將資源提供者的資訊儲存至檔案。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

## 支援的區域

部署資源時，通常需要指定資源的區域。所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。此外，您的訂用帳戶上可能會有一些限制，以防止您使用某些支援該資源的區域。這些限制可能與您所在國家/地區的稅務問題有關，或者與由您的訂用帳戶管理員所放置，只能使用特定區域的原則結果有關。

如需所有 Azure 服務支援的所有區域之完整清單，請參閱[依地區提供的服務](https://azure.microsoft.com/regions/#services)。不過，這份清單可能包含您的訂用帳戶不支援的區域。您可以藉由執行下列其中一個命令，來判斷您的訂用帳戶支援之特定資源類型的區域。

### REST API

若要探索哪些區域可供您訂用帳戶中特定資源類型使用，請使用[列出所有資源提供者](https://msdn.microsoft.com/library/azure/dn790524.aspx)作業。

### PowerShell

下列範例示範如何取得支援網站的區域。

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

### Azure CLI

下列範例會針對每個資源類型傳回所有支援的位置。

    azure location list

您也可以使用 **jq** 之類的工具來篩選位置結果。若要了解 jq 之類的工具，請參閱[能與 Azure 互動的有用工具](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure)。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

它會傳回：

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## 支援的 API 版本

當您部署範本時，您必須指定要用來建立每個資源的 API 版本。API 版本會對應至資源提供者所發行的 REST API 作業版本。當資源提供者啟用新功能時，它會發行新版本的 REST API。因此，您在範本中指定的 API 版本會影響您可以在範本中指定的屬性。一般而言，您會想要在建立新範本時選取最新的 API 版本。對於現有的範本，您可以決定是否繼續使用舊的 API 版本，或更新您的範本以便最新版本利用新功能。

### REST API

若要探索哪些 API 版本可供資源類型使用，請使用[列出所有資源提供者](https://msdn.microsoft.com/library/azure/dn790524.aspx)作業。

### PowerShell

下列範例示範如何取得特定資源類型可用的 API 版本。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
輸出將類似於：
    
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

### Azure CLI

您可以使用下列命令，將資源提供者的資訊 (包括可用的 API 版本) 儲存至檔案。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

您可以開啟該檔案，然後尋找 **apiVersions** 元素

## 後續步驟

- 若要了解如何建立資源管理員範本，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 若要了解如何部署資源，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0114_2016-->