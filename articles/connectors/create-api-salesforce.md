<properties
pageTitle="在 PowerApps Enterprise 及邏輯應用程式中新增 Salesforce API | Microsoft Azure"
description="搭配 REST API 參數來使用 Salesforce API 的概觀"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/25/2016"
ms.author="deonhe"/>

# 開始使用 Salesforce API
連線到 Salesforce 並建立物件、取得物件等等。您可以從下列應用程式使用 Salesforce API：

- PowerApps 
- 邏輯應用程式 

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。若為 2014-12-01-preview 結構描述版本，請按一下 [Salesforce API](../app-service-logic/app-service-logic-connector-salesforce.md)。

您可以利用 Salesforce 來：

- 根據您從 SalesForce 所取得的資料，來建置您的商務流程。 
- 在物件建立或更新時使用觸發程序。
- 使用動作來建立物件、刪除物件等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當 Salesforce 中有新物件建立時，您可以利用 Office 365 來傳送電子郵件。
- 將 Salesforce API 新增到 PowerApps Enterprise，讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需有關如何在 PowerApps Enterprise 中新增 API 的資訊，請移至[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Salesforce API 包含下列觸發程序及動作。

| 觸發程序 | 動作|
| --- | --- |
|<ul><li>當物件建立時</li><li>當物件遭到修改時</li></ul> | <ul><li>建立物件</li><li>取得物件</li><li>當物件建立時</li><li>當物件遭到修改時</li><li>刪除物件</li><li>取得物件</li><li>取得物件類型 (SObjects)</li><li>更新物件</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 Salesforce 的連線 

### 在 PowerApps 中新增其他組態
當您把 Salesforce 新增到 PowerApps Enterprise 時，請輸入您的 Salesforce 應用程式的 [應用程式金鑰] 及 [應用程式密碼] 值。[重新導向 URL] 值也會用在您的 Salesforce 應用程式中。如果您沒有 Salesforce 應用程式，可以使用下列步驟來建立：

1. [登入 Salesforce 開發人員首頁][5]，選取您的設定檔，然後選取 [設定]：![Salesforce 首頁][6]

3. 選取 [**Create (建立)**]，然後選取 [**Apps (應用程式)**]。在 [應用程式] 頁面中，選取 [已連線的應用程式] 下方的 [新增]：![Salesforce 建立應用程式][7]

4. 在 [**New Connected App (新增連接的應用程式)**] 中：

	1. 輸入 [已連線的應用程式名稱] 的值。  
	2. 輸入 [API 名稱] 的值。  
	3. 輸入 [連絡人電子郵件] 的值。  
	4. 在 [API (啟用 OAuth 設定)] 下方，選取 [啟用 OAuth 設定]，然後把 [回呼 URL] 設定為您在 Azure 入口網站中新增 Salesforce API 時所看到的值。  

5. 在 [_Selected OAuth scopes (選取的 OAuth 範圍)_] 下方，將下列範圍新增至 [**Selected OAuth Scopes (選取的 OAuth 範圍)**]：

	- 存取與管理您的 Chatter 資料 (chatter\_api)
	- 存取與管理您的資料 (api)
	- 允許存取您的唯一識別碼 (openid)
	- 隨時代表您執行要求 (refresh\_token，offline\_access)

6. **儲存**您的變更：![Salesforce 新的應用程式][8]

現在，在 Azure 入口網站的 Salesforce 組態中，複製/貼上 [應用程式金鑰] 及 [應用程式密碼] 值。

### 在邏輯應用程式中新增其他組態
當您將這個 API 新增到邏輯應用程式時，您必須授權，讓邏輯應用程式能夠連線到您的 Salesforce。

1. 登入您的 Salesforce 帳戶。
2. 允許您的邏輯應用程式連線並使用您的 Salesforce 帳戶。 

當您建立連線之後，請輸入 Salesforce 的屬性，例如資料表名稱。本主題的＜REST API 參考＞一節說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的連線。

## Swaggers REST API 參考
適用的版本：1.0。


### 建立物件
建立 Salesforce 物件。```POST: /datasets/default/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|Salesforce SObject 類型 (例如：「Lead」)|
|item| |yes|body|無|要建立的 Salesforce 物件|

### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 取得物件
擷取 Salesforce 物件。```GET: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|Salesforce SObject 類型 (例如：「Lead」)|
|id|字串|yes|路徑|無|要擷取之 Salesforce 物件的唯一識別碼|

### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 刪除物件
刪除 Salesforce 物件。```DELETE: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|Salesforce SObject 類型 (例如：「Lead」)|
|id|字串|yes|路徑|無|要刪除之 Salesforce 物件的唯一識別碼|

### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 更新物件
更新 Salesforce 物件。```PATCH: /datasets/default/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|Salesforce SObject 類型 (例如：「Lead」)|
|id|字串|yes|路徑|無|要更新之 Salesforce 物件的唯一識別碼|
|item| |yes|body|無|屬性已更新的 Salesforce 物件|

### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 當物件建立時
當 Salesforce 中有物件建立時，就會觸發某個流程。```GET: /datasets/default/tables/{table}/onnewitems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|Salesforce SObject 類型 (例如：「Lead」)|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 當物件遭到修改時 
當 Salesforce 中有物件遭到修改時，就會觸發某個流程。```GET: /datasets/default/tables/{table}/onupdateditems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|Salesforce SObject 類型 (例如：「Lead」)|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



## 物件定義 

#### DataSetsMetadata

| 名稱 | 資料類型 | 必要|
|---|---|---|
|表格式|未定義|no|
|blob|未定義|no|


#### TabularDataSetsMetadata

| 名稱 | 資料類型 | 必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|
|tableDisplayName|字串|no|
|tablePluralName|字串|no|


#### BlobDataSetsMetadata

| 名稱 | 資料類型 | 必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|


#### TableMetadata

| 名稱 | 資料類型 | 必要|
|---|---|---|
|名稱|字串|no|
|title|字串|no|
|x-ms-permission|字串|no|
|結構描述|未定義|no|


#### DataSetsList

| 名稱 | 資料類型 | 必要|
|---|---|---|
|value|array|no|


#### DataSet

| 名稱 | 資料類型 | 必要|
|---|---|---|
|名稱|字串|
|DisplayName|字串|no|


#### 資料表

| 名稱 | 資料類型 | 必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|


#### 項目

| 名稱 | 資料類型 | 必要|
|---|---|---|
|ItemInternalId|字串|no|


#### ItemsList

| 名稱 | 資料類型 | 必要|
|---|---|---|
|value|array|no|


#### TablesList

| 名稱 | 資料類型 | 必要|
|---|---|---|
|value|array|no|


## 後續步驟
當您把 Salesforce API 新增到 PowerApps Enterprise 之後，請[授與使用者在自己的應用程式中使用 API 的權限](../power-apps/powerapps-manage-api-connection-user-access.md)。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。


[5]: https://developer.salesforce.com
[6]: ./media/create-api-salesforce/salesforce-developer-homepage.png
[7]: ./media/create-api-salesforce/salesforce-create-app.png
[8]: ./media/create-api-salesforce/salesforce-new-app.png

<!---HONumber=AcomDC_0302_2016-->