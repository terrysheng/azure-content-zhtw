<properties
   pageTitle="資源管理員支援的服務和支援的區域 |Microsoft Azure"
   description="說明支援資源管理員的資源提供者以及可裝載資源的區域。"
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
   ms.date="11/11/2015"
   ms.author="tomfitz"/>

# 資源管理員對於服務、區域和 API 版本的支援

Azure 資源管理員提供一種新方式來部署和管理組成應用程式的服務。大部分但並非所有的服務都支援資源管理員，有些服務僅部分支援資源管理員。Microsoft 會針對每個服務啟用資源管理員，這對於未來的解決方案而言很重要，但在支援一致化之前，您需要了解每個服務的目前狀態。本主題提供支援 Azure 資源管理員的資源提供者清單。

部署資源時，您也需要知道哪些區域支援這些資源，以及哪些 API 版本適用於資源。[支援區域](#supported-regions)一節說明如何找出哪些區域適用於您的訂用帳戶和資源。[支援的 API 版本](#supported-api-versions)一節說明如何判斷您可以使用哪些 API 版本。

下表列出哪些服務可透過資源管理員支援部署和管理，哪些則否。標題為**移動資源**的欄表示這種類型的資源是否可以移至新的資源群組和新的訂用帳戶。標題為 **Preview入口網站**的欄表示您是否可以透過 Preview 入口網站建立服務。


## 計算

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| 虛擬機器 | 是 | 是，許多選項 | 否 | [建立 VM](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| 批次 | 是 | [是 (僅限傳統)](https://portal.azure.com/#create/Microsoft.BatchAccount) | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Dynamics 週期服務 | 是 | 否 | | | |
| 虛擬機器 (傳統) | 有限 | 是 | 部分 (請參閱下文) | - | - |
| 遠端 App | 否 | - | - | - | - |
| Service Fabric | 否 | - | - | - | - |

虛擬機器 (傳統) 是指已透過傳統部署模型部署的資源，而不是透過資源管理員部署模型部署的資源。一般而言，這些資源不支援資源管理員作業，但已啟用某些作業。如需這些部署模型的詳細資訊，請參閱[了解資源管理員部署和傳統部署](resource-manager-deployment-model.md)。

虛擬機器 (傳統) 資源可以移至新的資源群組，但不能移至新的訂用帳戶。

## Web 與行動

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API 管理 | 是 | 否 | 是 | [建立 API](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API 應用程式 | 是 | [是](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| Web Apps | 是 | [是](https://portal.azure.com/#create/Microsoft.WebSite) | 是，但有限制 (請參閱下文) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| 通知中樞 | 是 | [是](https://portal.azure.com/#create/Microsoft.NotificationHub) | | [建立通知中樞](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| Logic Apps | 是 | [是](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | | | |
| Mobile Engagement | 是 | 否 | 是 | | |

使用 Web 應用程式時，您無法只移動 App Service 方案。若要移動 Web 應用程式，您的選項如下：

- 如果目的地資源群組還沒有 Microsoft.Web 資源，將所有的資源從某個資源群組移動到不同的資源群組。
- 將 Web 應用程式移動到不同的資源群組，但在原始的資源群組中保留 App Service 方案。


## 資料與儲存體

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | 是 | [是](https://portal.azure.com/#create/Microsoft.DocumentDB) | 是 | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| 儲存體 | 是 | [是](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | | [建立儲存體](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [儲存體帳戶](resource-manager-template-storage.md) |
| Redis 快取 | 是 | [是](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | 是 | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL Database | 是 | [是](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.9-preview) | 是 | [建立資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| 搜尋 | 是 | [是](https://portal.azure.com/#create/Microsoft.Search) | 是 | [搜尋 REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL 資料倉儲 | 是 | [是](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | 否 | 否 | - | - | - |
| 備份 | 否 | 否 | - | - | - |
| Site Recovery | 否 | 否 | - | - | - |
| 受管理的快取 | 否 | 否 | - | - | - |
| 資料目錄 | 否 | 否 | - | - | - |

## Analytics

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 事件中心 | 是 | 否 | | [建立事件中樞](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| 串流分析 | 是 | [是](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | 是 | [是](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | | | |
| Data Factory | 是 | [是](https://portal.azure.com/#create/Microsoft.DataFactory) | 是 | [建立 Data Factory](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| 機器學習服務 | 否 | 否 | - | - | - | | 資料目錄 | 否 | 否 | - | - | - |

## 網路

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| 應用程式閘道 | 是 | | | | |
| DNS | 是 | | | [建立 DNS 區域](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 負載平衡器 | 是 | | | [建立負載平衡器](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 虛擬網路 | 是 | [是](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | 否 | [建立虛擬網路](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 流量管理員 | 是 | 否 | | [建立流量管理員設定檔](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| ExpressRoute | 是 | 否 | 否 | [ExpressRoute REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## 媒體與 CDN

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| CDN | 是 (預覽) | 否 | | | |
| 媒體服務 | 否 | 否 | | | |


## 混合式整合

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk 服務 | 是 | 否 | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| 服務匯流排 | 是 | 否 | | [服務匯流排 REST](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| 備份 | 否 | 否 | - | - | - | | Site Recovery | 否 | 否 | - | - | - |

## 身分識別與存取管理 

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | 否 | 否 | - | - | - |
| Azure Actice Directory B2C | 否 | 否 | - | - | - |
| Multi-Factor Authentication | 否 | 否 | - | - | - |

## 開發人員服務 

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | 是 | [是](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing 地圖 | 是 | [是](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Visual Studio 帳戶 | 是 | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## 管理 

| 服務 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 自動化 | 是 | [是](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | | | |
| 金鑰保存庫 | 是 | 否 | 是 | [金鑰保存庫 REST](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| 排程器 | 是 | 否 | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | 是 | 否 | 是 | | |
| IoTHubs | 是 | [是](https://portal.azure.com/#create/Microsoft.IotHub) | | | |

## 資源管理員

| 功能 | 已啟用資源管理員 | 預覽入口網站 | 移動資源 | REST API | 結構描述 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Authorization | 是 | N/A | N/A | [管理鎖定](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[角色型存取控制](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [資源鎖定](resource-manager-template-lock.md)<br />[角色指派](resource-manager-template-role.md) |
| 資源 | 是 | N/A | N/A | [Linked resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [資源連結](resource-manager-template-links.md) |


## 支援的區域

部署資源時，通常需要指定資源的區域。所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。此外，您的訂用帳戶上可能會有一些限制，以防止您使用某些支援該資源的區域。這些限制可能與您所在國家/地區的稅務問題有關，或者與由您的訂用帳戶管理員所放置，只能使用特定區域的原則結果有關。

若要取得所有 Azure 服務之所有支援區域的完整清單，請參閱[依地區提供的服務](https://azure.microsoft.com/regions/#services)。不過，這份清單可能包含您的訂用帳戶不支援的區域。您可以藉由執行下列其中一個命令，來判斷您的訂用帳戶支援之特定資源類型的區域。

### REST API

若要探索哪些區域可供您的訂用帳戶中的特定資源類型使用，請使用[列出所有資源提供者](https://msdn.microsoft.com/library/azure/dn790524.aspx)作業。

### PowerShell

下列範例示範如何使用 Azure PowerShell 1.0 Preview 來取得支援網站的區域。如需 1.0 Preview 版本的詳細資訊，請參閱 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)

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

Azure PowerShell 0.9.8 請使用下列命令：

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure CLI

下列範例會針對每個資源類型傳回所有支援的位置。

    azure location list

您也可以使用 **jq** 之類的工具來篩選位置結果。若要了解 jq 等工具，請參閱[與 Azure 互動的有用工具](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure)。

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

下列範例示範如何使用 Azure PowerShell 1.0 Preview 來取得特定資源類型可用的 API 版本。

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

Azure PowerShell 0.9.8 請使用：

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

您可以使用下列命令，將資源提供者的資訊 (包括可用的 API 版本) 儲存至檔案。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

您可以開啟該檔案並尋找 **apiVersions** 元素

## 後續步驟

- 若要了解如何建立資源管理員範本，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 若要了解如何部署資源，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

<!---HONumber=Nov15_HO3-->