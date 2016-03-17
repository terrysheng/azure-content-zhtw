<properties
pageTitle="在 PowerApps Enterprise 中新增 Dynamics CRM Online API，或是新增到您的邏輯應用程式 | Microsoft Azure"
description="搭配 REST API 參數來使用 CRM Online API 的概觀"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/02/2016"
ms.author="deonhe"/>

# 開始使用 CRM API
連線到 Dynamics CRM Online 來建立新的記錄、更新項目等等。CRM Online API 可從以下位置使用：

- 邏輯應用程式
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

您可以利用 CRM Online 來：

- 根據您從 CRM Online 所取得的資料，來建置您的商務流程。 
- 使用會刪除檔案、取得實體等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當 CRM 中有項目更新時，您可以利用 Office 365 來傳送電子郵件。


如需有關如何在 PowerApps Enterprise 中新增 API 的資訊，請移至[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
CRM API 包含下列動作，但不包含觸發程序。

| 觸發程序 | 動作|
| --- | --- |
|None| <ul><li>建立新的記錄</li><li>取得記錄</li><li>刪除記錄</li><li>取得記錄</li><li>取得實體</li><li>更新項目</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 CRM Online 的連線


### 在 PowerApps 中新增其他組態
當您將 CRM Online 新增到 PowerApps Enterprise 時，請輸入 Dynamics CRM Online Azure Active Directory (AAD) 應用程式的 [用戶端識別碼] 及 [應用程式金鑰] 值。[重新導向 URL] 值也會用在您的 CRM Online 應用程式中。如果您沒有應用程式，可以使用下列步驟來建立：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟 [Active Directory]，然後選取您組織的租用戶名稱。
2. 在 [應用程式] 索引標籤中，選取 [新增]。在 [**新增應用程式**] 中：  

	1. 輸入應用程式庫的 [名稱]。  
	2. 讓應用程式類型保持為 [Web]。  
	3. 選取 [下一步]。

	![加入 AAD 應用程式 - 應用程式資訊][9]

3. 在 [應用程式屬性] 中：

	1. 輸入您應用程式的**登入 URL**。由於您即將利用適用於 PowerApps 的 AAD 來進行驗證，因此請把登入 URL 設定為 _https://login.windows.net_ 。
	2. 為您的應用程式輸入有效的 [應用程式識別碼 URI]。  
	3. 選取 [確定]。  

	![加入 AAD 應用程式 - 應用程式屬性][10]

4. 在新的應用程式中，選取 [設定]。
5. 在 [OAuth 2] 下方，將 [回覆 URL] 設定為您在 Azure 入口網站中新增 CRM Online API 時，所看到的重新導向 URL 值：![設定 Contoso AAD 應用程式][12]

現在，在 Azure 入口網站的 CRM Online 組態中，複製/貼上 [用戶端識別碼] 及 [應用程式金鑰]的值。

### 在邏輯應用程式中新增其他組態
當您將這個 API 新增到邏輯應用程式時，您必須登入 Dynamics CRM Online。

請依照下列步驟在線上登入 CRM，並在邏輯應用程式中完成**連線**設定：

1. 選取 [週期]
2. 選取 [頻率] 並輸入 [間隔]
3. 選取 [新增動作]  
![線上設定 CRM][13]
4. 在搜尋方塊中輸入 CRM，並等候搜尋傳回所有名稱中有 CRM 的項目
5. 選取 [Dynamics CRM Online - 建立新的記錄]
6. 選取 [線上登入 Dynamics CRM]：  
![線上設定 CRM][14]
7. 提供您的 CRM Online 認證來登入並授權應用程式
![線上設定 CRM][15]  
8. 請在登入後返回邏輯應用程式，新增其他所需的觸發與動作以完成動作。
9. 選取上方功能表列的 [儲存]，即可儲存您的工作。


當您建立連線之後，請輸入 CRM Online 的屬性，例如資料表或資料集。本主題的〈**REST API 參考**〉一節會說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中使用這個連線。

## Swagger REST API 參考
適用的版本：1.0。

### 建立新的記錄 
在實體中建立新的記錄。```POST: /datasets/{dataset}/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|CRM 組織 contoso.crm 用的唯一名稱|
|資料表|字串|yes|路徑|無|實體的名稱|
|item| |yes|body|無|要建立的記錄|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得記錄 
 取得某個實體的記錄。```GET: /datasets/{dataset}/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|CRM 組織 contoso.crm 用的唯一名稱|
|資料表|字串|yes|路徑|無|實體的名稱|
|$skip|integer|no|query|無|要略過的項目數目。預設值為 0。|
|$top|integer|no|query|無|要擷取的項目數目上限。預設值為 100。|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢。|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|




### 傳回資料集 
 傳回資料集。```GET: /datasets```

這個呼叫沒有參數。

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 取得資料表項目 
用來取得 CRM 實體的特定記錄。```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|CRM 組織 contoso.crm 用的唯一名稱|
|資料表|字串|yes|路徑|無|實體的名稱|
|id|字串|yes|路徑|無|記錄的識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|

### 刪除清單中的項目 
刪除清單中的項目。```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|CRM 組織 contoso.crm 用的唯一名稱|
|資料表|字串|yes|路徑|無|實體的名稱|
|id|字串|yes|路徑|無|記錄的識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 修補現有的資料表項目 
用來部分更新 CRM 實體的現有記錄。```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|CRM 組織 contoso.crm 用的唯一名稱|
|資料表|字串|yes|路徑|無|實體的名稱|
|id|字串|yes|路徑|無|記錄的識別碼|
|item| |yes|body|無|要更新的記錄|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|

### 取得實體 
用來取得 CRM 執行個體中的實體清單。```GET: /datasets/{dataset}/tables```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料集|字串|yes|路徑|無|CRM 組織 contoso.crm 用的唯一名稱|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## 物件定義

#### DataSetsMetadata

|屬性名稱 | 資料類型 | 必要|
|---|---|---|
|表格式|未定義|no|
|blob|未定義|no|

#### TabularDataSetsMetadata

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|
|tableDisplayName|字串|no|
|tablePluralName|字串|no|

#### BlobDataSetsMetadata

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|


#### TableMetadata

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|title|字串|no|
|x-ms-permission|字串|no|
|結構描述|未定義|no|

#### DataSetsList

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|

#### DataSet

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|


#### 資料表

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|

#### 項目

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|ItemInternalId|字串|no|

#### ItemsList

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### TablesList

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


## 後續步驟
當您把 CRM online API 新增到 PowerApps Enterprise 之後，[請授與使用者權限](../power-apps/powerapps-manage-api-connection-user-access.md)，方便他們使用其應用程式中的 API。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png
[13]: ./media/create-api-crmonline/crmconfig1.png
[14]: ./media/create-api-crmonline/crmconfig2.png
[15]: ./media/create-api-crmonline/crmconfig3.png

<!---HONumber=AcomDC_0302_2016-------->


