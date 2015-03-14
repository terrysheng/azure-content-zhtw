<properties 
	pageTitle="如何啟用 Google 雲端通訊" 
	description="請遵循本教學課程，使用 Azure 行動服務建立新的服務。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# 如何啟用 Google 雲端通訊

本主題說明如何使用 Google 雲端通訊 (GCM) 啟用 Android 應用程式的推播通知。取得的 API 金鑰可用來在 [Azure 管理入口網站][管理入口網站]中註冊 Android 應用程式的推播通知。如需完整的端對端教學課程 (包括應用程式的更新)，請參閱[開始使用推播通知]。 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

接下來，您將使用此 API 金鑰值，讓服務能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[行動服務 SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[管理入口網站]: https://manage.windowsazure.com/
[.NET 後端版本]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started


<!--HONumber=42-->
