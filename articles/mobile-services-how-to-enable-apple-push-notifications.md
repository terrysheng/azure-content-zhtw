<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 如何啟用 Apple 推播通知

本主題說明如何使用 Apple 推播通知服務 (APNS) 啟用 iOS 應用程式的推播通知。取得的憑證可用來在 [Azure 管理入口網站][Azure 管理入口網站]中註冊 iOS 應用程式的推播通知。如需完整的端對端教學課程 (包括應用程式的更新)，請參閱[開始使用推播通知][開始使用推播通知]。

[WACOM.INCLUDE [啟用 Apple 推播通知](../includes/enable-apple-push-notifications.md)]

接下來，您將使用此 .p12 憑證，讓服務能夠使用 APNS 進行驗證，並代表您的應用程式傳送推播通知。


  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
