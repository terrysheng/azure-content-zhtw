<properties
   pageTitle="建立資源的多個執行個體"
   description="說明如何在部署資源時於「Azure 資源管理員」範本中使用 copy 作業，以進行多次逐一執行。"
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
   ms.date="07/14/2015"
   ms.author="tomfitz"/>

# 在 Azure 資源管理員中建立資源的多個執行個體

此主題說明如何逐一查看您的「Azure 資源管理員」範本，以建立資源的多個執行個體。

## copy 和 copyIndex()

若要在資源中建立多次，您可以定義 **copy** 物件，以指定逐一執行的次數。copy 的格式如下：

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

您可以使用 **copyIndex()** 函式存取目前反覆項目的值，如以下 concat 函式中所示。

    [concat('examplecopy-', copyIndex())]

## 在名稱中使用索引值

您可以使用 copy 作業建立資源的多個執行個體，並以遞增的索引值命名其唯一名稱。例如，您可以在每個已部署之資源名稱的結尾加上唯一的數字。部署名稱如下的三個網站：

- examplecopy-0
- examplecopy-1
- examplecopy-2。

使用下列範本：

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

## 位移索引值

您會在上述範例中注意到，索引值從零到 2。若要位移索引值，您可以傳遞 **copyIndex()** 函數中的值，例如 **copyIndex(1)**。要執行的反覆項目數仍然在複製項目中指定，但 copyIndex 的值會由指定的值位移。因此，使用和前一個範例相同的範本，但指定 **copyIndex(1)** 會部署下列三個名稱的網站：

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 搭配陣列使用
   
使用陣列時，複製作業會特別有幫助，因為您可以逐一查看陣列中的每個項目。部署名稱如下的三個網站：

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

使用下列範本：

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

## 後續步驟
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)
- [使用 Azure 資源管理員範本部署應用程式](azure-portal/resource-group-template-deploy.md)

<!---HONumber=August15_HO6-->