<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

在行動服務中開始使用驗證
========================

[Windows 市集 C#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

[觀看教學課程](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) [播放影片](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:42

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊應用程式以進行驗證和設定行動服務](#register)
2.  [對通過驗證的使用者限制資料表權限](#permissions)
3.  [將驗證新增至應用程式](#add-authentication)

本教學課程以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-android)。

完成本教學課程需要有 Eclipse 和 Android 4.2 或更新版本。

註冊應用程式註冊應用程式以進行驗證和設定行動服務
------------------------------------------------

您必須向身分識別提供者註冊應用程式，才能驗證使用者。然後，您必須向行動服務註冊由提供者產生的用戶端密碼。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

   	![](./media/mobile-services-android-get-started-users/mobile-services-selection.png)

2.  按一下 **[儀表板]** 索引標籤並記下 **[網站 URL]** 值。

   	![](./media/mobile-services-android-get-started-users/mobile-service-uri.png)

    註冊應用程式時，您可能需要提供此值給身分識別提供者。

3.  從下列清單中選擇支援的身分識別提供者，並依照步驟向該提供者註冊應用程式：

	-   [Microsoft 帳戶](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Facebook 登入](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Twitter 登入](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Google 登入](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

	    請記下提供者所產生的用戶端身分識別和密碼值。

	    **安全性注意事項**

	    提供者產生的密碼是很重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。

1.  回到管理入口網站，按一下 **[識別]** 索引標籤，輸入您從身分識別提供者取得的應用程式識別碼和共用密碼值，然後按一下 **[儲存]**。

   	![](./media/mobile-services-android-get-started-users/mobile-identity-tab.png)

現在，行動服務和應用程式已設定為使用您選擇的驗證提供者。

限制權限只將權限授與已驗證的使用者
----------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

   	![](./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png)

2.  按一下 **[權限]** 索引標籤，將所有權限設為 **[Only authenticated users]**，然後按一下 **[儲存]**。如此可確保 **TodoItem** 資料表上的所有作業只能由已驗證的使用者執行。這也簡化下一個教學課程中的指令碼，讓指令碼不需要顧慮可能有匿名使用者。

   	![](./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png)

3.  在 Eclipse 中，開啟您完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-android)時建立的專案。

4.  從 **[執行]** 功能表，按一下 **[執行]** 來啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

新增驗證將驗證新增至應用程式
----------------------------

1.  在 Eclipse 的 [套件瀏覽器] 中，開啟 ToDoActivity.java 檔案，並加入下列 import 陳述式。

         import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  將下列方法加入至 **ToDoActivity** 類別：

         private void authenticate() {
            
        // Login using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google,
        new UserAuthenticationCallback() {

        @Override
        public void onCompleted(MobileServiceUser user,
        Exception exception, ServiceFilterResponse response) {

        if (exception == null) {
        createAndShowDialog(String.format(
        "You are now logged in - %1$2s",
        user.getUserId()), "Success");
        createTable();
        } else {
        createAndShowDialog("You must log in. Login Required", "Error");
                             }
                         }
                     });
         }

    這會建立新的方法來處理驗證程序。使用者透過 Google 登入來驗證。將出現對話方塊來顯示已驗證的使用者的識別碼。必須通過驗證才能繼續。

    **注意**

    如果您使用的身分識別提供者不是 Google，請將傳給上述 **login** 方法的值變更為下列其中一個：*MicrosoftAccount*、*Facebook* 或 *Twitter*。

3.  在 **onCreate** 方法中，在具現化 `MobileServiceClient` 物件的程式碼後面加入下列這一行程式碼。

         authenticate();

    此呼叫會啟動驗證程序。

4.  將 **onCreate** 方法中的 `authenticate();` 後面的其餘程式碼移至新的 **createTable** 方法，如下所示：

         private void createTable() {

        // Get the Mobile Service Table instance to use
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from the Mobile Service
        refreshItemsFromTable();
         }

5.  從 **[執行]** 功能表，按一下 **[執行]** 來啟動應用程式，並以您選擇的身分識別提供者登入。

   	When you are successfully logged-in, the app should run without errors, and you should be able to query Mobile Services and make updates to data.

後續步驟
--------

在下一個教學課程[使用指令碼來授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android)中，您將根據驗證的使用者來取得行動服務所提供的使用者識別碼，並用來篩選行動服務所傳回的資料。

