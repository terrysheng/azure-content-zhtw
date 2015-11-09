<properties
   pageTitle="儲存體的資源管理員範本 | Microsoft Azure"
   description="顯示儲存體帳戶的資源管理員結構描述。"
   services="azure-resource-manager,storage"
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
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# 儲存體帳戶 - 範本結構描述

建立儲存體帳戶。

## 結構描述格式

若要建立儲存體帳戶，在範本的資源區段中新增下列結構描述。

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
        	"accountType": string
        }
    }

## 值

下表描述您在結構描述中必須設定的值。

| 名稱 | 類型 | 必要 | 允許的值 | 說明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | 列舉 | 是 | **Microsoft.Storage/storageAccounts** | 要建立的資源類型。 |
| apiVersion | 列舉 | 是 | **2015-06-15** <br /> **2015-05-01-preview** | 要用來建立資源的應用程式開發介面 (API) 版本。 | 
| name | 字串 | 是 | 介於 3 到 24 個字元，只能使用數字和小寫字母 | 要建立的儲存體帳戶的名稱。名稱在整個 Azure 中必須是唯一的。請考慮使用 [uniqueString](resource-group-template-functions.md#uniquestring) 函式搭配您的命名慣例，如以面範例所示。 |
| location | 字串 | 是 | 若要判斷有效的區域，請參閱[支援的區域](resource-manager-supported-services.md#supported-regions)。 | 要裝載儲存體帳戶的區域。 |
| 屬性 | 物件 | 是 | (如下所示) | 指定儲存體帳戶要建立之類型的物件。

### 屬性物件

| 名稱 | 類型 | 必要 | 允許的值 | 說明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | 字串 | 是 | **Standard\_LRS**<br />**Standard\_ZRS**<br />**Standard\_GRS**<br />**Standard\_RAGRS**<br />**Premium\_LRS** | 儲存體帳戶的類型。允許的值分別對應至標準本地備援、標準區域備援、標準異地備援、標準讀取存取異地備援、高階本地備援。如需這些帳戶類型的詳細資訊，請參閱 [Azure 儲存體複寫](./storage/storage-redundancy.md)。 |

	
## 範例

下列範例使用依據資源群組識別碼而命名的唯一名稱來部署標準本機備援儲存體帳戶。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
		         "location": "[resourceGroup().location]",
        	     "properties": 
        	     {
        		      "accountType": "Standard_LRS"
        	     }
	        }
	    ],
	    "outputs": {}
    }

## 後續步驟

- 如需儲存體的詳細資訊，請參閱 [Microsoft Azure 儲存體簡介](./storage/storage-introduction.md)。
- 如需搭配使用新儲存體帳戶與虛擬機器的範例範本，請參閱[部署簡單的 Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) 或 [部署簡單的 Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)。

<!---HONumber=Nov15_HO1-->