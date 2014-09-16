<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

開始在行動服務中使用推播通知
============================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><!--<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title=".NET 後端" class="current">.NET 後端</a> | <a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務與 .NET 後端傳送推播通知至 Windows 市集應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時從 .Net 後端使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

> [WACOM.NOTE]行動服務與通知中心的整合目前是預覽版所提供的功能，僅適用於 Windows 平台。在此期間，您仍可依照**開始使用通知中心** ([iOS](/en-us/documentation/articles/notification-hubs-ios-get-started)/[Android](/en-us/documentation/articles/notification-hubs-android-get-started)) 中的說明使用已連接的通知中心，從您的 .NET 後端服務將推播通知傳送至 iOS 和 Android 服務。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [向 WNS 註冊您的應用程式，並設定行動服務](#register)
2.  [更新應用程式以註冊通知](#update-app)
3.  [更新伺服器以傳送推播通知](#update-server)
4.  [插入資料以接收推播通知](#test)

本教學課程是以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started)或[開始使用資料](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。

向 WNS 註冊您的應用程式，並設定行動服務
---------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

您的行動服務和應用程式現在都已完成設定，而可與 WNS 和通知中心搭配使用。接下來，您將更新 Windows 市集應用程式以註冊通知。

更新應用程式以註冊通知
----------------------

您必須先註冊通知通道，您的應用程式才能接收推播通知。

1.  在 Visual Studio 中開啟 App.xaml.cs 檔案，並新增下列 `using` 陳述式：

         using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  將下列 `InitNotificationAsync` 方法新增至 **App** 類別，以建立推播通知通道及註冊推播通知：

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

4.  在 Visual Studio 中開啟 Package.appxmanifest 檔案，並在 **[應用程式 UI]** 索引標籤上確認 **[支援快顯通知]** 設為 **[是]**。儲存檔案。

   	![][1]

   	“如此可確定您的應用程式可以提出快顯通知。” 

更新伺服器以傳送推播通知
------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

在應用程式中測試推播通知
------------------------

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  在應用程式的 **[Insert a TodoItem]** 中輸入文字，然後按一下 **[儲存]**。

   	![][2]

   	“注意插入完成之後，應用程式會收到來自 WNS 的推播通知。”

   	![][3]

後續步驟
--------

本教學課程示範讓 Windows 市集應用程式能夠在行動服務中處理資料的基本概念。接下來，考慮完成本教學課程中採用 GetStartedWithData 應用程式所建立的下列其中一個教學課程：

-   [開始使用通知中心](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>了解如何在 Windows 市集應用程式中利用通知中心。

-   [將通知傳送給訂閱者](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>了解使用者如何註冊及接收其所需類別的推播通知。

-   [將通知傳送給使用者](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>了解如何將推播通知從行動服務傳送給任何裝置上的特定使用者。

-   [將交叉平台通知傳送給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

考慮更深入了解下列行動服務主題：

-   [開始使用資料](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)
    <br/>深入了解如何 .Net 執行階段使用行動服務儲存和查詢資料。

-   [開始使用驗證](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users)
    <br/>了解如何使用 .Net 執行階段行動服務驗證使用不同帳戶類型的應用程式使用者。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>深入了解如何註冊和使用伺服器指令碼。

-   [行動服務 .NET 作法概念性參考](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    <br/>深入了解如何搭配使用行動服務與 .NET。


<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
