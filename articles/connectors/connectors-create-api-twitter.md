<properties
	pageTitle="在 PowerApps Enterprise 及邏輯應用程式中新增 Twitter API | Microsoft Azure"
	description="搭配 REST API 參數來使用 Twitter API 的概觀"
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
   ms.date="03/16/2016"
   ms.author="mandia"/>


# 開始使用 Twitter API
連線到 Twitter 來張貼推文、取得使用者的時間軸等等。您可以從下列應用程式使用 Twitter API：

- 邏輯應用程式 
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/connectors-create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

&nbsp;

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。若為 2014-12-01-preview 結構描述版本，請按一下 [Twitter 連接器](../app-service-logic/app-service-logic-connector-twitter.md)。

您可以利用 Twitter 來：

- 根據您從 Twitter 所取得的資料，來建置您的商務流程。 
- 在有新推文時使用觸發程序。
- 使用動作來張貼推文、搜尋推文等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當有新推文出現時，您可以在 Facebook 上張貼這個推文。
- 將 Twitter API 新增到 PowerApps Enterprise，讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需如何在 PowerApps Enterprise 中加入 API 的資訊，請移至[在 PowerApps 中註冊 API](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。


## 觸發程序及動作
Twitter 包含下列觸發程序及動作。

觸發程序 | 動作
--- | ---
<ul><li>當有新推文出現時</li></ul>| <ul><li>張貼新推文</li><li>當有新推文出現時</li><li>取得首頁時間軸</li><li>取得使用者</li><li>取得使用者的時間軸</li><li>搜尋推文</li><li>取得跟隨者</li><li>取得我的跟隨者</li><li>取得正在關注的對象</li><li>取得我正在關注的對象</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。


## 建立至 Twitter 的連線

當您將這個 API 新增到邏輯應用程式時，您必須授權，讓邏輯應用程式能夠連線到您的 Twitter 帳戶。

1. 登入您的 Twitter 帳戶。
2. 選取 [授權]，然後允許您的邏輯應用程式連線並使用您的 Twitter 帳戶。 

當您建立連線之後，請輸入 Twitter 的屬性，例如推文的文字。本主題的＜REST API 參考＞一節會說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Twitter 連線。


## Swagger REST API 參考
適用的版本：1.0。

### 張貼新推文 
推文。```POST: /posttweet```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tweetText|字串|no|query|無|要張貼的文字|
|body| 字串 (二進位) |no|body|無|要張貼的媒體|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 當有新推文出現時 
當某個新推文符合您的搜尋查詢時，就會觸發某個工作流程。 ```GET: /onnewtweet```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|searchQuery|字串|yes|query|無|查詢文字 (您可以使用 Twitter 支援的任何查詢運算子：http://www.twitter.com/search)|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|202|已接受|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得首頁時間軸 
擷取我和我的跟隨者最新的推文及轉推推文。```GET: /hometimeline```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|要擷取的推文數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得使用者 
擷取指定使用者的詳細資料 (例如：使用者名稱、說明、跟隨者數目等)。```GET: /user```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userName|字串|yes|query|無|使用者的 Twitter 使用者名稱|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得使用者的時間軸 
擷取由指定使用者所張貼的最新推文集合。```GET: /usertimeline```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userName|字串|yes|query|無|Twitter 使用者名稱|
|maxResults|integer|no|query|20|要擷取的推文數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 搜尋推文 
擷取符合指定查詢條件的相關推文集合。```GET: /searchtweets```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|searchQuery|字串|yes|query|無|查詢文字 (您可以使用 Twitter 支援的任何查詢運算子：http://www.twitter.com/search)|
|maxResults|integer|no|query|20|要擷取的推文數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得跟隨者 
擷取正在關注指定使用者的使用者。```GET: /followers```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userName|字串|yes|query|無|使用者的 Twitter 使用者名稱|
|maxResults|integer|no|query|20|要擷取的使用者數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得我的跟隨者 
擷取正在關注我的使用者。```GET: /myfollowers```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|要擷取的使用者數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得正在關注的對象 
擷取指定使用者正在關注的使用者。```GET: /friends```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|userName|字串|yes|query|無|使用者的 Twitter 使用者名稱|
|maxResults|integer|no|query|20|要擷取的使用者數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


### 取得我正在關注的對象 
擷取我正在關注的使用者。```GET: /myfriends```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|maxResults|integer|no|query|20|要擷取的使用者數目上限|

#### Response
|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|401|未經授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。發生未知錯誤|
|預設值|作業失敗。|


## 物件定義

### TweetModel：推文物件的表示方式

| 屬性名稱 | 資料類型 | 必要 |
|---|---| --- | 
|TweetText|字串|yes|
|TweetId|字串|no|
|CreatedAt|字串|no|
|RetweetCount|integer|yes|
|TweetedBy|字串|yes|
|MediaUrls|array|no|

### UserDetailsModel：Twitter 使用者詳細資料的表示方式

|屬性名稱 | 資料類型 | 必要 |
|---|---|---|
|FullName|字串|yes|
|位置|字串|yes|
|識別碼|integer|no|
|UserName|字串|yes|
|FollowersCount|integer|no|
|說明|字串|yes|
|StatusesCount|integer|no|
|FriendsCount|integer|no|

### TweetResponseModel：代表已張貼推文的模型

| 名稱 | 資料類型 | 必要 |
|---|---|---|
|TweetId|字串|yes|

### TriggerBatchResponse[TweetModel]

|屬性名稱 | 資料類型 |必要 |
|---|---|---|
|value|array|no|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

<!--References-->

[6]: ./media/connectors-create-api-twitter/twitter-apps-page.png
[7]: ./media/connectors-create-api-twitter/twitter-app-create.png

<!---HONumber=AcomDC_0323_2016-->