<properties
pageTitle="將 Office 365 Outlook API 新增到 Logic Apps | Microsoft Azure"
description="搭配 REST API 參數來使用 Office 365 Outlook API 的概觀"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# 開始使用 Office365 Outlook API

Office 365 API 可讓您與 Office 365 互動。例如：建立、編輯和更新連絡人及行事曆項目。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Office 365 API](../app-service-logic/app-service-logic-connector-Office365.md)。

Office 365 可讓您：

* 建置邏輯應用程式
* 建置 power 應用程式

如需如何在 PowerApps Enterprise 中新增 API 的資訊，請前往[在 PowerApps 中註冊 API](../power-apps/powerapps-register-from-available-apis.md)。

若要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 讓我們談一下觸發程序和動作。

Office365 API 可當做動作，它具有觸發程序。所有 API 都支援 JSON 和 XML 格式的資料。

 Office365 API 有下列動作和/或觸發程序可用：

### Office365 動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|GetEmails|從資料夾擷取電子郵件|
|SendEmail|傳送電子郵件訊息|
|DeleteEmail|依識別碼刪除電子郵件訊息|
|MarkAsRead|將電子郵件訊息標示為已讀取|
|ReplyTo|回覆電子郵件訊息|
|GetAttachment|依識別碼擷取訊息附件|
|SendMailWithOptions|傳送具有多個選項的電子郵件，並等候收件者選擇其中一個選項來回應。|
|SendApprovalMail|傳送核准電子郵件，並等候收件者的回應。|
|CalendarGetTables|擷取行事曆|
|CalendarGetItems|從行事曆擷取項目|
|CalendarPostItem|建立新的事件|
|CalendarGetItem|從行事曆擷取特定項目|
|CalendarDeleteItem|刪除行事曆項目|
|CalendarPatchItem|部分更新行事曆項目|
|ContactGetTables|擷取連絡人資料夾|
|ContactGetItems|從連絡人資料夾中擷取連絡人|
|ContactPostItem|建立新的連絡人|
|ContactGetItem|從連絡人資料夾中擷取特定連絡人|
|ContactDeleteItem|刪除連絡人|
|ContactPatchItem|部分更新連絡人|
### Office365 觸發程序
您可以接聽這些事件：

|觸發程序 | 說明|
|--- | ---|
|OnUpcomingEvents|即將來臨的行事曆事件啟動時觸發流程|
|OnNewEmail|新的電子郵件送達時觸發流程|
|CalendarGetOnNewItems|建立新的行事曆項目時觸發|
|CalendarGetOnUpdatedItems|修改行事曆項目時觸發|


## 建立 Office365 的連接
若要使用 Office365 API，您需要先建立**連接**，再提供這些屬性的詳細資料：

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Office365 認證|


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Office365 REST API 參考
#### 本文件適用的版本：1.0


### 事件快要開始時 


 即將來臨的行事曆事件啟動時觸發流程 ```GET: /Events/OnUpcomingEvents```



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
------



### 取得電子郵件 


 從資料夾擷取電子郵件 ```GET: /Mail```



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
------



### 傳送電子郵件 


 傳送電子郵件訊息 ```POST: /Mail```



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
------



### 刪除電子郵件 


 依識別碼刪除電子郵件訊息 ```DELETE: /Mail/{messageId}```



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
------



### 標示為已讀取 


 將電子郵件訊息標示為已讀取 ```POST: /Mail/MarkAsRead/{messageId}```



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
------



### 回覆訊息 


 回覆電子郵件訊息 ```POST: /Mail/ReplyTo/{messageId}```



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
------



### 取得附件 


 依識別碼擷取訊息附件 ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



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
------



### 有新的電子郵件時 


 新的電子郵件送達時觸發流程 ```GET: /Mail/OnNewEmail```



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
------



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
------



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
------



### 取得行事曆 


 擷取行事曆 ```GET: /datasets/calendars/tables```

這個呼叫沒有參數
#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 取得事件 


 從行事曆擷取項目 ```GET: /datasets/calendars/tables/{table}/items```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|要擷取的行事曆的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 建立事件 


 建立新的事件 ```POST: /datasets/calendars/tables/{table}/items```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|item| |yes|body|無|要建立的行事曆項目|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 取得事件 


 從行事曆擷取特定項目 ```GET: /datasets/calendars/tables/{table}/items/{id}```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|id|字串|yes|路徑|無|要擷取的行事曆項目的唯一識別碼|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 刪除事件 


 刪除行事曆項目 ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼。|
|id|字串|yes|路徑|無|要刪除的行事曆項目的唯一識別碼|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 更新事件 


 部分更新行事曆項目 ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



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
------



### 有新的項目時 


 建立新的行事曆項目時觸發 ```GET: /datasets/calendars/tables/{table}/onnewitems```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 更新項目時 


 修改行事曆項目時觸發 ```GET: /datasets/calendars/tables/{table}/onupdateditems```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|行事曆的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 取得連絡人資料夾 


 擷取連絡人資料夾 ```GET: /datasets/contacts/tables```

這個呼叫沒有參數
#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 取得連絡人 


 從連絡人資料夾中擷取連絡人 ```GET: /datasets/contacts/tables/{table}/items```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|要擷取的連絡人資料夾的唯一識別碼|
