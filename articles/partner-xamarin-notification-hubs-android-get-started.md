<properties 
	pageTitle="開始使用適用於 Xamarin.Android 應用程式的通知中心" 
	description="了解如何使用 Azure 通知中心，將推播通知傳送到 Xamarin Android 應用程式。"
	authors="yuaxu" 
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
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/zh-tw/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-tw/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-tw/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

本主題將示範如何使用 Azure 通知中心將推播通知傳送至 Xamarin.Android 應用程式。 
在本教學課程中，您將使用 Google 雲端通訊 (GCM)，建立可接收推播通知的空白 Xamarin.Android 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。完成的程式碼可從 [NotificationHubs 應用程式][GitHub]範例中取得。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [啟用 Google 雲端通訊]
2. [設定通知中心]
3. [將您的應用程式連接到通知中心]
4. [使用模擬器執行您的應用程式]
5. [從後端傳送通知]

本教學課程將示範使用通知中心的簡單廣播案例。本教學課程需要下列各項：

+ [Xamarin.Android]
+ 有效的 Google 帳戶
+ [Azure 行動服務元件]
+ [Google 雲端通訊元件]

完成本教學課程是 Xamarin.Android 應用程式所有其他通知中樞教學課程的先決條件。 

> [AZURE.IMPORTANT]若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-tw%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F"%20target="_blank)。

<h2><a name="register"></a>啟用 Google 雲端通訊</h2>

<p></p>

> [AZURE.IMPORTANT]若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302"%20target="_blank)。 

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> 網站、使用 Google 帳戶認證登入，然後按一下 [**建立專案...**]。

   	![][1]   
	
	> [AZURE.NOTE] 如果您現已有專案，登入後會將您重新導向至 [**儀表板**] 頁面。若要從 [儀表板] 建立新的專案，請展開 [**API Project**]，並按一下 [**其他專案**] 下的 [**建立**]，然後輸入專案名稱，並按一下 [**建立專案**]。

2. 按一下左欄中的 [**概觀**]，並記下 [**儀表板**] 區段中的專案編號。 

	在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT_ID 變數。

3. 在 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> 頁面上，按一下 [**服務**]，然後按一下切換開關來啟用 [**Google Cloud Messaging for Android**]，並接受服務條款。 

4. 按一下 [**API Access**]，然後按一下 [**Create new Server key...**] 

   	![][2]

5. 在 [**Configure Server Key for API Project**] 中，按一下 [**Create**]。

   	![][3]

6. 記下 [**API key**] 值。

   	![][4] 

接下來，您將使用此 API 金鑰值，讓通知中心能夠使用 GCM 進行驗證並代表您的應用程式傳送推播通知。

<h2><a name="configure-hub"></a>設定通知中心</h2>

1. 登入 [Azure 管理入口網站]，然後按一下畫面底部的 [**+新增**]。

2. 依序按一下 **[應用程式服務 (App Services)]**、**[服務匯流排 (Service Bus)]**、**[通知中樞 (Notification Hub)]**、**[快速建立 (Quick Create)]**。

   	![][7]

3. 為您的通知中心輸入名稱、選取所需的區域，然後按一下 [**建立新的通知中心**]。

   	![][8]

4. 按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 [**設定**]。

   	![][9]

5. 按一下頂端的 [**通知中心**] 索引標籤，然後按一下您剛才建立的通知中心。

   	![][10]

6. 按一下頂端的 [**設定**] 索引標籤，輸入前一節中取得的 [**API 金鑰**] 值，然後按一下 [**儲存**]。

   	![][11]

7. 選取頂端的 [**儀表板**] 索引標籤，然後按一下 [**連接資訊**]。記下這兩個連接字串。

   	![][12]

現在已將您的通知中心設定成使用 GCM，而且您已擁有可用來註冊應用程式和傳送推播通知的連接字串。

<h2><a name="connecting-app"></a>將您的應用程式連接到通知中心</h2>

### 建立新專案

1. 在 Xamarin Studio (或 Visual Studio) 中，建立新的 Android 專案 ([檔案] > [新增] > [方案] > [Android Application])。

   	![][13]   
   	![][14]

