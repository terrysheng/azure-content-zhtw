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
   ms.date="03/09/2016"
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

## 針對一部虛擬機器建立多個資料磁碟

常見的案例是針對一部虛擬機器建立多個資料磁碟。您不能將 **copy** 與資料磁碟搭配使用，因為 **dataDisks** 是虛擬機器上的屬性，而不是它自己的資源類型。**copy** 僅適用於資源。相反地，您可以建立連結的範本，在其中定義所需的資料磁碟數目，並輸出具有指定資料磁碟數目的陣列。您可以從部署範本連結到此範本，並傳入要傳回的資料磁碟數目。在部署範本中，您會將 **dataDisks** 屬性設定為來自連結範本的輸出值。

此模式的完整範例會顯示於 [Create a VM with a dynamic selection of data disks (使用動態選取的資料磁碟來建立 VM)](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) 範本中。

部署範本的相關區段如下所示。已移除範本中的大部分內容，以便反白顯示動態建立多個資料磁碟時所涉及的區段。請注意參數 **numDataDisks**，其可讓您傳入要建立的磁碟數目。名為 **diskSelection** 的連結範本是指定於資源區段中。該連結範本的輸出會指派給虛擬機器上的 **dataDisks** 屬性。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        ...
        "numDataDisks": {
          "type": "string",
          "allowedValues": [
            "1",
            "2",
            "3",
            "4",
            "5",
            "6",
            "7",
            "8",
            "9",
            "10",
            "11",
            "12",
            "13",
            "14",
            "15",
            "16",
            "32"
          ],
          "metadata": {
            "description": "This parameter allows the user to select the number of disks they want"
          }
        }
      },
      "variables": {
        "artifactsLocation": "https://raw.githubusercontent.com/singhkay/azure-quickstart-templates/master/201-vm-dynamic-data-disks-selection/", 
        "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
        "sizeOfDataDisksInGB": 100,
        "diskCaching": "ReadWrite",
        ...
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "diskSelection",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "uri": "[concat(variables('artifactsLocation'), 'disksSelector', '.json')]",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "numDataDisks": {
                "value": "[parameters('numDataDisks')]"
              },
              "diskStorageAccountName": {
                "value": "[variables('storageAccountName')]"
              },
              "diskCaching": {
                "value": "[variables('diskCaching')]"
              },
              "diskSizeGB": {
                "value": "[variables('sizeOfDataDisksInGB')]"
              }
            }
          }
        },
        ...
        {
          "type": "Microsoft.Compute/virtualMachines",
          "properties": {
            "storageProfile": {
              ...
              "dataDisks": "[reference('diskSelection').outputs.dataDiskArray.value]"
            },
            ...
          }
        }
      ]
    }

