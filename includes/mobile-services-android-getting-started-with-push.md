1.  開啟檔案`AndroidManifest.xml`. 在下面兩個步驟的程式碼中，將 `**my_app_package**` 以專案的應用程式套件名稱 (即`package` 屬性值，位於`manifest` 標籤中) 取代。

2.  在現有`uses-permission` 元素後新增下列新權限：

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  在`application` 起始標籤後新增下列程式碼：

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  下載並解壓縮[行動服務 Android SDK][]，開啟 **notifications** 資料夾，將 **notifications-1.0.1.jar** 檔案複製到您 Eclipse 專案的 *libs* 資料夾，然後重新整理 *libs* 資料夾。

    <div class="dev-callout"><b>注意</b>
<p>檔案名稱結尾的數字在後續 SDK 版本中可能會變更。</p>
</div>

5.  開啟 *ToDoItemActivity.java* 檔案，新增下列 import 陳述式：

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  新增下列私用變數至類別，其中 *`<PROJECT_NUMBER>`* 是指先前程序中由 Google 指派給您應用程式的專案編號：

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  在 **onCreate** 方法中，在 MobileServiceClient 具現化之前，新增此程式碼來註冊裝置的通知處理常式：

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    稍後我們會定義此程式碼中參考的 MyHandler.class。

8.  在 [封裝總管] 中，以滑鼠右鍵按一下封裝 (在`src` 節點下)，按一下 [新增]，再按一下 [類別]。

9.  在 [名稱] 中輸入`MyHandler`，在 [超級類別] 中輸入`com.microsoft.windowsazure.notifications.NotificationsHandler`，然後按一下 [完成]

    ![][]

    如此即會建立新的 MyHandler 類別。

10. 為`MyHandler` 類別新增下列 import 陳述式：

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. 接著為`MyHandler` 類別新增下列成員：

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. 在`MyHandler` 類別中，新增下列程式碼，以覆寫 **onRegistered** 方法：(此方法會對行動服務通知中心註冊您的裝置)。

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. 在`MyHandler` 類別中，新增下列程式碼，以覆寫 **onReceive** 方法 (使用此方法的結果是在收到通知時隨即顯示)。

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);

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

您的應用程式現在已更新為支援推播通知。

<!-- URLs. -->

  [行動服務 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  []: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
