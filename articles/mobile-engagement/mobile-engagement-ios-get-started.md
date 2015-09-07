<properties
	pageTitle="開始使用適用於 iOS (Objective C) 的 Azure Mobile Engagement"
	description="了解如何使用 iOS app 的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

# 開始使用適用於 iOS 應用程式 (Objective C) 的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

本主題說明如何使用 Azure Mobile Engagement 來了解您的應用程式使用量，並傳送推播通知給 iOS 應用程式的區隔使用者。在本教學課程中，您將使用 Apple 推播通知服務 (APNS)，建立可收集基本資料，並接收推播通知的空白 iOS 應用程式。完成本教學課程之後，您便能夠依據使用者的裝置特性，向所有裝置或目標特定使用者廣播推播通知。

本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。請務必搭配下一個教學課程來了解如何使用 Mobile Engagement 處理特定使用者與裝置群組。

本教學課程需要下列各項：

+ Xcode，可以從您的 MAC App Store 安裝
+ [Mobile Engagement iOS SDK]
+ 推播通知憑證 (.p12)，您可以在 Apple Dev Center 取得

完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件。

> [AZURE.IMPORTANT]完成本教學課程是所有其他 iOS 應用程式 Mobile Engagement 教學課程的先決條件，若要完成此課程，您必須擁有有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>為您的應用程式設定 Mobile Engagement

1. 登入 Azure 入口網站，並按一下畫面底部的 [+新增]。

2. 依序按一下 [應用程式服務]、[Mobile Engagement]，然後按一下 [建立]。

	![][7]

3. 在顯示的快顯視窗中，輸入以下資訊：

   ![][8]

   - **應用程式名稱**：輸入您應用程式的名稱。可自由使用任何字元。
   - **平台**：為應用程式選取目標平台 (**iOS**。如果您的應用程式是針對多種平台，請針對每種平台重複參與此教學課程)。
   - **應用程式資源名稱**：這是此應用程式可透過 API 與 URL 存取時的名稱。您只可以使用 傳統 URL 自字元。自動產生的名稱應該能提供您強健的基礎。建議您附加平台名稱，以避免發生名稱衝突，因為名稱必須是唯一的。
   - **位置**：選取將裝載此應用程式 (更重要的是其「集合」) 的資料中心 。
   - **集合**：如果您已經建立了應用程式，請選取之前建立的**集合**，若沒有則選取 [新集合]。
   - **集合名稱**：這代表您的應用程式群組。它也會確保您所有的應用程式都在一個群組中，以允許計量的彙總計算。如果適用於此欄位，建議使用您的公司名稱或部門。

4. 選取剛才在 [應用程式] 索引標籤建立的應用程式。

5. 按一下 [連線資訊] 來顯示要置入您行動應用程式中 SDK 整合的連線設定。

	![][10]

6. 複製 [連接字串]：在您的應用程式程式碼中，以及從您的 Phone app 與 Mobile Engagement 連接時，將需要它來識別這個 app。

	![][11]

##<a id="connecting-app"></a>將您的應用程式連線至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。在 Mobile Engagement iOS SDK 文件中可以找到完整的整合文件。

我們將會使用 XCode 建立基本 app 來示範整合。

###建立新的 iOS 專案

如果您已經有 app，並熟悉 iOS 開發過程，可以略過此步驟

1. 啟動 Xcode，並在快顯視窗中，選取 [Create a new Xcode project]

	![][12]

2. 選取 [Single View Application]，然後按一下 [Next]。

	![][14]

3. 輸入 [Product Name]、[Organization Name] 和 [Organization Identifier]。請確定您已在 [Language] 方塊中選取 [Objective-C]。

	![][13]

Xcode 會建立示範 app，我們將和它整合 Mobile Engagement。

###將您的應用程式連線至 Mobile Engagement 後端

1. 下載 [Mobile Engagement iOS SDK]。
2. 將 .tar.gz 檔案解壓所到您電腦中的某個資料夾。
3. 以滑鼠右鍵按一下專案，然後選取 [Add files to]。

	![][17]

4. 瀏覽至您解壓縮 SDK 的資料夾，選取 `EngagementSDK` 資料夾，然後按 [OK]。

	![][18]

5. 開啟 [Build Phases] 索引標籤，在 [Link Binary With Libraries] 功能表新增架構，如下所示：

	![][19]

6. 回到 Azure 入口網站中您 app 的 [連線資訊] 頁面，複製連接字串。

	![][11]