2. 按一下 [方案] 檢視中的新專案來開啟專案屬性，並選擇 [**選項**]。選取 [**建置**] 區段中的 [**Android Application**] 項目。

   	![][15]

3. 將 [**Minimum Android version**] 設定為 API 層級 8。

4. 將 [**Target Android version**] 設定為想要的目標 API 版本 (必須是 API 層級 8 或更高版本)。

5. 確定 [**封裝名稱**] 的第一個字母是小寫。

	> [AZURE.IMPORTANT]封裝名稱的第一個字母必須是小寫。否則，當您針對以下推播通知註冊 **BroadcastReceiver** 和 **IntentFilter** 時，您會收到應用程式資訊清單錯誤。

### 將 Google Cloud Messaging 用戶端新增至您的專案

可在 Xamarin 元件存放區中取得的 Google Cloud Messaging 用戶端會簡化在 Xamarin.Android 中支援推播通知的程序。

1. 以滑鼠右鍵按一下 Xamarin.Android 應用程式中的 [元件] 資料夾，然後選擇 [**取得更多元件...**]

2. 搜尋 [**Google Cloud Messaging 用戶端**] 元件。

3. 將該元件新增至 Xamarin.Android 應用程式。系統會自動新增必要的組件參考。

### 將 Xamarin.NotificationHub 新增至專案

這個組件可提供簡單的方式來註冊 Azure 通知中心。您可以使用下列指示來下載，也可以在[範例下載][GitHub]中找到此組件。

1. 造訪 [Xamarin.NotificationHub Github 頁面]，下載並建置 source 資料夾。

2. 在 Xamarin.Android 專案資料夾中建立 **_external** 資料夾，然後將編譯過的 **ByteSmith.WindowsAzure.Messaging.Android.dll** 複製到該處。

3. 在 Xamarin Studio (或 Visual Studio) 中開啟 Xamarin.Android 專案。

4. 以滑鼠右鍵按一下專案 [**參考**] 資料夾，並選擇 [**編輯參考...**]

5. 移至 [**.Net Assembly**] 索引標籤，瀏覽至專案的 **_external** 資料夾，並選取稍早建置的 **ByteSmith.WindowsAzure.Messaging.Android.dll**，然後按一下 [**新增**]。按一下 [確定] 關閉對話方塊。 

### 在專案中設定通知中心

1. 建立 **Constants.cs** 類別，並定義下列常數值 (以值取代預留位置)：

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. 在 **MainActivity.cs** 中新增下列 using 陳述式：

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

3. 在 **MainActivity** 類別中建立下列方法：

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. 建立新類別 **MyBroadcastReceiver**。

	> [AZURE.NOTE] 我們將在下文中從頭逐步建立 **BroadcastReceiver**。不過，除了手動建立 **MyBroadcastReceiver.cs**，還有個快速的方式，那就是參考 GitHub 上提供之範例 Xamarin.Android 專案中的 **GcmService.cs** 檔案。複製 **GcmService.cs** 之後再變更類別名稱，也是很好的開始。

5. 將下列的 using 陳述式新增至 **MyBroadcastReceiver.cs** (請參閱稍早新增的元件和組件)：

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

5. 在 **using** 陳述式與 **namespace** 宣告之間新增下列權限要求：

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. 在 **MyBroadcastReceiver.cs** 中，變更 **MyBroadcastReceiver** 類別以符合下列內容：

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. 在 **MyBroadcastReceiver.cs** 中，新增另一個衍生自 **PushHandlerServiceBase** 且名稱為 **PushHandlerService** 的類別。務必在該類別上使用 **Service** 指示詞：

    	[Service] //Must use the service tag
    	public class GcmService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public GcmService() : base(Constants.SenderID) 
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
        	}
    	}


8. **GcmServiceBase** 會實作 **OnRegistered()**、**OnUnRegistered()**、**OnMessage()**、**OnRecoverableError()** 和 **OnError()** 等方法。我們的實作類別 **GcmService** 必須覆寫這些方法，而這些方法將會在與通知中心進行互動時觸發以作為回應。

9. 以下列程式碼覆寫 **PushHandlerService** 中的 **OnRegistered()** 方法：

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

	> [AZURE.NOTE] 在以上的 **OnRegistered()** 程式碼中，您應該發現能夠指定標籤來註冊特定傳訊通道。
    
