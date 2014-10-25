<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 如何啟用 Google 雲端通訊

本主題說明如何使用 Google 雲端通訊 (GCM) 啟用 Android 應用程式的推播通知。取得的 API 金鑰可用來在 [Azure 管理入口網站][]中註冊 Android 應用程式的推播通知。如需完整的端對端教學課程 (包括應用程式的更新)，請參閱[開始使用推播通知][]。

[WACOM.INCLUDE [啟用 GCM][]]

接下來，您將使用此 API 金鑰值，讓服務能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [啟用 GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
