<properties 
	pageTitle="使用.NET 執行階段行動服務開始使用推播通知中樞" 
	description="了解如何使用 Azure 行動服務及通知中心傳送推播通知至通用 Windows 市集應用程式。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc,ricksal"/>


# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

本主題說明如何使用 Azure 行動服務與 .NET 後端傳送推播通知至通用 Windows 應用程式。在本教學課程中，您會使用 Azure 通知中心，讓推播通知能夠傳送至通用 Windows 快速入門專案。完成後，您的行動服務就會在每次插入記錄時從 .Net 後端使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

>[AZURE.NOTE]本主題說明如何針對 Windows 市集應用程式，使用 Windows Notification Service (WNS) 來手動設定推播通知。您可以利用 Visual Studio 2013 工具，在 Windows 應用程式專案中自動設定相同的推播通知。如需詳細資訊，請參閱本教學課程的[通用 Windows 應用程式版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push) 。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [向 WNS 註冊您的應用程式，並設定行動服務](#register)
2. [更新應用程式以註冊通知](#update-app)
3. [更新伺服器以傳送推播通知](#update-server)
4. [啟用推播通知以進行本機測試](#local-testing)
3. [插入資料以接收推播通知](#test)

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務]或[開始使用資料]，將您的專案連接到行動服務。 

>[AZURE.NOTE]此主題支援 Windows Phone 市集 8.1 應用程式。若要將推播通知新增至Windows Phone 8 或 Windows Phone Silverlight 8.1 應用程式，請檢視此版本的[開始在行動服務中使用推播通知](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)。

## <a id="register"></a>向 WNS 註冊您的應用程式，並設定行動服務

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

您的行動服務和應用程式現在都已完成設定，可與 WNS 和通知中心搭配使用。接下來，您將更新通用 Windows 應用程式以註冊通知。

## <a id="update-app"></a> 更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1. 在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列  `using` 陳述式:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. 將下列 `InitNotificationAsync` 方法新增至 **App** 類別，以建立推播通知通道，並註冊推播通知： 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


    此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後為推播通知註冊該 ChannelURI。
    
3. 在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

        InitNotificationsAsync();

	這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。 

4. 在 Visual Studio 中開啟 Package.appxmanifest 檔案，並在 [**應用程式 UI**] 索引標籤上確認 [**支援快顯通知**] 設為 [**是**]。儲存檔案。

   	![][1]

   	如此可確定您的應用程式可以提出快顯通知。 

## <a id="update-server"></a> 更新伺服器以傳送推播通知

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <a id="local-testing"></a> 啟用推播通知以進行本機測試

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <a id="test"></a> 在應用程式中測試推播通知

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>後續步驟

本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。接下來，請考慮閱讀下個教學課程[將推播通知傳送給驗證的使用者]，此課程說明如何使用標籤將行動服務中的推播通知指定傳送給驗證的使用者。

<!--+ [傳送推播通知給已驗證的使用者]
	<br/>了解如何利用標籤，從行動服務將推播通知只傳送給驗證的使用者。

+ [將廣播通知傳送給訂閱者]
	<br/>了解使用者如何註冊及接收其所需類別的推播通知。

+ [將範本型通知傳送給訂閱者]
	<br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。
-->
在下列主題中深入了解行動服務和通知中心：

* [開始使用資料]
  <br/>深入了解如何使用行動服務儲存和查詢資料。

* [開始使用驗證]
  <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

* [什麼是通知中樞？]
  <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

* [偵錯通知中樞應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得通知中樞解決方案的疑難排解和偵錯指引。 

* [行動服務 .NET 做法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[將推播通知傳送給驗證的使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[什麼是通知中樞？]: /zh-tw/documentation/articles/notification-hubs-overview/
[將廣播通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[將範本型通知傳送給訂閱者]: /zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[行動服務 .NET 做法概念性參考]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library



<!--HONumber=42-->
