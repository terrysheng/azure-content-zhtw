<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

開始使用通知中心
================

[Windows 市集 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 市集 C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

本主題說明如何使用 Azure 通知中心傳送推播通知至 iOS 應用程式。在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可接收推播通知的空白 iOS 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [產生憑證簽署要求](#certificates)
2.  [註冊應用程式並啟用推播通知](#register)
3.  [建立應用程式的佈建設定檔](#profile)
4.  [設定您的通知中心](#configure-hub)
5.  [將您的應用程式連接到通知中心](#connecting-app)
6.  [從後端傳送通知](#send)

本教學課程將示範使用通知中心的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以了解如何使用通知中心來處理特定使用者和裝置群組。本教學課程需要下列先決條件：

-   [行動服務 iOS SDK](http://go.microsoft.com/fwLink/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   支援 iOS 5.0 (或更新版本) 的裝置
-   iOS 開發人員計劃成員資格

    **注意**

    因為推播通知組態需求，您必須在支援 iOS 的裝置 (iPhone 或 iPad) 上 (而非在模擬器中) 部署與測試推播通知。

完成本教學課程是 iOS 應用程式所有其他通知中心教學課程的先決條件。

**注意**

若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)。

Apple 推播通知服務 (APNS) 會使用憑證來驗證行動服務。依照這些指示進行，以建立必要的憑證並上傳至行動服務。如需官方的 APNS 功能文件，請參閱 [Apple 推播通知服務]。

產生 CSR 檔案產生憑證簽署要求檔案
---------------------------------

首先，您必須產生 Apple 用來產生簽署憑證的憑證簽署要求 (CSR) 檔案。

1.  從 [公用程式] 資料夾中執行 [Keychain Access] 工具。

2.  按一下 **[Keychain Access]**、展開 **[Certificate Assistant]**，然後按一下 **[Request a Certificate from a Certificate Authority...]**。

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

1.  選取您的 **[使用者電子郵件地址]** 和 **[一般名稱]**，確定已勾選 **[Saved to disk]**，然後按一下 **[繼續]**。請將 **[CA Email Address]** 欄位留空，因為它不是必要資訊。

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

1.  在 **[另存新檔]** 中輸入憑證簽署要求 (CSR) 檔案的名稱，在 **[Where]** 中選取位置，然後按一下 **[儲存]**。

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)

這會在選取的位置中儲存 CSR 檔案，預設的位置是在桌面上。請記住為檔案選擇的位置。

接下來，在 Apple 註冊您的應用程式、啟用推播通知，並上傳此匯出的 CSR 以建立推播憑證。

註冊應用程式註冊應用程式以取得推播通知
--------------------------------------

為了要從行動服務將推播通知傳送給 iOS 應用程式，您必須向 Apple 註冊應用程式，以及針對推播通知註冊應用程式。

1.  如果您尚未註冊您的應用程式，請瀏覽到 Apple 開發人員中心的 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)，使用您的 Apple ID 登入，按一下 **[識別碼]**，然後按一下 **[App IDs]**，最後按一下 **[+]** 加號以註冊新的應用程式。

	![][B102] 

2.  在 **[描述]** 中輸入您應用程式的名稱，在 **[Bundle Identifier]** 中輸入 *MobileServices.Quickstart* 值，勾選 [應用程式服務] 區段中的 [推播通知] 選項，然後按一下 **[繼續]**。此範例會使用識別碼 **MobileServices.Quickstart**，但您可能不會重複使用此相同識別碼，因為應用程式識別碼在所有使用者中必須是唯一的。因此，建議您在應用程式名稱之後附加您的全名或縮寫。

	![][B103]
           
	This generates your app ID and requests you to **Submit** the information.Click **Submit**
           
	![][B104] 
           
	Once you click **Submit**, you will see the **Registration complete** screen, as shown below.Click **Done**.
           
	![][B105]

    > [WACOM.NOTE] 如果您選擇提供 *MobileServices.Quickstart* 以外的 **[Bundle Identifier]** 值，您也必須在 Xcode 專案中更新套件組合識別碼值。

3.  尋找您剛才所建立的應用程式識別碼，並按一下該列。

	![][B106]
           
	Clicking on the app ID will display details on the app and app ID. Click the **Settings** button.
           
	![][B107] 

4.  捲動到畫面底部，然後按一下 **[Development Push SSL Certificate]** 區段下方的 **[Create Certificate...]** 按鈕。

	![][B108] 

	This displays the "Add iOS Certificate" assistant.
           
 	![][B108] 

    > [WACOM.NOTE] 本教學課程使用開發憑證。註冊生產憑證時會使用相同程序。將憑證上傳至行動服務時，只要確保您設定相同的憑證類型即可。

5.  按一下 **[選擇檔案]**，瀏覽到您在第一個工作中所建立之 CSR 檔案的儲存位置，然後按一下 **[產生]**。

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png)

1.  在入口網站建立憑證之後，依序按一下 **[下載]** 按鈕和 **[完成]**。

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png)
 
	這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。 ![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] By default, the downloaded file a development certificate is named **aps_development.cer**.

1.  在下載的推播通知 **aps\_development.cer** 上按兩下。

	This installs the new certificate in the Keychain, as shown below:

	![][B10]

    > [WACOM.NOTE] 您憑證中的名稱可能會有所不同，但它會以 **Apple Development iOS Push Notification Services:** 作為開頭。

之後，您會使用該憑證來產生 .p12 檔案，並將該檔案上傳到行動服務以啟用 APNS 驗證。

佈建應用程式為應用程式建立佈建設定檔
------------------------------------

1.  返回 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456)中，依序選取 **[佈建設定檔]**、**[全部]**，然後按一下 **[+]** 按鈕以建立新設定檔。這會顯示 **[Add iOS Provisioning Profile]** 精靈。

	![][120]

2.  選取 **[開發]** 下的 **[iOS App Development]** 作為佈建設定檔類型，並按一下 **[繼續]**。

	![][121]

3.  接著，從 **[App ID]** 下拉式清單中，選取行動服務快速入門應用程式的應用程式識別碼，並按一下 **[繼續]**。

	![][122]

4.  在 **[選取憑證]** 畫面中，選取您稍早建立的憑證，並按一下 **[繼續]**。

	![][123]

5.  接著，選取要用於測試的 **[裝置]**，並按一下 **[繼續]**。

	![][124]

6.  最後，在 **[設定檔名稱]** 中選擇設定檔的名稱，依序按一下 **[產生]**、**[完成]**。

	![][125]
           
 	![][126]

	這會建立新的佈建設定檔。

7.  在 Xcode 中開啟 [組合管理]、選取 [裝置] 檢視、在左窗格中的 **[程式庫]** 區段中選取 **[佈建設定檔]**，然後匯入您剛才建立的佈建設定檔。

8.  在左手邊選取您的裝置，並再次匯入佈建設定檔。

9.  在 [Keychain Access] 中以滑鼠右鍵按一下新憑證、按一下 **[匯出]**、輸入您的憑證名稱、選取 **.p12** 格式，然後按一下 **[儲存]**。

	![][18]

	記下檔案名稱和匯出憑證的位置。

設定您的通知中心設定您的通知中心
--------------------------------

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並按一下畫面底部的 **[+NEW]**。

2.  依序按一下 **[App Services]**、**[服務匯流排]**、**[Notification Hub]**、**[快速建立]**。

	![][27]

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 **[Create a new Notification Hub]**。

	![][28]

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 **[設定]** 索引標籤。

	![][29]

5.  按一下頂端的 **[Notification Hubs]** 索引標籤，然後按一下您剛才建立的通知中心。

	![][210]

6.  選取頂端的 **[設定]** 索引標籤，然後按一下 Apple 通知設定的 **[上傳]**。接著選取您稍早匯出的 **.p12** 憑證，以及此憑證的密碼。請確定是要選取使用 **[生產]** (如果您想要針對從市集中購買您應用程式的使用者傳送推播通知)，還是要選取 **[沙箱]** (開發期間) 推播服務。

	![][211]

7.  按一下頂端的 **[儀表板]** 索引標籤，然後按一下 **[連接資訊]**。記下這兩個連接字串。

	![][212]

現在已將您的通知中心設定成使用 APNS，而且您已擁有可用來註冊應用程式和傳送通知的連接字串。

連接應用程式將您的應用程式連接到通知中心
----------------------------------------

1.  在 XCode 中建立新的 iOS 專案，並選取 **[Single View Application]** 範本。

	![][31]

2.  在 **[目標]** 下按一下您的專案名稱，然後展開 **[Code Signing Identity]**，接著在 **[偵錯]** 下選取程式碼簽署識別設定檔。另外，如果使用較新版本的 XCode，請將 **[層級]** 從 **[基本]** 切換成 **[全部]**，並將 **[Provisioning Profile]** 行項目設定為佈建設定檔。

	![][32]

3.  下載 Azure Mobile SDK for iOS。開啟 .zip 檔案，並將 WindowsAzureMessaging.framework 資料夾拖曳到 XCode 專案中的 Framework 資料夾。選取 **[Copy items in destination group's folder]**，然後按一下 **[確定]**。

	![][33]

4.  在 AppDelegate.h 檔案中新增下列 import 指示詞：

          #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  在 AppDelegate.m 檔案的 `didFinishLaunchingWithOptions` 方法中新增下列程式碼：

	[[UIApplication sharedApplication] registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  在相同檔案中新增下列方法：

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
        @"<connection string>" notificationHubPath:@"mynh"];
                
        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
        if (error != nil) {
        NSLog(@"Error registering for notifications:%@", error);
                 }
            }];
         }

