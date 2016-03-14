<properties
	pageTitle="在您的邏輯應用程式中新增 Facebook API | Microsoft Azure"
	description="搭配 REST API 參數來使用 Facebook API 的概觀"
	services=""
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# 開始使用 Facebook API
連線到 Facebook 並張貼在動態時報上、取得頁面摘要等等。您可以從下列應用程式使用 Facebook API：

- 邏輯應用程式 

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Facebook 連接器](../app-service-logic/app-service-logic-connector-facebook.md)。


您可以利用 Facebook 來：

- 根據您從 Facebook 所取得的資料，來建置您的商務流程。 
- 在接收到新貼文時使用觸發程序。
- 使用會張貼到您的動態時報、取得頁面摘要等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當您的動態時報上有新貼文時，您可以取得該貼文，然後把它推送到您的 Twitter 摘要。 

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Facebook API 包含下列觸發程序及動作。

| 觸發程序 | 動作|
| --- | --- |
| <ul><li>當我的動態時報上有新貼文時</li></ul> |<ul><li>取得我動態時報的摘要</li><li>張貼到我的動態時報上</li><li>當我的動態時報上有新貼文時</li><li>取得頁面摘要</li><li>取得使用者的動態時報</li><li>張貼到頁面上</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 Facebook 的連線
當您將這個 API 新增到邏輯應用程式時，您必須授權，讓邏輯應用程式能夠連線到您的 Facebook。

1. 登入您的 Facebook 帳戶。
2. 選取 [授權]，然後允許您的邏輯應用程式連線並使用您的 Facebook。 

當您建立連線之後，請輸入 Facebook 的屬性。本主題的＜REST API 參考＞一節說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Facebook 連線。

## Swagger REST API 參考
適用的版本：1.0。

### 取得我動態時報的摘要
取得已登入使用者之動態時報的摘要。```GET: /me/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|fields|字串|no|query|無 |指定要傳回的欄位。例如：識別碼、名稱、圖片。|
|limit|integer|no|query| 無|要擷取的貼文數目上限|
|取代為|字串|no|query| 無|將貼文清單限制為只包含擁有位置資訊的貼文。|
|filter|字串|no|query| 無|只擷取符合特定串流篩選器的貼文。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 張貼到我的動態時報上
將近況訊息張貼到已登入使用者的動態時報上。```POST: /me/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|post|字串 |yes|body|無 |要張貼的新訊息|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 當我的動態時報上有新貼文時
當已登入使用者的動態時報上有新貼文時，就會觸發某個新流程。```GET: /trigger/me/feed```

沒有參數。

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 取得頁面摘要
取得指定頁面摘要中的貼文。```GET: /{pageId}/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|pageId|字串|yes|路徑| 無|要擷取之貼文所在頁面的識別碼。|
|limit|integer|no|query| 無|要擷取的貼文數目上限|
|include\_hidden|布林值|no|query|無 |是否要包含頁面所隱藏的貼文|
|fields|字串|no|query|無 |指定要傳回的欄位。例如：識別碼、名稱、圖片。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 取得使用者的動態時報
取得使用者的動態時報上的貼文。```GET: /{userId}/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userId|字串|yes|路徑|無 |要遭到擷取之動態時報擁有者的使用者識別碼。|
|limit|integer|no|query|無 |要擷取的貼文數目上限|
|取代為|字串|no|query|無 |將貼文清單限制為只包含擁有位置資訊的貼文。|
|filter|字串|no|query| 無|只擷取符合特定串流篩選器的貼文。|
|fields|字串|no|query| 無|指定要傳回的欄位。例如：識別碼、名稱、圖片。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 張貼到頁面上
以已登入使用者的身分，將訊息張貼在 Facebook 頁面上。```POST: /{pageId}/feed```

| 名稱|資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|pageId|字串|yes|路徑|無 |要張貼貼文之頁面的識別碼。|
|post|多種 |yes|body|無 |要張貼的新訊息。|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## 物件定義

#### GetFeedResponse

|屬性名稱 | 資料類型 | 必要|
|---|---|---|
|data|array|no|

#### TriggerFeedResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|array|no|

