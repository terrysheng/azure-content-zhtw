<properties
pageTitle="在 PowerApps Enterprise 及邏輯應用程式中新增 OneDrive API | Microsoft Azure"
description="搭配 REST API 參數來使用 OneDrive API 的概觀"
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
ms.date="03/03/2016"
ms.author="mandia"/>

# 開始使用 OneDrive API

連線到 OneDrive 來管理您的檔案，包括上傳檔案、取得、刪除檔案等等。您可以從下列應用程式使用 OneDrive API：

- 邏輯應用程式 
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

&nbsp;

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。若為 2014-12-01-preview 結構描述版本，請按一下 [OneDrive API](../app-service-logic/app-service-logic-connector-onedrive.md)。

您可以利用 OneDrive 來：

- 根據您從 OneDrive 所取得的資料，來建置您的商務流程。 
- 在檔案建立或更新時使用觸發程序。
- 使用動作來建立檔案、刪除檔案等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當 OneDrive 中有新檔案建立時，您可以利用 Office 365 來傳送已附加該檔案的電子郵件。
- 將 OneDrive API 新增到 PowerApps Enterprise讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需如何在 PowerApps Enterprise 中加入 API 的資訊，請移至[在 PowerApps 中註冊 API](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
OneDrive API 包含下列觸發程序及動作。

| 觸發程序 | 動作|
| --- | --- |
|<ul><li>當檔案建立時</li><li>當檔案遭到修改時</li></ul> | <ul><li>建立檔案</li><li>列出某個資料夾中的檔案</li><li>當檔案建立時</li><li>複製檔案</li><li>刪除檔案</li><li>解壓縮資料夾</li><li>使用識別碼來取得檔案內容</li><li>使用路徑來取得檔案內容</li><li>使用識別碼來取得檔案中繼資</li><li>使用路徑來取得檔案中繼資料</li><li>列出根資料夾</li><li>更新檔案</li><li>當檔案遭到修改時</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 OneDrive 的連線

當您將這個 API 新增到邏輯應用程式時，您必須授權，讓邏輯應用程式能夠連線到您的 OneDrive。

1. 登入您的 OneDrive 帳戶。
2. 允許您的邏輯應用程式連線並使用您的 OneDrive。 

當您建立連線之後，請輸入 OneDrive 的屬性，例如資料夾路徑或檔案名稱。本主題的 REST API 參考會說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的連線。

## Swagger REST API 參考
適用的版本：1.0。


### 使用識別碼來取得檔案中繼資料
使用識別碼來擷取 OneDrive 中的檔案中繼資料。```GET: /datasets/default/files/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|OneDrive 中檔案的唯一識別碼|

### 回應
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 更新檔案
更新 OneDrive 中的檔案。```PUT: /datasets/default/files/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|OneDrive 中要更新之檔案的唯一識別碼|
|body| |yes|body|無|OneDrive 中要更新之檔案的內容|


### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|

### 刪除檔案
刪除 OneDrive 中的檔案。```DELETE: /datasets/default/files/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|要從 OneDrive 刪除之檔案的唯一識別碼|


### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 使用路徑來取得檔案中繼資料
使用路徑來擷取 OneDrive 中的檔案中繼資料。```GET: /datasets/default/GetFileByPath```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|路徑|字串|yes|query|無|OneDrive 中檔案的唯一路徑|


### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|




### 使用路徑來取得檔案內容
使用路徑來擷取 OneDrive 中的檔案內容。```GET: /datasets/default/GetFileContentByPath```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|路徑|字串|yes|query|無|OneDrive 中檔案的唯一路徑|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|




### 使用識別碼來取得檔案內容
使用識別碼來擷取 OneDrive 中的檔案內容。```GET: /datasets/default/files/{id}/content```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|OneDrive 中檔案的唯一識別碼|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|




### 建立檔案
把檔案上傳到 OneDrive。```POST: /datasets/default/files```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderPath|字串|yes|query|無|用來把檔案上傳到 OneDrive 的資料夾路徑|
|名稱|字串|yes|query|無|要在 OneDrive 中建立之檔案的名稱|
|body| |yes|body|無|要上傳到 OneDrive 之檔案的內容|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 複製檔案
把檔案複製到 OneDrive。```POST: /datasets/default/copyFile```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|來源|字串|yes|query|無|來源檔案的 URL|
|目的地|字串|yes|query|無|OneDrive 中的目的檔案路徑，包括目標檔案名稱|
|overwrite|布林值|no|query|false|如果設定為「True」，則會覆寫目的檔案|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 當檔案建立時
當某個 OneDrive 資料夾中有新檔案建立時，就會觸發某個流程。```GET: /datasets/default/triggers/onnewfile```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderId|字串|yes|query|無|OneDrive 中資料夾的唯一識別碼|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 當某個 OneDrive 資料夾中有檔案遭到修改時，就會觸發某個流程。
當某個 OneDrive 資料夾中有檔案遭到修改時，就會觸發某個流程。```GET: /datasets/default/triggers/onupdatedfile```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderId|字串|yes|query|無|OneDrive 中資料夾的唯一識別碼|


### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|



### 解壓縮到資料夾
將資料夾解壓縮到 OneDrive。```POST: /datasets/default/extractFolderV2```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|來源|字串|yes|query|無|封存檔案的路徑|
|目的地|字串|yes|query|無|用來把封存檔案內容解壓縮到 OneDrive 中的路徑|
|overwrite|布林值|no|query|false|如果設定為「True」，則會覆寫目的檔案|


### Response

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



#### BlobMetadata

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|識別碼|字串|no|
|名稱|字串|no|
|DisplayName|字串|no|
|Path|字串|no|
|LastModified|字串|no|
|大小|integer|no|
|MediaType|字串|no|
|IsFolder|布林值|no|
|ETag|字串|no|
|FileLocator|字串|no|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

[5]: https://account.live.com/developers/applications/create
[6]: ./media/create-api-onedrive/onedrive-new-app.png
[7]: ./media/create-api-onedrive/onedrive-app-api-settings.png

<!---HONumber=AcomDC_0309_2016-->