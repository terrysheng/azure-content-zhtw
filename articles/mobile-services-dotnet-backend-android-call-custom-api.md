<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Windows Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Call a custom API from the client" />

從用戶端呼叫自訂 API
====================

[Windows 市集 C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Windows 市集 JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS")[Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android")

[.NET 後端](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api ".NET 後端") | [JavaScript 後端](/en-us/documentation/articles/mobile-services-android-call-custom-api "JavaScript 後端")

本主題將示範如何從 Android 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，該要求會將行動服務的資料表中所有 todo 項目的「已完成」**旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/)或[開始使用資料](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/)教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1.  [定義自訂 API](#define-custom-api)
2.  [更新應用程式以呼叫自訂 API](#update-app)
3.  [測試應用程式](#test-app)

本教學課程是以行動服務快速入門為基礎。開始此教學課程之前，您必須先完成[開始使用行動服務](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/)。

定義自訂 API
------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

後續步驟
--------

您已建立自訂 API，並從您的 Android 應用程式呼叫此 API，現在請考慮搜尋有關下列行動服務主題的更多資訊：

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
     (英文) 深入了解建立自訂 API。

-   [在來源控制中儲存伺服器指令碼](/en-us/documentation/articles/mobile-services-store-scripts-source-control)
    了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。


