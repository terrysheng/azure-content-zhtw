<properties 
	pageTitle="開始使用 Azure Mobile Engagement for iOS" 
	description="了解如何在 iOS 上使用 Azure Mobile Engagement 與分析和推送通知" 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# 開始使用 Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows 市集</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用情形，並將推送通知傳送至 iOS 應用程式的區隔使用者。 
在本教學課程中，您會建立空白 iOS 應用程式，收集基本資料，並使用 Apple Push Notification System (APNS) 接收推送通知。完成時，您將可以廣播推送通知給所有裝置或根據裝置屬性將目標鎖定在特定使用者。

本教學課程會示範使用 Mobile Engagement 的簡單廣播案例。請務必接著進行下一個教學課程，以查看如何使用 Mobile Engagement 來處理特定使用者和裝置群組。 

本教學課程需要下列各項：

+ Xcode，您可以從您的 MAC 應用程式市集安裝
+ [Mobile Engagement iOS SDK]
+ 推送通知憑證 (.p12)，您可以在 Apple Dev Center 取得

完成本教學課程是 iOS 應用程式所有其他 Mobile Engagement 教學課程的先決條件。 

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>針對您的應用程式設定 Mobile Engagement

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [**+新增**]。

2. 依序按一下 [**應用程式服務**]、[**Mobile Engagement**]、[**建立**]。

   	![][7]

3. 在顯示的快顯視窗中，您有幾個欄位需要填寫：
 
   	![][8]

	1.[ *應用程式名稱*]：您可以輸入應用程式的名稱。您可以使用任何字元
	2.[ *平台*]：選取該應用程式的目標平台 (如果您的應用程式以多個平台為目標，請針對每個平台重複本教學課程)
	3.[ *應用程式資源名稱*]：這是可以藉此透過 API 和 URL 存取此應用程式的名稱。我們建議您只使用傳統的 URL 字元：自動產生的名稱應該提供您很好的基礎。我們也建議您附加平台名稱要避免任何名稱衝突，因為此名稱必須是唯一的
	4. *位置*：選取將裝載此應用程式 (以及更重要的是其集合 - 請參閱底下) 的資料中心
	5. *集合*：如果您已經建立應用程式，請選取先前建立的集合，否則請選取 [新集合]


	當您完成時，按一下核取按鈕以完成應用程式的建立

4. 現在按一下/選取您剛才在 [應用程式] 索引標籤中建立的應用程式：
 
   	![][9]

5. 然後按一下 [連線資訊] 以顯示要放入 SDK 整合的連線設定：
 
   	![][10]

6. 最後，請記下連接字串，這是您從應用程式程式碼識別此應用程式時所需要的東西。

   	![][11]

	>[AZURE.TIP] 為了方便起見，您可以使用連接字串右邊的 [複製] 圖示，將它複製到剪貼簿。

##<a id="connecting-app"></a>將應用程式連接到 Mobile Engagement 後端

此教學課程提供「基本整合」，這是收集資料並傳送推送通知所需的最小集合。完整的整合文件位於 [Mobile Engagement iOS SDK 文件]

我們將使用 Android Studio 建立基本應用程式，來示範整合

###建立新的 iOS 專案

如果您已經有應用程式，並熟悉 iOS 開發過程，可以略過此步驟

1. 啟動 Xcode，並在快顯視窗中，選取 [建立新的 Xcode 專案]

   	![][12]

2. 填入應用程式名稱、公司名稱和識別碼。請寫下這些資訊，因為您稍後將需要它們，然後按 [下一步]

   	![][13]

3. 現在選取 [單一檢視應用程式]，然後按 [下一步]

   	![][14]


Xcode 會建立示範應用程式，我們將和它整合 Mobile Engagement

###在您的專案中包含 SDK 程式庫

下載並整合 SDK 程式庫

1. 下載 [Mobile Engagement iOS SDK]
2. 將 .tar.gz 檔案解壓縮到電腦中的資料夾
3. 以滑鼠右鍵按一下專案，然後選取 [新增檔案至]

	![][17]

4. 瀏覽至您解壓縮 SDK 資料夾、選取 [ `EngagementSDK`] 資料夾，然後按 [確定]

	![][18]

5. 開啟 [ `建置階段`] 索引標籤，在 [ `連結二進位檔與程式庫`] 功能表新增架構，如下所示：

	![][19]


###使用連接字串將您的應用程式連接到 Mobile Engagement 後端

1. 複製應用程式委派實作檔案頂端的下列這行程式碼

		#import "EngagementAgent.h"

2. 回到 Azure 入口網站應用程式的 [ *連接資訊*] 頁面，然後複製連接字串

	![][11]

3. 將它貼在  `didFinishLaunchingWithOptions` 委派		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##傳送畫面到 Mobile Engagement