10. 以下列程式碼覆寫 **PushHandlerService** 中的 **OnMessage** 方法：

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

11. 如前面使用方式一樣，在 **PushHandlerService** 中新增下列 **createNotification** 方法以通知使用者：

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }
        
12. 覆寫抽象成員 **OnUnRegistered()**、**OnRecoverableError()** 和 **OnError()**，以便您的程式碼進行編譯。


<h2><a name="run-app"></a>在模擬器中執行您的應用程式</h2>

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1. 從 [**工具**] 中，按一下 [**Open Android Emulator Manager**]，選取您的裝置，然後按一下 [**Edit**]。

   	![][18]

2. 在 [**目標**] 中選取 [**Google API**]，然後按一下 [**確定**]。

   	![][19]

3. 在頂端工具列上，按一下 [**執行**]，然後選取您的應用程式。這將啟動模擬器，並執行應用程式。

4. 應用程式將從 GCM 擷取  *registrationId*，並向通知中心註冊。

	> [AZURE.IMPORTANT]若要收到推播通知，您必須在 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽至 [**設定**]，然後按一下 [**新增帳戶**])。另外，確定模擬器已連線到網際網路。

<h2><a name="send"></a>從後端傳送通知</h2>

您可以使用 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 介面</a>，從任何後端使用通知中心來傳送通知。在本教學課程中，我們將透過 .NET 主控台應用程式，以及使用節點指令碼透過行動服務來傳送通知。

使用 .NET 應用程式傳送通知：

1. 建立新的 Visual C# 主控台應用程式： 

   	![][20]

2. 使用 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 封裝</a>來新增 Azure 服務匯流排 SDK 的參考。在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入：

        Install-Package WindowsAzure.ServiceBus

    並按 Enter 鍵。

2. 開啟檔案 Program.cs，並新增下列 using 陳述式：

        using Microsoft.ServiceBus.Notifications;

3. 在  `Program` 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. 接著在您的 Main 方法中新增下列程式碼行：

         SendNotificationAsync();
		 Console.ReadLine();

5. 按 F5 鍵以執行應用程式。您應會收到快顯通知。

   	![][21]

若要使用行動服務傳送通知，請依照[開始使用行動服務]中的步驟進行，然後執行下列動作：

1. 登入 [Azure 管理入口網站]，然後選取您的行動服務。

2. 選取頂端的 [**排程器**] 索引標籤。

   	![][22]

3. 建立新的排定工作、插入名稱，然後選取 [**隨選**]。

   	![][23]

4. 在工作建立之後，按一下此工作名稱。然後按一下頂端列中的 [**指令碼**]。

5. 將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的  *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。按一下 **[儲存]**。

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
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

6. 按一下底列上的 [**執行一次**]。您應會收到快顯通知。

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您將廣播通知到您的所有 Android 裝置。若要以特定使用者為目標，請參閱[使用通知中心來推播通知給使用者]教學課程，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞]。若要深入了解如何使用通知中心，請參閱 [Azure 通知中心指引]和 [Android 的通知中心作法]。

<!-- Anchors. -->
[啟用 Google 雲端通訊]: #register
[設定通知中心]: #configure-hub
[將您的應用程式連接到通知中心]: #connecting-app
[使用模擬器執行您的應用程式]: #run-app
[從後端傳送通知]: #send
[後續步驟]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript 和 HTML]: /zh-tw/develop/mobile/tutorials/get-started-with-push-js

[Azure 管理入口網站]: https://manage.windowsazure.com/
[wns 物件]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Azure 通知中心指引]: http://msdn.microsoft.com/library/jj927170.aspx
[Android 的通知中心作法]: http://msdn.microsoft.com/library/dn282661.aspx

[使用通知中心來推播通知給使用者]: /zh-tw/manage/services/notification-hubs/notify-users-aspnet
[使用通知中心傳送即時新聞]: /zh-tw/manage/services/notification-hubs/breaking-news-dotnet
[GCMClient 元件頁面]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub Github 頁面]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
[Google 雲端通訊元件]: http://components.xamarin.com/view/GCMClient/

<!--HONumber=45--> 
