<properties
	pageTitle="在 PowerApps Enterprise 或邏輯應用程式中新增 Dropbox API | Microsoft Azure"
	description="搭配 REST API 參數來使用 Dropbox API 的概觀"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="mandia"/>

# 開始使用 Dropbox API 
連線到 Dropbox 來管理檔案，例如建立檔案、取得檔案等等。您可以從下列應用程式使用 Dropbox API：

- 邏輯應用程式 
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

您可以利用 Dropbox 來：

- 根據您從 Dropbox 所取得的資料，來建置您的商務流程。 
- 在檔案建立或更新時使用觸發程序。
- 使用動作來建立檔案、刪除檔案等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當 Dropbox 中有新檔案建立時，您可以利用 Office 365 來傳送已附加該檔案的電子郵件。
- 將 Dropbox API 新增到 PowerApps Enterprise，讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需有關如何在 PowerApps Enterprise 中新增 API 的資訊，請移至[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Dropbox 包含下列觸發程序及動作。

觸發程序 | 動作
--- | ---
<ul><li>當檔案建立時</li><li>當檔案遭到修改時</li></ul> | <ul><li>建立檔案</li><li>當檔案建立時</li><li>複製檔案</li><li>刪除檔案</li><li>將封存檔案解壓縮到資料夾</li><li>使用識別碼來取得檔案內容</li><li>使用路徑來取得檔案</li><li>使用識別碼來取得檔案中繼資料</li><li>使用路徑來取得檔案中繼資料</li><li>更新檔案</li><li>當檔案遭到修改時</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 Dropbox 的連線

### 在 PowerApps 中新增其他組態
當您將 Dropbox 新增到 PowerApps Enterprise 時，請輸入您的 Dropbox 應用程式的 [應用程式金鑰] 及 [應用程式密碼] 值。[重新導向 URL] 值也會用在您的 Dropbox 應用程式中。如果您沒有 Dropbox 應用程式，可以使用下列步驟來建立：

1. 登入 [Dropbox][1]。
2. 移至 Dropbox - Developers (Dropbox 開發人員) 網站，然後選取 [**My Apps (我的應用程式)**]：![Dropbox - Developers (Dropbox 開發人員) 網站][8]  
3. 選取 [**Create app (建立應用程式)**]：![Dropbox create app (建立應用程式)][9]  
4. 在 [**Create a new app on the Dropbox platform (在 Dropbox 平台上建立新的應用程式)**] 中：  

	1. 在 [選擇 API] 中，選取 [Dropbox API]。
	2. 在 [選擇您需要的存取類型] 中，選取 [完整的 Dropbox...]。  
	3. 輸入您應用程式的名稱。  

	![Dropbox create app (建立應用程式) 第 1 頁][10]

5. 在應用程式設定頁面中：

	1. 在 [OAuth 2] 中，輸入您在 Azure 入口網站中新增 Dropbox API 時所看到的 [重新導向 URL] 值。選取 [新增]。  
	2. 選取 [顯示] 連結來顯示**應用程式密碼**：  

	![Dropbox create app (建立應用程式) 第 2 頁][11]

現在，在 Azure 入口網站的 Dropbox 組態中，複製/貼上這些 [應用程式金鑰] 及 [應用程式密碼] 值。

### 在邏輯應用程式中新增其他組態
當您將這個 API 新增到邏輯應用程式時，您必須授權，讓邏輯應用程式能夠連線到您的 Dropbox。

1. 登入您的 Dropbox 帳戶。
2. 選取 [授權]，然後允許您的邏輯應用程式連線並使用您的 Dropbox。 

當您建立連線之後，請輸入 Dropbox 的屬性，例如資料夾路徑或檔案名稱。本主題的＜REST API 參考＞一節說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Dropbox 連線。

## Swagger REST API 參考
適用的版本：1.0。

### 建立檔案    
把檔案上傳到 Dropbox。```POST: /datasets/default/files```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderPath|字串|yes|query|無 |用來把檔案上傳到 Dropbox 的資料夾路徑|
|名稱|字串|yes|query|無 |要在 Dropbox 中建立之檔案的名稱|
|body|字串 (二進位) |yes|body|無 |要上傳到 Dropbox 之檔案的內容|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 當檔案建立時    
當某個 Dropbox 資料夾中有新檔案建立時，就會觸發某個流程。```GET: /datasets/default/triggers/onnewfile```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderId|字串|yes|query|無 |Dropbox 中資料夾的唯一識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 複製檔案    
把檔案複製到 Dropbox。```POST: /datasets/default/copyFile```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|來源|字串|yes|query|無 |來源檔案的 URL|
|目的地|字串|yes|query| 無|Dropbox 中的目的檔案路徑，包括目標檔案名稱|
|overwrite|布林值|no|query|無 |如果設定為「True」，則會覆寫目的檔案|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 刪除檔案    
刪除 Dropbox 中的檔案。```DELETE: /datasets/default/files/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無|要從 Dropbox 刪除之檔案的唯一識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 將封存檔案解壓縮到資料夾    
將封存檔案 (例如 .zip) 解壓縮到 Dropbox 中的資料夾。**```POST: /datasets/default/extractFolderV2```**

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|來源|字串|yes|query|無 |封存檔案的路徑|
|目的地|字串|yes|query|無 |用來把封存檔案內容解壓縮到 Dropbox 中的路徑|
|overwrite|布林值|no|query|無 |如果設定為「True」，則會覆寫目的檔案|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 使用識別碼來取得檔案內容    
使用識別碼來擷取 Dropbox 中的檔案內容。```GET: /datasets/default/files/{id}/content```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無 |Dropbox 中檔案的唯一識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 使用路徑來取得檔案內容    
使用路徑來擷取 Dropbox 中的檔案內容。```GET: /datasets/default/GetFileContentByPath```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|路徑|字串|yes|query|無 |Dropbox 中檔案的唯一路徑|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 使用識別碼來取得檔案中繼資料    
使用檔案識別碼來擷取 Dropbox 中的檔案中繼資料。```GET: /datasets/default/files/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑|無 |Dropbox 中檔案的唯一識別碼|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 使用路徑來取得檔案中繼資料    
使用路徑來擷取 Dropbox 中的檔案中繼資料。```GET: /datasets/default/GetFileByPath```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|路徑|字串|yes|query|無 |Dropbox 中檔案的唯一路徑|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 更新檔案    
更新 Dropbox 中的檔案。```PUT: /datasets/default/files/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|id|字串|yes|路徑| 無|Dropbox 中要更新之檔案的唯一識別碼|
|body|字串 (二進位) |yes|body|無 |Dropbox 中要更新之檔案的內容|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 當檔案遭到修改時    
當某個 Dropbox 資料夾中有檔案遭到修改時，就會觸發某個流程。```GET: /datasets/default/triggers/onupdatedfile```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderId|字串|yes|query|無 |Dropbox 中資料夾的唯一識別碼|

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
當您把 Dropbox API新增到 PowerApps Enterprise 之後，請[授與使用者在自己的應用程式中使用 API 的權限](../power-apps/powerapps-manage-api-connection-user-access.md)。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0302_2016-->