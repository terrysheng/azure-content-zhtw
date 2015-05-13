<properties
   pageTitle="Azure 資源管理員進階範本作業"
   description="描述將應用程式部署至 Azure 時，如何在 Azure 資源管理員範本中使用巢狀範本和複製作業。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# 進階範本作業

本主題描述將資源部署至 Azure 時可用來執行更進階工作的複製作業和巢狀範本。

## 複製

可讓您在部署資源時逐一查看陣列並使用每個元素。
   
下列範例會部署名稱為 examplecopy-Contoso、examplecopy-Fabrikam、examplecopy-Coho 的三個網站。

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## 巢狀範本

有時，您可能需要合併兩個範本，也可能需要從父系範本啟動子系範本。做法是在主版範本內使用部署資源來部署子範本。您提供巢狀範本的 URI，如下所示。

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## 後續步驟
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)
- [使用 Azure 資源管理員範本部署應用程式](./resouce-group-template-deploy.md)
- [Azure 資源管理員概觀](./resource-group-overview.md)

<!--HONumber=52-->
