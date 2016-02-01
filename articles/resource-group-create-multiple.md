<properties
   pageTitle="部署資源的多個執行個體 | Microsoft Azure"
   description="使用「Azure 資源管理員」範本中的複製作業和陣列，並在部署資源時多次逐一執行。"
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
   ms.date="01/15/2016"
   ms.author="tomfitz"/>

# 在 Azure 資源管理員中建立資源的多個執行個體

此主題說明如何逐一查看您的「Azure 資源管理員」範本，以建立資源的多個執行個體。

## copy、copyIndex 和 length

若要在資源中建立多次，您可以定義 **copy** 物件，以指定逐一執行的次數。copy 的格式如下：

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

您可以使用 **copyIndex()** 函式存取目前反覆項目的值，如以下 concat 函式中所示。

    [concat('examplecopy-', copyIndex())]

從值的陣列建立多個資源時，您可以使用 **length** 函數來指定計數。您可以提供陣列為 length 函數的參數。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

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
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
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
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

您當然可以將列印張數設為陣列長度以外的值。比方說，您可以建立具有許多值的陣列，然後傳入參數值，指定要部署多少陣列項目。在此情況下，您可以設定列印張數，如第一個範例所示。

## 依迴圈中的資源而定

您可以透過使用 **dependsOn** 項目來指定在另一個資源之後部署資源。當您需要部署相依於迴圈中之資源集合的資源時，您可以使用 **dependsOn** 項目中複製迴圈的名稱。下列範例示範如何在部署虛擬機器之前部署 3 個儲存體帳戶。不會顯示完整的虛擬機器定義。請注意，copy 項目將**名稱**設定為 **storagecopy** 且虛擬機器的 **dependsOn** 項目也設定為 **storagecopy**。

    {
	    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "contentVersion": "1.0.0.0",
	    "parameters": {},
	    "resources": [
	        {
		        "apiVersion": "2015-06-15",
		        "type": "Microsoft.Storage/storageAccounts",
		        "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
		        "location": "[resourceGroup().location]",
		        "properties": {
                    "accountType": "Standard_LRS"
            	 },
		        "copy": { 
         	        "name": "storagecopy", 
         	        "count": 3 
      		    }
	        },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
	    ],
	    "outputs": {}
    }

## 在巢狀資源使用迴圈

您無法為巢狀資源使用複製迴圈。如果您需要為一個資源 (您通常該資源定義為另一個資源中的巢狀資源) 建立多個執行個體，您必須改為將該資源建立為最上層資源，並透過 **type** 和 **name** 屬性定義該資源與父資源的關聯性。

例如，假設您通常將資料集定義為 Data Factory 中的巢狀資源。

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
若要建立資料集的多個執行個體，您必須變更您的範本，如下所示。請注意到，完整類型和名稱都包含了該 Data Factory 的名稱。

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## 後續步驟
- 若要了解範本區段的相關資訊，請參閱[編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。
- 如需可以在範本中使用的函式清單，請參閱 [Azure 資源管理員範本函式](./resource-group-template-functions.md)。
- 若要了解如何部署範本，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0121_2016-->