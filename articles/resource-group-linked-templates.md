<properties
   pageTitle="透過 Azure 資源管理員使用連結的範本"
   description="描述如何在「Azure 資源管理員」範本中使用連結的範本，以建立模組化範本方案。示範如何傳遞參數值、指定參數檔案，以及動態建立 URL。"
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
   ms.date="09/04/2015"
   ms.author="tomfitz"/>

# 透過 Azure 資源管理員使用連結的範本

您可以從某個「Azure 資源管理員」範本連結到另一個範本，這樣可讓您將部署分解成一組具有目標與特定目的的範本。就像將應用程式分解數個程式碼類別，分解有利於測試、重複使用和可讀性。

您可以從主要範本傳遞參數到連結的範本，且那些參數可以直接對應到由發出呼叫之範本所公開的參數或變數。連結的範本也可以將輸出變數傳遞回來源範本，讓範本之間可進行雙向資料交換。

## 連結至範本

您可以透過在主要範本中新增指向連結的範本之部署資源，以在兩個範本之間建立連結。將 **templateLink** 屬性設為連結的範本之 URI。您可以透過在範本中直接指定值，或透過連結到參數檔案，以為連結的範本提供參數值。以下範例會使用 **parameters** 屬性直接指定參數值。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

資源管理員服務必須能夠存取連結的範本，這表示您無法將本機檔案，或是將只能在本機網路上取得的檔案，指定為連結的範本。您可以只提供 URI 值，其中包含 **http** 或 **https**。有一個選擇是將連結的範本放在儲存體帳戶中，並將 URI 用於該項目，如底下所示。

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }


## 連結到參數檔案

下一個範例會使用 **parametersLink** 屬性連接至參數檔案。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

連結的參數檔案的 URI 值不能是本機檔案，而且必須包含 **http** 或 **https**。

## 使用變數來連結範本

先前的範例示範了範本連結的硬式編碼 URL 值。這種方法可能適用於簡單的範本，但不適合一組大型的模組化範本。不過，您可以建立一個儲存主要範本之基底 URL 的靜態變數，然後再從該基底 URL 連結動態建立連結的範本之 URL。這個方法的好處是您可以輕鬆地移動範本或建立分支範本，因為您只需要變更主要範本中的靜態變數。主要範本會於整個分解的範本傳遞正確的 URI。

下列範例示範如何使用基底 URL 來為連結的範本 (**sharedTemplateUrl** 和 **vmTemplate**) 建立兩個 URL。

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

## 從連結的範本傳回值

如果您需要從連結的範本將值傳遞給主要範本，您可以在連結範本的 **outputs** 區段中建立一個值。如需範例，請參閱[在 Azure 資源管理員範本中共用狀態](best-practices-resource-manager-state.md)。

## 後續步驟
- [撰寫範本](./resource-group-authoring-templates.md)
- [部署範本](azure-portal/resource-group-template-deploy.md)

<!---HONumber=Oct15_HO3-->