連結的範本會定義要傳回的陣列。以下所示的範本會省略介於 3 到 32 之間的重複磁碟定義，但在您的實際範本中需要包含所有的這些定義。如果您需要 32 個以上的資料磁碟，您可以繼續使用此模式。請注意，在這整個範本中並未部署任何資源；相反地，它只會傳回一個陣列，其中包含定義資料磁碟之物件的要求數目。

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "string",
      "allowedValues": [
        "1",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "32"
      ],
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    },
    "diskStorageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the storage account where the data disks are stored"
      }
    },
    "diskCaching": {
      "type": "string",
      "allowedValues": [
        "None",
        "ReadOnly",
        "ReadWrite"
      ],
      "metadata": {
        "description": "Caching type for the data disks"
      }
    },
    "diskSizeGB": {
      "type": "int",
      "minValue": 1,
      "maxValue": 1023,
      "metadata": {
        "description": "Size of the data disks"
      }
    }
  },
  "variables": {
    "disksArray": {
      "1": "[variables('dataDisks')['1']]",
      "2": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'])]",
      "3": "[concat(variables('dataDisks')['1'], variables('dataDisks')['2'], variables('dataDisks')['3'])]",
      "4": "[variables('diskDeltas')['4delta']]",
      "5": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'])]",
      "6": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'])]",
      "7": "[concat(variables('diskDeltas')['4delta'], variables('dataDisks')['5'], variables('dataDisks')['6'], variables('dataDisks')['7'])]",
      "8": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'])]",
      "9": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'])]",
      "10": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'])]",
      "11": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('dataDisks')['9'], variables('dataDisks')['10'], variables('dataDisks')['11'])]",
      "12": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'])]",
      "13": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'])]",
      "14": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'])]",
      "15": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('dataDisks')['13'], variables('dataDisks')['14'], variables('dataDisks')['15'])]",
      "16": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'])]",
      "32": "[concat(variables('diskDeltas')['4delta'], variables('diskDeltas')['8delta'], variables('diskDeltas')['12delta'], variables('diskDeltas')['16delta'], variables('diskDeltas')['32delta'])]"
    },
    "dataDisks": {
      "1": [
        {
          "name": "datadisk1",
          "lun": 0,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "2": [
        {
          "name": "datadisk2",
          "lun": 1,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      "3": [
        {
          "name": "datadisk3",
          "lun": 2,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ],
      ...
      "32": [
        {
          "name": "datadisk32",
          "lun": 31,
          "vhd": {
            "uri": "[concat('http://', parameters('diskStorageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk32.vhd')]"
          },
          "createOption": "Empty",
          "caching": "[parameters('diskCaching')]",
          "diskSizeGB": "[parameters('diskSizeGB')]"
        }
      ]
    },
    "_comment2": "The delta arrays below build the difference from 0 to 4, 4 to 8, 8 to 12 disks and so on",
    "diskDeltas": {
      "4delta": [
        "[variables('dataDisks')['1'][0]]",
        "[variables('dataDisks')['2'][0]]",
        "[variables('dataDisks')['3'][0]]",
        "[variables('dataDisks')['4'][0]]"
      ],
      "8delta": [
        "[variables('dataDisks')['5'][0]]",
        "[variables('dataDisks')['6'][0]]",
        "[variables('dataDisks')['7'][0]]",
        "[variables('dataDisks')['8'][0]]"
      ],
      "12delta": [
        "[variables('dataDisks')['9'][0]]",
        "[variables('dataDisks')['10'][0]]",
        "[variables('dataDisks')['11'][0]]",
        "[variables('dataDisks')['12'][0]]"
      ],
      "16delta": [
        "[variables('dataDisks')['13'][0]]",
        "[variables('dataDisks')['14'][0]]",
        "[variables('dataDisks')['15'][0]]",
        "[variables('dataDisks')['16'][0]]"
      ],
      "32delta": [
        "[variables('dataDisks')['17'][0]]",
        "[variables('dataDisks')['18'][0]]",
        "[variables('dataDisks')['19'][0]]",
        "[variables('dataDisks')['20'][0]]",
        "[variables('dataDisks')['21'][0]]",
        "[variables('dataDisks')['22'][0]]",
        "[variables('dataDisks')['23'][0]]",
        "[variables('dataDisks')['24'][0]]",
        "[variables('dataDisks')['25'][0]]",
        "[variables('dataDisks')['26'][0]]",
        "[variables('dataDisks')['27'][0]]",
        "[variables('dataDisks')['28'][0]]",
        "[variables('dataDisks')['29'][0]]",
        "[variables('dataDisks')['30'][0]]",
        "[variables('dataDisks')['31'][0]]",
        "[variables('dataDisks')['32'][0]]"
      ]
    }
  },
  "resources": [],
  "outputs": {
    "dataDiskArray": {
      "type": "array",
      "value": "[variables('disksArray')[parameters('numDataDisks')]]"
    }
  }
}

```

## 後續步驟
- 若要了解範本區段的相關資訊，請參閱[編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。
- 如需可以在範本中使用的函式清單，請參閱 [Azure 資源管理員範本函式](./resource-group-template-functions.md)。
- 若要了解如何部署範本，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0316_2016-->