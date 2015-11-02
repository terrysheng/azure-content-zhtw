<properties 
	pageTitle="開始在 Xamarin iOS 中使用行動應用程式的驗證" 
	description="了解如何使用行動應用程式透過眾多識別提供者驗證 Xamarin iOS 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="na" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/27/2015" 
	ms.author="mahender"/>

# 將驗證新增至 Xamarin.iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

本主題說明如何從用戶端應用程式驗證 App Service 行動應用程式的使用者。在本教學課程中，您將使用 App Service 支援的身分識別提供者，將驗證新增至快速入門專案。由行動應用程式成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以行動應用程式快速入門為基礎。您也必須先完成[建立 Xamarin.iOS 應用程式]教學課程。如果您不要使用下載的快速入門伺服器專案，必須將驗證擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

[AZURE.INCLUDE [app-service-mobile-to-web-and-api](../../includes/app-service-mobile-to-web-and-api.md)]

##<a name="create-gateway"></a>建立 App Service 閘道

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4.在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。失敗都會記錄到偵錯工具主控台。因此在 Visual Studio 中，您應該會在 [輸出] 視窗中看到失敗。

&nbsp;&nbsp;應用程式嘗試以未經驗證的使用者身分存取您的行動應用程式後端，因此發生此未經授權的失敗。*TodoItem* 資料表現在需要驗證。

接下來，您將會更新用戶端應用程式，利用已驗證的使用者身分來要求行動應用程式後端的資源。

##<a name="add-authentication"></a>將驗證新增至應用程式

在本節中您將修改應用程式，以先顯示登入畫面再顯示資料。應用程式在啟動時將不會連接到您的應用程式服務，且不會顯示任何資料。在使用者第一次執行重新整理動作後，登入畫面將會出現；在成功登入後，將會顯示 todo 項目清單。

1. 在用戶端專案中開啟檔案 **QSTodoService.cs**，並使用陳述式和成員宣告將下列程式碼加入 QSTodoService：


		// Logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. 針對 UIKit 加入 `using` 陳述式，並使用下列定義，將名為 **Authenticate** 的新方法加入 **QSTodoService**：

	```
		using UIKit;
	```

        public async Task Authenticate(UIViewController view)
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

	>[AZURE.NOTE]如果您使用的身分識別提供者不是 Facebook，請將傳給上述 **LoginAsync** 的值變更為下列其中一個：_MicrosoftAccount_、_Twitter_、_Google_ 或 _WindowsAzureActiveDirectory_。

3. 開啟 **QSTodoListViewController.cs**。修改 **ViewDidLoad** 的方法定義，以移除結尾附近的 **RefreshAsync()** 呼叫：

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. 修改要驗證的方法 **RefreshAsync**，並在 [使用者] 屬性為 null 時顯示登入畫面。在方法定義最上方的下列程式碼上：

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. 在 Visual Studio 或連線到您 Mac 上之 Xamarin 建置主機的 Xamarin Studio 中，以裝置或模擬器為目標執行用戶端專案。確認應用程式未顯示資料。

	將項目清單往下拉以執行重新整理動作，這會使登入畫面出現。在您成功輸入有效認證後，應用程式將會顯示 todo 項目清單，且您可以對資料進行更新。

 
<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[建立 Xamarin.iOS 應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Oct15_HO4-->