若要開始傳送資料，並確保使用者在作用中，您必須傳送至少個畫面 (活動) 到 Mobile Engagement 後端。我們將以我們 SDK 提供之 `EngagementViewController` 多載  `UIViewController` 類別來達到這點。
要這麼做，請開啟 `ViewController.h` 檔案，匯入 `EngagementViewController.h`，然後將 `ViewController` 介面的超級類別取代為 `EngagementViewController`，如下所示：

![][22]

##<a id="monitor"></a>如何用即時監視功能檢查您的應用程式已連線

本節說明如何使用 Mobile Engagement 的即時監視功能，確認您的應用程式連接到 Mobile Engagement 後端。

1. 瀏覽至您的 Mobile Engagement 入口網站

	從 Azure 入口網站，確定您已在我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕：

	![][26]

2. 您將登陸在應用程式 Engagement 入口網站的 [設定] 頁面中。從該處按一下 [監視] 索引標籤：

	![][30]

3. 監視器可以即時顯示將會啟動您應用程式的任何裝置：

	![][31]

4. 回到 Xcode，在模擬器或連接的裝置啟動您的應用程式

5. 如果有用，您現在應該會看到在監視器中有一個工作階段！ 

**恭喜您！**您成功完成本教學課程的第一個步驟，有了連接到 Mobile Engagement 後端的應用程式，並且它已經在傳送資料

6. 按一下模擬器的 [首頁] 按鈕會讓監視器中的工作階段數目回到 0，如上所示

	![][33]



##<a id="integrate-push"></a>啟用推送通知和應用程式內傳訊

Mobile Engagement 可讓您使用推送通知和應用程式內，依照活動的內容與使用者互動和觸達。此模組在 Mobile Engagement 入口網站中稱為 REACH。
下列各節將會設定您的應用程式，以便收到通知。

### 將 Reach 程式庫加入至專案

1. 以滑鼠右鍵按一下您的專案
2. 選取 [`新增檔案至`]
3. 瀏覽至您解壓縮 SDK 所在的資料夾
4. 選取 [ `EngagementReach`] 資料夾
5. 按一下 [新增]

### 修改您的應用程式委派

1. 在實作檔案頂端匯入 Engagement Reach 模組

		#import "AEReachModule.h"
	
2. 在 `application:didFinishLaunchingWithOptions` 內建立 reach 模組並將它傳遞到您現有的 Engagement 初始化行：

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###讓您的應用程式能接收 APNS 推送通知

1. 將下行新增至 `application:didFinishLaunchingWithOptions` 方法：

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 新增 `application:didRegisterForRemoteNotificationsWithDeviceToken` 方法，如下所示：
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. 新增 `didReceiveRemoteNotification` 方法，如下所示：
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###將推送憑證的存取權授與給 Mobile Engagement

若要讓 Mobile Engagement 以您的名義傳送推送通知，您需要授與它對您憑證的存取權。這是藉由設定和輸入您的憑證到 Mobile Engagement 入口網站中而完成。請確定您取得 .p12 憑證，如 Apple 的文件中所述。

1. 瀏覽至您的 Mobile Engagement 入口網站

	從 Azure 入口網站，確定您已在我們用於此專案的應用程式，然後按一下底部的 [Engage] 按鈕：

	![][26]

2. 您將登陸在 Engagement 入口網站的 [設定] 頁面中。從該處按一下 [原生推送] 區段以上傳 p12 憑證：

	![][27]

3. 選取您的 p12、將它上傳並輸入您的密碼：

	![][28]

4. 現在，新增佈建設定檔，並建置目標裝置的應用程式

您全都準備好了，現在我們要驗證您已正確完成這項基本整合

##<a id="send"></a>如何傳送通知至應用程式

現在，我們將建立簡單的推送通知活動，它會傳送推送至我們的應用程式：

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [Reach] 索引標籤

2. 按一下 [新公告] 建立推送活動
	
	![][35]

3. 設定活動的第一個欄位：

	![][36]

	1. 以任何您想要的名稱來命名您的活動
	2. 將 [傳遞時間] 選取為 [僅從應用程式外]：這是主要為一些文字的簡單 Apple 推送通知類型
	3. 在通知文字中，先輸入將成為推送第一行的標題
	4. 然後輸入將成為第二行的訊息

4. 向下捲動，在 [內容] 區段中選取 [僅限通知]

	![][37]

5. 您已完成設定最基本的活動，現在請再次向下捲動並建立活動，然後儲存它！
![][38]

6. 最後一步，啟動您的活動
![][39]

7. 您應該在裝置中看到推送通知！

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK 文件]: http://go.microsoft.com/?linkid=9874682
[Azure 管理入口網站]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
