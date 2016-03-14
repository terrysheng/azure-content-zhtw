<properties
	pageTitle="使用 Azure Mobile Apps 新增推播通知至 Apache Cordova 應用程式| Azure App Service"
	description="了解如何使用 Azure 行動應用程式將推播通知傳送至 Apache Cordova 應用程式。"
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# 新增推播通知至您的 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## 概觀

在本教學課程中，您會將推播通知新增至 [Apache Cordova 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知。本教學課程以 [Apache Cordova 快速入門]教學課程為基礎，您必須先完成該教學課程。如果您有 ASP.NET 後端，且不使用下載的快速入門伺服器專案，您必須將推播通知擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK]。

##<a name="prerequisites"></a>必要條件

本教學課程涵蓋在 Visual Studio 2015 內開發且在 Google Android 模擬器上執行的 Apache Cordova 應用程式。此外，您也可以在其他模擬器或裝置上，以及不同的開發平台上完成本教學課程。

若要完成此教學課程，您需要下列項目：

* 具有已驗證電子郵件地址的 [Google 帳戶]。
* 具有 [Visual Studio Community 2015] 或更新版本的電腦。
* [Visual Studio Tools for Apache Cordova]。
* 有效的 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。
* 已完成的 [Apache Cordova 快速入門]專案。可以先完成其他教學課程 (例如，[驗證])，但並非必要。
* Android 裝置。

雖然 Android 模擬器支援推播通知，但是我們發現它們很不穩定，因此不建議您在模擬器上測試推播通知。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>啟用 Google Cloud Messaging

因為我們是以 Google Android 平台為目標，所以您必須啟用 Google 雲端通訊。如果您以 Apple iOS 裝置為目標，您應該啟用 APNS 支援。同樣地，如果您以 Microsoft Windows 裝置為目標，您應該啟用 WNS 或 MPNS 支援。

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>設定行動應用程式後端以傳送推播要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>設定 Android 裝置進行 USB 偵錯

在您可以將應用程式部署到您的 Android 裝置之前，您需要啟用 USB 偵錯。在您的 Android 手機上執行下列步驟：

1. 移至 [設定] > [關於手機]
2. 點選 [組建編號]，直到啟用開發人員模式
3. 回到 [設定]
4. 選取 [開發人員選項]
5. 開啟 [USB 偵錯]
6. 使用 USB 纜線將 Android 手機連接至開發電腦。

在測試本教學課程時，我們使用執行 Android 6.0 (Marshmallow) 版本的 Google Nexus 5X。不過，這些技術在任何現代化 Android 版本中都是相同的。

##<a name="add-push-to-app"></a>將推播通知新增至應用程式

您必須確認自己的 Apache Cordova 應用程式專案已經能夠處理推播通知。

### 安裝 Apache Cordova 推播外掛程式

Apache Cordova 應用程式原本就不會處理裝置或網路功能。這些功能是由 [npm](https://www.npmjs.com/) 或 GitHub 上發佈的外掛程式所提供。`phonegap-plugin-push` 外掛程式是用來處理網路推播通知。

您可透過下列其中一種方式安裝推撥外掛程式：

**從命令提示：**

    cordova plugin add phonegap-plugin-push

**從 Visual Studio 內：**

1.  在 [方案總管] 中開啟 `config.xml` 檔案。
2.  按一下 [外掛程式] > [自訂]，請選取 [Git] 做為安裝來源，然後輸入 `https://github.com/phonegap/phonegap-plugin-push` 做為來源。
	
	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)
	
4.  按一下安裝來源旁的箭頭，然後按一下 [新增]

推撥外掛程式現已安裝。

### 安裝 Android Google Play 服務

PhoneGap 推播外掛程式仰賴 Google Play 服務來進行推播通知。若要安裝：

1.  開啟 **Visual Studio**
2.  按一下 [工具] > [Android] > [Android SDK Manager]
3.  勾選 Extras 資料夾中各個未安裝但必要之 SDK 旁邊的核取方塊。下列為必要封裝：
    * Android Support Library (版本 23 或更高版本)
    * Android Support Repository (版本 20 或更高版本)
    * Google Play 服務 (版本 27 或更高版本)
    * Google Repository (版本 22 或更高版本)
4.  按一下 [安裝封裝]。
5.  等待安裝完成。

目前的必要程式庫會在 [phonegap-plugin-push 安裝文件]中列出。

### 註冊您的裝置在啟動時推播

1. 在登入程序的回呼期間或是在 **onDeviceReady** 方法的底部，新增對 **registerForPushNotifications** 的呼叫：

 
		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');
		
		        // Refresh the todoItems
		        refreshDisplay();
		
		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);
		
				// Added to register for push notifications.
		        registerForPushNotifications();
		
		    }, handleError);

	此範例為驗證成功後呼叫 **registerForPushNotifications**，如果應用程式中同時使用推播通知和驗證，建議採此方法。

2. 新增 `registerForPushNotifications()` 方法，如下所示：

	    // Register for Push Notifications.
		// Requires that phonegap-plugin-push be installed.
	    var pushRegistration = null;
	    function registerForPushNotifications() {
	        pushRegistration = PushNotification.init({
	            android: {
	                senderID: 'Your_Project_ID'
	            },
	            ios: {
	                alert: 'true',
	                badge: 'true',
	                sound: 'true'
	            },
	            wns: {
	
	            }
	        });
	
	        pushRegistration.on('registration', function (data) {
	            client.push.register('gcm', data.registrationId);
	        });
	
	        pushRegistration.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });
	
	        pushRegistration.on('error', handleError);
	    }

3. 請在上述程式碼中，從 [Google Developer Console] 使用應用程式的數字專案識別碼取代 `Your_Project_ID`。

## 對已發佈的行動服務進行應用程式測試

您可以直接使用 USB 纜線連接 Android 手機，以測試應用程式。請選取 [裝置]，而非 [Google Android 模擬器]。Visual Studio 會將應用程式下載至裝置並執行。您接著可以在裝置上與應用程式互動。

改善您的開發經驗。畫面共用應用程式 (例如 [Mobizen]) 可在電腦上將您的 Android 畫面投射到 Web 瀏覽器，並協助您開發 Android 應用程式。

您也可以在 Android 模擬器上測試 Android 應用程式。請務必先在模擬器上新增 Google 帳戶。

##<a name="next-steps"></a>後續步驟

* 請閱讀[通知中樞]，了解推播通知的相關資訊。
* 如果您尚未這麼做，請[新增驗證]至您的 Apache Cordova 應用程式，繼續教學課程。

<!-- URLs -->
[新增驗證]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova 快速入門]: app-service-mobile-cordova-get-started.md
[驗證]: app-service-mobile-cordova-get-started-users.md
[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google 帳戶]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push 安裝文件]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/zh-TW/features/cordova-vs.aspx
[通知中樞]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0302_2016-->