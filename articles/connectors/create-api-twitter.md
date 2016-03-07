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
   ms.date="02/23/2016"
   ms.author="mandia"/>


# 開始使用 Twitter API
連線到 Twitter 來張貼推文、取得使用者的時間軸等等。

您可以從 PowerApps Enterprise 及邏輯應用程式使用 Twitter API。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Twitter 連接器](../app-service-logic/app-service-logic-connector-twitter.md)。

您可以利用 Twitter 來：

- 根據您從 Twitter 所取得的資料，來建置您的商務流程。 
- 在有新推文時使用觸發程序。
- 使用動作來張貼推文、搜尋推文等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當有新推文出現時，您可以在 Facebook 上張貼這個推文。
- 將 Twitter API 新增到 PowerApps Enterprise，讓您的使用者能夠在自己的應用程式中使用這個 AP。 

如需如何在 PowerApps Enterprise 中新增 API 的資訊，請前往[在 PowerApps 中為 API 註冊](../power-apps/powerapps-register-from-available-apis.md)。

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。


## 觸發程序及動作
Twitter 包含下列觸發程序及動作。

觸發程序 | 動作
--- | ---
<ul><li>當有新推文出現時</li></ul>| <ul><li>張貼新推文</li><li>當有新推文出現時</li><li>取得首頁時間軸</li><li>取得使用者</li><li>取得使用者的時間軸</li><li>搜尋推文</li><li>取得跟隨者</li><li>取得我的跟隨者</li><li>取得正在關注的對象</li><li>取得我正在關注的對象</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。


## 建立至 Twitter 的連線

### 在 PowerApps 中新增其他組態
當您把 Twitter 新增到 PowerApps Enterprise 時，請輸入您 Twitter 應用程式的 [取用者金鑰] 及 [取用者密碼] 值。[重新導向 URL] 值也會用在您的 Twitter 應用程式中。如果您沒有 Twitter 應用程式，可以使用下列步驟來建立：

1. 登入 [Twitter](https://apps.twitter.com)。

2. 選取 [**Create New App (建立新的應用程式)**]：![Twitter 應用程式頁面][6]

3. 在 [**Create an application (建立應用程式)**] 中：
   
	1. 在 [Name] (名稱)、[Description] (說明) 及 [Website] (網站) 中，輸入任何您想要的值。
	2. 在 [Callback URL] (回呼 URL) 中，輸入您在 Azure 入口網站中新增 Twitter API 時所看到的 [重新導向 URL] 值。
	5. 同意合約，然後 [Create your Twitter application] (建立您的 Twitter 應用程式)。  

	![Twitter 應用程式建立][7]

現在，在 Azure 入口網站的 Twitter 組態中，複製/貼上 [取用者金鑰] 及 [取用者密碼] 的值。


### 在邏輯應用程式中新增其他組態
當您將這個 API 新增到邏輯應用程式時，您必須授權，讓邏輯應用程式能夠連線到您的 Twitter 帳戶。

1. 登入您的 Twitter 帳戶。
2. 選取 [授權]，然後允許您的邏輯應用程式連線並使用您的 Twitter 帳戶。 

當您建立連線之後，請輸入 Twitter 的屬性，例如推文的文字。本主題的＜REST API 參考＞一節會說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Twitter 連線。


## Swagger REST API 參考

### 張貼新推文 
張貼推文。```POST: /posttweet```

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
|searchQuery|字串|yes|query|無|查詢文字 (您可以使用任何 Twitter 支援的查詢運算子：http://www.twitter.com/search)|

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
|searchQuery|字串|yes|query|無|查詢文字 (您可以使用任何 Twitter 支援的查詢運算子：http://www.twitter.com/search)|
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
當您把 Dropbox API新增到 PowerApps Enterprise 之後，[請授與使用者能使用自己應用程式中 API 的權限](../power-apps/powerapps-manage-api-connection-user-access.md)。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!--References-->

[6]: ./media/create-api-twitter/twitter-apps-page.png
[7]: ./media/create-api-twitter/twitter-app-create.png

<!---HONumber=AcomDC_0224_2016-->