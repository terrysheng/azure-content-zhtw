<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 開始使用通知中心

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-TW/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/zh-TW/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/zh-TW/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/zh-TW/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/zh-TW/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/zh-TW/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-TW/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

本主題將示範如何使用 Azure 通知中樞將推播通知傳送至 Android 應用程式。
在本教學課程中，您將建立可使用 Google 雲端通訊 (GCM) 接收推播通知的空白 Android 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

-   [啟用 Google 雲端通訊][啟用 Google 雲端通訊]
-   [設定您的通知中心][設定您的通知中心]
-   [將您的應用程式連接到通知中心][將您的應用程式連接到通知中心]
-   [如何傳送通知至應用程式][如何傳送通知至應用程式]
-   [測試應用程式][測試應用程式]

本教學課程將示範使用通知中心的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以查看如何使用通知中心來處理特定使用者和裝置群組。

本教學課程需要下列各項：

-   Android SDK (假設您將使用 Eclipse)，您可以從[此處][此處]下載。
-   [行動服務 Android SDK][行動服務 Android SDK]

完成本教學課程是 Android 應用程式所有其他通知中心教學課程的先決條件。

<div class="dev-callout"><strong>注意</strong> <p>若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-TW/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。</p></div>

## <span id="register"></span></a>啟用 Google 雲端通訊

