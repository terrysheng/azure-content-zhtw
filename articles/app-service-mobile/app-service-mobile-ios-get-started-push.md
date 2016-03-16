<properties
	pageTitle="使用 Azure 行動應用程式將推播通知新增至 iOS 應用程式"
	description="了解如何使用 Azure 行動應用程式將推播通知傳送至 iOS 應用程式。"
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="krisragh"/>


# 將推播通知新增至您的 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## 概觀
在本教學課程中，您會將推播通知加入 [iOS 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知。本教學課程以 [iOS 快速入門]教學課程為基礎，您必須先完成該教學課程。如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

[iOS 模擬器不支援推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)，所以在此教學課程中，您需要有實體 iOS 裝置和 [Apple Developer Program 成員資格](https://developer.apple.com/programs/ios/)。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>註冊應用程式以取得推播通知

[AZURE.INCLUDE [啟用 Apple 推播通知](../../includes/enable-apple-push-notifications.md)]

## 設定 Azure 來傳送推播通知

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>將推播通知新增至應用程式

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>在應用程式中測試推播通知

[AZURE.INCLUDE [在應用程式中測試推播通知](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>更多資訊

* 範本可讓您彈性地傳送跨平台推播和當地語系化推播。[如何使用適用於 Azure 行動應用程式的 iOS 用戶端程式庫](app-service-mobile-ios-how-to-use-client-library.md#templates)一文，說明如何註冊範本。
* 標記可讓您使用推播鎖定區隔的客戶。[使用適用於 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)一文，說明如何在裝置安裝中新增標記。

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[iOS 快速入門]: app-service-mobile-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->