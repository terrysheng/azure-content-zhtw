<properties
	pageTitle="開始使用適用於 Xamarin.iOS 的 Azure Mobile Engagement"
	description="了解如何使用 Xamarin.iOS 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# 開始使用適用於 Xamarin.iOS 應用程式的 Azure Mobile Engagement

[AZURE.INCLUDE [Hero 教學課程切換器](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure Mobile Engagement 來了解您的應用程式使用量，並傳送推播通知給 Xamarin.iOS 應用程式的區隔使用者。在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 Xamarin.iOS 應用程式。

本教學課程需要下列各項：

+ [Xamarin Studio](http://xamarin.com/studio) 
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started)。

##<a id="setup-azme"></a>為您的 iOS 應用程式設定 Mobile Engagement

[AZURE.INCLUDE [在入口網站中建立 Mobile Engagement 應用程式](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。

我們將會使用 Xamarin 建立基本應用程式來示範整合：

###建立新的 Xamarin.iOS 專案

1. 啟動 Xamarin Studio。移至 [檔案] -> [新增] -> [方案] 

    ![][1]

2. 選取 [單一檢視應用程式]，確定選取的語言是 **C#**，然後按 [下一步]。

    ![][2]

3. 填入 [應用程式名稱] 和 [組織識別碼]，然後按 [下一步]。

    ![][3]

	> [AZURE.IMPORTANT] 請確定您最後用來部署您的 iOS 應用程式的發行設定檔使用的應用程式識別碼完全符合這裡顯示的 [套件組合識別碼]。

4. 如有必要，請更新 [專案名稱]、[方案名稱] 和 [位置]，然後按一下 [建立]。

    ![][4]
 
Xamarin Studio 會建立示範應用程式，我們將在其中整合 Mobile Engagement

###將您的應用程式連線至 Mobile Engagement 後端

1. 以滑鼠右鍵按一下 [方案] 視窗中的 [封裝] 資料夾，然後選取 [新增封裝...]

    ![][5]

2. 搜尋 **Microsoft Azure Mobile Engagement Xamarin SDK** 並將它新增至您的方案。

    ![][6]
   
3. 開啟 **AppDelegate.cs** 並新增下列 using 陳述式：

		using Microsoft.Azure.Engagement.Xamarin;

4. 在 **FinishedLaunching** 方法中，加入下列程式碼來初始化與 Mobile Engagement 後端的連接。請務必新增您的 **ConnectionString**。此程式碼也會使用由 Mobile Engagement SDK 加入的虛擬 **NotificationIcon**，建議您加以取代。

		EngagementConfiguration config = new EngagementConfiguration {
		                ConnectionString = "YourConnectionStringFromAzurePortal",
		                NotificationIcon = UIImage.FromBundle("close")
		            };
	    EngagementAgent.Init (config);

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個螢幕到 Mobile Engagement 後端。

1. 開啟 **ViewController.cs** 並新增以下 using 陳述式：

		using Microsoft.Azure.Engagement.Xamarin;

2. 取代 `ViewController` 從 `UIViewController` 繼承到 `EngagementViewController` 的類別。

##<a id="monitor"></a>將應用程式與即時監視連接

[AZURE.INCLUDE [將 App 與即時監視連接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推播通知與 App 內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

### 修改您的應用程式代理人

1. 開啟 **AppDelegate.cs** 並新增下列 using 陳述式：

		using System; 

2. 現在在 `FinishedLaunching` 方法中，加入下列程式碼以註冊 `EngagementAgent.init(...)` 之後的推播訊息

		if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. 最後，更新或新增下列方法︰

		public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. 在方案的 **Info.plist** 檔案中，確認 [套件組合識別碼] 符合 Apple Dev Center 中您的佈建設定檔中的 [應用程式識別碼]。

	![][7]

5. 在同一個 **Info.plist** 檔案中，確定您已核取 [啟用背景模式] 和 [遠端通知]。

 	![][8]

6. 在與此發行設定檔相關聯的裝置上執行應用程式。

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png

<!---HONumber=AcomDC_0330_2016-->