<properties writer="ricksal" pageTitle="從 Android 用戶端呼叫自訂 API | 行動開發人員中心" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="ricksal,glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# 從用戶端呼叫自訂 API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

本主題將示範如何從 Android 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，該要求會將行動服務的資料表中所有 todo 項目的「*已完成*」旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務]或[開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1. [定義自訂 API]
2. [更新應用程式以呼叫自訂 API]
3. [測試應用程式] 


>[AZURE.NOTE] 若要查看已完成應用程式的原始程式碼，請移至 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">開始</a>。

##必要條件

[WACOM.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>定義自訂 API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## 後續步驟

您已建立自訂 API，並從您的 Android 應用程式呼叫此 API，現在請考慮搜尋有關下列行動服務主題的更多資訊：



* [行動服務伺服器指令碼參考]
  <br/>深入了解如何建立自訂 API。

* [在原始檔控制中儲存伺服器指令碼]
  <br/> 了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[定義自訂 API]: #define-custom-api
[更新應用程式以呼叫自訂 API]: #update-app
[測試應用程式]: #test-app
[後續步驟]: #next-steps

<!-- URLs. -->
[行動服務 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[我的應用程式儀表板]: http://go.microsoft.com/fwlink/?LinkId=262039
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-android-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-android-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-android-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-android-get-started-push/

[在原始檔控制中儲存伺服器指令碼]: /zh-tw/documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=35_1-->
