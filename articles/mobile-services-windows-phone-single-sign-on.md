<properties 
	pageTitle="使用 Live Connect (Windows Phone) | 行動開發人員中心驗證您的應用程式" 
	description="了解如何從 Windows Phone 應用程式在 Azure 行動服務中使用 Live Connect 單一登入。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# 使用 Live Connect 單一登入驗證 Windows Phone 8 應用程式

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

本主題說明如何在 Windows Phone 8 應用程式的 Azure 行動服務中，使用 Live Connect 單一登入來驗證使用者。在本教學課程中，您會使用 Live Connect 將驗證新增至快速入門專案。經 Live Connect 成功驗證後，畫面會顯示名稱來歡迎登入使用者並顯示使用者識別碼。  

>[AZURE.NOTE]本教學課程將示範使用由適用於 Windows Phone 應用程式的 Live Connect 所提供之單一登入經驗的優點。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。如需支援多個驗證提供者的更一般化驗證經驗的詳細資訊，請參閱<a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users">將驗證新增至您的應用程式</a>主題。 

本教學課程會逐步引導您完成啟用 Live Connect 驗證的基本步驟：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增到應用程式]

本教學課程需要下列各項：

+ [Live SDK for Windows and Windows Phone]
+ Microsoft Visual Studio 2012 Express for Windows Phone
+ 您還必須先完成教學課程[新增行動服務至現有應用程式]。

<h2><a name="register"></a>使用 Live Connect 註冊您的應用程式</h2>

若要驗證使用者，您必須在 Live Connect 開發人員中心中註冊您的應用程式。接著您必須註冊用戶端密碼，以將 Live Connect 與行動服務進行整合。

1. 登入 [Azure 管理入口網站]，按一下 [**行動服務**]，然後按一下您的行動服務。

   	![][4]

2. 按一下 [**儀表板**] 索引標籤，並記下 [**網站 URL**] 值。

   	![][5]

    您將使用此值來定義重新導向網域。

3. 導覽至 Live Connect 開發人員中心的<a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">我的應用程式</a>頁面，並視需要使用您的 Microsoft 帳戶登入。 

4. 按一下 [**建立應用程式**]、然後輸入 [**應用程式名稱**]，並按一下 [**I accept**]。

   	![][1] 

   	這會向 Live Connect 註冊應用程式。

5. 依序按一下 [**應用程式設定**] 頁面、[**API 設定**]，並記下 [**用戶端識別碼**] 和 [**用戶端密碼**] 的值。 

   	![][2]

 > [AZURE.NOTE] **安全性注意事項**用戶端密碼是重要的安全性認證。請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。

6. 在 [**重新導向網域**] 中輸入步驟 2 中的行動服務 URL，按一下 [**行動用戶端應用程式**] 下的 [**是**]，然後按一下 [**儲存**]。

7. 回到管理入口網站，按一下 [**身分識別**] 索引標籤，輸入從 Live Connect 取得的 [**用戶端密碼**]，然後按一下 [**儲存**]。

   	![][13]

您的行動服務和您的應用程式現在都已設定成使用 Live Connect。

<h2><a name="permissions"></a>限制只有經驗證的使用者具有權限</h2>

1. 在管理入口網站中，按一下 [**資料**] 索引標籤，然後按一下 [**TodoItem**] 資料表。 

   	![][14]

2. 按一下 [**權限**] 索引標籤，將所有權限設定為 [**僅限通過驗證的使用者**]，然後按一下 [**儲存**]。這可確保針對 **TodoItem** 資料表所做的所有操作都需要一位通過驗證的使用者。這亦可簡化下一堂教學課程的指令碼，因為他們將無需容許匿名使用者的可能性。

   	![][15]

3. 在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成[將行動服務新增至現有應用程式]教學課程時所建立的專案。 

4. 按 F5 鍵執行此快速入門型應用程式；確認發生狀態代碼 401 (未經授權) 的例外狀況。 
   
   	因為應用程式以未驗證的使用者身分存取行動服務才會發生此情況，但 _TodoItem_ 資料表現在需要驗證。

接下來，您將更新應用程式，先使用 Live Connect 驗證使用者再向行動服務要求資源。

<h2><a name="add-authentication"></a>將驗證新增到應用程式</h2>

1. 下載並安裝 [Live SDK for Windows and Windows Phone]。

2. 在 Visual Studio 的 [**專案**] 功能表中，按一下 [**新增參考**]，然後展開 [**組件**]，按一下 [**擴充功能**]，勾選 [**Microsoft.Live**]，然後按一下 [**確定**]。 

   	![][16]

  	這會在專案中新增 Live SDK 的參考。

5. 開啟專案檔案 mainpage.xaml.cs，並新增下列 using 陳述式：

        using Microsoft.Live;      

6. 將下列程式碼片段新增至 MainPage 類別：
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

            while (session == null)
            {
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
                    MessageBox.Show(message, title, MessageBoxButton.OK);                    
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);                    
                }
            }
         }

    這會建立儲存目前 Live Connect 工作階段的成員變數，以及處理驗證程序的方法。

7. 使用向 Live Connect 註冊應用程式時所產生的用戶端識別碼值，來更新上一個步驟中的字串 _<< INSERT CLIENT ID HERE >>_。

    > [AZURE.NOTE] 在 Windows Phone 8 應用程式中，您可透過將此用戶端識別碼值傳遞至類別建構函式來建立 **LiveAuthClient** 類別的執行個體。在 [Windows 市集應用程式](/ zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/) 中，相同類別會藉由傳遞重新導向網域 URI 具現化。

8. 刪除或註解化現有的 **OnNavigatedTo** 方法，這將會以下列處理頁面上 **Loaded** 事件的方法覆寫和取代該現有方法。 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	此方法會呼叫新的 **Authenticate** 方法。 

9. 以下列程式碼取代 MainPage 建構函式：

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	此建構函式也會為 Loaded 事件註冊處理常式。
		
9. 按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入 Live Connect。 

   	當您成功登入之後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。如需有關如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證]。 

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增到應用程式]: #add-authentication
[後續步驟]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png

[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-per
	ms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows and Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[將行動服務新增至現有應用程式]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/
[新增行動服務至現有應用程式]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users
[使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/

[Azure 管理入口網站]: https://manage.windowsazure.com/


<!--HONumber=42-->
