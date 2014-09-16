<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始在行動服務中使用推播通知
============================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="..NET 後端">.NET 後端</a> | <a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/"  title="avaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何使用 Azure 行動服務傳送推播通知至 Windows 市集應用程式。在本教學課程中，您會啟用透過 Azure 通知中心將推播通知傳送至快速入門專案的功能。完成後，您的行動服務就會在每次插入記錄時使用通知中心傳送推播通知。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

> [WACOM.NOTE]本教學課程將示範如何整合行動服務與通知中心，目前這是預覽版中的功能。依預設不會啟用從 JavaScript 後端使用通知中心傳送推播通知的功能。新的通知中心一旦建立後，整合程序將無法回復。目前，iOS 和 Android 的推播通知只能透過[此版本的主題](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/)所說明的預設推播支援來使用。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [向 WNS 註冊您的應用程式，並設定行動服務](#register)
2.  [更新應用程式以註冊通知](#update-app)
3.  [更新伺服器指令碼以傳送推播通知](#update-scripts)
4.  [插入資料以接收推播通知](#test)

本教學課程是以行動服務快速入門為基礎。開始本教學課程之前，您必須先完成[開始使用行動服務](/en-us/documentation/articles/mobile-services-windows-store-get-started)或[開始使用資料](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data)，將您的專案連接到行動服務。若未連接行動服務，[新增推播通知] 精靈會為您建立此連線。

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

2.  將下列方法新增至 **App** 類別：

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

3.  在 App.xaml.cs 中的 **OnLaunched** 事件處理常式頂端，將下列呼叫新增至新 **InitNotificationsAsync** 方法：

         InitNotificationsAsync();

    這會確使在每次載入頁面時都會要求註冊。在您的應用程式中，您可能只想定期進行此註冊，以確保註冊是最新的。

4.  按 **F5** 鍵以執行應用程式。包含註冊金鑰的快顯對話方塊隨即顯示。

5.  (選用) 如果您不要使用管理入口網站產生的快速入門專案，請開啟 Package.appxmanifest 檔案，並在 **[應用程式 UI]** 索引標籤中確認 **[支援快顯通知]** 設為 **[是]**。

   	![][2]

   	This makes sure that your app can raise toast notifications.These notifications are already enabled in the downloaded quickstart project.

更新伺服器指令碼以傳送推播通知
------------------------------

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

在應用程式中測試推播通知
------------------------

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  在應用程式的 **[Insert a TodoItem]** 中輸入文字，然後按一下 **[儲存]**。

   	![][13]

   	“注意插入完成之後，應用程式會收到來自 WNS 的推播通知。”

   	![][14]

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

-   [開始使用資料](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data)
    <br/>深入了解如何使用行動服務儲存和查詢資料。

-   [開始使用驗證](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users)
    <br/>了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [行動服務伺服器指令碼參考](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>深入了解如何註冊和使用伺服器指令碼。

-   [行動服務 .NET 作法概念性參考](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    <br/>深入了解如何搭配使用行動服務與 .NET。

<!-- Images. -->


[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png

