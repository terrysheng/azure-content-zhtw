<properties 
	pageTitle="將資源移動到新的資源群組" 
	description="使用 Azure PowerShell 或 REST API 將資源移動到至新的「Azure 資源管理員」資源群組。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="tomfitz"/>

# 將資源移動到新的資源群組或訂用帳戶

此主題說明如何將資源從某個資源群組移動到另一個資源群組。您也可以將資源移動到新的訂用帳戶。在下列情況中，您可能需要移動資源：

1. 因計費之目的，資源必須位於不同的訂用帳戶。
2. 資源不會再與先前的相同群組資源共用相同的生命週期。您想要將它移動到新的資源群組，以便您可以將該資源與其他資源分開管理。
3. 資源現在共用與不同資源群組中之其他資源相同的生命週期。您想要將它移動到其他資源所屬的資源群組，以便一起管理。

移動資源時有一些重要的考量：

1. 您無法變更資源的位置。移動資源只會將它移動到新的資源群組。新的資源群組可能會有不同的位置，但那樣不會變更資源的位置。
2. 您要將資源移動到其中的目的地資源群組，只應包含與該資源共用相同應用程式生命週期的資源。
3. 若使用 Azure PowerShell，請確定您使用最新版本。**Move-AzureResource** 命令經常更新。若要更新您的版本，執行 Microsoft Web Platform Installer 並檢查是否有新的版本可用(如需詳細資訊，請參閱[如何安裝及設定 Azure PowerShell](powershell-install-configure.md))。
4. 移動作業可能需要一段時間來完成，且作業期間您的 PowerShell 提示字元會等候直到作業完成。

## 支援的服務

目前並非所有服務都支援移動資源的功能。

目前支援移動到新資源群組與訂用帳戶的服務有：

- API 管理
- Azure 搜尋
- Data Factory
- 金鑰保存庫
- Mobile Engagement
- Operational Insights
- Redis 快取

支援移動到新資源群組，但不支援移動到新訂用帳戶的服務有：

- 運算 (傳統)
- 儲存體 (傳統)

目前不支援移動資源的服務有：

- 虛擬網路

## 使用 PowerShell 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 **Move-AzureResource** 命令。

第一個範例顯示如何將某個資源移動到新的資源群組。

    PS C:> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

第二個範例顯示如何將多個資源移動到新的資源群組。

    PS C:> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

若要移動到新的訂用帳戶，請包含 **DestinationSubscriptionId** 參數的值。

## 使用 REST API 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請執行：

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

使用目前包含您要移動之資源的訂用帳戶和資源群組取代 **{source-subscription-id}** 和 **{source-resource-group-name}**。針對 {api-version} 使用 **2015-01-01**。

在要求中包含定義目標資源群組和您想要移動之資源的 JSON 物件。

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## 後續步驟
- [Azure PowerShell 搭配資源管理員使用](./powershell-azure-resource-manager.md)
- [Azure CLI 搭配資源管理員使用](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [使用 Azure 入口網站管理資源](azure-portal/resource-group-portal.md)
- [使用標記組織您的資源](./resource-group-using-tags.md)

<!---HONumber=July15_HO4-->