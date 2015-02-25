<properties pageTitle="如何啟用 Apple 推播通知" description="請遵循本教學課程，使用 Azure 行動服務建立新的服務。" services="mobile-services, notification-hubs" documentationCenter="ios" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# 如何啟用 Apple 推播通知

本主題說明如何使用 Apple 推播通知服務 (APNS) 啟用 iOS 應用程式的推播通知。取得的憑證可用來在 [Azure 管理入口網站][管理入口網站]中註冊 iOS 應用程式的推播通知。如需完整的端對端教學課程 (包括應用程式的更新)，請參閱[開始使用推播通知]。 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

接下來，您將使用此 .p12 憑證，讓服務能夠使用 APNS 進行驗證，並代表您的應用程式傳送推播通知。

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[行動服務 SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[管理入口網站]: https://manage.windowsazure.com/



<!--HONumber=42-->
