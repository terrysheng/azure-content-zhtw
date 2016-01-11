<properties
	pageTitle="使用 Azure 行動應用程式將推播通知新增至 Android 應用程式"
	description="了解如何使用 Azure 行動應用程式將推播通知傳送至 Android 應用程式。"
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="yuaxu"/>

# 將推播通知新增至 Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀
在本教學課程中，您會將推播通知新增至 [Android 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知。本教學課程以 [Android 快速入門]教學課程為基礎，您必須先完成該教學課程。如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##必要條件

若要完成此教學課程，您需要下列項目：

* 具有已驗證電子郵件地址的 [Google 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=268302)。
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) &mdash; Node.js 後端專案則不需要。
* 完成[快速入門教學課程](../app-service-mobile-android-get-started.md)。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## 啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##設定行動應用程式後端以傳送推送要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## 將推播通知新增至應用程式

您必須確認自己的 Android 應用程式專案能夠處理推播通知。

###驗證 Android SDK 版本

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

下一個步驟是安裝 Google Play 服務。Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考 [設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

###新增 Google Play 服務至專案

[AZURE.INCLUDE [新增 Play 服務](../../includes/app-service-mobile-add-google-play-services.md)]

###新增程式碼

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## 對已發佈的行動服務進行應用程式測試

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

##<a id="more"></a>更多資訊

* 標記可讓您使用推播鎖定區隔的客戶。[使用適用於 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)一文，說明如何在裝置安裝中新增標記。

<!-- URLs -->
[Android 快速入門]: app-service-mobile-android-get-started.md

<!----HONumber=AcomDC_1223_2015-->