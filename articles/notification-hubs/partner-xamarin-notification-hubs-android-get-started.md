<properties
	pageTitle="開始使用適用於 Xamarin.Android 應用程式的通知中樞 | Microsoft Azure"
	description="在本教學課程中，您會了解如何使用 Azure 通知中樞將推播通知傳送至 Xamarin Android 應用程式。"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/17/2015"
	ms.author="wesmc"/>

# 開始使用適用於 Android 應用程式的通知中樞

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概觀

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 Xamarin.Android 應用程式。您將建立可使用 Google Cloud Messaging (GCM) 接收推播通知的空白 Xamarin.Android app。完成時，您便能夠使用通知中樞，將推播通知廣播到所有執行您 app 的裝置。[NotificationHubs 應用程式][GitHub]範例中提供完成的程式碼。

本教學課程示範使用通知中樞的簡單廣播案例。


## 開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以在[此處](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid)的 GitHub 上找到本教學課程的完整程式碼。



##必要條件

本教學課程需要下列各項：

+ [Xamarin.Android]
+ 有效的 Google 帳戶
+ [Azure 行動服務元件]
+ [Azure 訊息元件]
+ [Google Cloud Messaging 用戶端元件]

完成本教學課程是 Xamarin.Android app 所有其他通知中樞教學課程的先決條件。

> [AZURE.IMPORTANT] 若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)。

##啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##設定您的通知中樞

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>按一下頂端的 [設定]<b></b> 索引標籤，輸入前一節中取得的 [API Key]<b></b> 值，然後按一下 [儲存]<b></b>。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


現在已將您的通知中樞設定成使用 GCM，而且您已擁有可用來註冊應用程式以接收通知和傳送推播通知的連接字串。

##將您的應用程式連接到通知中樞

###建立新專案

