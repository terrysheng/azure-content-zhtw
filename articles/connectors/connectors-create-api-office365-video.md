<properties
pageTitle="在您的邏輯應用程式中新增 Office 365 影片 API | Microsoft Azure"
description="開始在您的 Microsoft Azure App service Logic apps 中使用 Office 365 影片 API (連接器)"
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
ms.date="03/23/2016"
ms.author="deonhe"/>

# 開始使用 Office 365 影片 API
連接至 Office 365 影片，以取得 Office 365 影片的相關資訊、影片清單等。可從以下位置使用 Office 365 影片 API：

- 邏輯應用程式 

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。此 API 不受任何舊版結構描述支援。

有了 Office 365 影片，您可以：

- 根據您從 Office 365 影片所取得的資料，來建置您的商務流程。 
- 檢查影片入口網站狀態、取得頻道影片清單等動作。這些動作會收到回應，然後輸出能讓其他動作使用的資料。例如，您可以使用 Bing 搜尋 API 來搜尋 Office 365 影片，然後使用 Office 365 影片 API 取得該影片的相關資訊。如果影片符合您的需求，您可以將該影片張貼在 Facebook 上。 

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 觸發程序及動作

Office 365 影片 API 提供下列動作。但不包含觸發程序。

| 觸發程序 | 動作|
| --- | --- |
| None | <ul><li>檢查影片入口網站狀態</li><li>取得所有可檢視的頻道</li><li>取得某支影片之 Azure 媒體服務資訊清單的播放 URL</li><li>取得權限為影片解密的持有人權杖</li><li>取得特定 office365 影片的相關資訊</li><li>列出某個頻道中的所有 office365 影片</li></ul>

所有 API 皆支援 JSON 與 XML 格式的資料。

## 建立 Office365 影片 API 的連接
當您將此 API 新增到邏輯應用程式時，您必須登入您的 Office 365 影片帳戶，並允許邏輯應用程式連線到您的帳戶。

1. 登入 Office 365 影片帳戶。
2. 允許您的邏輯應用程式連線並使用您的 Office 365 帳戶。 

當您建立連線之後，請輸入 Office 365 影片的屬性，例租用戶名稱或頻道識別碼。本主題的＜REST API 參考＞一節會說明這些屬性。

>[AZURE.TIP] 您可以在其他邏輯應用程式中，使用這個相同的 Office 365 影片連線。

## Swagger REST API 參考
適用的版本：1.0。

### 檢查視訊入口網站狀態 
查看影片入口網站的狀態，來確認影片服務是否已啟用。```GET: /{tenant}/IsEnabled```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|



### 取得所有可檢視的頻道 
取得使用者可檢視的所有頻道。```GET: /{tenant}/Channels```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### 列出某個頻道中的所有 Office 365 影片 
列出某個頻道中的所有 office365 影片。```GET: /{tenant}/Channels/{channelId}/Videos```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|需要擷取之影片所屬的頻道識別碼|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### 取得特定 Office 365 影片的相關資訊 
取得特定 office365 影片的相關資訊。```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|頻道識別碼|
|videoId|字串|yes|路徑|無|視訊的識別碼|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### 取得某個影片之 Azure 媒體服務資訊清單的播放 URL 
取得某個影片之 Azure 媒體服務資訊清單的播放 URL。```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|頻道識別碼|
|videoId|字串|yes|路徑|無|視訊的識別碼|
|streamingFormatType|字串|yes|query|無|串流格式類型。1：Smooth Streaming 或 MPEG-DASH。0：HLS Streaming|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|




### 取得有權限來為影片解密的持有人權杖 
取得權限為影片解密的持有人權杖。```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|頻道識別碼|
|videoId|字串|yes|路徑|無|視訊的識別碼|


#### Response

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## 物件定義

#### 頻道：頻道類別

| 名稱 | 資料類型 | 必要|
|---|---|---|
|識別碼|字串|no|
|課程名稱|字串|no|
|說明|字串|no|


#### 影片 

| 名稱 | 資料類型 |必要|
|---|---|---|
|識別碼|字串|no|
|課程名稱|字串|no|
|說明|字串|no|
|CreationDate|字串|no|
|擁有者|字串|no|
|ThumbnailUrl|字串|no|
|VideoUrl|字串|no|
|VideoDuration|integer|no|
|VideoProcessingStatus|integer|no|
|ViewCount|integer|no|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0330_2016-->