<properties
	pageTitle="在 PowerApps Enterprise 或邏輯應用程式中加入 Office 365 Outlook API | Microsoft Azure"
	description="搭配 REST API 參數來使用 Office 365 Outlook API 的概觀"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/03/2016"
ms.author="mandia"/>

# 開始使用 Office 365 Outlook API 

連接至 Office 365 Outlook 以取得電子郵件、回覆電子郵件、更新您的行事曆和連絡人等等。Office 365 Outlook API 可以從下列位置使用：

- 邏輯應用程式 
- PowerApps

> [AZURE.SELECTOR]
- [邏輯應用程式](../articles/connectors/create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。如需 2014-12-01-preview 結構描述版本，請按一下 [Office 365 API](../app-service-logic/app-service-logic-connector-office365.md)。

使用 Office 365 Outlook，您可以：

- 根據您從 Office 365 Outlook 所取得的資料，來建置您的商務流程。 
- 可在有新的電子郵件、建立新的連絡人等等時，使用觸發程序。
- 可使用回覆電子郵件、建立新的行事曆事件等等的動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。例如，當在 Salesforce 中有新的物件時，您可接受該物件，並更新您的 Office 365 Outlook 連絡人。 
- 新增 Office 365 Outlook API 至 PowerApps Enterprise。接著，您的使用者便能夠在自己的應用程式中使用這個 API。 

如需如何在 PowerApps Enterprise 中加入 API 的資訊，請移至[在 PowerApps 中註冊 API](../power-apps/powerapps-register-from-available-apis.md)。

如果要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

Office 365 Outlook API 提供下列觸發程序和動作。

| 觸發程序 | 動作|
| --- | --- |
|<ul><li>即將開始的事件</li><li>新的電子郵件</li><li>新的項目</li><li>更新的項目</li></ul>| <ul><li>建立連絡人</li><li>建立事件</li><li>傳送核准電子郵件</li><li>傳送電子郵件</li><li>刪除連絡人</li><li>刪除電子郵件</li><li>刪除事件</li><li>取得附件</li><li>取得行事曆</li><li>取得連絡人</li><li>取得連絡人資料夾</li><li>取得連絡人</li><li>取得電子郵件</li><li>取得事件</li><li>取得事件</li><li>標記為已讀取</li><li>即將開始的事件</li><li>新的電子郵件</li><li>新的項目</li><li>更新的項目</li><li>回覆訊息</li><li>傳送電子郵件選項</li><li>更新連絡人</li><li>更新事件</li></ul> |

所有 API 皆支援 JSON 與 XML 格式的資料。


## 建立 Office365 的連接

當您將這個 API 新增到邏輯應用程式時，您必須登入您的 Office 365 Outlook 帳戶，並允許邏輯應用程式連線到您的帳戶。

1. 登入您的 Office 365 Outlook 帳戶。
2. 允許您的邏輯應用程式連線並使用您的 Office 365 帳戶。 

連線建立之後，您需要輸入 Office 365 Outlook 屬性，像是收件匣資料夾路徑或電子郵件訊息。本主題的＜REST API 參考＞一節說明這些屬性。

>[AZURE.TIP] 您可以在其他的邏輯應用程式中，使用這個相同的 Office 365 Outlook 連線。

## Swagger REST API 參考
適用的版本：1.0。


### 事件快要開始時 
即將來臨的行事曆事件啟動時觸發流程。```GET: /Events/OnUpcomingEvents```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|query|無|行事曆的唯一識別碼|
|lookAheadTimeInMinutes|integer|no|query|15|即將來臨的事件的倒數時間 (以分鐘為單位)。|

#### Response
|名稱|說明|
|---|---|
|200|作業已順利完成|
|202|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 取得電子郵件 
從資料夾擷取電子郵件。```GET: /Mail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderPath|字串|no|query|Inbox|要從中擷取訊息的資料夾路徑 (預設值：'Inbox')|
|top|integer|no|query|10|要擷取的電子郵件數目 (預設值：10)|
|fetchOnlyUnread|布林值|no|query|true|只擷取未讀取的訊息嗎？|
|includeAttachments|布林值|no|query|false|如果設為 true，則除了電子郵件訊息，還會擷取附件。|
|searchQuery|字串|no|query|無|搜尋 query 來篩選電子郵件|
|skip|integer|no|query|0|要略過的電子郵件數目 (預設值：0)|
|skipToken|字串|no|query|無|跳過語彙基元來提取新頁面|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 傳送電子郵件 
傳送電子郵件訊息。```POST: /Mail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|無|電子郵件訊息執行個體|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 刪除電子郵件 
依識別碼刪除電子郵件訊息。```DELETE: /Mail/{messageId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|要刪除的訊息的識別碼。|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 標示為已讀取 
將電子郵件訊息標示為已讀取。```POST: /Mail/MarkAsRead/{messageId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|要標示為已讀取的訊息的識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 回覆訊息 
回覆電子郵件訊息。```POST: /Mail/ReplyTo/{messageId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|要回覆的訊息的識別碼|
|comment|字串|yes|query|無|回覆意見|
|replyAll|布林值|no|query|false|回覆所有收件者|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 取得附件 
依識別碼擷取訊息附件。```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|messageId|字串|yes|路徑|無|訊息的識別碼|
|attachmentId|字串|yes|路徑|無|要下載的附件的識別碼|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 有新的電子郵件時 
新的電子郵件送達時觸發流程。```GET: /Mail/OnNewEmail```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|folderPath|字串|no|query|Inbox|要擷取的電子郵件資料夾 (預設值：Inbox)|
|to|字串|no|query|無|收件者電子郵件地址|
|from|字串|no|query|無|寄件者地址|
|importance|字串|no|query|正常|電子郵件的重要性 (High、Normal、Low) (預設值：Normal)|
|fetchOnlyWithAttachment|布林值|no|query|false|只擷取含有附件的電子郵件|
|includeAttachments|布林值|no|query|false|包含附件|
|subjectFilter|字串|no|query|無|在主旨中尋找的字串。|

#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成。|
|202|已接受|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 傳回具有選項的電子郵件 
傳送具有多個選項的電子郵件，並等候收件者選擇其中一個選項來回應。```POST: /mailwithoptions/$subscriptions```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|無|選項電子郵件的訂用帳戶要求|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|201|已建立訂用帳戶|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### 傳送核准電子郵件 
傳送核准電子郵件，並等候收件者的回應。```POST: /approvalmail/$subscriptions```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|無|核准電子郵件的訂用帳戶要求。|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|201|已建立訂用帳戶|
|400|BadRequest|
|401|未經授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### 取得行事曆 
擷取行事曆。```GET: /datasets/calendars/tables```

這個呼叫沒有參數。

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|




### 取得事件 
從行事曆擷取項目。```GET: /datasets/calendars/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|要擷取的行事曆的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 建立事件 
建立新的事件。```POST: /datasets/calendars/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|item| |yes|body|無|要建立的行事曆項目|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得事件 
從行事曆擷取特定項目。```GET: /datasets/calendars/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|id|字串|yes|路徑|無|要擷取的行事曆項目的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 刪除事件 
刪除行事曆項目。```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼。|
|id|字串|yes|路徑|無|要刪除的行事曆項目的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 更新事件 
部分更新行事曆項目。```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|id|字串|yes|路徑|無|要更新的行事曆項目的唯一識別碼|
|item| |yes|body|無|要更新的行事曆項目|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 有新的項目時 
建立新的行事曆項目時觸發。```GET: /datasets/calendars/tables/{table}/onnewitems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 更新項目時 
修改行事曆項目時觸發。```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得連絡人資料夾 
擷取連絡人資料夾。```GET: /datasets/contacts/tables```

這個呼叫沒有參數。

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得連絡人 
從連絡人資料夾中擷取連絡人。```GET: /datasets/contacts/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|要擷取的連絡人資料夾的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數目 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數目上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數目的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 建立連絡人 
建立新的連絡人。```POST: /datasets/contacts/tables/{table}/items```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|item| |yes|body|無|要建立的連絡人|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 取得連絡人 
從連絡人資料夾中擷取特定連絡人。```GET: /datasets/contacts/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|id|字串|yes|路徑|無|要擷取的連絡人的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 刪除連絡人 
刪除連絡人。```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼。|
|id|字串|yes|路徑|無|要刪除的連絡人的唯一識別碼|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


### 更新連絡人 
部分更新連絡人。```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|id|字串|yes|路徑|無|要更新的連絡人的唯一識別碼|
|item| |yes|body|無|要更新的連絡人項目|

#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|


## 物件定義

#### TriggerBatchResponse[IDictionary[String,Object]]

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### SendMessage：傳送電子郵件訊息

| 名稱 | 資料類型 |必要|
|---|---|---|
|附件|array|no|
|從|字串|no|
|副本|字串|no|
|密件副本|字串|no|
|主旨|字串|yes|
|內文|字串|yes|
|重要性|字串|no|
|IsHtml|布林值|no|
|收件人|字串|yes|

#### SendAttachment：附件

| 名稱 | 資料類型 |必要|
|---|---|---|
|@odata.type|字串|no|
|名稱|字串|yes|
|ContentBytes|字串|yes|


#### ReceiveMessage：接收電子郵件訊息

| 名稱 | 資料類型 |必要|
|---|---|---|
|識別碼|字串|no|
|IsRead|布林值|no|
|HasAttachment|布林值|no|
|DateTimeReceived|字串|no|
|附件|array|no|
|從|字串|no|
|副本|字串|no|
|密件副本|字串|no|
|主旨|字串|yes|
|內文|字串|yes|
|重要性|字串|no|
|IsHtml|布林值|no|
|收件人|字串|yes|


#### ReceiveAttachment：接收附件

| 名稱 | 資料類型 |必要|
|---|---|---|
|識別碼|字串|yes|
|ContentType|字串|yes|
|@odata.type|字串|no|
|名稱|字串|no|
|ContentBytes|字串|yes|


#### DigestMessage：傳送電子郵件訊息

| 名稱 | 資料類型 |必要|
|---|---|---|
|主旨|字串|yes|
|內文|字串|no|
|重要性|字串|no|
|Digest|array|yes|
|附件|array|no|
|收件人|字串|yes|

#### TriggerBatchResponse[ReceiveMessage]

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### DataSetsMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|表格式|未定義|no|
|blob|未定義|no|


#### TabularDataSetsMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|
|tableDisplayName|字串|no|
|tablePluralName|字串|no|


#### BlobDataSetsMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|來源|字串|no|
|displayName|字串|no|
|urlEncoding|字串|no|


#### TableMetadata

| 名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|title|字串|no|
|x-ms-permission|字串|no|
|結構描述|未定義|no|


#### OptionsEmailSubscription：選項電子郵件訂用帳戶的模型

| 名稱 | 資料類型 |必要|
|---|---|---|
|NotificationUrl|字串|no|
|訊息|未定義|no|

#### MessageWithOptions：使用者選項電子郵件訊息。這是預期使用者輸入中包含的訊息

| 名稱 | 資料類型 |必要|
|---|---|---|
|主旨|字串|yes|
|選項|字串|yes|
|內文|字串|no|
|重要性|字串|no|
|附件|array|no|
|收件人|字串|yes|

#### SubscriptionResponse：核准電子郵件訂用帳戶的模型

| 名稱 | 資料類型 |必要|
|---|---|---|
|id|字串|no|
|resource|字串|no|
|notificationType|字串|no|
|notificationUrl|字串|no|


#### ApprovalEmailSubscription：核准電子郵件訂用帳戶的模型

| 名稱 | 資料類型 |必要|
|---|---|---|
|NotificationUrl|字串|no|
|訊息|未定義|no|


#### ApprovalMessage：核准電子郵件訊息。這是預期使用者輸入中包含的訊息

| 名稱 | 資料類型 |必要|
|---|---|---|
|主旨|字串|yes|
|選項|字串|yes|
|內文|字串|no|
|重要性|字串|no|
|附件|array|no|
|收件人|字串|yes|

#### ApprovalEmailResponse：核准電子郵件回應

| 名稱 | 資料類型 |必要|
|---|---|---|
|SelectedOption|字串|no|

#### TablesList

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### 資料表

| 名稱 | 資料類型 |必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|


#### 項目

| 名稱 | 資料類型 |必要|
|---|---|---|
|ItemInternalId|字串|no|


#### CalendarItemsList：行事曆項目清單

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### CalendarItem：代表行事曆資料表項目

| 名稱 | 資料類型 |必要|
|---|---|---|
|ItemInternalId|字串|no|


#### ContactItemsList：連絡人項目清單

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### ContactItem：代表連絡人資料表項目

| 名稱 | 資料類型 |必要|
|---|---|---|
|ItemInternalId|字串|no|


#### DataSetsList

| 名稱 | 資料類型 |必要|
|---|---|---|
|value|array|no|


#### DataSet

| 名稱 | 資料類型 | 必要|
|---|---|---|
|名稱|字串|no|
|DisplayName|字串|no|


## 後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

返回 [API 清單](apis-list.md)。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0309_2016-->