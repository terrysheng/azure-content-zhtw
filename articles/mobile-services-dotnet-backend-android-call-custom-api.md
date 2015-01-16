<properties writer="ricksal" pageTitle="從 Android 用戶端呼叫自訂 API | 行動開發人員中心" metaKeywords="" description="了解如何定義自訂 API 然後從使用 Azure 行動服務的 Windows Android 應用程式呼叫它。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# 從用戶端呼叫自訂 API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

本主題將示範如何從 Android 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可讓您傳送單一 POST 要求，該要求會將行動服務的資料表中所有 todo 項目的「*已完成*」旗標設為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，以更新資料表中每個 todo 項目的旗標。

將此功能新增至您在完成[開始使用行動服務]或[開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1. [定義自訂 API]
2. [更新應用程式以呼叫自訂 API]
3. [測試應用程式] 

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。 

## <a name="define-custom-api"></a>定義自訂 API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

## 後續步驟

您已建立自訂 API，並從您的 Android 應用程式呼叫此 API，現在請考慮搜尋有關下列行動服務主題的更多資訊：

* [行動服務伺服器指令碼參考]
  <br/>進一步了解如何建立自訂 API。

* [在原始檔控制中儲存伺服器指令碼]
  <br/> 了解如何使用原始檔控制功能，更輕鬆且安全地開發及發佈自訂 API 指令碼。

<!-- Anchors. -->
[定義自訂 API]: #define-custom-api
[更新應用程式以呼叫自訂 API]: #update-app
[測試應用程式]: #test-app
[後續步驟]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[行動服務 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[我的應用程式儀表板]: http://go.microsoft.com/fwlink/?LinkId=262039
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[在原始檔控制中儲存伺服器指令碼]: /zh-tw/documentation/articles/mobile-services-store-scripts-source-control
