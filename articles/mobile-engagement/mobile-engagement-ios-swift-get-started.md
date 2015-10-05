<properties
	pageTitle="開始使用適用於 iOS (Swift) 的 Azure Mobile Engagement"
	description="了解如何使用 iOS 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# 開始使用適用於 iOS 應用程式 (Swift) 的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 來了解您的應用程式使用量，並傳送推播通知給 iOS 應用程式的區隔使用者。在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 iOS 應用程式。

本教學課程需要下列各項：

+ Xcode 6 或 XCode 7，可以從您的 MAC App Store 安裝
+ [Mobile Engagement iOS SDK]
+ 推播通知憑證 (.p12)，您可以在 Apple Dev Center 取得

完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件。

> [AZURE.IMPORTANT]完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件，若要完成此課程，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

##<a id="setup-azme"></a>為您的 iOS App 設定 Mobile Engagement

[AZURE.INCLUDE [在入口網站中建立 Mobile Engagement App](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。完整的整合文件位於 [Mobile Engagement iOS SDK 整合](../mobile-engagement-ios-sdk-overview/)。

我們將會使用 XCode 建立基本應用程式來示範整合：

###建立新的 iOS 專案

[AZURE.INCLUDE [建立新的 iOS 專案](../../includes/mobile-engagement-create-new-ios-app.md)]

###將您的應用程式連線至 Mobile Engagement 後端

1. 下載 [Mobile Engagement iOS SDK]
2. 將 .tar.gz 檔案解壓縮到電腦中的資料夾
3. 以滑鼠右鍵按一下專案，然後選取 [Add files to ...]

	![][1]

4. 瀏覽至您解壓縮 SDK 的資料夾，選取 `EngagementSDK` 資料夾，然後按 [OK]。

	![][2]

5. 開啟 `Build Phases` 索引標籤，並在 `Link Binary With Libraries` 功能表中新增框架，如下所示：

	![][3]

6. 針對 **XCode 7** - 新增 `libxml2.tbd` 而不是 `libxml2.dylib`。

7. 選擇 [File] > [New] > [File] > [iOS] > [Source] > [Header File] 來建立「橋接」標頭，以使用 SDK 的 Objective C API。

	![][4]

8. 編輯橋接標頭檔案來將 AzME Objective-C 程式碼公開至 Swift 程式碼，請新增以下匯入：

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. 在 [Build Settings]，請確定在 [Swift Compiler - Code Generation] 下的 [Objective-C Bridging Header] 組件設定有指向此標頭的路徑。路徑的範例：**$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (依路徑而定)**

10. 回到 Azure 入口網站中您應用程式的 [連線資訊] 頁面，並複製 [連線字串]。

	![][5]

11. 現在 `didFinishLaunchingWithOptions` 代理人中貼上連接字串。

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1. 開啟 **ViewController.h** 檔案，並匯入 **EngagementViewController.h**：

    `# import "EngagementViewController.h"`

2. 現在以 **EngagementViewController** 取代 **ViewController** 介面的超級類別：

	`@interface ViewController : EngagementViewController`

##<a id="monitor"></a>將應用程式與即時監視連接

[AZURE.INCLUDE [將應用程式與即時監視連接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

### 啟用應用程式接收無聲推播通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### 將觸達程式庫加入至專案

1. 以滑鼠右鍵按一下您的專案
2. 選取 `Add file to ...`
3. 瀏覽至您解壓縮 SDK 所在的資料夾
4. 選取 `EngagementReach` 資料夾
5. 按一下 [新增]
6. 編輯橋接標頭檔案來將 AzME Objective-C 程式碼公開至觸達標頭，請新增以下匯入：

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### 修改您的應用程式代理人

1. 在 `didFinishLaunchingWithOptions` 內建立觸達模組並將它傳遞到您現有的 Engagement 初始化行：

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###讓您的應用程式能接收 APNS 推播通知
1. 將下行新增至 `didFinishLaunchingWithOptions` 方法：

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. 新增 `didRegisterForRemoteNotificationsWithDeviceToken` 方法，如下所示：

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. 新增 `didReceiveRemoteNotification:fetchCompletionHandler:` 方法，如下所示：

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

<!---HONumber=Sept15_HO4-->