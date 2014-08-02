<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

使用 Live Connect 單一登入驗證 Windows 市集應用程式
===================================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

本主題將示範如何使用 Live Connect 單一登入，從 Windows 市集應用程式中驗證 Azure 行動服務的使用者。在本教學課程中，您會使用 Live Connect 將驗證新增至快速入門專案。當 Live Connect 驗證成功時，便會顯示已登入使用者的名稱和使用者 ID 值來表示歡迎。

**注意**

本教學課程將示範使用由 Live Connect 為 Windows 市集應用程式所提供的單一登入體驗好處。這可使您更容易使用行動服務來驗證已登入使用者。如需支援多個驗證提供者的更為一般化驗證體驗，請參閱主題[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)。

本教學課程將逐步引導您完成這些啟用 Live Connect 驗證的基本步驟：

1.  [註冊應用程式以進行驗證和設定行動服務](#register)
2.  [對通過驗證的使用者限制資料表權限](#permissions)
3.  [將驗證新增至應用程式](#add-authentication)

本教學課程需要下列項目：

-   [Live SDK for Windows (英文)](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express for Windows 8 RC 或更新版本

本教學課程以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)。

註冊應用程式向 Windows 市集註冊應用程式
---------------------------------------

若要能夠驗證使用者，您必須將應用程式提交到 Windows 市集。然後必須註冊用戶端密碼，以將 Live Connect 與行動服務進行整合。

1.  如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面](http://go.microsoft.com/fwlink/p/?LinkID=266582)，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png)

2.  在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png)

     This creates a new Windows Store registration for your app.

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程時所建立的專案。

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **[市集]** 和 **[將應用程式與市集建立關聯...]**。

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png) 

   這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

1.  在此精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

2.  選取您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png)

     This adds the required Windows Store registration information to the application manifest.    

3.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png)

4.  按一下 **[儀表板]** 索引標籤並記下 **[網站 URL]** 值。

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png)

    您將使用此值來定義重新導向網域。

5.  瀏覽到 Live Connect 開發人員中心的[我的應用程式](http://go.microsoft.com/fwlink/p/?LinkId=262039)頁面，並在 **[我的應用程式]** 清單中按一下您的應用程式。

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png) 

6.  依序按一下 **[編輯設定]**、**[API 設定]**，並記下 **[用戶端 ID]** 和 **[用戶端密碼]** 的值。

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Security Note</b>
    <p>The client secret is an important security credential.Do not share the client secret with anyone or distribute it with your app.</p>
    </div>

1.  在 **[重新導向網域]** 中，輸入您行動服務的 URL (取自步驟 8)，然後按一下 **[儲存]**。

2.  回到管理入口網站，按一下 **[身分識別]** 索引標籤、輸入取自 Windows 市集中的 **[用戶端密碼]**，然後按一下 **[儲存]**。

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png)

您的行動服務和應用程式現已設定使用 Live Connect。

限制權限只將權限授與已驗證的使用者
----------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png)

2.  按一下 **[權限]** 索引標籤，將所有權限設為 **[Only authenticated users]**，然後按一下 **[儲存]**。如此可確保 **TodoItem** 資料表上的所有作業只能由已驗證的使用者執行。這也簡化下一個教學課程中的指令碼，讓指令碼不需要顧慮可能有匿名使用者。

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png)

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程時所建立的專案。

4.  按 F5 鍵以執行此快速入門架構的應用程式；驗證是否發生具備狀態碼 401 (未經授權) 的例外狀況。

    This happens because the app is accessing Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

接下來，您可以更新應用程式，以便在要求行動服務的資源之前先使用 Live Connect 來驗證使用者。

新增驗證將驗證新增至應用程式
----------------------------

1.  下載並安裝 [Live SDK for Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253) (英文)。

2.  在 Visual Studio 的 **[專案]** 功能表中，按一下 **[加入參考]**，然後展開 **[Windows]**，按一下 **[擴充功能]**，勾選 **[Live SDK]**，然後按一下 **[確定]**。

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png) 
 
   這會將 Live SDK 的參考加入專案中。

1.  開啟專案檔案 MainPage.xaml.cs，並新增下列 using 陳述式：

         using Microsoft.Live;        

2.  將下列程式碼片段新增至 MainPage 類別：

        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
         {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
             {
               // Force a logout to make it easier to test with multiple Microsoft Accounts
               if (liveIdClient.CanLogout)
               liveIdClient.Logout();

        LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
        if (result.Status == LiveConnectSessionStatus.Connected)
              {
                 session = result.Session;
                 LiveConnectClient client = new LiveConnectClient(result.Session);
                 LiveOperationResult meResult = await client.GetAsync("me");
                 MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                 string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                 var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                 var dialog = new MessageDialog(message, title);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }
             else
             {
                 session = null;
                 var dialog = new MessageDialog("You must log in.", "Login Required");
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
                 }
             }
          }

    這會建立儲存目前 Live Connect 工作階段的成員變數，以及處理驗證程序的方法。

    **注意**

    此程式碼會儘可能地強制登出，以確保每次執行應用程式時會提示使用者提供認證。這將有助於使用不同 Microsoft 帳戶測試應用程式，以確保驗證正常運作。只有當已登入使用者沒有已連線的 Microsoft 帳戶時，此機制才能運作。

3.  使用在 Live Connect 中設定應用程式時所指定的重新導向網域，來更新上一步的 *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>*，格式為 **https://*service-name*.azure-mobile.net/**。

    **注意**

    在 Windows 市集應用程式中，**LiveAuthClient** 類別的執行個體建立方式，是將重新導向網域 URI 值傳遞到類別建構函式。在 [Windows Phone 8 應用程式](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)中，您可透過傳遞用戶端 ID 來具現化相同類別。

4.  使用呼叫新 **Authenticate** 方法的處理常式，來取代現有的 **OnNavigatedTo** 事件處理常式：

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

5.  按 F5 鍵以執行此應用程式，並使用您的 Microsoft 帳戶登入 Live Connect。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

後續步驟
--------

在下一個教學課程[使用指令碼來授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)中，您將根據驗證的使用者來取得行動服務所提供的使用者識別碼，並用來篩選行動服務所傳回的資料。如需如何使用其他身分識別提供者進行驗證的相關資訊，請參閱[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)。您可以在[行動服務 .NET 作法概念參考資料](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)中深入了解如何使用搭配 .NET 的行動服務。

