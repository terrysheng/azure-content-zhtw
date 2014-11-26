<properties linkid="notification-hubs-ios-send-breaking-news" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# 使用通知中心傳送即時新聞

<div class="dev-center-tutorial-selector sublanding">       
<a href="/zh-TW/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/zh-TW/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/zh-TW/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
<a href="/zh-TW/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

本主題將說明如何使用 Azure 通知中心，將即時新聞通知廣播至 iOS 應用程式。完成時，您便能夠註冊您所感興趣的即時新聞類別，並僅接收這些類別的推播通知。此情況是許多應用程式的共同模式，這些應用程式必須將通知傳送給先前宣告對通知有興趣的使用者群組，例如，RSS 閱讀程式、供樂迷使用的應用程式等等。

在通知中心內建立註冊時，您可以透過包含一或多個 *tags* 來啟用廣播案例。當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。由於標籤只是簡單的字串而已，您無需預先佈建標籤。如需標籤的詳細資訊，請參閱[通知中心指引][通知中心指引]。

本教學課程會逐步引導您完成啟用此案例的基本步驟：

1.  [在應用程式中新增類別選項][在應用程式中新增類別選項]
2.  [註冊通知][註冊通知]
3.  [從後端傳送通知][從後端傳送通知]
4.  [執行應用程式並產生通知][執行應用程式並產生通知]

本主題會以您在[開始使用通知中心][開始使用通知中心]中所建立的應用程式為基礎。開始本教學課程之前，您必須已完成[開始使用通知中心][開始使用通知中心]。

## <a name="adding-categories"></a>在應用程式中新增類別選項

第一個步驟是在您現有的腳本上新增 UI 元素，以便使用者選取要註冊的類別。使用者所選取的類別會儲存在裝置上。啟動應用程式時，您的通知中心內會建立以所選取類別作為標籤的裝置註冊。

1.  在您的 MainStoryboard\_iPhone.storyboard 中，從物件程式庫新增下列元件：

    -   具有「即時新聞」文字的標籤，
    -   具有「世界」、「政治」、「商業」、「技術」、「科學」、「體育」等類別文字的標籤，
    -   六個參數，每個類別各一個，
    -   一個標示為「訂閱」的按鈕

    您的腳本應如下所示：

    ![][0]

2.  在輔助編輯器中，建立所有參數的出口，並將其命名為 "WorldSwitch"、"PoliticsSwitch"、"BusinessSwitch"、"TechnologySwitch"、"ScienceSwitch"、"SportsSwitch"

    ![][1]

3.  為名為「訂閱」的按鈕建立「動作」。您的 BreakingNewsViewController.h 應包含下列內容：

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4.  建立名為 `Notifications` 的新類別。在 Notifications.h 的介面區段中複製下列程式碼：

        @property NSData* deviceToken;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
        - (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

5.  在 Notifications.m 中新增下列 import 指示詞：

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6.  在 Notifications.m 檔案的實作區段中複製下列程式碼：

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }

        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

            [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
        }

    此類別會使用本機儲存體來儲存此裝置必須接收的新聞類別。此外，它也包含註冊這些類別的方法。