7.  *(選用)* 同樣地，如果應用程式正在作用中時收到通知，您可以在相同檔案中新增下列方法以顯示 **UIAlert**：

         - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
        [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
        @"OK" otherButtonTitles:nil, nil];
        [alert show];
         }

8.  在裝置上執行應用程式。

傳送通知從後端傳送通知
----------------------

您可以使用我們的 [REST 介面](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx)，從任何後端使用通知中心傳送通知。在本教學課程中，我們將透過 .NET 主控台應用程式和使用節點指令碼的行動服務來傳送通知。

使用 .NET 應用程式傳送通知：

1.  建立新的 Visual C\# 主控台應用程式：

    ![](./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png)

2.  使用 [WindowsAzure.ServiceBus NuGet 封裝](http://nuget.org/packages/WindowsAzure.ServiceBus/)來新增 Azure 服務匯流排 SDK 的參考。在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入：

         Install-Package WindowsAzure.ServiceBus

    並按 Enter 鍵。

3.  開啟 Program.cs 檔案，並新增下列 using 陳述式：

         using Microsoft.ServiceBus.Notifications;

4.  在 `Program` 類別中，新增下列方法。請確保使用出現在入口網站 **[Notification Hubs]** 索引標籤上的通知中心名稱，來取代 "hub name" 預留位置 (例如，上一個範例中的 **mynotificationhub2**)：

         private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{ private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;aps private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;:{ private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;alert private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;: private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;Hello from .NET! private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;}}";
        await hub.SendAppleNativeNotificationAsync(alert);
         }