[WACOM.INCLUDE [啟用 GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

接下來，您將使用此 API 金鑰值，讓通知中心能夠使用 GCM 進行驗證並代表您的應用程式傳送推播通知。

## <span id="configure-hub"></span></a>設定您的通知中心

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，並按一下畫面底部的 [+新增]。

2.  依序按一下 [App Services]、[服務匯流排]、[Notification Hub]、[快速建立]。

    ![][0]

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 [Create a new Notification Hub]。

    ![][1]

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 [設定]。

    ![][2]

5.  按一下頂端的 [Notification Hubs] 索引標籤，然後按一下您剛才建立的通知中心。

    ![][3]

6.  按一下頂端的 [設定] 索引標籤，輸入前一節中取得的 [API Key] 值，然後按一下 [儲存]。

    ![][4]

7.  選取頂端的 [儀表板] 索引標籤，然後按一下 [View Connection String]。記下這兩個連接字串。

現在已將您的通知中心設定成使用 GCM，而且您已擁有可用來註冊應用程式和傳送推播通知的連接字串。

## <span id="connecting-app"></span></a>將您的應用程式連接到通知中心

### 建立新的 Android 專案

1.  在 Eclipse ADT 中建立新的 Android 專案 ([檔案]、[新增]、[Android 應用程式])。

    ![][5]

2.  請確定 [Minimum Required SDK] 已設定為 *API 8:Android 2.2 (Froyo)*，且接下來的兩個 SDK 項目已設定為最新的可用版本。選擇 [下一步]，並依照精靈的指示進行，確定已勾選 [建立活動] 以建立空白活動。接受下一個方塊上預設的 [啟動器] 圖示，並按一下最後一個方塊中的 [完成]。

    ![][6]

### 新增 Google Play 服務至專案

[WACOM.INCLUDE [新增 Play 服務](../includes/mobile-services-add-Google-play-services.md)]

### 新增程式碼

1.  從[此處][行動服務 Android SDK]下載通知中心 Android SDK。解壓縮 .zip 檔案，並在 [封裝總管] 中將 notificationhubs\\notification-hubs-0.1.jar 檔案複製到專案的 \\libs 目錄。

2.  下載並解壓縮[行動服務 Android SDK][行動服務 Android SDK]，開啟 **notifications** 資料夾，將 **notifications-1.0.1.jar** 檔案複製到您 Eclipse 專案的 *libs* 資料夾，然後重新整理 *libs* 資料夾。

    <div class="dev-callout"><b>注意</b>
<p>檔案名稱結尾的數字在後續 SDK 版本中可能會變更。</p>
</div>

    現在，請設定應用程式以從 GCM 中取得 *registrationId*，並使用此值在通知中心註冊此應用程式執行個體。

3.  在 AndroidManifest.xml 檔案中，在 <uses-sdk></uses-sdk> 元素正下方新增下列程式碼行。請確定以您在步驟 1 中為應用程式所選取的封裝取代 `<your package>` (在此範例中為 `com.yourCompany.wams_notificationhubs`)。

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  在 **MainActivity** 類別中，新增下列陳述式。

        import android.os.AsyncTask;    
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  在類別頂端新增下列 Private 成員。

    <div class="dev-callout"><b>注意</b>
<p>請務必將 SENDER_ID 設定為稍早取得的專案編號。</p>
</div>

        private String SENDER_ID = "<your project number>";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;

6.  在 **OnCreate** 方法中新增下列程式碼，並確定以在上一節中取得且具備接聽存取權的連接字串，和出現在 Azure 中頁面頂端的通知中心名稱 (**非**完整 URL) 來取代中心的預留位置。

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

        gcm = GoogleCloudMessaging.getInstance(this);

        String connectionString = "<your listen access connection string>";
        hub = new NotificationHub("<your notification hub name>", connectionString, this);

        registerWithNotificationHubs();

7.  在 MainActivity.java 中建立下列方法：

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

8.  由於 Android 不會顯示通知，因此您必須撰寫自己的接收器。在 **AndroidManifest.xml** 中，在 `<application/>` 元素內新增下列元素。

    <div class="dev-callout"><b>注意</b>
<p>以您的封裝名稱取代預留位置。</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  在 Package Explorer 中，以滑鼠右鍵按一下封裝 (在 `src` 節點下)，按一下 [New]，按一下 [Class]。

10. 在 [名稱] 中，輸入 `MyHandler`；在然後按一下 [Superclass] 中輸入 `com.microsoft.windowsazure.notifications.NotificationsHandler`，然後按一下 [完成]。

    ![][7]

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

## <a name="send"></a>如何傳送通知至應用程式

您可以使用 [REST 介面][REST 介面]，從任何後端使用通知中樞傳送通知。在本教學課程中，您將透過 .NET 主控台應用程式來傳送通知。有關如何從與通知中樞整合的 Azure Mobile Services 傳送通知的範例，請參閱**開始在行動服務中使用推播通知** ([.NET 後端][.NET 後端] | [JavaScript 後端][.NET 後端])。如需如何使用 REST API 傳送通知的範例，請參閱**如何從 Java/PHP 使用通知中樞** ([Java][Java] | [PHP][PHP])。

1.  在 Visual Studio 中，從 [檔案] 功能表選取 [開新檔案]、[專案...]，然後按一下 [Visual C#] 下方的 [Windows] 和 [主控台應用程式]，再按一下 [確定]。

    ![][8]

    這會建立新的主控台應用程式專案。

2.  在 [工具] 功能表中，依序按一下 [Library Package Manager] 及 [Package Manager Console]。

    這會顯示 [Package Manager Console]。

3.  在主控台視窗中，執行下列命令：

        Install-Package WindowsAzure.ServiceBus

    這會使用 [WindowsAzure.ServiceBus NuGet 封裝][WindowsAzure.ServiceBus NuGet 封裝]，來新增 Azure 服務匯流排 SDK 的參考。

4.  開啟檔案 Program.cs，並新增下列 `using` 陳述式：

        using Microsoft.ServiceBus.Notifications;

5.  在 **Program** 類別中，新增下列方法：

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    請確保使用出現在入口網站 [Notification Hubs] 索引標籤上的通知中樞名稱，來取代 "hub name" 預留位置。此外，請使用您在＜設定您的通知中樞＞一節中取得之名為 **DefaultFullSharedAccessSignature** 的連接字串，來取代連接字串預留位置。

    > [WACOM.NOTE]請確定您會使用包含 [完整] 存取權 (而非 [接聽] 存取權) 的連接字串。接聽存取權字串不具備傳送通知的權限。

6.  接著在 **Main** 方法中新增下列程式碼行。

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>測試應用程式

在模擬器上測試應用程式前，您需要完成以下模擬器設定步驟 (使用實體裝置測試者可跳過)：

1.  請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

2.  從 [Window] 中按一下 [Android Virtual Device Manager]，選取您的裝置，然後按一下 [編輯]。

    ![][9]

3.  在 [Target] 中選取 [Google API]，然後按一下 [OK]。

    ![][10]

4.  若要接收推播通知，您必須在您的 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽到 [設定]，並按一下 [新增帳戶])。另外，確定模擬器已連線到網際網路。

請依照以下步驟在裝置或模擬器上執行應用程式：

1.  在 Eclipse 頂端工具列中，按一下 [執行]，然後選取您的應用程式。

    這會啟動模擬器 (如果您使用的是模擬器) 並載入和執行應用程式。應用程式將從 GCM 擷取 *registrationId*，並向通知中樞註冊。

2.  在 Visual Studio 按 F5 鍵以執行主控台應用程式。

    會傳送一則通知到您的應用程式。

3.  通知區域 (左上角) 出現圖示 時，下拉通知抽屜來查看通知。

    ![][11]

## <a name="next-steps"> </a>後續步驟

在此簡單範例中，您將廣播通知到您的所有 Android 裝置。若要以特定使用者為目標，請參閱教學課程[使用通知中心來推播通知給使用者][使用通知中心來推播通知給使用者]，在此同時，如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞][使用通知中心傳送即時新聞]。在[通知中心指引][通知中心指引]中深入了解如何使用通知中心。

<!-- Images. --> 
<!-- URLs. -->

  [啟用 Google 雲端通訊]: #register
  [設定您的通知中心]: #configure-hub
  [將您的應用程式連接到通知中心]: #connecting-app
  [如何傳送通知至應用程式]: #send
  [測試應用程式]: #run-app
  [此處]: http://go.microsoft.com/fwlink/?LinkId=389797
  [行動服務 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [啟用 GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [新增 Play 服務]: ../includes/mobile-services-add-Google-play-services.md
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [REST 介面]: http://msdn.microsoft.com/zh-TW/library/windowsazure/dn223264.aspx
  [.NET 後端]: /zh-TW/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /zh-TW/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /zh-TW/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet 封裝]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [使用通知中心來推播通知給使用者]: /zh-TW/manage/services/notification-hubs/notify-users-aspnet
  [使用通知中心傳送即時新聞]: /zh-TW/manage/services/notification-hubs/breaking-news-dotnet
  [通知中心指引]: http://msdn.microsoft.com/zh-TW/library/jj927170.aspx
