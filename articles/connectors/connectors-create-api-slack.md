<properties
pageTitle="在您的邏輯應用程式中使用 Slack API | Microsoft Azure"
description="開始在您的 Microsoft Azure App service Logic apps 中使用 Slack API (連接器)"
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
ms.date="03/16/2016"
ms.author="deonhe"/>

# 開始使用 Slack API

Slack 是團隊通訊工具，能把您團隊的通訊都集中在一個地方，讓您不但在何處都可使用，還能搜尋各項記錄。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Slack](../app-service-logic/app-service-logic-connector-Slack.md)。

您可以使用 Slack 連接器來：

* 建置邏輯應用程式

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 我們來談談觸發程序及動作。

Slack 連接器可當做動作來使用；它沒有觸發程序。所有連接器都支援 JSON 和 XML 格式的資料。

 Slack 連接器提供下列動作及/或觸發程序：

### Slack 的動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|PostMessage|將訊息張貼到指定通道。|
## 建立至 Slack 的連線
如要使用 Slack API，您必須先建立「連線」，再提供下列屬性的詳細資料：

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Slack 的認證|

請依照下列步驟登入 Slack，並在邏輯應用程式中完成 Slack **連線**設定：

1. 選取 [週期]
2. 選取 [頻率] 並輸入 [間隔]
3. 選取 [加入動作]![設定 Slack][1]  
4. 在搜尋方塊中輸入 Slack，並等候搜尋傳回所有名稱中有 Slack 的項目
5. 選取 [Slack - 張貼訊息]
6. 選取 [登入 Slack]：![設定 Slack][2]
7. 提供您的 Slack 認證來登入並授權應用程式 ![設定 Slack][3]  
8. 您將會重新導向至組織的登入頁面。**授權** Slack 與邏輯應用程式互動：![設定 Slack][5] 
9. 驗證完成後，您會被重新導向至邏輯應用程式，設定 [Slack - 取得所有訊息] 區段後，即可完成動作。加入其他所需的觸發和動作。![設定 Slack][6]
10. 選取上方功能表列的 [儲存]，即可儲存您的工作。


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Slack REST API 參考
#### 本文件適用的版本：1.0


### 將訊息張貼到指定通道。
**```POST: /chat.postMessage```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|channel|字串|yes|query|無|用來傳送訊息的通道、私人群組，或 IM 通道。可以是名稱 (例如：#general) 或已編碼的識別碼。|
|文字|字串|yes|query|無|要傳送之訊息的文字。如需格式的選項，請參閱 https://api.slack.com/docs/formatting.|
|username|字串|no|query|無|Bot 的名稱|
|as\_user|布林值|no|query|無|傳遞 True 來以已驗證使用者的身分 (而不是 Bot) 張貼訊息|
|parse|字串|no|query|無|變更訊息的處理方式。如需詳細資料，請參閱 https://api.slack.com/docs/formatting.|
|link\_names|integer|no|query|無|尋找並連結通道名稱及使用者名稱。|
|unfurl\_links|布林值|no|query|無|傳遞 True 來啟用主要為文字式內容的展開功能。|
|unfurl\_media|布林值|no|query|無|傳遞 False 來停用媒體內容的展開功能。|
|icon\_url|字串|no|query|無|要做為此訊息之圖示的圖像 URL|
|icon\_emoji|字串|no|query|無|要做為此訊息之圖示的 Emoji|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|408|要求逾時|
|429|太多要求|
|500|內部伺服器錯誤。發生未知錯誤|
|503|Slack 服務無法使用|
|504|閘道逾時|
|預設值|作業失敗。|
------



## 物件定義： 

 **Message**：Yammer 訊息

Message 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|id|integer|
|content\_excerpt|字串|
|sender\_id|integer|
|replied\_to\_id|integer|
|created\_at|字串|
|network\_id|integer|
|message\_type|字串|
|sender\_type|字串|
|url|字串|
|web\_url|字串|
|group\_id|integer|
|body|沒有定義|
|thread\_id|integer|
|direct\_message|布林值|
|client\_type|字串|
|client\_url|字串|
|語言|字串|
|notified\_user\_ids|array|
|privacy|字串|
|liked\_by|沒有定義|
|system\_message|布林值|



 **PostOperationRequest**：代表要張貼到 Yammer 上的 Yammer 連接器張貼要求

PostOperationRequest 的必要屬性：

body

**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|body|字串|
|group\_id|integer|
|replied\_to\_id|integer|
|direct\_to\_id|integer|
|broadcast|布林值|
|topic1|字串|
|topic2|字串|
|topic3|字串|
|topic4|字串|
|topic5|字串|
|topic6|字串|
|topic7|字串|
|topic8|字串|
|topic9|字串|
|topic10|字串|
|topic11|字串|
|topic12|字串|
|topic13|字串|
|topic14|字串|
|topic15|字串|
|topic16|字串|
|topic17|字串|
|topic18|字串|
|topic19|字串|
|topic20|字串|



 **MessageList**：訊息的清單

MessageList 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|messages|array|



 **MessageBody**：訊息內文

MessageBody 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|parsed|字串|
|plain|字串|
|rich|字串|



 **LikedBy**：按讚的人

LikedBy 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|計數|integer|
|names|array|



 **YammmerEntity**：按讚的人

YammmerEntity 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|類型|字串|
|id|integer|
|full\_name|字串|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)
## 物件定義： 

 **WebResultModel**：Bing 的網頁搜尋結果

WebResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|課程名稱|字串|
|說明|字串|
|DisplayUrl|字串|
|識別碼|字串|
|FullUrl|字串|



 **VideoResultModel**：Bing 的影片搜尋結果

VideoResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|課程名稱|字串|
|DisplayUrl|字串|
|識別碼|字串|
|MediaUrl|字串|
|執行階段|integer|
|縮圖|沒有定義|



 **ThumbnailModel**：多媒體元素的縮圖屬性

ThumbnailModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|MediaUrl|字串|
|ContentType|字串|
|寬度|integer|
|高度|integer|
|FileSize|integer|



 **ImageResultModel**：Bing 的圖像搜尋結果

ImageResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|課程名稱|字串|
|DisplayUrl|字串|
|識別碼|字串|
|MediaUrl|字串|
|SourceUrl|字串|
|縮圖|沒有定義|



 **NewsResultModel**：Bing 的新聞搜尋結果

NewsResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|課程名稱|字串|
|說明|字串|
|DisplayUrl|字串|
|識別碼|字串|
|來源|字串|
|Date|字串|



 **SpellResultModel**：Bing 的拼字建議結果

SpellResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|值|字串|



 **RelatedSearchResultModel**：Bing 的相關搜尋結果

RelatedSearchResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|課程名稱|字串|
|識別碼|字串|
|BingUrl|字串|



 **CompositeSearchResultModel**：Bing 的綜合搜尋結果

CompositeSearchResultModel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|WebResultsTotal|integer|
|ImageResultsTotal|integer|
|VideoResultsTotal|integer|
|NewsResultsTotal|integer|
|SpellSuggestionsTotal|integer|
|WebResults|array|
|ImageResults|array|
|VideoResults|array|
|NewsResults|array|
|SpellSuggestionResults|array|
|RelatedSearchResults|array|


## 物件定義： 

 **PostOperationResponse**：代表張貼至 Slack 後 Slack 連接器張貼作業的回應

PostOperationResponse 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|確定|布林值|
|channel|字串|
|ts|字串|
|訊息|沒有定義|
|錯誤|字串|



 **MessageItem**：通道訊息。

MessageItem 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|文字|字串|
|id|字串|
|user|字串|
|created|integer|
|is\_user-deleted|布林值|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

<!---HONumber=AcomDC_0323_2016-->