|$skip|integer|no|query|無|要略過的項目數 (預設值 = 0)|
|$top|integer|no|query|無|要擷取的項目數上限 (預設值 = 256)|
|$filter|字串|no|query|無|用來限制項目數的 ODATA filter 查詢|
|$orderby|字串|no|query|無|用來指定項目順序的 ODATA orderBy 查詢|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 建立連絡人 


 建立新的連絡人 ```POST: /datasets/contacts/tables/{table}/items```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|item| |yes|body|無|要建立的連絡人|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 取得連絡人 


 從連絡人資料夾中擷取特定連絡人 ```GET: /datasets/contacts/tables/{table}/items/{id}```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼|
|id|字串|yes|路徑|無|要擷取的連絡人的唯一識別碼|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 刪除連絡人 


 刪除連絡人 ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|資料表|字串|yes|路徑|無|連絡人資料夾的唯一識別碼。|
|id|字串|yes|路徑|無|要刪除的連絡人的唯一識別碼|


#### Response

|名稱|說明|
|---|---|
|200|OK|
|預設值|作業失敗。|
------



### 更新連絡人 


 部分更新連絡人 ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



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
------



## 物件定義： 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

TriggerBatchResponse[IDictionary[String,Object]] 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **SendMessage**：傳送電子郵件訊息

SendMessage 的必要屬性：

Subject、Body、To

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|附件|array|
|從|字串|
|副本|字串|
|密件副本|字串|
|主旨|字串|
|內文|字串|
|重要性|字串|
|IsHtml|布林值|
|收件人|字串|



 **SendAttachment**：附件

SendAttachment 的必要屬性：

Name、ContentBytes

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|@odata.type|字串|
|名稱|字串|
|ContentBytes|字串|



 **ReceiveMessage**：接收電子郵件訊息

ReceiveMessage 的必要屬性：

Subject、Body、To

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|IsRead|布林值|
|HasAttachment|布林值|
|DateTimeReceived|字串|
|附件|array|
|從|字串|
|副本|字串|
|密件副本|字串|
|主旨|字串|
|內文|字串|
|重要性|字串|
|IsHtml|布林值|
|收件人|字串|



 **ReceiveAttachment**：接收附件

ReceiveAttachment 的必要屬性：

Id、ContentType、Name、ContentBytes

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|ContentType|字串|
|@odata.type|字串|
|名稱|字串|
|ContentBytes|字串|



 **DigestMessage**：傳送電子郵件訊息

DigestMessage 的必要屬性：

Subject、Digest、To

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|主旨|字串|
|內文|字串|
|重要性|字串|
|Digest|array|
|附件|array|
|收件人|字串|



 **TriggerBatchResponse[ReceiveMessage]**:

TriggerBatchResponse[ReceiveMessage] 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **DataSetsMetadata**:

DataSetsMetadata 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|tabular|未定義|
|blob|未定義|



 **TabularDataSetsMetadata**:

TabularDataSetsMetadata 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|來源|字串|
|displayName|字串|
|urlEncoding|字串|
|tableDisplayName|字串|
|tablePluralName|字串|



 **BlobDataSetsMetadata**:

BlobDataSetsMetadata 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|來源|字串|
|displayName|字串|
|urlEncoding|字串|



 **TableMetadata**:

TableMetadata 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|名稱|字串|
|title|字串|
|x-ms-permission|字串|
|結構描述|未定義|



 **OptionsEmailSubscription**：選項電子郵件訂用帳戶的模型

OptionsEmailSubscription 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|NotificationUrl|字串|
|訊息|未定義|



 **MessageWithOptions**：使用者選項電子郵件訊息。這是預期使用者輸入中包含的訊息

MessageWithOptions 的必要屬性：

Subject、Options、To

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|主旨|字串|
|選項|字串|
|內文|字串|
|重要性|字串|
|附件|array|
|收件人|字串|



 **SubscriptionResponse**：核准電子郵件訂用帳戶的模型

SubscriptionResponse 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|id|字串|
|resource|字串|
|notificationType|字串|
|notificationUrl|字串|



 **ApprovalEmailSubscription**：核准電子郵件訂用帳戶的模型

ApprovalEmailSubscription 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|NotificationUrl|字串|
|訊息|未定義|



 **ApprovalMessage**：核准電子郵件訊息。這是預期使用者輸入中包含的訊息

ApprovalMessage 的必要屬性：

Subject、Options、To

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|主旨|字串|
|選項|字串|
|內文|字串|
|重要性|字串|
|附件|array|
|收件人|字串|



 **ApprovalEmailResponse**：核准電子郵件回應

ApprovalEmailResponse 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|SelectedOption|字串|



 **TablesList**:

TablesList 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **資料表**：

Table 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|名稱|字串|
|DisplayName|字串|



 **Item**:

Item 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|



 **CalendarItemsList**：行事曆項目清單

CalendarItemsList 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **CalendarItem**：代表行事曆資料表項目

CalendarItem 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|



 **ContactItemsList**：連絡人項目清單

ContactItemsList 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **CalendarItem**：代表連絡人資料表項目

ContactItem 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|ItemInternalId|字串|



 **DataSetsList**:

DataSetsList 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|value|array|



 **DataSet**:

DataSet 的必要屬性：


屬性都不是必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|名稱|字串|
|DisplayName|字串|


## 後續步驟
當您把 Office 365 API 新增到 PowerApps Enterprise 之後，請[授與使用者權限](../power-apps/powerapps-manage-api-connection-user-access.md)在他們的應用程式中使用 API。

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0224_2016-->