5.  接著在您的 `Main` 方法中新增下列程式碼行：

          SendNotificationAsync();
        Console.ReadLine();

6.  按 F5 鍵以執行應用程式。您的裝置上應會收到警示。如果您使用的是 Wi-fi，請確定您的連線已正常運作。

您可以在 Apple《[本機與推播通知程式設計指南](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1)》(英文) 中找到所有可能的內容。

若要使用行動服務傳送通知，請依照[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-ios)中的步驟進行，然後：

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並選取您的行動服務。

2.  選取頂端的 **[排程器]** 索引標籤。

	![][215]

3.  建立新的排定工作、插入名稱，並選取 **[隨選]**。

	![][216]

4.  在工作建立之後，按一下此工作名稱。然後按一下頂列中的 **[指令碼]** 索引標籤。

5.  將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。按一下 **[儲存]**。

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);

6.  按一下底列上的 **[執行一次]**。您的裝置上應會收到警示。

後續步驟
--------

在此簡單範例中，您會將通知廣播到您的所有 iOS 裝置。為了鎖定特定使用者，請參閱[使用通知中心來推播通知給使用者](/en-us/manage/services/notification-hubs/notify-users-aspnet)教學課程，同時如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞](/en-us/manage/services/notification-hubs/breaking-news-dotnet)。若要深入了解如何使用通知中心，請參閱 [Azure 通知中心概觀](http://msdn.microsoft.com/en-us/library/jj927170.aspx)和 [iOS 的通知中心作法](http://msdn.microsoft.com/en-us/library/jj927168.aspx)。

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


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
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/en-us/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
