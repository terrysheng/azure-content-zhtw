<properties 
	pageTitle="佈建 Redis 快取" 
	description="使用 Azure 資源管理員範本來部署 Azure Redis 快取。" 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# 使用範本建立 Redis 快取

在本主題中，您將學習如何建立 Azure 資源管理員範本，以部署 Azure Redis 快取。快取可以搭配現有的儲存體帳戶以保留診斷資料。您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

目前對於訂用帳戶，同一區域中所有快取的診斷設定是共用的。更新區域中的一個快取將會影響區域中的所有其他快取。

如需關於建立範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

如需完整範本，請參閱 [Redis 快取範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)。

>[AZURE.NOTE]新的 [Premium 層](cache-premium-tier-intro.md)中有可用的 ARM 範本。
>
>-    [建立具有叢集的 Premium Redis 快取](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [建立具有資料永續性的 Premium Redis 快取](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [建立具有 VNet 和選擇性叢集的 Premium Redis 快取](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>若要檢查最新的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)並搜尋 `Redis Cache`。

## 部署內容

在此範本中，您將部署使用現有儲存體帳戶的 Azure Redis 快取來診斷資料。

若要自動執行部署，請按一下下列按鈕：

[![部署至 Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## 參數

透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。此範本有一個 Parameters 區段，內含所有參數值。您應該為會隨著要部署的專案或要部署到的環境而變化的值定義參數。請不要為永遠保持不變的值定義參數。每個參數值都可在範本中用來定義所部署的資源。

我們將說明範本中的每個參數。

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Redics 快取的位置。針對最佳效能，使用要與快取搭配使用之應用程式相同的位置。

    "redisCacheLocation": {
      "type": "string"
    }

### diagnosticsStorageAccountName

要用於診斷的現有儲存體帳戶名稱。

    "diagnosticsStorageAccountName": {
      "type": "string"
    }

### enableNonSslPort

指出是否允許透過非 SSL 連接埠存取的布林值。

    "enableNonSslPort": {
      "type": "bool"
    }

### diagnosticsStatus

指出診斷是否啟用的值。使用 ON 或 OFF。

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## 要部署的資源

### Redis 快取

建立 Azure Redis 快取。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "redisVersion": "[parameters('redisCacheVersion')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
        "resources": [
          {
            "apiVersion": "2014-04-01",
            "type": "diagnosticSettings",
            "name": "service", 
            "location": "[parameters('redisCacheLocation')]",
            "dependsOn": [
              "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
            ],
            "properties": {
              "status": "[parameters('diagnosticsStatus')]",
              "storageAccountName": "[parameters('diagnosticsStorageAccountName')]",
              "retention": "30"
            }
          }
        ]
    }

## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_1217_2015-->