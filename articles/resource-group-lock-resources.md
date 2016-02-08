<properties 
	pageTitle="使用資源管理員鎖定資源 | Microsoft Azure" 
	description="透過套用限制到所有使用者和角色，防止使用者更新或刪除特定資源。" 
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
	ms.date="01/21/2016" 
	ms.author="tomfitz"/>

# 使用 Azure 資源管理員來鎖定資源

身為系統管理員，您會想在某些情況下鎖定訂用帳戶、資源群組或資源，以防止組織中其他使用者不小心刪除重要資源。

「Azure 資源管理員」透過資源管理鎖，提供限制在資源上執行作業的能力。鎖定是會在特定領域強制執行鎖定層級的原則。範圍可以是訂用帳戶、資源群組或資源。鎖定層級會識別原則的強制類型，其目前可設定為 **CanNotDelete**。**CanNotDelete** 表示經過授權的使用者仍然可以讀取和修改資源，但它們不能刪除任何受限制的資源。

鎖定不同於使用以角色為基礎的存取控制來指派使用者權限以執行特定動作。如要了解使用者和角色的設定權限，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制，而您一般僅在有限間套用限制鎖定。

當您在父範圍套用鎖定時，所有子系資源都會都繼承相同的鎖定。

## 常見案例

常見案例之一是，您有一個資源群組包含某些以頻繁開關模式運作的資源。VM 資源會定期開啟，以在給定的時間間隔內處理資料，然後便關閉。在此案例中，您會想要讓 VM 關機，但絕不會刪除儲存體帳戶。在此案例中，您會在儲存體帳戶上使用鎖定層級為 **CanNotDelete** 的資源鎖定。

## 誰可以建立或刪除您的組織中的鎖定

若要建立或刪除管理鎖定，您必須擁有 **Microsoft.Authorization/*** 或 **Microsoft.Authorization/locks/*** 動作的存取權。在內建角色中，只有**擁有者**和**使用者存取系統管理員**被授與這些動作的存取權。如需指派存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

## 在範本中建立鎖定

以下範例顯示的範本會在儲存體帳戶建立鎖定。要套用鎖定的儲存體帳戶會提供做為參數，並與 concat () 函式搭配使用。結果會是資源名稱的結尾附加了「Microsoft.Authorization」，然後是鎖定的名稱 (在此案例中為 **myLock**)。

必須依資源型別提供型別。針對儲存體，此型別為 "Microsoft.Storage/storageaccounts/providers/locks"。

範圍層級是使用資源的 **level** 屬性所設定。由於範例著重在協助使用者避免意外刪除，因此層級設定為 **CannotDelete**。

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

您可以使用[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)，來鎖定已部署的資源。此 REST API 可讓您建立及刪除鎖定，以及抓取現有鎖定的相關資訊。

若要建立鎖定，請執行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可以是訂用帳戶、資源群組或資源。lock-name 是您想要命名鎖定的任何名稱。對於 api-version，請使用 **2015-01-01**。

在要求中，包含指定鎖定屬性的 JSON 物件。

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

對於 lock-level，請指定 **CanNotDelete**。

如需範例，請參閱[管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)。

## 使用 Azure PowerShell 建立鎖定

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

您可以使用 **New-AzureRmResourceLock**，來利用 Azure PowerShell 鎖定已部署的資源，如下所示。透過 PowerShell，您只能將 **LockLevel** 設定為 **CanNotDelete**。

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell 為使用中的鎖定提供其他命令，例如可更新鎖定的 **Set-AzureRmResourceLock**，以及可刪除鎖定的 **Remove-AzureRmResourceLock**。

## 後續步驟

- 的如需使用資源鎖定的詳細資訊，請參閱[鎖定您的 Azure 資源](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- 若要了解如何邏輯地組織您的資源，請參閱[使用標記來組織您的資源](resource-group-using-tags.md)。
- 若要變更資源所在的資源群組，請參閱[將資源移動到新的資源群組](resource-group-move-resources.md)
- 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。如需詳細資訊，請參閱[使用原則來管理資源和控制存取](resource-manager-policy.md)。

<!---HONumber=AcomDC_0128_2016-->