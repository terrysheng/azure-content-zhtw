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
ms.date="02/18/2016"
ms.author="deonhe"/>

# 開始使用 Office 365 影片 API

Office 365 影片 API 提供 API 來讓您使用 Office 365 的頻道及影片。

>[AZURE.NOTE] 這一版的文章適用於邏輯應用程式 2015-08-01-preview 結構描述版本。對於 2014-12-01-preview 結構描述版本，請按一下 [Office 365 影片連接器](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md)。

您可以利用 Office 365 影片來：

* 建置邏輯應用程式

如要在邏輯應用程式中新增作業，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## 我們來談談觸發程序及動作。

Office 365 影片 API 可當做動作來使用；它沒有觸發程序。所有 API 都支援 JSON 和 XML 格式的資料。

 Office 365 影片 API 提供下列動作及/或觸發程序：

### Office365 影片 API 的動作
您可以採取下列動作：

|動作|說明|
|--- | ---|
|IsVideoPortalEnabled|查看影片入口網站的狀態，來確認影片服務是否已啟用|
|ListViewableChannels|取得使用者可檢視的所有頻道|
|ListVideos|列出某個頻道中的所有 Office 365 影片|
|GetVideo|取得特定 Office 365 影片的相關資訊|
|GetPlaybackUrl|取得某個影片之 Azure 媒體服務資訊清單的播放 URL|
|GetStreamingKeyAccessToken|取得有權限來為影片解密的持有人權杖|
ActionsTableReplaceMeLater## 建立至 Office 365 影片 API 的連線 如要使用 Office 365 影片 API，您必須先建立「連線」，再提供下列屬性的詳細資料：

|屬性| 必要|說明|
| ---|---|---|
|權杖|是|提供 SharePoint Online 的認證|


>[AZURE.TIP] 您可以在其他的邏輯應用程式中使用這個連線。

## Office 365 影片 REST API 參考
#### 本文件適用的版本：1.0


### 查看影片入口網站的狀態，來確認影片服務是否已啟用
**```GET: /{tenant}/IsEnabled```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得使用者可檢視的所有頻道
**```GET: /{tenant}/Channels```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 列出某個頻道中的所有 Office 365 影片
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|需要擷取之影片所屬的頻道識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得特定 Office 365 影片的相關資訊
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|頻道識別碼|
|videoId|字串|yes|路徑|無|視訊的識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得某個影片之 Azure 媒體服務資訊清單的播放 URL
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|頻道識別碼|
|videoId|字串|yes|路徑|無|視訊的識別碼|
|streamingFormatType|字串|yes|query|無|串流格式類型。1：Smooth Streaming 或 MPEG-DASH。0：HLS Streaming|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



### 取得有權限來為影片解密的持有人權杖
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| 名稱| 資料類型|必要|位於|預設值|說明|
| ---|---|---|---|---|---|
|tenant|字串|yes|路徑|無|使用者所屬目錄的租用戶名稱|
|channelId|字串|yes|路徑|無|頻道識別碼|
|videoId|字串|yes|路徑|無|視訊的識別碼|


### 下列為可能的回應：

|名稱|說明|
|---|---|
|200|作業已順利完成|
|400|BadRequest|
|401|未經授權|
|404|找不到|
|500|內部伺服器錯誤|
|預設值|作業失敗。|
------



## 物件定義： 

 **Channel**：頻道類別

Channel 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|課程名稱|字串|
|說明|字串|



 **Video**：影片類別

Video 的必要屬性：


這些屬性都是不必要的。


**所有屬性**：


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|課程名稱|字串|
|說明|字串|
|CreationDate|字串|
|擁有者|字串|
|ThumbnailUrl|字串|
|VideoUrl|字串|
|VideoDuration|integer|
|VideoProcessingStatus|integer|
|ViewCount|integer|


## 後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0224_2016-->