#### PostItem：個人檔案摘要中的單一項目
個人檔案可能是使用者、頁面、應用程式或群組。

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|
|admin\_creator|array|no|
|caption|字串|no|
|created\_time|字串|no|
|說明|字串|no|
|feed\_targeting|沒有定義|no|
|from|沒有定義|no|
|icon|字串|no|
|is\_hidden|布林值|no|
|is\_published|布林值|no|
|link|字串|no|
|訊息|字串|no|
|名稱|字串|no|
|object\_id|字串|no|
|picture|字串|no|
|place|沒有定義|no|
|privacy|沒有定義|no|
|屬性|array|no|
|來源|字串|no|
|status\_type|字串|no|
|story|字串|no|
|targeting|沒有定義|no|
|to|array|no|
|類型|字串|no|
|updated\_time|字串|no|
|with\_tags|沒有定義|no|

#### TriggerItem：個人檔案摘要中的單一項目
個人檔案可能是使用者、頁面、應用程式或群組。

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|
|created\_time|字串|no|
|from|沒有定義|no|
|訊息|字串|no|
|類型|字串|no|

#### AdminItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|
|link|字串|no|

#### PropertyItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|文字|字串|no|
|href|字串|no|

#### UserPostFeedRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|訊息|字串|yes|
|link|字串|no|
|picture|字串|no|
|名稱|字串|no|
|caption|字串|no|
|說明|字串|no|
|place|字串|no|
|tags|字串|no|
|privacy|沒有定義|no|
|object\_attachment|字串|no|

#### PagePostFeedRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|訊息|字串|yes|
|link|字串|no|
|picture|字串|no|
|名稱|字串|no|
|caption|字串|no|
|說明|字串|no|
|actions|array|no|
|place|字串|no|
|tags|字串|no|
|object\_attachment|字串|no|
|targeting|沒有定義|no|
|feed\_targeting|沒有定義|no|
|published|布林值|no|
|scheduled\_publish\_time|字串|no|
|backdated\_time|字串|no|
|backdated\_time\_granularity|字串|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|布林值|no|

#### PostFeedResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|

#### ProfileCollection

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|array|no|

#### UserItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|
|first\_name|字串|no|
|last\_name|字串|no|
|名稱|字串|no|
|gender|字串|no|
|about|字串|no|

#### ActionItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|link|字串|no|

#### TargetItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem：控制以此貼文為目標之新聞摘要的物件
這些群組中所有人看到此貼文的機會比較高，其他人看到的機會則比較低。僅適用於頁面。

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|integer|no|
|age\_max|integer|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|integer|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|
|名稱|字串|no|
|overall\_rating|number|no|
|location|沒有定義|no|

#### LocationItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|city|字串|no|
|country|字串|no|
|緯度|number|no|
|located\_in|字串|no|
|經度|number|no|
|名稱|字串|no|
|region|字串|no|
|state|字串|no|
|street|字串|no|
|zip|字串|no|

#### PrivacyItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|說明|字串|no|
|value|字串|yes|
|allow|字串|no|
|deny|字串|no|
|friends|字串|no|

#### ChildAttachmentsItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|link|字串|no|
|picture|字串|no|
|image\_hash|字串|no|
|名稱|字串|no|
|說明|字串|no|

#### PostPhotoRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|url|字串|yes|
|caption|字串|no|

#### PostPhotoResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|yes|
|post\_id|字串|yes|

#### PostVideoRequest

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|videoData|字串|yes|
|說明|字串|yes|
|title|字串|yes|
|uploadedVideoName|字串|no|

#### GetPhotoResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|沒有定義|yes|

#### GetPhotoResponseItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|url|字串|yes|
|is\_silhouette|布林值|yes|
|height|字串|no|
|width|字串|no|

#### GetEventResponse

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|data|array|yes|

#### GetEventResponseItem

|屬性名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|yes|
|名稱|字串|yes|
|start\_time|字串|no|
|end\_time|字串|no|
|timezone|字串|no|
|location|字串|no|
|說明|字串|no|
|ticket\_uri|字串|no|
|rsvp\_status|字串|yes|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0302_2016-------->