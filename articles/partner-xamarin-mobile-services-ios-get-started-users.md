<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" />

在行動服務中開始使用驗證
========================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務](#register)
2.  [對通過驗證的使用者限制資料表權限](#permissions)
3.  [將驗證新增至應用程式](#add-authentication)

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-xamarin-ios)。

完成本教學課程需使用 [Xamarin.iOS]、XCode 5.0 及 iOS 5.0 或更新版本。

註冊應用程式註冊應用程式以進行驗證和設定行動服務
------------------------------------------------

您必須向身分識別提供者註冊應用程式，才能驗證使用者。然後，您必須向行動服務註冊由提供者產生的用戶端密碼。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

	![](./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png)

2.  按一下 **[儀表板]** 索引標籤並記下 **[網站 URL]** 值。

	![](./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png)

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

	![](./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png)

現在，行動服務和應用程式已設定為使用您選擇的驗證提供者。

限制權限對通過驗證的使用者限制權限
----------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![](./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png)

2.  按一下 **[權限]** 索引標籤，將所有權限設定為 **[僅限通過驗證的使用者]**，然後按一下 **[儲存]**。這可確保針對 **TodoItem** 資料表所做的所有操作都需要一位通過驗證的使用者。這亦可簡化下一堂教學課程的指令碼，因為他們將無需容許匿名使用者的可能性。

	![](./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png)

3.  在 Xcode 中，開啟您完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-xamarin-ios)時建立的專案。

4.  按下 **[執行]** 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

	This happens because the app attempts to access Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

新增驗證將驗證新增至應用程式
----------------------------

1.  開啟 **TodoService** 專案檔案，並新增下列變數。

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  接著將名稱為 **Authenticate** 的新方法新增至 **TodoService**，定義如下：

         private async Task Authenticate(UIViewController view)
         {
             try
             {
                 user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
             }
             catch (Exception ex)
             {
                 Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
             }
         }

    **注意**

    如果您使用的身分識別提供者不是 Microsoft 帳戶，請將傳給上述 **LoginAsync** 的值變更為下列其中一個：*Facebook*、*Twitter* 或 *Google*。

3.  將 **TodoItem** 的要求從 **TodoService** 建構函式移至名稱為 **CreateTable** 的新方法中：

        private async Task CreateTable()
        {
        // Create an MSTable instance to allow us to work with the TodoItem table
        todoTable = client.GetTable<TodoItem>();
        }

4.  建立名稱為 **LoginAndGetData** 的新非同步公用方法，定義如下：

        public async Task LoginAndGetData(UIViewController view)
        {
        await Authenticate(view);
        await CreateTable();
        }

5.  在 **TodoListViewController** 中覆寫 **ViewDidAppear** 方法，並且按照下列方式定義該方法。如果 **TodoService** 在使用者上還沒有控點，如此會將使用者登入。

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO::show error
                return;
            } 
                    
            RefreshAsync();
        }

6.  從 **TodoListViewController.ViewDidLoad** 移除 **RefreshAsync** 的原始呼叫。

7.  按 **[執行]** 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式，然後使用您選擇的身分識別提供者登入。

	When you are successfully logged-in, the app should run without errors, and you should be able to query Mobile Services and make updates to data.

取得完成的範例
--------------

下載[完成的範例專案](http://go.microsoft.com/fwlink/p/?LinkId=331328)。務必以您自己的 Azure 設定更新 **applicationURL** 和 **applicationKey** 變數。

後續步驟
--------

在下一堂教學課程[使用指令碼授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios)中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。

