<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

開始使用通知中心
================

[Windows 市集 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 市集 C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

本主題說明如何使用 Azure 通知中心傳送推播通知至 Android 應用程式。在本教學課程中，您將建立可使用 Google 雲端通訊 (GCM) 接收推播通知的空白 Android 應用程式。完成時，您便能夠使用通知中心，將推播通知廣播到所有正在執行您應用程式的裝置。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

-   [啟用 Google 雲端通訊](#register)
-   [設定您的通知中心](#configure-hub)
-   [將您的應用程式連接到通知中心](#connecting-app)
-   [如何傳送通知至應用程式](#send)
-   [測試應用程式](#run-app)

本教學課程將示範使用通知中心的簡單廣播案例。請確定依照下一個教學課程的步驟進行，以查看如何使用通知中心來處理特定使用者和裝置群組。

本教學課程需要下列各項：

-   Android SDK (假設您將使用 Eclipse)，您可以從[此處](http://go.microsoft.com/fwlink/?LinkId=389797)下載。
-   [行動服務 Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)

完成本教學課程是 Android 應用程式所有其他通知中心教學課程的先決條件。

**注意**

若要完成此教學課程，您必須具備有效的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)。

啟用 Google 雲端通訊
--------------------

[WACOM.INCLUDE [啟用 GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

接下來，您將使用此 API 金鑰值，讓通知中心能夠使用 GCM 進行驗證並代表您的應用程式傳送推播通知。

設定您的通知中心
----------------

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，然後按一下畫面底部的 **[+NEW]**。

2.  依序按一下 **[App Services]**、**[服務匯流排]**、**[Notification Hub]**、**[快速建立]**。

	![][7]

3.  為您的通知中心輸入名稱、選取所需的區域，然後按一下 **[Create a new Notification Hub]**。

	![][8]

4.  按一下您剛才建立的命名空間 (通常為 ***notification hub name*-ns**)，然後按一下頂端的 **[設定]**。

	![][9]

5.  按一下頂端的 **[Notification Hubs]** 索引標籤，然後按一下您剛才建立的通知中心。

	![][10]

6.  按一下頂端的 **[設定]** 索引標籤，輸入上一節中取得的 **[API Key]** 值，然後按一下 **[儲存]**。

	![][11]

7.  選取頂端的 **[儀表板]** 索引標籤，然後按一下 **[View Connection String]**。記下這兩個連接字串。

現在已將您的通知中心設定成使用 GCM，而且您已擁有可用來註冊應用程式和傳送推播通知的連接字串。

將您的應用程式連接到通知中心
----------------------------

### 建立新的 Android 專案

1.  在 Eclipse ADT 中建立新的 Android 專案 ([檔案]、[新增]、[Android 應用程式])。

	![][13]

2.  請確定 **[Minimum Required SDK]** 已設定為 *API 8:Android 2.2 (Froyo)*，且接下來的兩個 SDK 項目已設定為最新的可用版本。選擇 [下一步]，並依照精靈的指示進行，確定已勾選 **[建立活動]** 以建立空白活動。接受下一個方塊上預設的 [啟動器] 圖示，並按一下最後一個方塊中的 **[完成]**。

	![][14]

### 新增 Google Play 服務至專案

[WACOM.INCLUDE [新增 Play 服務](../includes/mobile-services-add-Google-play-services.md)]

### 新增程式碼

1.  從[此處](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)下載通知中心 Android SDK。解壓縮 .zip 檔案，並在 [封裝總管] 中將 notificationhubs\\notification-hubs-0.1.jar 檔案複製到專案的 \\libs 目錄。

2.  下載並解壓縮[行動服務 Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)，開啟 **notifications** 資料夾，將 **notifications-1.0.1.jar** 檔案複製到您 Eclipse 專案的 *libs* 資料夾，然後重新整理 *libs* 資料夾。

    **注意**

    檔案名稱結尾的數字在後續 SDK 版本中可能會變更。

    現在，請設定應用程式以從 GCM 中取得 *registrationId*，並使用此值在通知中心註冊此應用程式執行個體。

3.  在 AndroidManifest.xml 檔案中，在 &lt;uses-sdk/\> 元素正下方新增下列程式碼行。請確定以您在步驟 1 中為應用程式所選取的封裝取代 `<your package>` (在此範例中為 `com.yourCompany.wams_notificationhubs`)。

         <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  在 **MainActivity** 類別中，新增下列陳述式。

         import android.os.AsyncTask;    
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  在類別頂端新增下列 Private 成員。

    **注意**

    請務必將 SENDER\_ID 設定為稍早取得的專案編號。

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

    **注意**

    以您的封裝名稱取代預留位置。

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <category android:name="**my_app_package**" />
        </intent-filter>
        </receiver>

9.  在 Package Explorer 中，以滑鼠右鍵按一下封裝 (在 [src]`` 節點下)，按一下 **[New]**，按一下 **[Class]**。

10. 在 **[Name]** 輸入 `MyHandler`，在 **[Superclass]** 輸入 `com.microsoft.windowsazure.notifications.NotificationsHandler`，然後按一下 **[Finish]**

    ![](./media/notification-hubs-android-get-started/notification-hub-android-new-class.png)

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

如何傳送通知至應用程式
----------------------

您可以從任何使用 [REST 介面](http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223264.aspx)的後端中，透過通知中心來傳送通知。在本教學課程中，我們將示範兩種傳送通知的方式：透過 .NET 主控台應用程式，及透過使用節點指令碼的行動服務。

### 使用 .NET 主控台應用程式傳送通知：

1.  建立新的 Visual C\# 主控台應用程式：

	![][20]

2.  使用 [WindowsAzure.ServiceBus NuGet 封裝](http://nuget.org/packages/WindowsAzure.ServiceBus/)來新增 Azure 服務匯流排 SDK 的參考。在 Visual Studio 主功能表中，依序按一下 **[工具]**、**[Library Package Manager]**、**[Package Manager Console]**。接著，在主控台視窗中輸入：

         Install-Package WindowsAzure.ServiceBus

    並按 Enter 鍵。

3.  開啟 Program.cs 檔案，並新增下列 using 陳述式：

         using Microsoft.ServiceBus.Notifications;

4.  在 `Program` 類別中，新增下列方法：

         private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{  private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{ \"data\" :{\"msg\":\"Hello from Azure!\"}}");
         }
        quot;data private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{ \"data\" :{\"msg\":\"Hello from Azure!\"}}");
         }
        quot; :{ private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{ \"data\" :{\"msg\":\"Hello from Azure!\"}}");
         }
        quot;msg private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{ \"data\" :{\"msg\":\"Hello from Azure!\"}}");
         }
        quot;: private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{ \"data\" :{\"msg\":\"Hello from Azure!\"}}");
         }
        quot;Hello from Azure! private static async void SendNotificationAsync()
         {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
        await hub.SendGcmNativeNotificationAsync("{ \"data\" :{\"msg\":\"Hello from Azure!\"}}");
         }
        quot;}}");
         }

5.  接著在您的 Main 方法中新增下列程式碼行：

          SendNotificationAsync();
        Console.ReadLine();

### 使用行動服務傳送通知

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，並選取您的行動服務。如果您還沒有行動服務，請依照[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/#create-new-service)的步驟進行。

2.  選取頂端的 **[排程器]** 索引標籤。

	![][22]

3.  建立新的排定工作、插入名稱，並選取 **[隨選]**。

	![][23]

4.  在工作建立之後，按一下此工作名稱。然後按一下頂列中的 **[指令碼]** 索引標籤。

5.  將下列指令碼插入您的排程器函數內。請確定使用您的通知中心名稱和稍早取得的 *DefaultFullSharedAccessSignature* 連接字串，來取代預留位置。按一下 **[儲存]**。

         var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"msg" :"Hello from Mobile Services!"}}',

		function (error) {

             if (!error) {
        console.warn("Notification successful");
             }
        else
             {
        console.warn("Notification failed" + error);
             }
           }
         );

測試應用程式
------------

若要使用真正的電話來測試應用程式，您只需使用 USB 纜線來將它連接到電腦即可。

若要使用模擬器測試應用程式：

1.  請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

2.  從 **[Window]** 中按一下 **[Android Virtual Device Manager]**，選取您的裝置，然後按一下 **[編輯]**。

	![][18]

3.  在 **[目標]** 中選取 **[Google API]**，然後按一下 **[確定]**。

	![][19]

4.  若要接收推播通知，您必須在您的 Android 虛擬裝置上設定 Google 帳戶 (在模擬器中，瀏覽到 **[設定]**，並按一下 **[新增帳戶]**)。另外，請確定模擬器已連線到網際網路。

無論您選擇哪個裝置，接下來請執行下列動作：

1.  在 Eclipse 頂端工具列中，按一下 **[執行]**，然後選取您的應用程式。這會將您的應用程式載入連接的電話中，或者啟動模擬器的位置，然後載入並執行此應用程式。

2.  應用程式會從 GCM 中擷取 *registrationId*，並在通知中心註冊此應用程式。

3.  現在，請使用上一節中的其中一種方法來傳送通知至您的應用程式：

    -   如果您打算使用 .Net 主控台應用程式，請在 Visual Studio 中按 F5 鍵以執行將傳送通知的應用程式。
    -   否則，如果您打算使用行動服務指令碼，請按一下行動服務畫面底列上的 **[執行一次]**，指令碼便會傳送通知。

4.  通知區域 (左上角) 中即會出現一個圖示。下拉通知抽屜來查看通知。

	![][21]

後續步驟
--------

在此簡單範例中，您會將通知廣播到您的所有 Android 裝置。為了鎖定特定使用者，請參閱[使用通知中心來推播通知給使用者](/en-us/manage/services/notification-hubs/notify-users-aspnet)教學課程，同時如果您想要按興趣群組分隔使用者，您可以參閱[使用通知中心傳送即時新聞](/en-us/manage/services/notification-hubs/breaking-news-dotnet)。若要深入了解如何使用通知中心，請參閱 [Azure 通知中心概觀](http://msdn.microsoft.com/zh-tw/library/jj927170.aspx)和[Android 的通知中心作法](http://msdn.microsoft.com/zh-tw/library/dn282661.aspx)。

<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
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
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/zh-tw/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/zh-tw/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet