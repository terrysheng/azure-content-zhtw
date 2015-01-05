<properties urlDisplayName="Authenticate with single sign-on" pageTitle="使用 Live Connect (JavaScript) 驗證應用程式" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure JavaScript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# 使用 Live Connect 單一登入驗證 Windows 市集應用程式
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript" class="current">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	


本主題說明如何從 Windows 市集應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用 Live Connect 將驗證新增至快速入門專案。經 Live Connect 成功驗證後，畫面會顯示名稱來歡迎登入使用者並顯示使用者識別碼。  

>[WACOM.NOTE]本教學課程將示範使用由 Live Connect 為 Windows 市集應用程式所提供的單一登入體驗好處。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。如需支援多個驗證提供者的更一般化驗證經驗的詳細資訊，請參閱 <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/">開始使用驗證主題</a>。

本教學課程會逐步引導您完成啟用 Live Connect 驗證的基本步驟：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增到應用程式]

本教學課程需要下列各項：

+ [Live SDK for Windows]
+ Microsoft Visual Studio 2012 Express for Windows 8 RC 或更新版本
+ 您還必須先完成[新增行動服務至現有應用程式]教學課程。

##<a name="register"></a>向 Windows 市集註冊應用程式

若要能夠驗證使用者，您必須將應用程式提交到 Windows 市集。接著您必須註冊用戶端密碼，以將 Live Connect 與行動服務進行整合。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成 <a href="/zh-tw/documentation/articles/mobile-services-windows-store-get-started">開始使用行動服務教學課程時所建立的專案</a>。</p></li> 
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

1. 下載並安裝 [Live SDK for Windows] (英文)。

2. 在 Visual Studio 的 [**專案**] 功能表中，按一下 [**加入參考**]，然後展開 [**Windows**]，再依序按一下 [**延伸模組**]、[**Live SDK**] 及 [**確定**]。 

  	![][16]

  	這會在專案中新增 Live SDK 的參考。

3. 開啟 default.html 專案檔案，並在 &lt;head&gt; 元素中新增下列 &lt;script&gt; 元素。 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	This enables Microsoft IntelliSense in the default.html file.


4. 開啟專案檔案 default.js，並在檔案頂端新增下列註解。 

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	This enables Microsoft IntelliSense in the default.js file.

5. 在 **app.OnActivated** 方法多載中，以下列程式碼取代 **refreshTodoItems** 方法的呼叫： 
	
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

    這會初始化 Live Connect 用戶端、強制登出、將新的登入要求傳送至 Live Connect、將傳回的驗證權杖傳送至行動服務，然後顯示登入使用者的相關資訊。此程式碼會儘可能地強制登出，以確保每次執行應用程式時會提示使用者提供認證。這將有助於使用不同 Microsoft 帳戶測試應用程式，以確保驗證正常運作。只有當已登入使用者沒有已連線的 Microsoft 帳戶時，此機制才能運作。

	>[WACOM.NOTE]您不應在每次執行應用程式時，都要求 Live Connection 驗證權杖或行動服務授權權杖。這麼做不只效率不彰，而且如果同時有許多用戶試圖啟動您的應用程式時，還可能遇到使用量相關的問題。更好的方法是快取權杖並先嘗試使用快取的行動服務權杖，然後再呼叫 **LoginWithMicrosoftAccountAsync**。如需快取此權杖的範例，請參閱[開始使用驗證](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens)
	
7. 使用在 Live Connect 中設定應用程式時所指定的重新導向網域，來更新上一步的 _<< INSERT REDIRECT DOMAIN HERE >>_ 字串，格式為 **https://_service-name_.azure-mobile.net/**。
		
8. 按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入 Live Connect。 

   	成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。如需有關如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證]。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[對通過驗證的使用者限制資料表權限]: #permissions
[將驗證新增到應用程式]: #add-authentication
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png

<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[新增行動服務至現有應用程式]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[使用指令碼來授權使用者]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/

[Azure 管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