1. 在 Xamarin Studio 中，依序按一下 [新增方案]、[Android 應用程式] 和 [下一步]。

   	![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. 輸入您的**應用程式名稱**和**識別碼**。按一下您想要支援的 [目標平台]，然後按 [下一步] 和 [建立]。

   	![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


	這會建立新的 Android 專案。

2. 在 [方案] 檢視中以滑鼠右鍵按一下新專案來開啟專案屬性，並選擇 [選項]。選取 [建置] 區段中的 [Android Application] 項目。

	確定 [套件名稱] 的第一個字母是小寫。

	> [AZURE.IMPORTANT] 套件名稱的第一個字母必須是小寫。否則，當您在下文為推播通知註冊 **BroadcastReceiver** 和 **IntentFilter** 時，將收到應用程式資訊清單錯誤。

   	![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. 可選擇性地將 [最低 Android 版本] 設定為另一個 API 層級。

4. 可選擇性地將 [目標 Android 版本] 設定為另一個想要的目標 API 版本 (必須是 API 層級 8 或更高版本)。

按一下 [確定]，並關閉 [專案選項] 對話方塊。


###將必要元件新增至專案

可在 Xamarin 元件存放區中取得的 Google Cloud Messaging 用戶端會簡化在 Xamarin.Android 中支援推播通知的程序。

1. 以滑鼠右鍵按一下 Xamarin.Android app 中的 [元件] 資料夾，然後選擇 [取得其他元件]。

2. 搜尋 [**Azure 訊息**] 元件，並將該元件新增至專案。

3. 搜尋 [**Google Cloud Messaging 用戶端**] 元件，並將該元件新增至專案。


###在專案中設定通知中樞

1. 收集您的 Android 應用程式和通知中樞的下列資訊：

	- **GoogleProjectNumber**：在 Google 開發人員入口網站上，從您的應用程式概觀取得此專案編號值。當您在入口網站上建立應用程式時，您可以提早記下這個值。
	- **接聽連接字串**：在 [Azure 傳統入口網站]的儀表板上，按一下 [檢視連接字串]。複製此值的 *DefaultListenSharedAccessSignature* 連線字串。
	- **中樞名稱**：這是您在 [Azure 傳統入口網站]的中樞名稱。例如，*mynotificationhub2* 。

	為您的 Xamarin 專案建立 **Constants.cs** 類別，並定義類別中的下列常數值。以您的值取代預留位置。

		public static class Constants
		{
			public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
			public const string ListenConnectionString = "<Listen connection string>";
			public const string NotificationHubName = "<hub name>";
		}

2. 在 **MainActivity.cs** 中新增下列 using 陳述式：

		using Android.Util;
		using Gcm.Client;

3. 將執行個體變數新增至 `MainActivity` 類別，以用來在應用程式執行時顯示警示對話方塊。

		public static MainActivity instance;


3. 在 **MainActivity** 類別中建立下列方法：

		private void RegisterWithGCM()
		{
			// Check to ensure everything's set up right
			GcmClient.CheckDevice(this);
			GcmClient.CheckManifest(this);

			// Register for push notifications
			Log.Info("MainActivity", "Registering...");
			GcmClient.Register(this, Constants.SenderID);
		}

4. 在 **MainActivity.cs** 的 `OnCreate` 方法中，初始化 `instance` 變數並新增 `RegisterWithGCM` 的呼叫：

		protected override void OnCreate (Bundle bundle)
		{
			instance = this;

			base.OnCreate (bundle);

			// Set your view from the "main" layout resource
			SetContentView (Resource.Layout.Main);

			// Get your button from the layout resource,
			// and attach an event to it
			Button button = FindViewById<Button> (Resource.Id.myButton);

			RegisterWithGCM();
		}


4. 建立新類別 **MyBroadcastReceiver**。

	> [AZURE.NOTE] 我們將在下文中從頭逐步建立 **BroadcastReceiver** 類別。不過，手動建立 **MyBroadcastReceiver.cs** 之外的一個快速替代方式是參考可在 [NotificationHubs 範例][GitHub]隨附的範例 Xamarin.Android 專案中找到的 **GcmService.cs** 檔案。複製 **GcmService.cs** 之後再變更類別名稱，也是很好的開始。

5. 將下列 using 陳述式新增至 **MyBroadcastReceiver.cs** (請參閱稍早新增的元件和組件)：

		using System.Collections.Generic;
		using System.Text;
		using Android.App;
		using Android.Content;
		using Android.Util;
		using Gcm.Client;
		using WindowsAzure.Messaging;

5. 在 **MyBroadcastReceiver.cs** 中，於 **using** 陳述式與 **namespace** 宣告之間新增下列權限要求：

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. 在 **MyBroadcastReceiver.cs** 中，變更 **MyBroadcastReceiver** 類別以符合下列內容：

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. 在 **MyBroadcastReceiver.cs** 中，新增另一個衍生自 **GcmServiceBase** 且名稱為 **PushHandlerService** 的類別。請一定要將 **Service** 屬性套用至類別：

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
        	}
    	}


8. **GcmServiceBase** 實作 **OnRegistered()**、**OnUnRegistered()**、**OnMessage()**、**OnRecoverableError()** 和 **OnError()** 等方法。我們的 **PushHandlerService** 實作類別必須覆寫這些方法，而且這些方法將在發生與通知中心的互動時受到觸發作為回應。


9. 使用下列程式碼覆寫 **PushHandlerService** 中的 **OnRegistered()** 方法：

		protected override void OnRegistered(Context context, string registrationId)
		{
			Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
			RegistrationID = registrationId;

			createNotification("PushHandlerService-GCM Registered...",
								"The device has been Registered!");

			Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
										context);
			try
			{
				Hub.UnregisterAll(registrationId);
			}
			catch (Exception ex)
			{
				Log.Error(MyBroadcastReceiver.TAG, ex.Message);
			}

			//var tags = new List<string>() { "falcons" }; // create tags if you want
			var tags = new List<string>() {};

			try
			{
				var hubRegistration = Hub.Register(registrationId, tags.ToArray());
			}
			catch (Exception ex)
			{
				Log.Error(MyBroadcastReceiver.TAG, ex.Message);
			}
		}

	> [AZURE.NOTE] 在以上的 **OnRegistered()** 程式碼中，您應該發現能夠指定標籤來註冊特定傳訊通道。

10. 使用下列程式碼覆寫 **PushHandlerService** 中的 **OnMessage** 方法：

		protected override void OnMessage(Context context, Intent intent)
		{
			Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

			var msg = new StringBuilder();

			if (intent != null && intent.Extras != null)
			{
				foreach (var key in intent.Extras.KeySet())
					msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
			}

			string messageText = intent.Extras.GetString("message");
			if (!string.IsNullOrEmpty (messageText))
			{
				createNotification ("New hub message!", messageText);
			}
			else
			{
				createNotification ("Unknown message details", msg.ToString ());
			}
		}

11. 新增下列 **createNotification** 和 **dialogNotify** 方法至 **PushHandlerService**，以在接收到通知時通知使用者。

	>[AZURE.NOTE] Android 5.0 版與更新版本中的通知設計，與先前版本有極大的不同。如果您在 Android 5.0 版或更新版本上進行測試，必須執行應用程式才能收到通知。如需詳細資訊，請參閱 [Android 通知](http://go.microsoft.com/fwlink/?LinkId=615880)。

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
			dialogNotify (title, desc);
        }

		protected void dialogNotify(String title, String message)
		{

			MainActivity.instance.RunOnUiThread(() => {
				AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
				AlertDialog alert = dlg.Create();
				alert.SetTitle(title);
				alert.SetButton("Ok", delegate {
					alert.Dismiss();
				});
				alert.SetMessage(message);
				alert.Show();
			});
		}


