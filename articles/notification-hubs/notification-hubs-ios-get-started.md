<properties 
	pageTitle="開始使用 Azure 通知中心" 
	description="了解如何使用 Azure 通知中心推播通知。" 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# 開始使用通知中心

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本主題將示範如何使用 Azure 通知中心將推播通知傳送至 iOS 應用程式。在本教學課程中，您將使用 Apple 推播通知服務 (APN)，建立可接收推播通知的空白 iOS 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將示範使用通知中心的簡單廣播案例。

##必要條件

本教學課程需要下列先決條件：

+ [行動服o務 iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 5.0 (或以上版本) 功能裝置
+ iOS Developer Program 成員資格

>[AZURE.NOTE]基於推播通知組態需求，您必須在 iOS 功能裝置 (iPhone 或 iPad) 而非在模擬器上部署和測試推播通知。

完成本教學課程是 iOS 應用程式所有其他通知中心教學課程的先決條件。

> [AZURE.NOTE]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##設定您的通知中樞

1. 在 Keychain Access 中，以滑鼠右鍵按一下快速入門應用程式的新憑證 **[我的憑證]**。按一下 **[匯出]**、為檔案命名、選取 **[.p12]** 格式，然後按一下 **[儲存]**。

    ![][26]

  記下匯出憑證的檔案名稱和位置。

>[AZURE.NOTE]本教學課程會建立 QuickStart.p12 檔案。您可能會有不同的檔案名稱和位置。

2. 登入 [Azure 管理入口網站]，並按一下畫面底部的 **[+新增]**。

3. 依序按一下 **[App Services]**、**[服務匯流排]**、**[Notification Hub]**、**[快速建立]**。

   ![][27]

4. 為您的通知中心輸入名稱、選取所需的區域，然後按一下 **[Create a new Notification Hub]**。

   ![][28]

5. 按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 **[設定]** 索引標籤。

   ![][29]

6. 按一下頂端的 **[Notification Hubs]** 索引標籤，然後按一下您剛才建立的通知中心。

   ![][210]

7. 選取頂端的 **[設定]** 索引標籤，然後按一下 Apple 通知設定的 **[上傳]**。然後選取稍早匯出的 **.p12** 憑證，以及憑證的密碼。**務必選擇要使用「生產」** (如果要傳送推播通知給在市集購買您應用程式的使用者) **或「沙箱」** (開發期間) 推播服務。

   ![][211]

8. 按一下頂端的 **[儀表板]** 索引標籤，然後按一下 **[連接資訊]**。記下這兩個連接字串。

   ![][212]

現在已將您的通知中心設定成使用 APN，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

##將您的應用程式連接到通知中樞

1. 在 XCode 中建立新的 iOS 專案，並選取 **[Single View Application]** 範本。

   ![][31]

2. 在 **[目標]** 下按一下您的專案名稱，然後展開 **[Code Signing Identity]**，接著在 **[偵錯]** 下選取程式碼簽署識別設定檔。另外，如果使用較新版本的 XCode，請將 **[層級]** 從 **[基本]** 切換成 **[全部]**，並將 **[Provisioning Profile]** 行項目設定為佈建設定檔。

   ![][32]

3. 下載 Azure Mobile SDK for iOS。開啟 .zip 檔案，並將 WindowsAzureMessaging.framework 資料夾拖曳到 XCode 專案中的 Framework 資料夾。選取 **[Copy items in destination group's folder]**，然後按一下 **[確定]**。

   ![][33]

4. 在 AppDelegate.h 檔案中新增下列 import 指示詞：

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. 在 AppDelegate.m 檔案的 `didFinishLaunchingWithOptions` 方法中新增下列程式碼：

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	若是 iOS 8
   
	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                            UIUserNotificationTypeAlert |
                                            UIUserNotificationTypeBadge
					    					categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];   
	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                            UIUserNotificationTypeAlert |
                                            UIUserNotificationTypeBadge
					    					categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

6. 在相同檔案中新增下列方法：

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *(選用)* 同樣地，如果應用程式正在作用中時收到通知，您可以在相同檔案中新增下列方法以顯示 **UIAlert**：


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. 在您的裝置上執行應用程式。

##從後端傳送通知

您可以使用 [REST 介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)，從任何後端使用通知中樞傳送通知。在本教學課程中，您將透過 .NET 主控台應用程式來傳送通知。如需從整合通知中心之 Azure 行動服務後端傳送通知的範例，請參閱**開始在行動服務中使用推播通知** ([.NET backend](../mobile-services-javascript-backend-ios-get-started-push.md) | [JavaScript backend](../mobile-services-javascript-backend-ios-get-started-push.md))。如需使用 REST API 傳送通知的範例，請參閱**如何從 Java/PHP 使用通知中心** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md))。

1. 在 Visual Studio 中，從 **[檔案]** 功能表選取 **[開新檔案]**、**[專案...]**，然後按一下 **[Visual C#]** 下方的 **[Windows]** 和 **[主控台應用程式]**，再按一下 **[確定]**。  

   	![][20]

	這會建立新的主控台應用程式專案。

2. 在 **[工具]** 功能表中，依序按一下 **[Library Package Manager]** 及 **[Package Manager Console]**。

	這會顯示 [Package Manager Console]。

6. 在主控台視窗中，執行下列命令：

        Install-Package WindowsAzure.ServiceBus

	這會使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>新增 Azure 服務匯流排 SDK 的參考。

5. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

6. 在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	請務必使用出現在入口網站 **[通知中心]** 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置。此外，請將連接字串預留位置取代為您在「設定您的通知中心」一節中取得，且名為 **DefaultFullSharedAccessSignature** 的連接字串。

	>[AZURE.NOTE]請確定您使用的連接字串具有 [**完整**] 存取權，而非 [**接聽**] 存取權。接聽存取權的字串沒有傳送通知的權限。

5. 接著在 **Main** 方法中新增下列程式碼行。

         SendNotificationAsync();
		 Console.ReadLine();

5. 按 F5 鍵以執行主控台應用程式。

	您應該會在裝置上收到警示。如果您使用 Wi-fi，請確定連線正在運作。

您可以在 Apple [本機和推播通知程式設計指南] (英文) 中找到所有可能的裝載。

##後續步驟

在此簡單範例中，您將廣播通知到您的所有 iOS 裝置。若要以特定使用者為目標，請參閱教學課程[使用通知中心來推播通知給使用者]，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞]。在[通知中心指引]中深入了解如何使用通知中心。



<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[行動服o務 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure 管理入口網站]: https://manage.windowsazure.com/
[通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[使用通知中心來推播通知給使用者]: notification-hubs-ios-mobile-services-register-user-push-notifications.md
[使用通知中心傳送即時新聞]: notification-hubs-ios-send-breaking-news.md

[本機和推播通知程式設計指南]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


<!--HONumber=52-->
 