7.  在上述程式碼中，使用您的通知中樞名稱及先前取得的 *DefaultListenSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with listen access>` 預留位置。

    <div class="dev-callout"><strong>注意</strong> 
<p>因為隨用戶端應用程式散佈的憑證通常不安全，您應只將接聽存取權的金鑰隨用戶端應用程式散佈。您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。</p>
</div>

8.  在 BreakingNewsAppDelegate.h 檔案中，新增下列屬性：

        @property (nonatomic) Notifications* notifications;

    這會在 AppDelegate 中建立 Notification 類別的單一執行個體。

9.  在 BreakingNewsAppDelegate.m 的 **didFinishLaunchingWithOptions** 方法中，在 **registerForRemoteNotificationTypes** 前面新增下列程式碼：

        self.notifications = [[Notifications alloc] init];

    這會初始化通知 singleton。

10. 在 BreakingNewsAppDelegate.m 的 **didRegisterForRemoteNotificationsWithDeviceToken** 方法中，移除 **registerNativeWithDeviceToken** 的呼叫，然後新增下列程式碼：

        self.notifications.deviceToken = deviceToken;

    請注意，**didRegisterForRemoteNotificationsWithDeviceToken** 方法中此時不應有其他程式碼。

11. 在 BreakingNewsAppDelegate.m 中新增下列方法：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

    此方法會顯示簡易 **UIAlert**，以處理應用程式執行時接收到的通知。

12. 在 BreakingNewsViewController.m 中，將下列程式碼複製到 XCode 產生的 **subscribe** 方法中：

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    此方法會建立類別的 **NSMutableArray**，並使用 **Notifications** 類別在本機儲存體中儲存清單，並在您的通知中心註冊對應標籤。變更類別時，系統會使用新類別重新建立註冊。

您的應用程式現在可以在裝置上的本機儲存體中儲存一組類別，並在使用者每次變更類別選項時在通知中心註冊。

## <a name="register"></a>註冊通知

這些步驟會在啟動時，使用已儲存在本機儲存體中的類別在通知中心註冊。

<div class="dev-callout"><strong>注意</strong> 
<p>由於 Apple 推播通知服務 (APNS) 所指派的裝置權杖可能隨時會變更，因此您應經常註冊通知以避免通知失敗。此範例會在應用程式每次啟動時註冊通知。若是經常執行 (一天多次) 的應用程式，如果距離上次註冊的時間不到一天，則您可能可以略過註冊以保留頻寬。</p>
</div>

1.  在 Notifications.h 的介面區段中新增下列方法：

        - (NSSet*)retrieveCategories;

    此程式碼會擷取 Notifications 類別中的類別。

2.  在 Notifications.m 檔案中新增對應的實作：

        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }

3.  在 **didRegisterForRemoteNotificationsWithDeviceToken** 方法中新增下列程式碼：

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];
        [notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

    這會確保應用程式每次啟動時都會從本機儲存體擷取類別，並要求這些類別的註冊。

4.  在 BreakingNewsViewController.h 的 **viewDidLoad** 方法中，新增下列程式碼：

        Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

    這會根據先前儲存的類別狀態，更新啟動時的 UI。

現在已完成此應用程式，且可在裝置本機儲存體中儲存一組類別，以供每次使用者變更類別選項在通知中心註冊時使用。接著，您會定義可將類別通知傳送至此應用程式的後端。

## <a name="send"></a><span class="short-header">傳送通知</span>從後端傳送通知

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>執行應用程式並產生通知

1.  按 [執行] 按鈕，以建置專案並啟動應用程式。

    ![][2]

    請注意，應用程式 UI 提供一組切換，可讓您選擇要訂閱的類別。

2.  啟用一或多個類別切換，然後按一下 [訂閱]。

    當您選擇 [訂閱] 時，應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。

3.  若要從後端傳送新通知，您可以使用下列其中一種方式：

    -   **主控台應用程式：**啟動主控台應用程式。

    -   **Java/PHP：**執行您的應用程式/指令碼。

4.  選取的類別通知會以快顯通知方式出現。

## <a name="next-steps"> </a>後續步驟

在本教學課程中，我們了解到如何按類別廣播即時新聞。請考慮完成下列其中一個強調其他進階通知中心案例的教學課程：

-   **[使用通知中心廣播已當地語系化的即時新聞][使用通知中心廣播已當地語系化的即時新聞]**

    了解如何擴充即時新聞應用程式，以啟用傳送已當地語系化的通知。

-   **[使用通知中心來通知使用者][使用通知中心來通知使用者]**

    了解如何推播通知給特定的經驗證使用者。在僅傳送通知給特定使用者的情況下，這是很好的解決方案。

 
 


  [通知中心指引]: http://msdn.microsoft.com/zh-TW/library/jj927170.aspx
  [在應用程式中新增類別選項]: #adding-categories
  [註冊通知]: #register
  [從後端傳送通知]: #send
  [執行應用程式並產生通知]: #test-app
  [開始使用通知中心]: /zh-TW/manage/services/notification-hubs/get-started-notification-hubs-ios/
  [0]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
  [1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png
  [2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
  [使用通知中心廣播已當地語系化的即時新聞]: /zh-TW/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [使用通知中心來通知使用者]: /zh-TW/manage/services/notification-hubs/notify-users/
