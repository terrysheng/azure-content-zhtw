<properties 
	pageTitle="開始使用 Azure 通知中樞" 
	description="了解如何使用 Azure 通知中樞推播通知。" 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/15/2014" 
	ms.author="ricksal"/>

# 開始使用通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

本主題說明如何使用 Azure 通知中樞傳送推播通知至 Android 應用程式。 
在本教學課程中，您將建立可使用 Google 雲端通訊 (GCM) 接收推播通知的空白 Android 應用程式。完成時，您便能夠使用通知中樞，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

* [啟用 Google 雲端通訊](#register)
* [設定通知中樞](#configure-hub)
* [將您的應用程式連接到通知中樞](#connecting-app)
* [如何傳送通知至應用程式](#send)
* [測試應用程式](#run-app)

本教學課程將示範使用通知中樞的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以查看如何使用通知中樞來處理特定使用者和裝置群組。 

本教學課程需要下列項目：

+ Android SDK (假設您將使用 Eclipse)，您可以從<a href="http://go.microsoft.com/fwlink/?LinkId=389797">此處</a>下載
+ [行動服務 Android SDK]

完成本教學課程是 Android 應用程式所有其他通知中樞教學課程的先決條件。 

> [AZURE.NOTE] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-tw%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F)。

## <a id="register"></a>啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]


## <a id="configure-hub"></a>設定通知中樞

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

## <a id="connecting-app"></a>將您的應用程式連接到通知中樞

### 建立新的 Android 專案

1. 在 Eclipse ADT 中建立新的 Android 專案 ([檔案]、[新增]、[Android 應用程式])。

   	![][13]

2. 請確定 [**Minimum Required SDK**] 已設為  *API 8：Android 2.2 (Froyo)*，且接下來的兩個 SDK 項目已設定為最新的可用版本。選擇 [下一步]，並依照精靈的指示進行，確定已勾選 [**建立活動**] 以建立空白活動。接受下一個方塊上預設的 [啟動器] 圖示，並按一下最後一個方塊中的 [**完成**]。

   	![][14]

### 新增 Google Play 服務至專案

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### 新增程式碼

1. 從<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">此處</a>下載通知中樞 Android SDK。解壓縮 .zip 檔案，並在 [封裝總管] 中將 notificationhubs\notification-hubs-0.1.jar 檔案複製到專案的 \libs 目錄。

2. 下載並解壓縮[行動服務 Android SDK]，開啟 [**notifications**] 資料夾，將 **notifications-1.0.1.jar** 檔案複製到 Eclipse 專案的  *libs* 資料夾，然後重新整理  *libs* 資料夾。

    > [AZURE.NOTE] 檔案名稱結尾的數字在後續 SDK 版本中可能會變更。

	現在，請設定應用程式以從 GCM 中取得  *registrationId*，並使用此值在通知中樞註冊此應用程式執行個體。

3. 在 AndroidManifest.xml 檔案中，在 <uses-sdk/> 元素正下方新增下列程式碼行。請確定以您在步驟 1 中為應用程式所選取的封裝取代 `<your package>` (在此範例中為 `com.yourCompany.wams_notificationhubs`)。

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. 在 **MainActivity** 類別中，新增下列陳述式。

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. 在類別頂端新增下列 Private 成員。

	> [AZURE.NOTE] 請務必將 SENDER_ID 設定為稍早取得的專案編號。

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. 在 **OnCreate** 方法中新增下列程式碼，並確定以在上一節中取得且具備接聽存取權的連接字串，和出現在 Azure 中頁面頂端的通知中樞名稱 (**非**完整 URL) 來取代預留位置。

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. 在 MainActivity.java 中建立下列方法：

		@SuppressWarnings("unchecked")
		private void registerWithNotificationHubs() {
		   new AsyncTask() {
		      @Override
		      protected Object doInBackground(Object... params) {
		         try {
		            String regid = gcm.register(SENDER_ID);
		            hub.register(regid);
		         } catch (Exception e) {
		            return e;
		         }
		         return null;
		     }
		   }.execute(null, null, null);
		}

8. 由於 Android 不會顯示通知，因此您必須撰寫自己的接收器。在 **AndroidManifest.xml** 中，在 `<application/>` 元素內新增下列元素。

	> [AZURE.NOTE] 以您的封裝名稱取代預留位置。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. 在 [封裝總管] 中，以滑鼠右鍵按一下封裝 (在 `src` 節點下)，再依序按一下 [**新增**]、[**類別**]。

10. 在 [**名稱**] 中輸入 `MyHandler`，在 [**超級類別**] 中輸入 `com.microsoft.windowsazure.notifications.NotificationsHandler`，然後按一下 [**完成**]

	![][6]

	如此即會建立新的 MyHandler 類別。

11. 新增下列 import 陳述式：

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		

12. 在類別中新增下列程式碼：

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;
	
		
		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("msg");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, MainActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}
	

## <a name="send"></a>如何傳送通知給您的應用程式

>[AZURE.NOTE]您可以使用 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面</a>，從任何後端使用通知中樞來傳送通知。在本教學課程中，您將透過 .NET 主控台應用程式來傳送通知。如需如何從已與通知中樞整合的 Azure 行動服務後端傳送通知的範例，請參閱[開始在行動服務中使用推播通知](mobile-services-javascript-backend-android-get-started-push.md)。如需如何使用 REST API 傳送通知的範例，請參閱[如何從 Java 使用通知中樞](notification-hubs-java-backend-how-to.md) 或[如何從 PHP 使用通知中樞](notification-hubs-php-backend-how-to.md).

1. 在 Visual Studio 中，從 [**檔案**] 功能表選取 [**新建**]、[**專案...**]，然後按一下 [**Visual C#**] 下方的 [**Windows**] 和 [**主控台應用程式**]，再按一下 [**確定**]。  

   	![][20]

	這會建立新的主控台應用程式專案。

2. 在 [**工具**] 功能表中按一下 [**Library Package Manager**]，再按一下 [**Package Manager Console**]。 

	這會顯示 [Package Manager Console]。

3. 在主控台視窗中，執行下列命令：

        Install-Package WindowsAzure.ServiceBus
    
	這會使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>，來新增 Azure 服務匯流排 SDK 的參考。 

4. 開啟檔案 Program.cs，並新增下列  `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

5. 在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ "data" : {"msg":"Hello from Azure!"}}");
        }

   	請確實使用出現在入口網站 [**通知中樞**] 索引標籤上的通知中樞名稱，取代 "hub name" 預留位置。此外，請使用您在「設定通知中樞」一節中取得之名為 **DefaultFullSharedAccessSignature** 的連接字串，來取代連接字串預留位置。 

	>[AZURE.NOTE]請確定您使用包含 [**完整**] 存取權 (而非 [**接聽**] 存取權) 的連接字串。接聽存取權字串不具備傳送通知的權限。

5. 接著在 **Main** 方法中新增下列程式碼行：

         SendNotificationAsync();
		 Console.ReadLine();

## <a name="run-app"></a>測試應用程式

在模擬器上測試應用程式前，您需要完成以下模擬器設定步驟 (使用實體裝置測試者可跳過)：

1. 請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

2. 從 [**Window**] 中按一下 [**Android Virtual Device Manager**]，選取您的裝置，然後按一下 [**編輯**]。

   	![][18]

3. 在 [**目標**] 中選取 **Google API**，然後按一下 [**確定**]。

   	![][19]

4. 	若要收到推播通知，您必須在 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽至 [<strong>設定</strong>]，然後按一下 [<strong>新增帳戶</strong>])。另外，確定模擬器已連線到網際網路。


請依照以下步驟在裝置或模擬器上執行應用程式：

1. 在 Eclipse 上方工具列中，按一下 [**執行**]，然後選取您的應用程式。 
 
	這會啟動模擬器 (如果您使用的是模擬器) 並載入和執行應用程式。應用程式會從 GCM 擷取  *registrationId*，並向通知中樞註冊。

3. 在 Visual Studio 按 F5 鍵以執行主控台應用程式。 

	會傳送一則通知到您的應用程式。  
 
5. 通知區域 (左上角) 出現圖示 時，下拉通知抽屜來查看通知。  

   	![][21]

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您將廣播通知到您的所有 Android 裝置。若要以特定使用者為目標，請參閱[使用通知中樞來推播通知給使用者][使用通知中樞將通知推播給使用者]教學課程，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中樞傳送即時新聞]。在[通知中樞指引]中深入了解如何使用通知中樞。


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[行動服務 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[參考程式庫專案]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure 管理入口網站]: https://manage.windowsazure.com/
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx

[使用通知中樞將通知推播給使用者]: notification-hubs-aspnet-backend-android-notify-users.md
[使用通知中樞傳送即時新聞]: notification-hubs-aspnet-backend-android-breaking-news.md


<!--HONumber=49-->