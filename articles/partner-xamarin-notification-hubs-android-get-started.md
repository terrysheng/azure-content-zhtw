<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/zh-TW/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-TW/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-TW/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/zh-TW/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-TW/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-TW/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

本主題將示範如何使用 Azure 通知中樞，將推播通知傳送至 Xamarin.Android 應用程式。
在本教學課程中，您將使用 Google Cloud Messaging (GCM)，建立可接收推播通知的空白 Xamarin.Android 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。[NotificationHubs 應用程式][NotificationHubs 應用程式]範例中提供完成的程式碼。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [啟用 Google 雲端通訊][啟用 Google 雲端通訊]
2.  [設定您的通知中心][設定您的通知中心]
3.  [將您的應用程式連接到通知中心][將您的應用程式連接到通知中心]
4.  [使用模擬器執行您的應用程式][使用模擬器執行您的應用程式]
5.  [從後端傳送通知][從後端傳送通知]

本教學課程將示範使用通知中心的簡單廣播案例。本教學課程需要下列各項：

-   [Xamarin.Android][1]
-   有效的 Google 帳戶
-   [Azure 行動服務元件][Azure 行動服務元件]
-   [Google Cloud Messaging 元件][Google Cloud Messaging 元件]

完成本教學課程是 Xamarin.Android 應用程式所有其他通知中樞教學課程的先決條件。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-TW/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <a name="register"></a><span class="short-header">啟用 Google 雲端通訊</span>啟用 Google 雲端通訊

<div class="dev-callout"><b>注意</b>
<p>若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。</p>
</div>

1.  瀏覽至 [Google apis][Google apis] (英文) 網站、使用 Google 帳戶認證登入，然後按一下 [建立專案...]。

    ![][0]

    <div class="dev-callout"><b>注意</b>
<p>如果您現已有專案，登入後會將您重新導向至 [儀表板] 頁面。若要從 [儀表板] 建立新的專案，請展開 [API Project]，並按一下 [其他專案] 下的 [建立]，然後輸入專案名稱，並按一下 [建立專案]。</p>
</div>

2.  按一下左欄中的 [Overview]，並記下 [Dashboard] 區段中的專案編號。

    在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT\_ID 變數。

3.  在 [Google apis][Google apis] 頁面上，按一下 [Services]，然後按一下切換開關來啟用 [Google Cloud Messaging for Android]，並接受服務條款。

4.  按一下 [API Access]，然後按一下 [Create new Server key...]

    ![][2]

5.  在 [Configure Server Key for API Project] 中，按一下 [Create]。

    ![][3]

6.  記下 [API key] 值。

    ![][4]

接下來，您將使用此 API 金鑰值，讓通知中心能夠使用 GCM 進行驗證並代表您的應用程式傳送推播通知。

## <a name="configure-hub"></a><span class="short-header">設定您的通知中心</span>設定您的通知中心

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，並按一下畫面底部的 [+新增]。

2.  依序按一下 [App Services]、[服務匯流排]、[Notification Hub]、[快速建立]。

    ![][5]

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

    ![][6]

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 [設定]。

    ![][7]

5.  按一下頂端的 [Notification Hubs] 索引標籤，然後按一下您剛才建立的通知中心。

    ![][8]

6.  按一下頂端的 [設定] 索引標籤，輸入前一節中取得的 [API Key] 值，然後按一下 [儲存]。

    ![][9]

7.  選取頂端的 [儀表板] 索引標籤，然後按一下 [連接資訊]。記下這兩個連接字串。

    ![][10]

現在已將您的通知中心設定成使用 GCM，而且您已擁有可用來註冊應用程式和傳送推播通知的連接字串。

## <a name="connecting-app"></a><span class="short-header">連接應用程式</span>將您的應用程式連接到通知中心

### 建立新專案

1.  在 Xamarin Studio (或 Visual Studio) 中，建立新的 Android 專案 ([檔案] \> [新增] \> [方案] \> [Android Application])。

    ![][11]

    ![][12]

2.  按一下 [方案] 檢視中的新專案來開啟專案屬性，並選擇 [選項]。選取 [建置] 區段中的 [Android Application] 項目。

    ![][13]

3.  將 [Minimum Android version] 設定為 API 層級 8。

4.  將 [Target Android version] 設定為想要的目標 API 版本 (必須是 API 層級 8 或更高版本)。

