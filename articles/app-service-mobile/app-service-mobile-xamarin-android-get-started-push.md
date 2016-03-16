<properties
	pageTitle="使用 Azure App Service 將推播通知新增至 Xamarin.Android 應用程式"
	description="了解如何使用 Azure App Service 及 Azure 通知中樞將推播通知傳送到 Xamarin.Android 應用程式"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# 將推播通知新增至 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##概觀

在本教學課程中，您會將推播通知新增至 [Xamarin.Android 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知。本教學課程以 [Xamarin.Android 快速入門]教學課程為基礎，您必須先完成該教學課程。如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##必要條件

本教學課程需要下列各項：

+ 有效的 Google 帳戶。您可以在 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) 註冊 Google 帳戶。

+ [Google Cloud Messaging 用戶端元件](http://components.xamarin.com/view/GCMClient/)。您在教學課程中會新增此元件。

+ 完成 [Xamarin.Android 快速入門]教學課程。


##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [啟用 GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##設定行動應用程式後端以傳送推播要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>設定用於推播通知的用戶端專案

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>將推播通知程式碼新增至應用程式

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>在應用程式中測試推播通知

您可以在模擬器中使用虛擬裝置來測試應用程式。在模擬器上執行時，您需要採取其他設定步驟。

1. 針對您要部署的目的地虛擬裝置或偵錯的所在虛擬裝置，請確認該裝置具有已設定為目標的 Google API，如以下 Android 虛擬裝置管理員 (AVD) 所示。

	![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. 按一下 [應用程式] > [設定] > [加入帳戶] 將 Google 帳戶加入 Android 裝置，然後依照提示使用裝置現有的 Google 帳戶來建立新帳戶。

	![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. 按照先前的方法執行 todolist 應用程式，然後插入新的 todo 項目。這次，通知圖示會顯示在通知區域中。您可以開啟通知抽屜來檢視通知的完整內容。

	![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android 快速入門]: app-service-mobile-xamarin-android-get-started.md

[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0211_2016-->