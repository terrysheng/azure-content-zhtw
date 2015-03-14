<properties 
	pageTitle="從 iOS 應用程式呼叫自訂 API | 行動服務" 
	description="了解如何定義自訂 API 然後從使用 Azure 行動服務的 iOS 應用程式呼叫它。" 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	Writer="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# 從用戶端呼叫自訂 API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

本主題將示範如何從 iOS 應用程式呼叫自訂 API。自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。

本主題中所建立的自訂 API 可提供您傳送單一 POST 要求，並將資料表中所有 todo 項目的已完成旗標設定為  `true`。若沒有此自訂 API，用戶端必須傳送個別要求，來為資料表中的每個 todo 項目更新旗標。

將此功能新增至您在完成[開始使用行動服務]或[開始使用資料]教學課程時所建立的應用程式。若要達到此目的，您將需要完成下列步驟：

1. [定義自訂 API]
2. [更新應用程式以呼叫自訂 API]
3. [測試應用程式]

本教學課程會以行動服務快速入門為基礎。在開始本教學課程之前，您必須首先完成[開始使用行動服務]。本教學課程需要 [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) 和 [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) 及 iOS 5.0 或更新版本。

## <a name="define-custom-api"></a>定義自訂 API

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## 後續步驟

您已建立自訂 API，並從您的 iOS 應用程式呼叫此 API，現在請考慮搜尋有關下列行動服務主題的更多資訊：

* [行動服務伺服器指令碼參考]
  <br/>深入了解如何建立自訂 API。

* [在原始程式碼控制中儲存伺服器指令碼]
  <br/> 了解如何使用原始程式碼控制功能，來更輕鬆及安全地開發並發佈自訂 API 指令碼。

<!-- Anchors. -->
[定義自訂 API]: #define-custom-api
[更新應用程式以呼叫自訂 API]: #update-app
[測試應用程式]: #test-app
[後續步驟]: #next-steps

<!-- URLs. -->
[Windows 推播通知與 Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[我的應用程式儀表板]: http://go.microsoft.com/fwlink/?LinkId=262039
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-ios-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-ios-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-ios-get-started-push/
[在原始程式碼控制中儲存伺服器指令碼]: /zh-tw/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
