<properties urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="在 Xamarin iOS 應用程式中開始使用行動服務中的驗證 - Azure 行動服務" metaKeywords="" description="了解如何使用行動服務透過眾多識別提供者驗證 Xamarin iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# 在您的行動服務應用程式中新增驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。 

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

在本節中您將修改應用程式，以先顯示登入畫面再顯示資料。應用程式在啟動時將不會連接到您的行動服務，且不會顯示任何資料。在使用者第一次執行重新整理動作後，登入畫面將會出現；在成功登入後，將會顯示 todo 項目清單。

1. 在用戶端專案中開啟檔案 **QSTodoService.cs**，然後將下列宣告新增至 QSTodoService：

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. 使用下列定義，將新方法 **Authenticate** 新增至 **QSTodoService**：

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>注意</b>
	<p>如果您使用的身分識別提供者不是 Facebook，請將傳給上述 <strong>LoginAsync</strong> 的值變更為下列其中一個：<i>MicrosoftAccount</i>、<i>Twitter</i>、<i>Google</i> 或 <i>WindowsAzureActiveDirectory</i>。</p>
    </div>

3. 開啟 **QSTodoListViewController.cs**。修改 **ViewDidLoad** 的方法定義，以移除結尾附近的 **RefreshAsync()** 呼叫：

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;

			todoService.BusyUpdate += (bool busy) => {
				if (busy)
					activityIndicator.StartAnimating ();
				else 
					activityIndicator.StopAnimating ();
			};

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();

			AddRefreshControl ();
		}


4. 將 **RefreshAsync** 方法修改成當 [**User**] 屬性為 null 時進行驗證並顯示登入畫面。在方法定義最上方的下列程式碼上：

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. 按 [**執行**] 按鈕以建置專案，並在 iPhone 模擬器中啟動應用程式。確認應用程式未顯示資料。 

	將項目清單往下拉以執行重新整理動作，這會使登入畫面出現。在您成功輸入有效認證後，應用程式將會顯示 todo 項目清單，且您可以對資料進行更新。

<!-- ## <a name="next-steps"> </a>後續步驟

在下一個教學課程[行動服務使用者的伺服器端授權][使用指令碼授權使用者]中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，以篩選行動服務傳回的資料。 
 -->
 
<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[對通過驗證的使用者限制資料表權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[後續步驟]:#next-steps


<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push/
[使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript 和 HTML]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure 管理入口網站]: https://manage.windowsazure.com/
