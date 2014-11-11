<properties pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 從用戶端呼叫自訂 API

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-call-custom-api"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題將示範如何從 Windows 市集應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為 `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1.  [定義自訂 API][定義自訂 API]
2.  [更新應用程式以呼叫自訂 API][更新應用程式以呼叫自訂 API]
3.  [測試應用程式][測試應用程式]

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]。本教學課程將使用 Visual Studio 2012 Express for Windows 8。

## <a name="define-custom-api"></a>定義自訂 API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>更新應用程式以呼叫自訂 API

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

## 後續步驟

現在您已建立自訂 API，並可從您的 Windows 市集應用程式呼叫此 API，請考慮搜尋有關下列行動服務主題的更多資訊：

-   [定義支援定期通知的自訂 API][定義支援定期通知的自訂 API]

    了解如何在 Windows 市集應用程式中使用自訂 API 來支援定期通知。在啟用定期通知的情況下，Windows 將定期存取您的自訂 API 端點，並使用傳回的 XML (採用磚的特定格式) 來更新開始功能表上的應用程式磚。

-   [行動服務伺服器指令碼參照][行動服務伺服器指令碼參照]

     (英文) 深入了解建立自訂 API。

-   [在來源控制中儲存伺服器指令碼][在來源控制中儲存伺服器指令碼]

    了解如何使用來源控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started/
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [定義自訂 API]: #define-custom-api
  [更新應用程式以呼叫自訂 API]: #update-app
  [測試應用程式]: #test-app
  [mobile-services-dotnet-backend-create-custom-api]: ../includes/mobile-services-dotnet-backend-create-custom-api.md
  [mobile-services-windows-store-javascript-call-custom-api]: ../includes/mobile-services-windows-store-javascript-call-custom-api.md
  [定義支援定期通知的自訂 API]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
  [行動服務伺服器指令碼參照]: http://go.microsoft.com/fwlink/?LinkId=262293
  [在來源控制中儲存伺服器指令碼]: /zh-tw/documentation/articles/mobile-services-store-scripts-source-control
