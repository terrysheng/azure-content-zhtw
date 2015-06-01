<properties
   pageTitle="Azure 資源管理員範本函數"
   description="描述要在 Azure 資源管理員範本中使用以將應用程式部署到 Azure 的函數。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Azure 資源管理員範本函數

本主題描述您可以在 Azure 資源管理員範本中使用的所有函數。

## base64

**base64 (inputString)**

傳回輸入字串的 base64 表示法。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| inputString | 是 | 要以 base64 表示法傳回的字串值。

下列範例顯示如何使用 base64 函數。

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat (arg1, arg2, arg3, ...)**

結合多個字串值，並傳回產生的字串值。此函數可以接受任意數目的引數。

下列範例顯示如何結合多個值來傳回值。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## listKeys

**listKeys (resourceName 或 resourceIdentifier, [apiVersion])**

傳回儲存體帳戶的金鑰。使用 [resourceId](./#resourceid) 函數或使用格式 **providerNamespace/resourceType/resourceName**，即可指定 resourceId。您可以使用此函數來取得 primaryKey 和 secondaryKey。
  
| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier | 是 | 儲存體帳戶的唯一識別碼。
| apiVersion | 是 | 資源執行階段狀態的 API 版本。

下列範例顯示如何在 outputs 區段中從儲存體帳戶傳回金鑰。

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## 參數

**parameters (parameterName)**

傳回參數值。指定的參數名稱必須定義於範本的 parameters 區段中。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| parameterName | 是 | 要傳回的參數名稱。

下列範例顯示 parameters 函數的簡化用法。

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## provider

**provider (providerNamespace, [resourceType])**

傳回資源提供者和其所支援資源類型的相關資訊。如果未提供類型，則會傳回所有支援的類型。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| providerNamespace | 是 | 提供者的命名空間
| resourceType | 否 | 所指定命名空間內的資源類型。

每個支援的類型都會以下列格式傳回：

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

下列範例顯示如何使用 provider 函數：

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## reference

**reference (resourceName 或 resourceIdentifier, [apiVersion])**

可讓運算式從另一個資源的執行階段狀態衍生其值。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier | 是 | 資源的名稱或唯一識別碼。
| apiVersion | 否 | 資源執行階段狀態的 API 版本。如果在相同的範本內未供應資源，則應該使用參數。

**reference** 函數會從執行階段狀態衍生其值，因此不能用在 variables 區段中。它可以用於範本的 outputs 區段中。

如果在相同的範本內佈建所參考的資源，則可使用 reference 運算式來宣告一個資源相依於另一個資源。

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## resourceGroup

**resourceGroup()**

傳回代表目前資源群組的結構化物件。物件的格式如下：

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

下列範例使用資源群組位置來指派網站的位置。

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

傳回資源的唯一識別碼。如果資源名稱不確定或未佈建在相同的範本內，請使用此函數。識別碼會以下列格式傳回：

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| 參數 | 必要 | 說明
| :---------------: | :------: | :----------
| resourceGroupName | 否 | 選用資源群組名稱。預設值為目前資源群組。擷取另一個資源群組中的資源時，請指定此值。
| resourceType | 是 | 資源的類型 (包括資源提供者命名空間)。
| resourceName1 | 是 | 資源的名稱。
| resourceName2 | 否 | 如果是巢狀資源，則為下一個資源名稱區段。

下列範例顯示如何擷取網站和資料庫的資源識別碼。網站存在於名稱為 **myWebsitesGroup** 的資源群組中，而資料庫存在於此範本的目前資源群組中。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
通常，在替代資源群組中使用儲存體帳戶或虛擬網路時，需要使用此函數。儲存體帳戶或虛擬網路可能用於多個資源群組中；因此，您不想要在刪除單一資源群組時刪除它們。下列範例顯示如何輕鬆地使用外部資源群組中的資源：

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }


## 訂用帳戶)

**subscription()**

以下列格式傳回訂用帳戶的詳細資料。

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

下列範例顯示在 outputs 區段中所呼叫的 subscription 函數。

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## variables

**variables (variableName)**

傳回變數的值。指定的變數名稱必須定義於範本的 variables 區段中。

| 參數 | 必要 | 說明
| :--------------------------------: | :------: | :----------
| 變數名稱 | 是 | 要傳回的變數名稱。


## 後續步驟
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)
- [進階範本作業](./resource-group-advanced-template.md)
- [使用 Azure 資源管理員範本部署應用程式](./resouce-group-template-deploy.md)
- [Azure 資源管理員概觀](./resource-group-overview.md)

<!--HONumber=52-->
