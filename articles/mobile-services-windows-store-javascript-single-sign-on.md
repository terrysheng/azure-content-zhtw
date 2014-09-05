<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

使用 Live Connect 單一登入驗證 Windows 市集應用程式
===================================================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

本主題說明如何從您的 Windows 市集應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用 Live Connect 將驗證新增至快速入門專案。在經過 Live Connect 成功驗證後，畫面會顯示名稱和使用者識別碼值來歡迎已登入使用者。

**注意**

本教學課程將示範使用由 Live Connect 為 Windows 市集應用程式所提供的單一登入體驗好處。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。如需支援多個驗證提供者的更一般化驗證經驗的詳細資訊，請參閱[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-js/)主題。

本教學課程會逐步引導您完成啟用 Live Connect 驗證的基本步驟：

1.  [註冊應用程式進行驗證，並設定行動服務](#register)
2.  [限制只有經驗證的使用者具有資料表的權限](#permissions)
3.  [新增驗證至應用程式](#add-authentication)

本教學課程需要下列各項：

-   [Live SDK for Windows (英文)](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express for Windows 8 RC 或更新版本

本教學課程是以行動服務快速入門為基礎。您也必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程。

註冊應用程式向 Windows 市集註冊應用程式
---------------------------------------

若要能夠驗證使用者，您必須將應用程式提交到 Windows 市集。接著您必須註冊用戶端密碼，以將 Live Connect 與行動服務進行整合。

1.  如果您尚未註冊您的應用程式，請瀏覽至 Windows 市集應用程式之開發人員中心的[提交應用程式頁面](http://go.microsoft.com/fwlink/p/?LinkID=266582)，使用您的 Microsoft 帳戶登入，然後按一下 **[應用程式名稱]**。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png)

2.  在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png)

	如此會為您的應用程式建立新的 Windows 市集註冊。

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程時所建立的專案。

4.  在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 **[市集]** 和 **[將應用程式與市集建立關聯...]**。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png)

	這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

5.  在此精靈中，按一下 **[登入]**，然後使用您的 Microsoft 帳戶登入。

6.  選取您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

 	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png)

	如此會將必要的 Windows 市集註冊資訊新增至應用程式資訊清單。    

7.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png)

8.  按一下 **[儀表板]** 索引標籤，並記下 **[網站 URL]** 值。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png)

    您將使用此值來定義重新導向網域。

9.  瀏覽到 Live Connect 開發人員中心的[我的應用程式](http://go.microsoft.com/fwlink/p/?LinkId=262039)頁面，並在 **[我的應用程式]** 清單中按一下您的應用程式。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png)

10.  依序按一下 **[編輯設定]**、**[API 設定]**，並記下 **[用戶端 ID]** 和 **[用戶端密碼]** 的值。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>安全性注意事項</b> <p>用戶端密碼是十分重要的安全性憑證。請不要將用戶端密碼與任何人分享，或用應用程式散佈</p> </div>

11.  在 **[重新導向網域]** 中，輸入您行動服務的 URL (取自步驟 8)，然後按一下 **[儲存]**。

12.  回到管理入口網站，按一下 **[身分識別]** 索引標籤、輸入取自 Windows 市集中的 **[用戶端密碼]**，然後按一下 **[儲存]**。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png)

您的行動服務和您的應用程式現在都已設定成使用 Live Connect。

限制權限限制只有經驗證使用者具有權限
------------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png)

2.  按一下 **[權限]** 索引標籤，將所有的權限設為 **[Only authenticated users]**，然後按一下 **[儲存]**。如此可確保對 **TodoItem** 資料表的所有操作都必須由經過驗證的使用者進行。如此也可簡化下一個教學課程中的指令碼，因為那些指令碼將不需要顧及有匿名使用者的可能性。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png)

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程時所建立的專案。

4.  按 F5 鍵執行此快速入門型應用程式；確認發生狀態代碼 401 (未經授權) 的例外狀況。

	因為應用程式以未驗證的使用者身分存取行動服務才會發生此情況，但 _TodoItem_ table 現在需要驗證。

接下來，您將更新應用程式，先使用 Live Connect 驗證使用者再向行動服務要求資源。

新增驗證新增驗證至應用程式
--------------------------

1.  下載並安裝 [Live SDK for Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253) (英文)。

2.  在 Visual Studio 的 **[專案]** 功能表中，按一下 **[加入參考]**，然後展開 **[Windows]**，按一下 **[擴充功能]**，勾選 **[Live SDK]**，然後按一下 **[確定]**。

	![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png)

	這會在專案中新增 Live SDK 的參照。

3.  開啟 default.html 專案檔案，並在 &lt;head\> 元素中新增下列 &lt;script\> 元素。

        <script src="///LiveSDKHTML/js/wl.js"></script>

	This enables Microsoft IntelliSense in the default.html file.

4.  開啟專案檔案 default.js，並在檔案頂端新增下列註解。

        /// <reference path="///LiveSDKHTML/js/wl.js" />

	如此會在 default.js 檔案中啟用 Microsoft IntelliSense。

5.  在 **app.OnActivated** 方法多載中，以下列程式碼取代 **refreshTodoItems** 方法的呼叫：

        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
                        complete();
                    }
                });
            });
        };                  

        var login = function () {
            return new WinJS.Promise(function (complete) {                    
                WL.login({ scope: "wl.basic"}).then(function (result) {
                    session = result.session;

                    WinJS.Promise.join([
                        WL.api({ path: "me", method: "GET" }),
                        client.login(result.session.authentication_token)
                    ]).done(function (results) {
                        var profile = results[0];
                        var mobileServicesUser = results[1];
                        refreshTodoItems();
                        var title = "Welcome " + profile.first_name + "!";
                        var message = "You are now logged in as: " + mobileServicesUser.userId;
                        var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                        dialog.showAsync().done(complete);                                
                    });                       
                }, function (error) {                        
                    session = null;
                    var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                    dialog.showAsync().done(complete);                        
                });
            });
        }

        var authenticate = function () {
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           
            
        authenticate();

    這會初始化 Live Connect 用戶端、強制登出、將新的登入要求傳送至 Live Connect、將傳回的驗證權杖傳送至行動服務，然後顯示登入使用者的相關資訊。

    **注意**

    此程式碼會儘可能地強制登出，以確保每次執行應用程式時會提示使用者提供認證。這將有助於使用不同 Microsoft 帳戶測試應用程式，以確保驗證正常運作。只有當已登入使用者沒有已連線的 Microsoft 帳戶時，此機制才能運作。

6.  使用在 Live Connect 中設定應用程式時所指定的重新導向網域，來更新上一步的 *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>*，格式為 **https://*service-name*.azure-mobile.net/**。

7.  按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入 Live Connect。

	當您成功登入之後，應該會執行應用程式且不會發生錯誤，而且您應該能夠查詢行動服務並更新資料。

後續步驟
--------

在下一個教學課程[以指令碼授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js)中，您將使用由行動服務根據經驗證的使用者而提供的使用者 ID 值，來篩選行動服務傳回的資料。如需關於如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證](/en-us/develop/mobile/tutorials/get-started-with-users-js)。

