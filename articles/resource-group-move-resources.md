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
	ms.date="03/04/2016" 
	ms.author="tomfitz"/>

# 將資源移動到新的資源群組或訂用帳戶

此主題說明如何將資源從某個資源群組移動到另一個資源群組。您也可以將資源移動到新的訂用帳戶 (不過此訂用帳戶必須存在於相同[租用戶](./active-directory/active-directory-howto-tenant.md)中)。在下列情況中，您可能需要移動資源：

1. 因計費之目的，資源必須位於不同的訂用帳戶。
2. 資源不會再與先前的相同群組資源共用相同的生命週期。您想要將它移動到新的資源群組，以便您可以將該資源與其他資源分開管理。
3. 資源現在共用與不同資源群組中之其他資源相同的生命週期。您想要將它移動到其他資源所屬的資源群組，以便一起管理。

## 移動資源前的注意事項

在移動資源之前，要考慮的重要事項如下︰

1. 您無法變更資源的位置。移動資源只會將它移動到新的資源群組。新的資源群組可能會有不同的位置，但那樣不會變更資源的位置。
2. 要移動之資源的資源提供者必須在目的地訂用帳戶中註冊。將資源移至新的訂用帳戶時，您可能會遇到這個問題，但永遠不會以該資源類型使用此訂用帳戶。例如，如果您要將 API 管理服務執行個體移動至尚未註冊 Microsoft.ApiManagement 資源提供者的訂用帳戶，將不會成功移動。若要了解如何檢查登錄狀態，並註冊資源提供者，請參閱[資源提供者和類型](../resource-manager-supported-services/#resource-providers-and-types)。
2. 您要將資源移動到其中的目的地資源群組，只應包含與該資源共用相同應用程式生命週期的資源。
3. 如果您使用 Azure PowerShell 或 Azure CLI，請確認您使用的是最新版本。若要更新您的版本，執行 Microsoft Web Platform Installer 並檢查是否有新的版本可用如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，以及[安裝 Azure CLI](xplat-cli-install.md)。
4. 移動作業可能需要一段時間來完成，且在作業期間，您的提示字元會等候到作業完成。
5. 當移動資源時，會在作業期間鎖定來源群組和目標群組。群組上的寫入和刪除作業將會封鎖，直到移動完成。

## 支援的服務

目前並非所有服務都支援移動資源的功能。

目前支援移動到新資源群組與訂用帳戶的服務有：

- API 管理
- App Service 應用程式 (請參閱下方的 [App Service 限制](#app-service-limitations))
- 自動化
- 批次
- Data Factory
- DocumentDB
- HDInsight 叢集
- 金鑰保存庫
- Mobile Engagement
- 通知中樞
- Operational Insights
- Redis 快取
- 搜尋
- SQL Database 伺服器 (請參閱下方的 [SQL Database 限制](#sql-database-limitations))

支援移動到新資源群組，但不支援移動到新訂用帳戶的服務有：

- 虛擬機器 (傳統)
- 儲存體 (傳統)
- 虛擬網路
- 雲端服務

目前不支援移動資源的服務有：

- 虛擬機器
- 儲存體
- ExpressRoute

## App Service 限制

使用 App Service 應用程式時，您無法只移動 App Service 方案。若要移動 App Service 應用程式，您的選項如下：

- 如果目的地資源群組還沒有 Microsoft.Web 資源，將所有的資源從某個資源群組移動到不同的資源群組。
- 將 Web 應用程式移動到不同的資源群組，但在原始的資源群組中保留 App Service 方案。

## SQL Database 限制

您無法將 SQL Database 與其伺服器分開移動。資料庫和伺服器必須位於相同的資源群組。當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。

## 使用 PowerShell 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 **Move-AzureRmResource** 命令。

第一個範例顯示如何將某個資源移動到新的資源群組。

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

第二個範例顯示如何將多個資源移動到新的資源群組。

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

若要移動到新的訂用帳戶，請為 **DestinationSubscriptionId** 參數設定某個值。

## 使用 Azure CLI 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 **azure resource move** 命令。下列範例說明如何將 Redis 快取移動到新的資源群組。請在 **-i** 參數中，為要移動的資源識別碼提供以逗號分隔的清單。

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## 使用 REST API 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請執行：

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

在要求主體中，您可以指定目標資源群組以及要移動的資源。如需有關 REST 移動作業的詳細資訊，請參閱[移動資源](https://msdn.microsoft.com/library/azure/mt218710.aspx)。

## 使用入口網站移動資源

您可以透過入口網站移動一些資源；不過，並非所有支援移動作業的資源提供者都透過入口網站提供該功能。

若要移動資源，請選取該資源，然後選取 [移動] 按鈕。

![移動資源](./media/resource-group-move-resources/move-resources.png)

您可以指定您想要將資源移動到何處。如果其他資源必須連同此資源移動，便會列出這些資源。

![選取目的地](./media/resource-group-move-resources/select-destination.png)

## 後續步驟
- [Azure PowerShell 搭配資源管理員使用](./powershell-azure-resource-manager.md)
- [Azure CLI 搭配資源管理員使用](./xplat-cli-azure-resource-manager.md)
- [使用 Azure 入口網站管理資源](azure-portal/resource-group-portal.md)
- [使用標記組織您的資源](./resource-group-using-tags.md)

<!---HONumber=AcomDC_0309_2016-->