<properties
   pageTitle="連結資源的資源管理員範本 | Microsoft Azure"
   description="顯示在相關資源之間建立連結的資源管理員範本結構描述。"
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
   ms.date="12/09/2015"
   ms.author="tomfitz"/>

# 資源連結 - 範本結構描述

這會在兩個資源之間建立連結。該連結會套用至稱為「來源資源」的資源。連結中的第二個資源則稱為「目標資源」。

## 結構描述格式

若要建立連結，請將下列結構描述新增到範本的資源區段。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## 值

下表描述您在結構描述中必須設定的值。

| 名稱 | 類型 | 必要 | 允許的值 | 說明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| 類型 | 列舉 | 是 | **{namespace}/{type}/providers/links** | 要建立的資源類型。{namespace} 和 {type} 值指的是來源資源的提供者命名空間和資源類型。 |
| apiVersion | 列舉 | 是 | **2015-01-01** | 要用來建立資源的應用程式開發介面 (API) 版本。 |  
| 名稱 | 字串 | 是 | **{resouce}/Microsoft.Resources/{linkname}**<br /><br />最多 64 個位元<br />不能包含 <、>、%、&、? 或控制字元。 | 同時指定來源資源名稱和連結名稱的值。 |
| dependsOn | array | 否 | 以逗號分隔的資源名稱或資源唯一識別碼清單。 | 此連結所依存的資源集合。若您所連結的資源被部署到相同的範本，請在此元素中包含那些資源的名稱，以確保它們會被優先部署。 | 
| 屬性 | 物件 | 是 | (如下所示) | 能識別要連結的資源，以及該連結之相關資訊的物件。 |  

### 屬性物件

| 名稱 | 類型 | 必要 | 允許的值 | 說明 |
| ------- | ---- | ---------------- | -------- | ----------- |
| targetId | 字串 | 是 | | 要連結之目標資源的識別碼。 |
| 版本 | 字串 | 否 | 512 個字元 | 鎖定的描述。 |


## 如何使用連結資源

當兩個資源的相依性在部署後持續時，在它們之間套用連結。例如，某個 app 可能會連接到位於不同資源群組的資料庫。您可以藉由在該 app 和資料庫之間建立連結，來定義該相依性。連結能讓您記錄兩個資源之間的關聯性。稍後您或組織內的其他人可以向資源查詢連結，以探索該資源和其他資源一同運作的方式。

所有已連結的資源都必須屬於同一個訂用帳戶。每個資源都可以連結至其他 50 個資源。如果任何已連結的資源被刪除或移動，連結擁有者必須清除剩餘的連結。

若要透過 REST 使用連結，請參閱[連結的資源 (英文)](https://msdn.microsoft.com/library/azure/mt238499.aspx)。

使用下列 Azure PowerShell 命令來查看訂用帳戶中的所有連結。您可以提供其他參數來限制結果。

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -OutputObjectFormat New

## 範例

下列範例會將唯讀鎖定套用到 Web 應用程式。

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
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
        ],
        "outputs": {}
    }


## 後續步驟

- 如需範本結構的相關資訊，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

<!-------HONumber=AcomDC_1210_2015--->