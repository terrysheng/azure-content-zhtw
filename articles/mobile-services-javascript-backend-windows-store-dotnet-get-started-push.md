<properties 
	pageTitle="開始使用推播通知 (Windows 市集) | 行動開發人員中心" 
	description="了解如何使用 Azure 行動服務及通知中心傳送推播通知至通用 Windows 市集應用程式。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>


# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

本主題說明如何使用 Azure 行動服務，將推播通知傳送至通用 Windows 應用程式。 
在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

>[AZURE.NOTE]本主題說明如何使用 Windows 市集專案中的行動服務，手動設定推播通知。您可以利用 Visual Studio 2013，將相同的推播通知新增至 Windows 市集應用程式專案。如需詳細資訊，請參閱本教學課程的[通用 Windows 應用程式版本](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push/)。 

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [向 WNS 註冊您的應用程式，並設定行動服務](#register)
2. [更新應用程式以註冊通知](#update-app)
3. [更新伺服器指令碼以傳送推播通知](#update-scripts)
3. [插入資料以接收推播通知](#test)

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[開始使用資料]，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。 

>[AZURE.NOTE]此主題支援 Windows Phone 市集 8.1 應用程式。若要將推播通知新增至 Windows Phone 8 或 Windows Phone Silverlight 8.1 應用程式，請檢視此版本的[開始在行動服務中使用推播通知](/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push)。

##<a id="register"></a>向 WNS 註冊您的應用程式，並設定行動服務

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

您的行動服務和應用程式現在都已完成設定，而可與 WNS 和通知中心搭配使用。接下來，您將更新通用 Windows 市集應用程式來註冊通知。

##<a id="update-app"></a>更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列  `using` 陳述式：

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. 將下列方法新增至  **App** 類別： 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後為推播通知註冊該 ChannelURI。
    
4. 在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新的 **InitNotificationsAsync** 方法：

        InitNotificationsAsync();

	這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。 

5. 按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。
  
6. (選用) 如果您不要使用管理入口網站產生的快速入門專案，請開啟 Package.appxmanifest 檔案，並在 [**應用程式 UI**] 索引標籤中確認 [**支援快顯通知**] 設為 [**是**]。

   	![][2]

   	如此可確定您的應用程式可以提出快顯通知。下載的快速入門專案中已啟用這些通知。

##<a id="update-scripts"></a>更新伺服器指令碼以傳送推播通知

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a>在應用程式中測試推播通知

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。接著，請考慮完成下列其中一個教學課程：

+ [傳送推播通知給已驗證的使用者]
	<br/>了解如何利用標籤，從行動服務將推播通知只傳送給驗證的使用者。

+ [將廣播通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收其所需類別的推播通知。

+ [將範本型通知傳送給訂閱者]
	<br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]
  <br/>深入了解如何使用行動服務儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中心？]
  <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中心應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得指引疑難排解和偵測通知中心解決方案。 

* [行動服務 .NET 做法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET

* [行動服務伺服器指令碼參考]
  <br/>深入了解如何在您的行動服務中實作商務邏輯。

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users

[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[行動服務 .NET 做法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[傳送推播通知給已驗證的使用者]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
[將廣播通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[將範本型通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/



<!--HONumber=42-->
