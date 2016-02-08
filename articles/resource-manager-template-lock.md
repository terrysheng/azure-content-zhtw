<properties
   pageTitle="資源鎖定的資源管理員範本 | Microsoft Azure"
   description="說明可透過範本部署資源鎖定的資源管理員結構描述。"
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
   ms.date="01/21/2016"
   ms.author="tomfitz"/>

# 資源鎖定範本結構描述

在資源及其子資源上建立新的鎖定。

## 結構描述格式

若要建立鎖定，請將下列結構描述新增到範本的資源區段。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## 值

下表描述您在結構描述中必須設定的值。

| 名稱 | 類型 | 必要 | 允許的值 | 說明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| 類型 | 列舉 | 是 | 資源：<br />**{namespace}/{type}/providers/locks**<br /><br />資源群組：<br />**Microsoft.Authorization/locks** | 要建立的資源類型。 |
| apiVersion | 列舉 | 是 | **2015-01-01** | 要用來建立資源的應用程式開發介面 (API) 版本。 |  
| 名稱 | 字串 | 是 | 資源：<br />**{resouce}/Microsoft.Authorization/{lockname}**<br /><br />資源群組：<br />**{lockname}****<br /><br />最多 64 個位元<br />不能包含 <、>、%、&、? 或控制字元。 | 用於指定要鎖定資源與鎖定名稱的值。 | | dependsOn | 陣列 | No | 以逗號分隔的資源名稱清單或資源唯一的識別碼。 | 此鎖定所依賴的資源集合。如果您正在鎖定的資源是部署在同一個範本中，請在此元素中包含資源名稱，確保會先部署該資源。| | 屬性 | 物件 | 是 | (如下所示) | 識別鎖定類型的物件，以及鎖定的相關註解。| 

### 屬性物件

| 名稱 | 類型 | 必要 | 允許的值 | 說明 |
| ------- | ---- | ---------------- | -------- | ----------- |
| 層級 | 列舉 | 是 | **CannotDelete** | 要套用至範圍的鎖定類型。CanNotDelete 可允許修改，但會防止刪除。 |
| 版本 | 字串 | 否 | 512 個字元 | 鎖定的描述。 |


## 如何使用鎖定資源

您可以將此資源新增至範本，以避免在資源上進行指定的動作。鎖定會套用到所有的使用者和群組。一般而言，您只能套用鎖定一段有限的時間，例如當處理序正在執行時，或者您想要確定組織中的某個人不會不小心修改或刪除資源。

若要建立或刪除管理鎖定，您必須能夠存取 **Microsoft.Authorization/*** 或 **Microsoft.Authorization/locks/*** 動作。內建角色中，只有**擁有者**和**使用者存取系統管理員**被授與這些動作。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

鎖定會套用至指定的資源和任何子資源。

您可以使用 PowerShell 命令 **Remove-AzureRmResourceLock** 或使用 REST API 的[刪除作業](https://msdn.microsoft.com/library/azure/mt204562.aspx)移除鎖定。

## 範例

下列範例會將 cannot-delete 鎖定套用到 Web 應用程式。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
      			"type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

接下來的範例會將 cannot-delete 鎖定套用到資源群組。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## 後續步驟

- 如需範本結構的相關資訊，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 如需鎖定的詳細資訊，請參閱[使用 Azure 資源管理員鎖定資源](resource-group-lock-resources.md)。

<!---HONumber=AcomDC_0128_2016-->