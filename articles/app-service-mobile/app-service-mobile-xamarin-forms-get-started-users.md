<properties 
	pageTitle="開始在 Xamarin.Forms 應用程式中使用 Mobile Apps 的驗證" 
	description="了解如何使用 Mobile Apps 透過眾多識別提供者驗證 Xamarin Forms 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="12/07/2015" 
	ms.author="wesmc"/>

# 將驗證加入 Xamarin.Forms 應用程式中

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

本主題說明如何從用戶端應用程式驗證 App Service 行動應用程式的使用者。在本教學課程中，您將使用 App Service 支援的識別提供者，將驗證加入 Xamarin.Forms 快速入門專案中。由行動應用程式成功驗證並授權之後，就會顯示使用者 ID 值，而您也將可以存取受限制的資料表資料。

您必須先完成 [Xamarin.Forms 快速入門教學課程](app-service-mobile-xamarin-forms-get-started.md)。如果您不要使用下載的快速入門伺服器專案，必須將驗證擴充套件新增至您的專案。如需伺服器擴充套件的詳細資訊，請參閱[使用 Azure 行動應用程式的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。


##註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##將驗證加入可攜式類別庫中 

Mobile Apps 使用平台特定 `MobileServiceClient.LoginAsync` 方法，以顯示登入介面和快取資料。為了驗證 Xamarin Forms 專案，您將在可攜式類別庫中定義 `IAuthenticate` 介面。您想要支援的每個平台都會在平台特定專案中實作這個介面。

您也會更新可攜式類別庫中定義的使用者介面，並新增登入按鈕。使用者必須按一下此按鈕，才能在應用程式啟動後進行驗證。

1. 在 Visual Studio 或 Xamarin Studio 中，從**可攜式**專案開啟 App.cs。將下列 `using` 陳述式加入檔案中。

		using System.Threading.Tasks;

2. 在 App.cs 中，在 `App` 類別定義之前緊接著加入下列 `IAuthenticate` 介面定義。

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. 在 App.cs 中，加入下列靜態成員，以透過平台特定實作初始化介面。

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...


4. 從**可攜式**專案開啟 TodoList.xaml.cs。將下列旗標加入至 `TodoList` 類別，表示使用者是否已驗證。

        bool authenticated = false;


5. 在 TodoList.xaml.cs 中更新 `OnAppearing` 方法，如此您只會在使用者驗證後重新整理項目。


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

6. 在 TodoList.xaml.cs 中，於 `TodoList` 類別的建構函式頂端定義下列登入按鈕並按一下處理常式...

        public TodoList()
        {
            InitializeComponent();

            manager = TodoItemManager.DefaultManager;

            var loginButton = new Button
            {
                Text = "Login",
                TextColor = Xamarin.Forms.Color.Black,
                BackgroundColor = Xamarin.Forms.Color.Lime,
            };
            loginButton.Clicked += loginButton_Clicked;

            Xamarin.Forms.StackLayout bp = buttonsPanel as StackLayout;
            Xamarin.Forms.StackLayout bpParentStack = bp.Parent.Parent as StackLayout;

            bpParentStack.Padding = new Xamarin.Forms.Thickness(10, 30, 10, 20);
            bp.Orientation = StackOrientation.Vertical;
            bp.Children.Add(loginButton);

			...

7. 在 TodoList.xaml.cs 中，針對登入按鈕按一下事件加入下列處理常式

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }


8. 儲存變更，並建置可攜式類別庫專案以驗證沒有錯誤。


##將驗證加入 Android 應用程式中

在本節中，您將加入 droid 專案的驗證。如果未使用 Android 裝置，可以略過這一節。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下 **droid** 專案，然後按一下 [設定為啟始專案]。

2. 直接在偵錯工具中執行專案，以確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。由於您僅限授權使用者存取後端，因此會發生這個例外狀況。

3. 接下來，開啟 droid 專案中的 MainActivity.cs，然後加入下列 `using` 陳述式。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. 更新 `MainActivity` 類別以實作 `IAuthenticate` 介面。

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. 藉由加入以下所示的 `MobileServiceUser` 欄位和 `Authenticate` 方法來更新 `MainActivity` 類別，以支援 `IAuthenticate` 介面。
 
	如果您想要使用不同的 `MobileServiceAuthenticationProvider` 而不是 Facebook，請一併變更。

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage(message);
            builder.SetTitle(title);
            builder.Create().Show();
        }


6. 更新 `MainActivity` 類別的 `OnCreate` 方法以初始化驗證器，再載入應用程式。

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. 重新建置並執行應用程式。以您選擇的驗證提供者進行登入，並確認您是否能夠以驗證使用者身分存取資料表。



