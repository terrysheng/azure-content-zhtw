<properties
pageTitle="在您的邏輯應用程式中新增 Yammer API | Microsoft Azure"
description="搭配 REST API 參數來使用 Yammer API 的概觀"
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

# 開始使用 Yammer API

連線到 Yammer 來存取您企業網路中的交談。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Yammer](../app-service-logic/app-service-logic-connector-yammer.md)。

您可以利用 Yammer 來：

- 根據您從 Yammer 所取得的資料，來建置您的商務流程。 
- 在群組或您追蹤的摘要中有新訊息時使用觸發程序。
- 使用動作來張貼訊息、取得所有訊息等等。這些動作會收到回應，然後輸出能讓其他動作使用的資料。舉例來說，當新訊息出現時，您可以利用 Office 365 來傳送電子郵件。

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作
Yammer 包含下列觸發程序及動作。

觸發程序 | 動作
--- | ---
<ul><li>當群組有新訊息時</li><li>當我正在追蹤的摘要有新訊息時</li></ul>| <ul><li>取得所有訊息</li><li>取得群組中的訊息</li><li>取得我正在追蹤的摘要中的訊息</li><li>張貼訊息</li><li>當群組有新訊息時</li><li>當我正在追蹤的摘要有新訊息時</li></ul>

所有 API 都支援 JSON 和 XML 格式的資料。

## 建立至 Yammer 的連線
如要使用 Yammer API，您必須先建立「連線」，再提供下列屬性的詳細資料：

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 Yammer 的認證|

請依照下列步驟登入 Yammer，並在邏輯應用程式中完成 Yammer **連接**的組態：

1. 選取 [週期]
2. 選取 [頻率] 並輸入 [間隔]
3. 選取 [加入動作]![設定 Yammer][1]
4. 在搜尋方塊中輸入 yammer，等候搜尋傳回名稱中有 Yammer 的所有項目
5. 選取 [Yammer - 取得所有訊息]
6. 選取 [登入 Yammer]：![設定 Yammer][2]
7. 提供您的 Yammer 認證來登入以授權應用程式 ![設定 Yammer][3]  
8. 您將會重新導向至組織的登入頁面。**允許** Yammer 與邏輯應用程式互動：![設定 Yammer][4] 
9. 登入之後，返回邏輯應用程式，設定 [Yammer - 取得所有訊息] 區段並加入其他所需的觸發與動作後即可完成。![設定 Yammer][5]  
10. 選取上方功能表列的 [儲存] 來儲存您的工作。


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用這個連接。

## Yammer REST API 參考
本文件適用的版本：1.0


### 取得已登入使用者的 Yammer 網路中的所有公開訊息
對應到 Yammer Web 介面中的「所有」交談。```GET: /messages.json```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|older\_then|integer|no|query|無|傳回比指定為數值字串的訊息識別碼還要舊的訊息。這在您將訊息編頁時會很有用。舉例來說，如果您正在檢視 20 個訊息，其中最舊的訊息為 2912 號，則您可以讓要求附加「?older\_than=2912」，以便取得比您正在檢視的訊息更早的 20 個訊息。|
|newer\_then|integer|no|query|無|傳回比指定為數值字串的訊息識別碼還要新的訊息。當您輪詢新訊息時，就必須使用這個參數。如果您正在查看訊息，且傳回的最新訊息是 3516 號，則您可以傳送參數為「? newer\_than = 3516」的要求，以確保您不會收到已出現在您頁面上的相同訊息。|
|limit|integer|no|query|無|只傳回指定數目的訊息。|
|page|integer|no|query|無|取得指定的頁面。如果傳回的資料超出限制，您可以使用這個欄位來存取後續的頁面|


### Response

|名稱|說明|
|---|---|
|200|OK|
|400|不正確的要求|
|408|要求逾時|
|429|太多要求|
|500|內部伺服器錯誤。發生未知錯誤|
|503|Yammer 服務無法使用|
|504|閘道逾時|
|預設值|作業失敗。|


### 將訊息張貼到群組中或全公司摘要上
如果有提供群組識別碼，訊息將會張貼到指定群組中，否則將會張貼到全公司摘要上。```POST: /messages.json```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|input| |yes|body|無|張貼訊息的要求|


### Response

|名稱|說明|
|---|---|
|201|建立時間|



## 物件定義

#### Message：Yammer 訊息

| 名稱 | 資料類型 | 必要 |
|---|---| --- | 
|id|integer|no|
|content\_excerpt|字串|no|
|sender\_id|integer|no|
|replied\_to\_id|integer|no|
|created\_at|字串|no|
|network\_id|integer|no|
|message\_type|字串|no|
|sender\_type|字串|no|
|url|字串|no|
|web\_url|字串|no|
|group\_id|integer|no|
|body|沒有定義|no|
|thread\_id|integer|no|
|direct\_message|布林值|no|
|client\_type|字串|no|
|client\_url|字串|no|
|語言|字串|no|
|notified\_user\_ids|array|no|
|privacy|字串|no|
|liked\_by|沒有定義|no|
|system\_message|布林值|no|

#### PostOperationRequest：代表要張貼到 Yammer 上的 Yammer 連接器張貼要求

| 名稱 | 資料類型 | 必要 |
|---|---| --- | 
|body|字串|yes|
|group\_id|integer|no|
|replied\_to\_id|integer|no|
|direct\_to\_id|integer|no|
|broadcast|布林值|no|
|topic1|字串|no|
|topic2|字串|no|
|topic3|字串|no|
|topic4|字串|no|
|topic5|字串|no|
|topic6|字串|no|
|topic7|字串|no|
|topic8|字串|no|
|topic9|字串|no|
|topic10|字串|no|
|topic11|字串|no|
|topic12|字串|no|
|topic13|字串|no|
|topic14|字串|no|
|topic15|字串|no|
|topic16|字串|no|
|topic17|字串|no|
|topic18|字串|no|
|topic19|字串|no|
|topic20|字串|no|

#### MessageList：訊息的清單

| 名稱 | 資料類型 | 必要 |
|---|---| --- | 
|messages|array|no|


#### MessageBody：訊息內文

| 名稱 | 資料類型 | 必要 |
|---|---| --- | 
|parsed|字串|no|
|plain|字串|no|
|rich|字串|no|

#### LikedBy：設為喜歡的人

| 名稱 | 資料類型 | 必要 |
|---|---| --- | 
|計數|integer|no|
|names|array|no|

#### YammmerEntity：設為喜歡的人

| 名稱 | 資料類型 | 必要 |
|---|---| --- | 
|類型|字串|no|
|id|integer|no|
|full\_name|字串|no|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0323_2016-->