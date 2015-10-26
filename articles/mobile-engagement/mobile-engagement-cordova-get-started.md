<properties
	pageTitle="開始使用 Azure Mobile Engagement for Cordova/Phonegap"
	description="了解如何使用 Cordova/Phonegap 應用程式的 Azure Mobile Engagement 與分析和推播通知。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="article" 
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# 開始使用 Azure Mobile Engagement for Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

此主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給以 Cordova 開發之行動應用程式的特定使用者。

在本教學課程中，我們將會使用 Mac 建立空白的 Cordova 應用程式，然後整合 Mobile Engagement SDK。它會收集基本分析資料，並針對 iOS 使用 Apple Push Notification System (APNS)、針對 Android 使用 Google Cloud Messaging (GCM) 接收推播通知。我們會將它部署到 iOS 或 Android 裝置以進行測試。

> [AZURE.IMPORTANT]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

本教學課程需要下列各項：

+ XCode，您可以從您的 Mac App Store 安裝 (適用於部署到 iOS)
+ [Android SDK 和模擬器](http://developer.android.com/sdk/installing/index.html) (適用於部署到 Android)
+ 推播通知憑證 (.p12)，您可以在 Apple Dev Center for APNS 取得
+ 您可以從您的 Google Developer Console for GCM 取得的 GCM 專案編號
+ [Mobile Engagement Cordova 外掛程式](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]您可以在 [Github](https://github.com/Azure/azure-mobile-engagement-cordova) 上找到原始程式碼和 ReadMe for the Cordova 外掛程式

##<a id="setup-azme"></a>為您的 Cordova App 設定 Mobile Engagement

[AZURE.INCLUDE [在入口網站中建立 Mobile Engagement App](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>將您的應用程式連接至 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知時必要的最低設定。

我們將會使用 Cordova 建立基本應用程式來示範整合：

###建立新的 Cordova 專案

1. 啟動 Mac 電腦上的 *Terminal* 視窗，並輸入下列內容，其將會從預設範本中建立新的 Cordova 專案。請確定您最後用來部署您的 iOS 應用程式的發行設定檔是使用 'com.mycompany.myapp' 做為應用程式識別碼。 

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

2. 執行下列動作來設定 **iOS** 的專案，並在 iOS 模擬器中執行它：

		$ cordova platform add ios 
		$ cordova run ios

3. 執行下列動作來設定 **Android** 的專案，並在 Android 模擬器中執行它：

		$ cordova platform add android
		$ cordova run android

4. 	新增 Cordova 主控台外掛程式。

		$ cordova plugin add cordova-plugin-console 

###將您的應用程式連線至 Mobile Engagement 後端

1. 安裝 Azure Mobile Engagement Cordova 外掛程式，同時提供變數值以設定外掛程式：

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			 --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

*Android 觸達圖示*：必須是不含任何副檔名的資源名稱，也不含可繪製的前置詞 (例如：mynotificationicon)，而且必須將圖示檔複製到您的 android 專案 (platforms/android/res/drawable)

*iOS 觸達圖示*：必須是具有副檔名的資源名稱 (例如：mynotificationicon.png)，而且必須將圖示檔新增到含有 XCode 的 iOS 專案 (使用 [新增檔案] 功能表)

##<a id="monitor"></a>啟用即時監視

1. 在 Cordova 專案中，編輯 **www/js/index.js**，將呼叫加入至 Mobile Engagement，以便在收到 *deviceReady* 事件之後宣告新活動。

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. 執行應用程式：
		
	- **對於 iOS**
	
		藉由執行下列動作，在 `Terminal` 視窗的新模擬器執行個體中啟動您的 App：

			cordova run ios

	- **對於 Android**
		
		藉由執行下列動作，在 `Terminal` 視窗的新模擬器執行個體中啟動您的 App：

			cordova run android

3. 您可以在主控台記錄檔中看到下列項目：

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>將 App 與即時監視連接

[AZURE.INCLUDE [將應用程式與即時監視連接](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您使用「推播通知」和「應用程式內傳訊」，於活動進行時與使用者互動。此模組在 Mobile Engagement 入口網站中稱為 REACH。以下各節將設定您的用程式來接收它們。

###設定 Mobile Engagement 的推播認證

若要讓 Mobile Engagement 以您的名義傳送推播通知，您需要授與它對您的 Apple iOS 憑證或 GCM Server API 金鑰的存取權。
	
1. 瀏覽至您的 Mobile Engagement 入口網站。確認您正位在用於此專案的 App 內，然後按一下底部的 [接洽] 按鈕：
	
	![][1]
	
2. 您將登陸在 Engagement 入口網站的 [設定] 頁面中。在該處按一下 [原生推送] 區段：
	
	![][2]

3. 設定 iOS 憑證/GCM Server API 金鑰

	**[iOS]**

	a.選取您的 .p12、將它上傳並輸入您的密碼：
	
	![][3]

	**[Android]**

	a.在 [GCM 設定] 區段中按一下 [API 金鑰] 前面的編輯圖示，並在出現的快顯視窗中貼上 GCM 伺服器金鑰，然後按一下 [確定]。
		
	![][4]

###在 Cordova 應用程式中啟用推播通知

編輯 **www/js/index.js**，將呼叫加入至 Mobile Engagement 以要求推播通知，並宣告處理常式：

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###執行應用程式

**[iOS]**

1. 我們將使用 XCode 在裝置上建置及部署應用程式，以測試推播通知，因為 iOS 只允許推播通知到實際裝置。移至您建立 Cordova 專案的位置，然後瀏覽至 **...\\platforms\\ios** 位置。在 XCode 中開啟原生 .xcodeproj 檔案。 
	
2. 建置 Cordova 應用程式並部署至 iOS 裝置，方法是使用具有佈建設定檔的帳戶，該設定檔包含憑證 (您剛剛上傳至 Mobile Engagement 入口網站) 和應用程式識別碼 (符合您在建立 Cordova 應用程式時提供的識別碼)。您可以在 XCode 的 **Resources*-info.plist** 檔案中檢查「套件組合識別碼」來進行配對。

3. 您會在您的裝置上看到標準 iOS 快顯視窗，上面顯示應用程式要求傳送通知的權限。授與權限。

**[Android]**

您只能使用模擬器執行 Android 應用程式，因為 GCM 通知只在 Android 模擬器上受到支援。

	cordova run android

##<a id="send"></a>傳送通知至應用程式

現在，我們將建立簡單的推播通知行銷活動，它會傳送推播到裝置上執行中的應用程式：

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [觸達] 索引標籤

2. 按一下 [新增宣告] 來建立您的推播行銷活動

	![][6]

3. 提供輸入來建立您的行銷活動 **[Android]**
	
	- 為您的行銷活動提供**名稱**。 
	- 針對 [傳遞類型] 選取 [系統通知] - [簡易]
	- 針對 [傳遞時間] 選取 [任何時間]
	- 提供通知的**標題**，這將是推播中的第一行。
	- 提供通知的**訊息**，這將做為訊息內文。 

	![][11]

4. 提供輸入來建立您的行銷活動 **[iOS]**

	- 為您的行銷活動提供**名稱**。 
	- 針對 [傳遞時間] 選取 [僅限 App 外]
	- 提供通知的**標題**，這將是推播中的第一行。
	- 提供通知的**訊息**，這將做為訊息內文。 
 
	![][12]

5. 向下捲動，在 [內容] 區段中選取 [僅限通知]。

	![][8]

6. [選用] 您也可以提供動作 URL。請確定它會使用在設定外掛程式的 **AZME 重新導向 URL** 變數 (例如 **myapp://test*) 時提供的 URL 配置。

7. 您已完成能做的最基本活動設定。現在再次向下捲動，並按一下 [建立] 按鈕來儲存行銷活動。

8. 最後**啟用**您的行銷活動
	
	![][10]

9. 您現在應該會在裝置或模擬器上看到推播通知，做為此行銷活動的一部分。

##<a id="next-steps"></a>後續步驟
[可用於 Cordova Mobile Engagement SDK 之所有方法的概觀](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png

[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

<!---HONumber=Oct15_HO3-->