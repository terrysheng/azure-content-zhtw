<properties 
	pageTitle="使用 Azure 資源管理員來鎖定資源" 
	description="鎖定資源以防止使用者更新或刪除特定資源。" 
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
	ms.date="08/10/2015" 
	ms.author="tomfitz"/>

# 使用 Azure 資源管理員來鎖定資源

身為系統管理員，您會想在某些情況下鎖定資源或資源群組，以防止組織中其他使用者寫入，或不小心刪除重要資源。

「Azure 資源管理員」透過資源管理鎖，提供限制在資源上執行作業的能力。資源鎖定是會在特定領域強制執行鎖定層級的原則。鎖定層級會識別原則的強制類型，目前有兩種值 – **CanNotDelete** 與 **ReadOnly**。領域以 URI 表示，而且可以是資源或資源群組。

鎖不同於對執行特定動作指派使用者權限。若要深入了解為使用者與角色設定權限，請參閱[預覽入口網站中的角色型存取控制](role-based-access-control-configure.md)與[管理及稽核對資源的存取](resource-group-rbac.md)。

## 常見案例

常見案例之一是，您有一個資源群組包含某些以頻繁開關模式運作的資源。VM 資源會定期開啟，以在給定的時間間隔內處理資料，然後便關閉。在此案例中，您會想要讓 VM 關機，但絕不會刪除儲存體帳戶。在此案例中，您會在儲存體帳戶上使用鎖定層級為 **CanNotDelete** 的資源鎖定。

在另一個案例中，您的企業可能有一段時間是不希望更新進入實際執行環境。**ReadOnly** 鎖定層級會防止建立或更新。如果您是零售公司，您可能不想在假期購物期間允許更新。如果您是金融服務公司，您要在開市的特定時段部署可能會有相關限制。資源鎖定能提供可適當鎖定資源的原則。這可以只套用到特定資源或到整個資源群組。

## 在範本中建立鎖定

以下範例顯示的範本會在儲存體帳戶建立鎖定。要套用鎖定的儲存體帳戶會提供做為參數，並與 concat () 函式搭配使用。結果會在資源名稱結尾附加 ‘Microsoft.Authorization’，然後是鎖定的名稱 (在此案例中是 **myLock**)。

必須依資源型別提供型別。針對儲存體，此型別為 "Microsoft.Storage/storageaccounts/providers/locks"。

領域層級是使用資源的 **level** 屬性所設定。由於範例著重在協助避免意外刪除，所以層級是設定為 **CannotDelete**。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "lockedResource": {
                "type": "string"
            }
        },
        "resources": [
            {
                "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
                "type": "Microsoft.Storage/storageAccounts/providers/locks",
                "apiVersion": "2015-01-01",
                "properties": {
	                "level": "CannotDelete"
                }
            }
        ]
    }

## 使用 REST API 建立鎖定

您可以使用[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx) 來鎖定已部署的資源。此 REST API 可讓您建立及刪除鎖定，以及抓取現有鎖定的相關資訊。

若要建立鎖定，請執行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可以是訂用帳戶、資源群組或資源。lock-name 是您想要命名鎖定的任何名稱。針對 api-version，請使用 **2015-01-01**。

在要求中，包含指定鎖定屬性的 JSON 物件。

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

對於 lock-level，請指定 **CanNotDelete** 或 **ReadOnly**。

如需範例，請參閱[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)。

## 使用 Azure PowerShell 建立鎖定

您可以在 Azure PowerShell 使用 **New-AzureResourceLock** 鎖定已部署的資源，如下所示。透過，您只能將 **LockLevel** 設定為 **CanNotDelete**。

    PS C:\> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell 針對使用中的鎖定提供其他命令，例如 **Set-AzureResourceLock** 可以更新鎖定，而 **Remove-AzureResourceLock** 可以刪除鎖定。

## 後續步驟

- 如需使用資源鎖定的詳細資訊，請參閱[鎖定您 Azure 資源](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- 若要了解如何以邏輯方式組織資源，請參閱[使用標記來組織您的資源](resource-group-using-tags.md)。
- 若要變更資源所在的資源群組，請參閱[將資源移至新的資源群組](resource-group-move-resources.md)

<!---HONumber=August15_HO7-->