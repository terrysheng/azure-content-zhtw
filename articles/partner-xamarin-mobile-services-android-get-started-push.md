<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.Android apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services" documentationCenter="Mobile" authors="" />

開始在行動服務中使用推播通知
============================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

本主題說明如何使用 Azure 行動服務傳送推播通知至 Xamarin.Android 應用程式。在本教學課程中，您會使用 Google 雲端通訊 (GCM) 服務將推播通知新增至快速入門專案。完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程會逐步引導您完成啟用推播通知的基本步驟：

1.  [針對推播通知註冊應用程式](#register)
2.  [設定行動服務](#configure)
3.  [新增推播通知至應用程式](#add-push)
4.  [更新指令碼來傳送推播通知](#update-scripts)
5.  [插入資料以接收通知](#test)

本教學課程需要下列各項：

-   有效的 Google 帳戶

本教學課程是以行動服務快速入門為基礎。開始此教學課程之前，您必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-xamarin-android)。

註冊應用程式註冊應用程式以取得推播通知
--------------------------------------

**注意**

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。

1.  瀏覽到 [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 網站，使用您的 Google 帳戶認證登入，然後按一下 **[建立專案...]**。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png)

    > [WACOM.NOTE] 如果擁有現有的專案，系統會在您登入後將引導您前往 **[儀表板]** 頁面。若要從 [儀表板] 建立新專案，請展開 **[API Project&lt;]**、按一下 **[Other projects]** 下的 **[建立...]**，然後輸入專案名稱並按一下 **[Create project]**。

2.  按一下左側欄中的 [概觀] 按鈕，然後記下 [儀表板] 區段中的專案號碼。

    在本教學課程稍後的內容中，您會將此值設定為用戶端中的 **PROJECT\_ID** 變數。

3.  在 [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 頁面中，按一下 **[Services]**、按一下切換開關以開啟 **[Google Cloud Messaging for Android]**，然後接受服務條款。

4.  依序按一下 **[API Access]** 和 **[Create new Server key...]**。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png)

5.  在 **[Configure Server Key for API Project]** 中按一下 **[Create]**。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png)

6.  記下 **[API key]** 的值。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png)

接下來，您將使用此 API 金鑰值，讓行動服務能夠使用 GCM 進行驗證並代表您的應用程式傳送推播通知。

## 設定服務設定行動服務來傳送推播要求

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的應用程式。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png)

2.  按一下 **[發送]** 索引標籤，輸入前一個程序中從 GCM 取得的 **[API Key]** 值，然後按一下 **[儲存]**。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png)

    您的行動服務現在已設定成使用 GCM 來傳送推播通知。

## 新增推播通知新增推播通知至應用程式

1.  首先，我們要將 **PushSharp** 新增為專案中的參考。若要這樣做，我們必須編譯最新版本的 PushSharp，並將編譯過的 DLL 新增為 Xamarin.Android 專案的參考。