5.  確定 [套件名稱] 的第一個字母是小寫。

    <div class="dev-callout"><b>注意</b>
<p>套件名稱的第一個字母必須是小寫。否則，當您在下文為推播通知註冊 **BroadcastReceiver** 和 **IntentFilter**時，將收到應用程式資訊清單錯誤。</p>
</div>

### 將 Google Cloud Messaging 用戶端新增至您的專案

可在 Xamarin 元件存放區中取得的 Google Cloud Messaging 用戶端會簡化在 Xamarin.Android 中支援推播通知的程序。

1.  使用滑鼠右鍵按一下 Xamarin.Android 應用程式中的 [元件] 資料夾，然後選擇 [取得更多元件...]

2.  搜尋 [Google Cloud Messaging 用戶端] 元件。

3.  將該元件新增至 Xamarin.Android 應用程式。系統會自動新增必要的組件參考。

### 將 Xamarin.NotificationHub 新增至專案

這個組件可提供簡單的方式來註冊 Azure 通知中樞。您可以使用下列指示來下載，也可以在[範例下載][NotificationHubs 應用程式]中找到此組件。

1.  造訪 [Xamarin.NotificationHub Github 頁面][Xamarin.NotificationHub Github 頁面] (英文)，下載並建置 source 資料夾。

2.  在 Xamarin.Android 專案資料夾中建立 **\_external** 資料夾，然後將編譯過的 **ByteSmith.WindowsAzure.Messaging.Android.dll** 複製到該處。

3.  在 Xamarin Studio (或 Visual Studio) 中開啟 Xamarin.Android 專案。

4.  以滑鼠右鍵按一下專案 [參考] 資料夾，並選擇 [編輯參考...]

5.  移至 [.Net 組件] 索引標籤、瀏覽至專案的 **\_external** 資料夾、選取稍早建置的 **ByteSmith.WindowsAzure.Messaging.Android.dll**，然後按一下 [新增]。按一下 [確定] 關閉對話方塊。

### 在專案中設定通知中心

1.  建立 **Constants.cs** 類別，並定義下列常數值 (以值取代預留位置)：

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  在 **MainActivity.cs** 中新增下列 using 陳述式：

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  在 **MainActivity** 類別中建立下列方法：

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  建立新類別 **MyBroadcastReceiver**。

    <div class="dev-callout"><b>注意</b>
<p>我們將在下文中從頭逐步建立 **BroadcastReceiver**。不過，除了手動建立 **MyBroadcastReceiver.cs**，還有個快速的方式，那就是參考 GitHub 上提供之範例 Xamarin.Android 專案中的 **GcmService.cs** 檔案。複製 **GcmService.cs** 之後再變更類別名稱，也是很好的開始。</p>
</div>

5.  將下列的 using 陳述式新增至 **MyBroadcastReceiver.cs** (請參閱稍早新增的元件和組件)：

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  在 **using** 陳述式與 **namespace** 宣告之間新增下列權限要求：

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  在 **MyBroadcastReceiver.cs** 中，變更 **MyBroadcastReceiver** 類別以符合下列內容：

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  在 **MyBroadcastReceiver.cs** 中，新增另一個衍生自 **PushHandlerServiceBase** 且名稱為 **PushHandlerService** 的類別。務必在該類別上使用 **Service** 指示詞：

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

9.  **GcmServiceBase** 實作 **OnRegistered()**、**OnUnRegistered()**、**OnMessage()**、**OnRecoverableError()** 和 **OnError()** 等方法。我們的實作類別 **GcmService** 必須覆寫這些方法，而這些方法將會在與通知中樞進行互動時觸發以作為回應。

10. 以下列程式碼覆寫 **PushHandlerService** 中的 **OnRegistered()** 方法：

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

    <div class="dev-callout"><b>注意</b>
<p>在以上的 **OnRegistered()** 程式碼中，您應該發現能夠指定標籤來註冊特定傳訊通道。</p>
</div>

11. 以下列程式碼覆寫 **PushHandlerService** 中的 **OnMessage** 方法：

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

12. 如前面使用方式一樣，在 **PushHandlerService** 中新增下列 **createNotification** 方法以通知使用者：

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