12. 覆寫抽象成員 **OnUnRegistered()**、**OnRecoverableError()** 和 **OnError()**，以便您的程式碼進行編譯：

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

			createNotification("GCM Unregistered...", "The device has been unregistered!");
		}

		protected override bool OnRecoverableError(Context context, string errorId)
		{
			Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

			return base.OnRecoverableError (context, errorId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
		}


##在模擬器中執行您的應用程式

如果您在模擬器中執行此應用程式，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

> [AZURE.IMPORTANT] 若要收到推播通知，您必須在 Android 虛擬裝置上設定 Google 帳戶(在模擬器中，瀏覽至 [設定]，然後按一下 [新增帳戶])。 另外，確定模擬器已連線到網際網路。

>[AZURE.NOTE] Android 5.0 版與更新版本中的通知設計，與先前版本有極大的不同。如需詳細資訊，請參閱 [Android 通知](http://go.microsoft.com/fwlink/?LinkId=615880)。


1. 從 [工具] 中，按一下 [Open Android Emulator Manager]，選取您的裝置，然後按一下 [Edit]。

   	![][18]

2. 在 [目標] 中選取 [Google API]，然後按一下 [確定]。

   	![][19]

3. 在頂端工具列上，按一下 [執行]，然後選取您的應用程式。這將啟動模擬器，並執行應用程式。

  應用程式將從 GCM 擷取 *registrationId* ，並向通知中樞註冊。

##從後端傳送通知


在 [Azure 傳統入口網站]中透過通知中樞上的偵錯索引標籤 (如下列螢幕畫面所示) 來傳送通知，即可在應用程式中測試通知的接收。

![][30]


推播通知通常會在後端服務 (例如行動服務) 中或透過相容程式庫的 ASP.NET 傳送。如果您的後端沒有程式庫，也可以直接使用 REST API 來傳送通知訊息。

以下是可供您檢閱，用於傳送通知的一些其他教學課程清單：

- ASP.NET：請參閱[使用通知中樞將通知推播給使用者]。
- Azure 通知中樞 Java SDK：請參閱[如何從 Java 使用通知中樞](notification-hubs-java-backend-how-to.md)，以便從 Java 傳送通知。這已在 Eclipse for Android Development 中測試。
- PHP：請參閱[如何從 PHP 使用通知中樞](notification-hubs-php-backend-how-to.md)。


在本教學課程接下來的小節中，您會使用 .NET 主控台應用程式以及透過節點指令碼使用行動服務來傳送通知。

####(選用) 使用 .NET 應用程式傳送通知

在本節中，您將使用 .NET 主控台應用程式來傳送通知。

1. 建立新的 Visual C# 主控台應用程式：

   	![][20]

2. 在 Visual Studio 中，依序按一下 [工具]、[NuGet 封裝管理員] 和 [封裝管理員主控台]。

	這會在 Visual Studio 中顯示 [封裝管理員主控台]。

3. 在 [封裝管理員主控台] 視窗中，將 [預設專案] 設為新的主控台應用程式專案，然後在主控台視窗中執行下列命令：

        Install-Package Microsoft.Azure.NotificationHubs

	這會使用 <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet 封裝</a>加入 Azure 通知中樞 SDK 的參考。

	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：

        using Microsoft.Azure.NotificationHubs;

5. 在 `Program` 類別中，新增下列方法：使用您的 *DefaultFullSharedAccessSignature* 連接字串和 [Azure 傳統入口網站]的中樞名稱，來更新預留位置文字。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ "data" : {"message":"Hello from Azure!"}}");
        }

6. 在您的 **Main** 方法中新增下列程式碼行：

         SendNotificationAsync();
		 Console.ReadLine();

7. 按 F5 鍵以執行應用程式。您應該會在應用程式中收到通知。

   	![][21]

####(選用) 使用行動服務傳送通知

1. 依照[開始使用行動服務]執行作業。

1. 登入 [Azure 傳統入口網站]，然後選取您的行動服務。

2. 選取頂端的 [排程器] 索引標籤。

   	![][22]

3. 建立新的排定工作、插入名稱，然後選取 [隨選]。

   	![][23]

4. 在工作建立之後，按一下此工作名稱。然後按一下頂端列中的 [指令碼] 索引標籤。

5. 將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。按一下 [儲存]。

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
    	  function (error)
    	  {
        	if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
	    );

6. 按一下底列上的 [執行一次]。您應會收到快顯通知。

##後續步驟

在此簡單範例中，您廣播通知到您的所有 Android 裝置。為了鎖定特定使用者，請參閱教學課程[使用通知中心來推播通知給使用者]。如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞]。若要深入了解如何使用通知中樞，請參閱[通知中樞指引]和 [Android 的通知中樞作法]。

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]: #next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure 傳統入口網站]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知中樞指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Android 的通知中樞作法]: http://msdn.microsoft.com/library/dn282661.aspx

[使用通知中心來推播通知給使用者]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中樞將通知推播給使用者]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知中心傳送即時新聞]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
[Google Cloud Messaging 用戶端元件]: http://components.xamarin.com/view/GCMClient/
[Azure 訊息元件]: http://components.xamarin.com/view/azure-messaging

<!---HONumber=AcomDC_1210_2015-->