2.  請造訪 [PushSharp Github 頁面](https://github.com/Redth/PushSharp) (英文) 並下載最新版本。將檔案集合解壓縮之後，請瀏覽至以下範例專案資料夾：

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. 接著開啟專案檔案：

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  在 **[Release]** 模式下組建 MonoForAndroid PushSharp 用戶端範例。

4.  在 Xamarin.Android 專案資料夾內建立 **\_external** 資料夾

5.  將以下檔案從 MonoForAndroid PushSharp 用戶端範例複製到 Xamarin.Android 專案資料夾內新建立的 **\_external** 資料夾：

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  在 Xamarin Studio (或 Visual Studio) 中開啟 Xamarin.Android 專案。

7.  在專案 **References** 資料夾上按一下滑鼠右鍵，然後選擇 **[Edit References...]**。

8.  前往 **[.Net Assembly]** 索引標籤、瀏覽至專案的 **\_external** folder、選取先前組建的 **PushSharp.Client.MonoForAndroid.dll**，然後按一下 **[新增]**。按一下 [確定] 以關閉對話方塊。

9.  開啟 **Constants.cs** 並新增以下文字行，將 **PROJECT\_ID** 取代為先前記下的 Google Project\_ID：

         public const string SenderID = "PROJECT_ID"; // Google API Project Number

10. 從 MonoForAndroid PushSharp 用戶端範例將 **PushService.cs** 檔案複製到 Xamarin.Android 專案資料夾，然後將它新增到專案中。

11. 變更 **PushService.cs** 使用的命名空間，使其符合專案的命名空間 (如XamarinTodoQuickStart)。

12. 變更 **PushService.cs** 中的 **SENDER\_IDS** 陣列，以參考先前建立的 **SenderID** 常數：

        public static string[] SENDER_IDS = new string[] { Constants.SenderID };

13. 將新的靜態屬性新增至 **PushService.cs** 中的 **PushHandlerService**，以便追蹤裝置註冊 ID：

        public static string RegistrationID { get; private set; }

14. 更新 **PushService.cs** 中的 **OnRegistered** 方法，以將接收到的註冊 ID 存放在本機靜態變數：

        protected override void OnRegistered(Context context, string registrationId)
        {
        Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered:" + registrationId);
        RegistrationID = registrationId;
        }

15. 更新 **PushService.cs** 中的 **OnMessage** 方法，以顯示隨通知接收到的推播訊息 (取代現有的 **createNotification** 呼叫)：

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item:" + message);

16. 請記住，依預設，**OnMessage** 方法具有以下程式碼，它能用來儲存最後接收到的推播訊息：

        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. 更新 **PushService.cs** 中的 **createNotification** 方法以參考 **TodoActivity** (而非 **DefaultActivity**)。

18. 開啟 **TodoActivity.cs** 並新增以下 using 陳述式：

        using PushSharp.Client;

19. 在 **TodoActivity.cs** 中，將以下文字行插入建立 **MobileServiceClient** 之位置的正上方：

        // Check to ensure everything's setup right
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Register for push notifications
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. 開啟 **TodoItem.cs** 並新增欄位，以便追蹤新增 TodoItem 之人員的已註冊裝置 ID：

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. 在 **TodoActivity.cs** 中，更新 **AddItem** 方法以將新建立之 **TodoItem** 的 **RegistrationID** 設定為在註冊期間接收到的裝置註冊 ID：

        // Create a new item
        var item = new TodoItem() {
        Text = textNewTodo.Text,
        Complete = false,
        RegistrationId = PushHandlerService.RegistrationID
        };

您的應用程式現在已更新為支援推播通知。

更新 insert 指令碼在管理入口網站中更新已註冊的 insert 指令碼
------------------------------------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  在 **[TodoItem]** 中，按一下 **[指令碼]** 索引標籤，然後選取 **[插入]**。

	![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png)

    This displays the function that is invoked when an insert occurs in the **TodoItem** table.

1.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

         function insert(item, user, request) {
        request.execute({
        success:function() {
        // Write to the response and then send the notification in the background
        request.respond();
        push.gcm.send(item.channel, item.text, {
        success:function(response) {
        console.log('Push notification sent:', response);
        }, error:function(error) {
        console.log('Error sending push notification:', error);
                         }
                     });
                 }
             });
         }

    如此即會註冊新的 insert 指令碼，該指令碼會使用 [gcm 物件](http://go.microsoft.com/fwlink/p/?LinkId=282645)將推播通知 (插入的文字) 傳送給插入要求中所提供的裝置。

測試應用程式在您的應用程式中測試推播通知
----------------------------------------

1.  執行應用程式並新增 Todo 項目。確認您收到有關新增之 Todo 項目的推播通知。

2.  在 Azure 管理入口網站中檢閱行動應用程式的 **[記錄檔]** 索引標籤，查看我們先前在 **[TodoItem]** 資料表中新增至 **Insert** 方法的記錄訊息。

3.  在 Azure 管理入口網站中檢閱 **[TodoItem]** 資料表，查看新增的 **[channel]** 欄是否含有唯一的裝置註冊識別碼。

您已成功完成此教學課程。

取得完成的範例
--------------

下載[完成的範例專案](http://go.microsoft.com/fwlink/p/?LinkId=331303)。請務必將 **ApplicationURL**、**ApplicationKey** 及 **SenderID** 等變數更新為您自己的 Azure 設定。

後續步驟
--------

在這個簡單範例中，使用者收到推播通知，其中含有剛插入的資料。在下一個教學課程[推播通知至應用程式使用者](/en-us/develop/mobile/tutorials/push-notifications-to-users-android)中，您將另行建立 Devices 資料表來儲存裝置權杖，並在發生插入動作時傳送推播通知給已儲存的所有通道。