13. 覆寫抽象成員 **OnUnRegistered()**、**OnRecoverableError()** 和 **OnError()**，以便您的程式碼進行編譯。

## <a name="run-app"></a><span class="short-header">執行應用程式</span>在模擬器中執行應用程式

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1.  從 [工具] 中，按一下 [Open Android Emulator Manager]，選取您的裝置，然後按一下 [Edit]。

    ![][14]

2.  在 [Target] 中選取 [Google API]，然後按一下 [OK]。

    ![][15]

3.  在頂端工具列上，按一下 [執行]，然後選取您的應用程式。這將啟動模擬器，並執行應用程式。

4.  應用程式將從 GCM 擷取 *registrationId*，並向通知中心註冊。

    <div class="dev-callout"><b>注意</b>
<p>若要收到推播通知，您必須在 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽至 **Settings** 並按一下 **Add Account**)。另外，確定模擬器已連線到網際網路。</p>
</div>

## <a name="send"></a><span class="short-header">傳送通知</span>從後端傳送通知

您可以使用 [REST 介面][REST 介面]，從任何後端使用通知中樞傳送通知。在本教學課程中，我們將透過 .NET 主控台應用程式，以及使用節點指令碼透過行動服務來傳送通知。

使用 .NET 應用程式傳送通知：

1.  建立新的 Visual C# 主控台應用程式：

    ![][16]

2.  使用 [WindowsAzure.ServiceBus NuGet 封裝][WindowsAzure.ServiceBus NuGet 封裝]來新增 Azure 服務匯流排 SDK 的參照。在 Visual Studio 主功能表中，依序按一下 [工具]、[Library Package Manager]、[Package Manager Console]。接著，在主控台視窗中輸入：

        Install-Package WindowsAzure.ServiceBus

    並按 Enter 鍵。

3.  開啟檔案 Program.cs，並新增下列 using 陳述式：

        using Microsoft.ServiceBus.Notifications;

4.  在 `Program` 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  接著在您的 Main 方法中新增下列程式碼行：

         SendNotificationAsync();
         Console.ReadLine();

6.  按 F5 鍵以執行應用程式。您應會收到快顯通知。

    ![][17]

若要使用行動服務傳送通知，請依照[開始使用行動服務][開始使用行動服務]中的步驟進行，然後執行下列動作：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，然後選取您的行動服務。

2.  選取頂端的 [排程器] 索引標籤。

    ![][18]

3.  建立新的排定工作、插入名稱，然後選取 [隨選]。

    ![][19]

4.  在工作建立之後，按一下此工作名稱。然後按一下頂端列中的 [指令碼]。

5.  將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。按一下 [儲存]。

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

6.  按一下底列上的 [執行一次]。您應會收到快顯通知。

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您將廣播通知到您的所有 Android 裝置。若要以特定使用者為目標，請參閱教學課程[使用通知中心來推播通知給使用者][使用通知中心來推播通知給使用者]，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞][使用通知中心傳送即時新聞]。若要深入了解如何使用通知中心，請參閱 [Azure 通知中心概觀][Azure 通知中心概觀]和 [Android 的通知中心作法][Android 的通知中心作法] (英文)。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Xamarin.Android]: /zh-TW/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [NotificationHubs 應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [啟用 Google 雲端通訊]: #register
  [設定您的通知中心]: #configure-hub
  [將您的應用程式連接到通知中心]: #connecting-app
  [使用模擬器執行您的應用程式]: #run-app
  [從後端傳送通知]: #send
  [1]: http://xamarin.com/download/
  [Azure 行動服務元件]: http://components.xamarin.com/view/azure-mobile-services/
  [Google Cloud Messaging 元件]: http://components.xamarin.com/view/GCMClient/
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [Xamarin.NotificationHub Github 頁面]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [REST 介面]: http://msdn.microsoft.com/zh-TW/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet 封裝]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [開始使用行動服務]: /zh-TW/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [使用通知中心來推播通知給使用者]: /zh-TW/manage/services/notification-hubs/notify-users-aspnet
  [使用通知中心傳送即時新聞]: /zh-TW/manage/services/notification-hubs/breaking-news-dotnet
  [Azure 通知中心概觀]: http://msdn.microsoft.com/zh-TW/library/jj927170.aspx
  [Android 的通知中心作法]: http://msdn.microsoft.com/zh-TW/library/dn282661.aspx
