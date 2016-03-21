<properties
	pageTitle="開發 PowerApps Enterprise 的 API | Microsoft Azure"
	description="建置或建立 PowerApps 的自訂 API"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="rajram"/>

# 開發適用於 PowerApps 的 API

您可以建立或開發可用於 PowerApps 的 API。步驟包括：

- 建置和部署 API
- 為您的 API 撰寫 [Swagger 2.0](http://swagger.io/) API 定義

本主題列出這些步驟。

## 步驟 1：建置和部署 API

建置和部署 PowerApps 的 API 就和建立任何 API 一樣。您可以選擇慣用的程式設計語言和架構。也可以選擇將 API 託管到任何位置。建議您將它託管於和 PowerApps 的 App Service 一樣的環境中，作為 [API 應用程式](https://azure.microsoft.com/services/app-service/api/)。

下列文件會說明如何在 App Service 環境中建置和部署 .Net、Java 或 Node.js API：

- [在 Azure App Service 中建置和部署 .NET](../app-service-api/app-service-api-dotnet-get-started.md)
- [在 Azure App Service 中建置和部署 Java API 應用程式](../app-service-api/app-service-api-java-api-app.md)
- [在 Azure App Service 中建置和部署 Node.js API 應用程式](../app-service-api/app-service-api-nodejs-api-app.md)


## 步驟 2：為您的 API 撰寫 Swagger 2.0 API 定義

只要遵循下列一項參考文件的*步驟 1*，就會為您的 API 自動產生標準的 Swagger 2.0 API 定義。若要最佳化以適合 PowerApps，您可以選擇使用下列結構描述擴充功能，自訂產生的 Swagger 2.0 API 定義。

若要了解通常如何自訂 Swagger 2.0 API 定義，請參閱[自訂 Swashbuckle 產生的 API 定義](../app-service-api/app-service-api-dotnet-swashbuckle-customize.md)。

### 結構描述延伸模組
除了 Swashbuckle 自動產生的 swagger 之外，還有一些其他的 swagger 擴充功能可在建立 PowerApps 的 API 時使用。本節會列出並描述這些擴充功能。

##### x-ms-summary
描述實體顯示名稱的字串，這些實體在 Swagger 規格中未定義摘要欄位。例如 [參數名稱]。

##### x-ms-visibility
這個值描述是否在邏輯流程設計工具中顯示實體。可以使用下列值：

- 「無」(預設值)
- 「進階」
- 「內部」- 邏輯流程的設計工具不會顯示這些作業

如果作業標示為「重要」，邏輯流程用戶端應該要反白顯示這些作業。

##### x-ms-trigger
定義這項作業是否可用為邏輯流程的觸發程序。選項包括：
	
- 無 (預設值)：作業不能用為觸發程序。
- 單一：這項作業也可用為觸發程序。
- 批次處理：這項作業可用為觸發程序。此外，這項作業以 JSON 'array' 物件回應，然後邏輯流程引發陣列中每個項目的觸發程序。


##### x-ms-dynamic-values
這是邏輯流程設計工具的提示：API 提供這個參數的動態允許值清單。邏輯流程設計工具可以叫用這個欄位值定義的作業，並從結果擷取可能的值。邏輯流程設計工具接著將這些值顯示為使用者的選項。

值是包含下列屬性的物件：
	
- operationId：比對已叫用作業 operationId 的字串
- parameters：物件屬性定義作業所需參數的物件
- value-collection：在回應裝載中評估物件陣列的路徑字串
- value-path：參考參數值的 "value-collection" 內部物件中的路徑字串。
- value-title：參考值描述的 "value-collection" 內部物件中的路徑字串。


範例：

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

上例中，swagger 定義了在 Salesforce 中建立新物件的 _TableData\_CreateItem_ 作業。

Salesforce 有很多內建物件。_x-ms-dynamic-values_ 在這裡用來協助設計工具釐清 Salesforce 的內建物件清單。它呼叫 _TableMetadata\_ListTables_ 以取得清單。

##### x-ms-dynamic-schema
這是邏輯流程設計工具的提示：這個參數 (或回應) 之結構描述的本質是動態的。它可以叫用這個欄位值定義的作業，並以動態方式探索結構描述。然後顯示適當的 UI 以接受使用者輸入，或顯示可用的欄位。

範例：

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

這在作業輸入為動態的案例中很有用。例如，考量 SQL 的案例。每個資料表的結構描述都不相同。因此，當使用者選取特定的資料表時，邏輯流程設計工具就必須了解資料表的結構，以便顯示資料行名稱。本文中，如果 swagger 定義具有 _x-ms-dynamic-schema_，它就會呼叫對應的作業擷取結構描述。

<!---HONumber=AcomDC_0309_2016-->