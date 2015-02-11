<properties urlDisplayName="Authenticate with single sign-on" pageTitle="使用 Live Connect 驗證 Windows 市集應用程式" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="了解如何從 Windows 市集應用程式在 Azure 行動服務中使用 Live Connect 單一登入。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# 使用 Live Connect 單一登入驗證 Windows 市集應用程式
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#" class="current">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	

本主題說明如何在 Windows 市集或 Windows Phone 8.1 市集應用程式的 Azure 行動服務中，使用 Live Connect 單一登入來驗證使用者。在本教學課程中，您會使用 Live Connect 將驗證新增至快速入門專案。在經過 Live Connect 成功驗證後，畫面會顯示名稱和使用者識別碼值來歡迎已登入使用者。  

>[WACOM.NOTE]本教學課程將示範使用由 Live Connect 為 Windows 市集應用程式所提供的單一登入體驗好處。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。如需支援多個驗證提供者的更一般化驗證經驗的詳細資訊，請參閱 <a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/">「開始使用驗證」主題</a>。 

本教學課程會逐步引導您完成啟用 Live Connect 驗證的基本步驟：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增到應用程式]

本教學課程需要下列各項：

+ [Live SDK for Windows]
+ Microsoft Visual Studio 2012 Express for Windows 8 RC 或更新版本
+ 您還必須先完成教學課程[新增行動服務至現有應用程式]。

##<a name="register"></a>向 Windows 市集註冊應用程式

若要能夠驗證使用者，您必須將應用程式提交到 Windows 市集。接著您必須註冊用戶端密碼，以將 Live Connect 與行動服務進行整合。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成 <a href="/zh-tw/documentation/articles/mobile-services-windows-store-get-started">「開始使用行動服務」教學課程時建立的專案</a>。</p></li> 
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

1. 下載並安裝 [Live SDK for Windows] (英文)。

2. 在 Visual Studio 的 [**專案**] 功能表中，按一下 [**加入參考**]，然後展開 [**Windows**]，再依序按一下 [**延伸模組**]、[**Live SDK**] 及 [**確定**]。 

  	![][16]

  	這會在專案中新增 Live SDK 的參考。

5. 開啟專案檔案 MainPage.xaml.cs，並新增下列 using 陳述式：

        using Microsoft.Live;        

6. 將下列程式碼片段新增至 MainPage 類別：
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
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

    這會建立儲存目前 Live Connect 工作階段的成員變數，以及處理驗證程序的方法。此程式碼會儘可能地強制登出，以確保每次執行應用程式時會提示使用者提供認證。這將有助於使用不同 Microsoft 帳戶測試應用程式，以確保驗證正常運作。只有當已登入使用者沒有已連線的 Microsoft 帳戶時，此機制才能運作。 

	>[WACOM.NOTE]您不應在每次執行應用程式時，都要求 Live Connection 驗證權杖或行動服務授權權杖。這麼做不只效率不彰，而且如果同時有許多用戶試圖啟動您的應用程式時，還可能遇到使用量相關的問題。更好的方法是快取權杖並先嘗試使用快取的行動服務權杖，然後再呼叫 **LoginWithMicrosoftAccountAsync**。如需快取此權杖的範例，請參閱[開始使用驗證](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens)
	

7. 使用在 Live Connect 中設定應用程式時所指定的重新導向網域，來更新上一步的 _<< INSERT REDIRECT DOMAIN HERE >>_ 字串，格式為 **https://_service-name_.azure-mobile.net/**。

    <div class="dev-callout"><b>注意</b>
	<p>在 Windows 市集應用程式中，可藉由將重新導向網域 URI 值傳遞至類別建構函式來建立 <strong>LiveAuthClient</strong> 類別的執行個體。在 <a href="/zh-tw/develop/mobile/tutorials/single-sign-on-wp8/">Windows Phone 8 應用程式中，</a>可按下用戶端識別碼來具現化相同的類別。</p>
    </div>

8. 使用呼叫新**驗證**方法的處理常式，來取代現有的 **OnNavigatedTo** 事件處理常式：

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
9. 按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入 Live Connect。 

   成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。如需關於如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證]。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[對通過驗證的使用者限制資料表權限]: #permissions
[將驗證新增到應用程式]: #add-authentication
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png

<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[新增行動服務至現有應用程式]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念參考資料]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=35.1-->
