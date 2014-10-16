<properties linkid="mobile-services-call-custom-api-wp8" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Phone client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Phone app that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

從用戶端呼叫自訂 API
====================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows 市集 C#")[Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows 市集 JavaScript")[Windows Phone](/zh-tw/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone")[iOS](/zh-tw/documentation/articles/mobile-services-ios-call-custom-api "iOS")[Android](/zh-tw/documentation/articles/mobile-services-android-call-custom-api "Android")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-windows-phone-call-custom-api "JavaScript 後端")

本主題將說明如何從 Windows Phone 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/)教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1.  [定義自訂 API](#define-custom-api)
2.  [更新應用程式以呼叫自訂 API](#update-app)
3.  [測試應用程式](#test-app)

本教學課程是以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started/)或[開始使用資料](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/)。本教學課程將使用 Visual Studio 2012 Express for Windows Phone。

定義自訂 API
------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

後續步驟
--------

現在您已建立自訂 API，並可從您的 Windows Phone 應用程式呼叫此 API，建議您進一步了解關於下列行動服務主題的詳細資訊：

-   [行動服務伺服器指令碼參照](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>(英文) 深入了解建立自訂 API。

-   [在來源控制中儲存伺服器指令碼](/zh-tw/documentation/articles/mobile-services-store-scripts-source-control)
    <br/>了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。