7. 開啟應用程式委派實作檔案，並加入下列這行程式碼。

		#import "EngagementAgent.h"

8. 現在，在 `didFinishLaunchingWithOptions` 委派中貼上連接字串。

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

- 開啟 **ViewController.h** 檔案，匯入 **EngagementViewController.h**，並且將 **ViewController** 介面的超級類別取代為 **EngagementViewController**，如下所示：

![][22]

###確認您的應用程式已與即時監視連線

本節說明如何使用 Mobile Engagement 的即時監視功能來檢查您的應用程式是否連線至 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站

	從 Azure 入口網站，確定您已在我們用於此專案的 app，然後按一下底部的 [接洽] 按鈕：

	![][26]

2. 您將登陸在 app Engagement 入口網站的 [設定] 頁面中。從該處按一下 [監視器] 索引標籤：

	![][30]

3. 監視器可以即時顯示將會啟動您 app 的任何裝置。

	![][31]

4. 回到 Xcode，在模擬器或連接的裝置啟動您的 app。

5. 如果它能運作，您將在監視器中看到一個工作階段！

**恭喜！** 您成功完成本教學課程的第一個步驟，有了連接到 Mobile Engagement 後端的 app，而且它已經在傳送資料。

6. 按一下模擬器的 [Home] 按鈕會讓監視器中的工作階段數目回到 0，如圖上所示。

	![][33]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動和觸達 (REACH)。此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。以下各節將設定您的用程式來接收它們。

### 啟用應用程式接收無聲推播通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]


### 將觸達程式庫加入至專案

1. 以滑鼠右鍵按一下您的專案。
2. 選取 [新增檔案至]。
3. 瀏覽至您解壓縮 SDK 所在的資料夾。
4. 選取 `EngagementReach` 資料夾
5. 按一下 [加入]。

### 修改您的應用程式委派

1. 在您的實作檔案頂端，匯入 Engagement 觸達模組。

		#import "AEReachModule.h"

2. 在 **application:didFinishLaunchingWithOptions** 方法內建立觸達模組，並將它傳遞到您現有的 Engagement 初始化行：

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###讓您的應用程式能接收 APNS 推播通知

1. 將下列一行加入 **application:didFinishLaunchingWithOptions** 方法：

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 新增 **application:didRegisterForRemoteNotificationsWithDeviceToken** 方法如下：

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. 新增 **didReceiveRemoteNotification:fetchCompletionHandler** 方法如下：

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###授權 Mobile Engagement 存取推播憑證

若要讓 Mobile Engagement 以您的名義傳送推播通知，您需要授與它權限存取您的憑證。這是藉由設定和輸入您的憑證到 Mobile Engagement 入口網站中而完成。請確定您取得 .p12 憑證，如 Apple 的文件中所述。

1. 瀏覽至您的 Mobile Engagement 入口網站。確認您正位在用於此專案的 app 內，然後按一下底部的 [接洽] 按鈕：

	![][26]

2. 您將登陸在 Engagement 入口網站的 [設定] 頁面中。從該處按一下 [原生推送] 區段以上傳 p12 憑證：

	![][27]

3. 選取您的 p12 並將它上傳，然後輸入您的密碼：

	![][28]

4. 現在，新增佈建設定檔，並為目標裝置建置 app。

您全都準備好了。現在我們要驗證您已正確完成這項基本整合。

##<a id="send"></a>傳送通知至應用程式

現在，我們將建立簡單的推播通知活動，它會傳送推播至我們的 app：

1. 瀏覽至 Mobile Engagement 入口網站中的 [觸達] 索引標籤。

2. 按一下 [新增宣告] 來建立您的推播通知活動。

	![][35]

3. 設定活動的第一個欄位：

	![][36]

	- 	以任何您想要的名稱來命名活動。
	- 	將 [傳遞時間] 選取為 [僅從應用程式外]：這是主要為一些文字的簡單 Apple 推播通知類型。
	- 	在通知文字中，先輸入將成為推播第一行的標題。
	- 	然後輸入將成為第二行的訊息。


4. 向下捲動，在 [內容] 區段中選取 [僅限通知]。

	![][37]

5. 您已完成能做的最基本活動設定。現在再次向下捲動，然後建立您的活動並加以儲存！![][38]

6. 最後一步：啟動您的活動。![][39]

7. 您應該在裝置中看到推播通知！

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->