##將驗證加入 iOS 應用程式中

在本節中，您將加入 iOS 專案的驗證。如果未使用 iOS 裝置，可以略過這一節。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下 **iOS** 專案，然後按一下 [設定為啟始專案]。

2. 直接在偵錯工具中執行專案，以確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。由於您僅限授權使用者存取後端，因此會發生這個例外狀況。

3. 接下來，開啟 iOS 專案中的 AppDelegate.cs，然後加入下列 `using` 陳述式。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. 更新 `AppDelegate` 類別以實作 `IAuthenticate` 介面。

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. 藉由加入以下所示的 `MobileServiceUser` 欄位和 `Authenticate` 方法來更新 `AppDelegate` 類別，以支援 `IAuthenticate` 介面。
 
	如果您想要使用不同的 `MobileServiceAuthenticationProvider` 而不是 Facebook，請一併變更。

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        UIAlertView avAlert = new UIAlertView("Authentication", "You are now logged in " + user.UserId, null, "OK", null);
                        avAlert.Show();
                    }
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. 更新 `AppDelegate` 類別的 `FinishedLaunching` 方法以初始化驗證器，再載入應用程式。

        App.Init(this);

		LoadApplication (new App ());



7. 重新建置並執行應用程式。以您選擇的驗證提供者進行登入，並確認您是否能夠以驗證使用者身分存取資料表。




##將驗證加入 Windows 應用程式中

在本節中，您將加入 WinApp 專案的驗證。如果未使用 Windows 裝置，可以略過這一節。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **WinApp** 專案，然後按一下 [設定為啟始專案]。

2. 直接在偵錯工具中執行專案，以確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。由於您僅限授權使用者存取後端，因此會發生這個例外狀況。

3. 接下來，開啟 WinApp 專案中的 MainPage.xaml.cs，然後加入下列 `using` 陳述式。以您的可攜式類別庫命名空間取代 <*您的可攜式類別庫命名空間*>。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. 更新 `MainPage` 類別以實作 `IAuthenticate` 介面。

	    public sealed partial class MainPage : IAuthenticate


5. 藉由加入以下所示的 `MobileServiceUser` 欄位和 `Authenticate` 方法來更新 `MainPage` 類別，以支援 `IAuthenticate` 介面。
 
	如果您想要使用不同的 `MobileServiceAuthenticationProvider` 而不是 Facebook，請一併變更。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. 更新 `MainPage` 類別的建構函式以初始化驗證器，再載入應用程式。　以您的可攜式類別庫命名空間取代 <*您的可攜式類別庫命名空間*>。

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init(this);
            
            LoadApplication(new <Your portable class library namespace>.App());
        }



7. 重新建置並執行應用程式。以您選擇的驗證提供者進行登入，並確認您是否能夠以驗證使用者身分存取資料表。


##將驗證加入 Windows Phone 8.1 應用程式中

在本節中，您將加入 WinPhone81 專案的驗證。如果未使用 Windows Phone 8.1 裝置，可以略過這一節。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **WinPhone81** 專案，然後按一下 [設定為啟始專案]。

2. 直接在偵錯工具中執行專案，以確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。由於您僅限授權使用者存取後端，因此會發生這個例外狀況。


3. 接下來，開啟 WinPhone81 專案中的 MainPage.xaml.cs，然後加入下列 `using` 陳述式。以您的可攜式類別庫命名空間取代 <*您的可攜式類別庫命名空間*>。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. 更新 `MainPage` 類別以實作 `IAuthenticate` 介面。

	    public sealed partial class MainPage : IAuthenticate


5. 藉由加入以下所示的 `MobileServiceUser` 欄位和 `Authenticate` 方法來更新 `MainPage` 類別，以支援 `IAuthenticate` 介面。
 
	如果您想要使用不同的 `MobileServiceAuthenticationProvider` 而不是 Facebook，請一併變更。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. 更新 `MainPage` 類別的建構函式以初始化驗證器，再載入應用程式。　以您的可攜式類別庫命名空間取代 <*您的可攜式類別庫命名空間*>。

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }

7. 在 Windows Phone 上，您需要另外完成登入。開啟 App.xaml.cs 檔案，並將下列 `using` 陳述式和程式碼加入至 `App` 類別中的 `OnActivated` 處理常式。

	```
		using Microsoft.WindowsAzure.MobileServices;
	```

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);
		
		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}



8. 重新建置並執行應用程式。以您選擇的驗證提供者進行登入，並確認您是否能夠以驗證使用者身分存取資料表。

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1210_2015--->