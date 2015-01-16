<properties urlDisplayName="Localized Breaking News" pageTitle="iOS 通知中心已當地語系化的即時新聞教學課程 " metaKeywords="" description="了解如何使用 Azure 服務匯流排通知中心傳送本地化重大新聞通知 (iOS)。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news to iOS devices" authors="yuaxu" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="yuaxu" />
# 使用通知中心將當地語系化的即時新聞傳送至 iOS 裝置

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>


本主題將說明如何使用 Azure 通知中心的**範本**功能，廣播已由語言和裝置當地語系化的即時新聞通知。在本教學課程中，首先您會執行在[使用通知中心傳送即時新聞]中建立的 Windows 市集應用程式。完成之後，您將可註冊您感興趣的類別、指定您要接收哪種語言的通知，並以該語言針對選取的類別接收推播通知。

本教學課程會逐步引導您完成啟用此案例的基本步驟：

1. [範本概念]
2. [應用程式使用者介面]
3. [建置 iOS 應用程式]
4. [從後端傳送通知]


此案例分成兩部分：

- iOS 應用程式允許用戶端裝置指定語言，以及訂閱不同的即時新聞類別；

- 後端使用 Azure 通知中心的**標籤**和**範本**功能廣播通知。



##必要條件 ##

您必須已完成 [使用通知中心傳送即時新聞] 教學課程，並具有可用的程式碼，因為此教學課程是直接根據該程式碼而建置的。

您也需要 Visual Studio 2012。



<h2><a name="concepts"></a>範本概念</h2>

在 [使用通知中心傳送即時新聞] 中，您建置了使用**標籤**來訂閱不同即時新聞類別之通知的應用程式。
但有許多應用程式是以多個市場為目標的，因此需要當地語系化。這表示通知本身的內容必須進行當地語系化，並傳遞至正確的裝置集。
在此主題中，我們將說明如何使用通知中心的**範本**功能，輕鬆地傳遞已當地語系化的即時新聞通知。

注意：傳送當地語系化通知的方法之一，是為每個標籤建立多個版本。例如，若要支援英文、法文和中文，我們將必須為世界新聞建立三個不同的標籤："world_en"、"world_fr" 和 "world_ch"。接著，我們必須將當地語系化版本的世界新聞分別傳送至這三個標籤。在此主題中我們會使用範本，以避免使用過多的標籤和傳送過多訊息。

以較高的層級而言，範本可用來指定特定裝置接收通知的方式。範本可參照您的應用程式後端所傳送的訊息中包含的屬性，藉以指定確切的裝載格式。在此處的範例中，我們將傳送地區設定無從驗證、且包含所有支援語言的訊息：

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

接著，我們將確保裝置會為參照正確屬性的範本進行註冊。例如，想要註冊法文新聞的 iOS 應用程式，將會註冊下列項目：

	{
		aps:{
			alert: "$(News_French)"
		}
	}

範本的功能非常強大，您可以在 [通知中心指引] 一文中了解詳情。如需範本運算式語言的參考，請參閱我們的[作法：服務匯流排通知中心 (iOS 應用程式)]。

<h2><a name="ui"></a>應用程式使用者介面</h2>

現在，我們將修改您在 [使用通知中心傳送即時新聞] 主題中建立的即時新聞應用程式，以使用範本傳送當地語系化的即時新聞。


在您的 MainStoryboard_iPhone.storyboard 中，以我們支援的三種語言新增「分段控制」：英文、法文和中文。

![][13]

接著，請確實在您的 ViewController.h 中新增 IBOutlet，如下所示：

![][14]

<h2><a name="building-client"></a><span class="building app">應用程式 UI</span>建置 iOS 應用程式</h2>

若要調整用戶端應用程式使其能夠接收當地語系化的訊息，您必須以範本註冊取代您的*原生*註冊 (也就是指定範本的註冊)。

1. 在您的 Notification.h 中新增 *retrieveLocale* 方法，然後修改儲存和訂閱方法，如下所示：

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	在您的 Notification.m 中新增地區設定參數，並將其儲存在使用者預設值中，以修改 *storeCategoriesAndSubscribe* 方法：

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	然後修改 *subscribe* 方法，以加入地區設定：

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	請注意，我們現在使用的是 *registerTemplateWithDeviceToken* 方法，而非 *registerNativeWithDeviceToken*。我們在註冊範本時必須提供 json 範本，以及範本的名稱 (因為應用程式可能會註冊不同的範本)。請確實將您的類別註冊為標籤，因為我們要確保能夠收到這些新聞的通知。

	最後，請新增從使用者預設設定中擷取地區設定的方法：

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. 我們已修改 Notifications 類別，現在我們必須確保 ViewController 會使用新的 UISegmentControl。請在 *viewDidLoad* 方法中新增下列程式碼行，以確實顯示目前選取的地區設定：

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	接著請在 *subscribe* 方法中，將您對 *storeCategoriesAndSubscribe* 的呼叫變更為下列項目：

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

4. 最後，您必須在 AppDelegate.m 中更新 *didRegisterForRemoteNotificationsWithDeviceToken* 方法，以便能在應用程式啟動時正確重新整理您的註冊。請使用下列程式碼變更您對通知之 *subscribe* 方法的呼叫：

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

<h2><a name="send"></a>從後端傳送已當地語系化的通知</h2>

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]


## 後續步驟

如需使用範本的詳細資訊，請參閱：

- [使用通知中心來通知使用者：ASP.NET]
- [使用通知中心來通知使用者：行動服務]
- [通知中心指引]

如需範本運算式語言的參考，請參 [iOS 的通知中心作法]。




<!-- Anchors. -->
[範本概念]: #concepts
[應用程式使用者介面]: #ui
[建置 iOS 應用程式]: #building-client
[從後端傳送通知]: #send
[後續步驟]: #next-steps

<!-- Images. -->









[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[作法：服務匯流排通知中心 (iOS 應用程式)]: http://msdn.microsoft.com/zh-tw/library/jj927168.aspx
[使用通知中心傳送即時新聞]: /zh-tw/manage/services/notification-hubs/breaking-news-ios
[行動服務]: /zh-tw/develop/mobile/tutorials/get-started
[使用通知中心來通知使用者：ASP.NET]: /zh-tw/manage/services/notification-hubs/notify-users-aspnet
[使用通知中心來通知使用者：行動服務]: /zh-tw/manage/services/notification-hubs/notify-users
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-ios
[推播通知給應用程式使用者]: /zh-tw/develop/mobile/tutorials/push-notifications-to-users-ios
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript 和 HTML]: /zh-tw/develop/mobile/tutorials/get-started-with-push-js.md

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務的 Windows 開發人員預覽註冊步驟]: ../HowTo/mobile-services-windows-developer-preview-registration.md
[wns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中心指引]: http://msdn.microsoft.com/zh-tw/library/jj927170.aspx
[iOS 的通知中心作法]: http://msdn.microsoft.com/zh-tw/library/jj927168.aspx
