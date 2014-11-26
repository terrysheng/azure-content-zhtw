<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/zh-TW/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-TW/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/zh-TW/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-TW/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-TW/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-TW/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

本主題將示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。
在本教學課程中，您將建立可使用 Apple 推播通知服務 (APNs) 接收推播通知的空白 iOS 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [產生憑證簽署要求][產生憑證簽署要求]
2.  [註冊您的應用程式並啟用推播通知][註冊您的應用程式並啟用推播通知]
3.  [建立應用程式的佈建設定檔][建立應用程式的佈建設定檔]
4.  [設定您的通知中樞][設定您的通知中樞]
5.  [將您的應用程式連接到通知中樞][將您的應用程式連接到通知中樞]
6.  [從後端傳送通知][從後端傳送通知]

本教學課程將示範使用通知中心的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以了解如何使用通知中心來處理特定使用者和裝置群組。本教學課程需要下列先決條件：

-   [行動服務 iOS SDK][行動服務 iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   iOS 5.0 (或以上版本) 功能裝置
-   iOS Developer Program 成員資格

 <b>注意</b><br /> <p>基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。</p><br /></p>完成本教學課程是 iOS 應用程式所有其他通知中心教學課程的先決條件。

 <strong>注意</strong> <p>若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-TW/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。</p>

[WACOM.INCLUDE [啟用 Apple 推播通知](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>設定您的通知中樞

1.  在 Keychain Access 中，以滑鼠右鍵按一下快速入門應用程式的新憑證 [我的憑證]。按一下 [匯出]、為檔案命名、選取 [.p12] 格式，然後按一下 [儲存]。

    ![][0]

記下匯出憑證的檔案名稱和位置。

> [WACOM.NOTE] 本教學課程將建立 QuickStart.p12 檔案。Your file name and location might be different.

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，並按一下畫面底部的 [+新增]。

2.  依序按一下 [App Services]、[服務匯流排]、[Notification Hub]、[快速建立]。

    ![][1]

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

    ![][2]

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 [設定] 索引標籤。

    ![][3]

5.  按一下頂端的 [Notification Hubs] 索引標籤，然後按一下您剛才建立的通知中心。

    ![][4]

6.  選取頂端的 [設定] 索引標籤，然後按一下 Apple 通知設定的 [上傳]。然後選取稍早匯出的 **.p12** 憑證，以及憑證的密碼。務必選擇要使用「生產」(如果要傳送推播通知給在市集購買您應用程式的使用者) 或「沙箱」(開發期間) 推播服務。

    ![][5]

7.  按一下頂端的 [儀表板] 索引標籤，然後按一下 [連接資訊]。記下這兩個連接字串。

    ![][6]

現在已將您的通知中心設定成使用 APN，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

## <a name="connecting-app"></a>將您的應用程式連接到通知中樞

1.  在 XCode 中建立新的 iOS 專案，並選取 [Single View Application] 範本。

    ![][7]

2.  在 [目標] 下按一下您的專案名稱，然後展開 [Code Signing Identity]，接著在 [偵錯] 下選取程式碼簽署識別設定檔。另外，如果使用較新版本的 XCode，請將 [層級] 從 [基本] 切換成 [全部]，並將 [Provisioning Profile] 行項目設定為佈建設定檔。

    ![][8]

3.  下載 Azure Mobile SDK for iOS。開啟 .zip 檔案，並將 WindowsAzureMessaging.framework 資料夾拖曳到 XCode 專案中的 Framework 資料夾。選取 [Copy items in destination group's folder]，然後按一下 [確定]。

    ![][9]

4.  在 AppDelegate.h 檔案中新增下列 import 指示詞：

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  在 AppDelegate.m 檔案的 `didFinishLaunchingWithOptions` 方法中新增下列程式碼：

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  在相同檔案中新增下列方法：

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                      @"<connection string>" notificationHubPath:@"mynh"];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

7.  *(選用)* 同樣地，如果應用程式正在作用中時收到通知，您可以在相同檔案中新增下列方法以顯示 **UIAlert**：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

8.  在您的裝置上執行應用程式。

## <a name="send"></a>從後端傳送通知

您可以使用 [REST 介面][REST 介面]，從任何後端使用通知中樞傳送通知。在本教學課程中，您將透過 .NET 主控台應用程式來傳送通知。有關如何從與通知中樞整合的 Azure Mobile Services 傳送通知的範例，請參閱**開始在行動服務中使用推播通知** ([.NET 後端][.NET 後端] | [JavaScript 後端][.NET 後端])。如需如何使用 REST API 傳送通知的範例，請參閱**如何從 Java/PHP 使用通知中樞** ([Java][Java] | [PHP][PHP])。

1.  在 Visual Studio 中，從 [檔案] 功能表選取 [開新檔案]、[專案...]，然後按一下 [Visual C#] 下方的 [Windows] 和 [主控台應用程式]，再按一下 [確定]。

    ！[][20]

    這會建立新的主控台應用程式專案。

2.  在 [工具] 功能表中，依序按一下 [Library Package Manager] 及 [Package Manager Console]。

    這會顯示 [Package Manager Console]。

3.  在主控台視窗中，執行下列命令：

        Install-Package WindowsAzure.ServiceBus

    這會使用 [WindowsAzure.ServiceBus NuGet 封裝][WindowsAzure.ServiceBus NuGet 封裝]，來新增 Azure 服務匯流排 SDK 的參考。

4.  開啟檔案 Program.cs，並新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

5.  在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    請確保使用出現在入口網站 [Notification Hubs] 索引標籤上的通知中樞名稱，來取代 "hub name" 預留位置。此外，請使用您在＜設定您的通知中樞＞一節中取得之名為 **DefaultFullSharedAccessSignature** 的連接字串，來取代連接字串預留位置。

    > [WACOM.NOTE]請確定您會使用包含 [完整] 存取權 (而非 [接聽] 存取權) 的連接字串。接聽存取權字串不具備傳送通知的權限。

6.  接著在 **Main** 方法中新增下列程式碼行。

         SendNotificationAsync();
         Console.ReadLine();

7.  按 F5 鍵以執行主控台應用程式。

    您應該會在裝置上收到警示。如果您使用 Wi-fi，請確定連線正在運作。

您可以在 Apple [本機和推播通知程式設計指南][本機和推播通知程式設計指南] (英文) 中找到所有可能的裝載。

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您將廣播通知到您的所有 iOS 裝置。若要以特定使用者為目標，請參閱教學課程[使用通知中心來推播通知給使用者][使用通知中心來推播通知給使用者]，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞][使用通知中心傳送即時新聞]。在[通知中心指引][通知中心指引]中深入了解如何使用通知中心。

 
 


  [產生憑證簽署要求]: #certificates
  [註冊您的應用程式並啟用推播通知]: #register
  [建立應用程式的佈建設定檔]: #profile
  [設定您的通知中樞]: #configure-hub
  [將您的應用程式連接到通知中樞]: #connecting-app
  [從後端傳送通知]: #send
  [行動服務 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [REST 介面]: http://msdn.microsoft.com/zh-TW/library/windowsazure/dn223264.aspx
  [.NET 後端]: /zh-TW/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /zh-TW/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /zh-TW/documentation/articles/notification-hubs-php-backend-how-to/
  [WindowsAzure.ServiceBus NuGet 封裝]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [本機和推播通知程式設計指南]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [使用通知中心來推播通知給使用者]: /zh-TW/manage/services/notification-hubs/notify-users-aspnet
  [使用通知中心傳送即時新聞]: /zh-TW/manage/services/notification-hubs/breaking-news-dotnet
  [通知中心指引]: http://msdn.microsoft.com/zh-TW/library/jj927170.aspx
