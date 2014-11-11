<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />

# 開始在行動服務中使用推播通知

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

本主題說明如何使用 Azure 行動服務搭配 .NET 後端，以將推播通知傳送至通用 Windows 應用程式。在本教學課程中，您會使用 Azure 通知中心，讓推播通知能夠傳送至通用 Windows 快速入門專案。完成後，您的行動服務就會在每次插入記錄時從 .Net 後端使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

> [WACOM.NOTE]本主題說明如何針對 Windows 市集應用程式，使用 Windows Notification Service (WNS) 來手動設定推播通知。您可以利用 Visual Studio 2013 工具，在 Windows 應用程式專案中自動設定相同的推播通知。如需詳細資訊，請參閱此教學課程的[通用 Windows 應用程式版本][通用 Windows 應用程式版本]。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [向 WNS 註冊您的應用程式，並設定行動服務][向 WNS 註冊您的應用程式，並設定行動服務]
2.  [更新應用程式以註冊通知][更新應用程式以註冊通知]
3.  [更新伺服器以傳送推播通知][更新伺服器以傳送推播通知]
4.  [啟用推播通知以進行本機測試][啟用推播通知以進行本機測試]
5.  [插入資料以接收推播通知][插入資料以接收推播通知]

本教學課程會以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]，將您的專案連接到行動服務。

> [WACOM.NOTE]此教學課程支援 Windows Phone 市集 8.1 應用程式。若要將推播通知新增至 Windows Phone 8 或 Windows Phone Silverlight 8.1 應用程式，請參閱此版本的[開始在行動服務中使用推播通知][開始在行動服務中使用推播通知]。

## <span id="register"></span></a>向 WNS 註冊您的應用程式，並設定行動服務

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

您的行動服務和應用程式現在都已完成設定，可與 WNS 和通知中心搭配使用。接下來，您將更新通用 Windows 應用程式以註冊通知。

## <span id="update-app"></span></a>更新應用程式以註冊通知

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1.  在 Visual Studio 中，開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  將下列 `InitNotificationAsync` 方法新增至 **App** 類別，以建立推播通知通道，並註冊推播通知：

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

3.  在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

        InitNotificationsAsync();

    這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

4.  在 Visual Studio 中開啟 Package.appxmanifest 檔案，並在 [應用程式 UI] 索引標籤上確認 [支援快顯通知] 設為 [是]。儲存檔案。

    ![][0]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">如此可確定您的應用程式可以提出快顯通知。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

## <span id="update-server"></span></a>更新伺服器以傳送推播通知

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> 啟用推播通知以進行本機測試

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>在應用程式中測試推播通知

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>後續步驟

此教學課程示範如何讓 Windows 市集應用程式能夠使用行動服務和通知中心來傳送推播通知的基本步驟。接著，請考慮完成下一個教學課程：[將推播通知傳送給驗證的使用者][將推播通知傳送給驗證的使用者]，其顯示如何利用標籤，從行動服務將推播通知只傳送給驗證的使用者。

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

在下列主題中深入了解行動服務和通知中心：

-   [開始使用資料][開始使用資料]
    深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證][開始使用驗證]
    了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。

-   [什麼是通知中心？][什麼是通知中心？]
    深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。

-   [行動服務 .NET 作法概念參考資料][行動服務 .NET 作法概念參考資料]
    深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [通用 Windows 應用程式版本]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [向 WNS 註冊您的應用程式，並設定行動服務]: #register
  [更新應用程式以註冊通知]: #update-app
  [更新伺服器以傳送推播通知]: #update-server
  [啟用推播通知以進行本機測試]: #local-testing
  [插入資料以接收推播通知]: #test
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [開始在行動服務中使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [將推播通知傳送給驗證的使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [什麼是通知中心？]: /zh-tw/documentation/articles/notification-hubs-overview/
  [行動服務 .NET 作法概念參考資料]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library
