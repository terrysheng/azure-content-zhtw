<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 使用 Live Connect 單一登入驗證 Windows Phone 8 應用程式

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

本主題說明如何在 Windows Phone 8 應用程式的 Azure 行動服務中，使用 Live Connect 單一登入來驗證使用者。在本教學課程中，您會使用 Live Connect 將驗證新增至快速入門專案。在經過 Live Connect 成功驗證後，畫面會顯示名稱和使用者識別碼值來歡迎已登入使用者。

<div class="dev-callout"><b>注意</b>
<p>本教學課程將示範使用由適用於 Windows Phone 應用程式的 Live Connect 所提供之單一登入經驗的優點。這可讓您更輕鬆地使用行動服務來驗證已登入使用者。如需支援多個驗證提供者的更一般化驗證經驗的詳細資訊，請參閱<a href="/zh-tw/develop/mobile/tutorials/get-started-with-users-wp8/">開始使用驗證</a>主題。 </p>
</div>

本教學課程會逐步引導您完成啟用 Live Connect 驗證的基本步驟：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]

本教學課程需要下列各項：

-   [Live SDK for Windows and Windows Phone][]
-   Microsoft Visual Studio 2012 Express for Windows Phone

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

## <a name="register"></a><span class="short-header">註冊應用程式</span>向 Live Connect 註冊應用程式

若要驗證使用者，您必須在 Live Connect 開發人員中心中註冊您的應用程式。接著您必須註冊用戶端密碼，以將 Live Connect 與行動服務進行整合。

1.  登入 [Azure 管理入口網站][]，按一下 [行動服務]，然後按一下您的行動服務。

    ![][0]

2.  按一下 [儀表板] 索引標籤並記下 [網站 URL] 值。

    ![][1]

    您將使用此值來定義重新導向網域。

3.  瀏覽到 Live Connect 開發人員中心的[我的應用程式][]頁面，並視需要使用您的 Microsoft 帳戶登入。

4.  按一下 [建立應用程式]、然後輸入 [應用程式名稱]，並按一下 [I accept]。

    ![][2]

    這會向 Live Connect 註冊應用程式。

5.  依序按一下 [應用程式設定] 頁面、[API 設定]，並記下 [用戶端識別碼] 和 [用戶端密碼] 的值。

    ![][3]

    <div class="dev-callout"><b>安全性注意事項</b>
<p>用戶端密碼是重要的安全性認證。請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。</p>
</div>

6.  在 [重新導向網域] 中輸入步驟 2 中的行動服務 URL，按一下 [行動用戶端應用程式] 下的 [是]，然後按一下 [儲存]。

7.  回到管理入口網站，按一下 [身分識別] 索引標籤，輸入從 Live Connect 取得的 [用戶端密碼]，然後按一下 [儲存]。

    ![][4]

您的行動服務和您的應用程式現在都已設定成使用 Live Connect。

## <a name="permissions"></a><span class="short-header">限制權限</span>對通過驗證的使用者限制權限

1.  在管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][5]

2.  按一下 [權限] 索引標籤，將所有權限設定為 [僅限通過驗證的使用者]，然後按一下 [儲存]。這可確保針對 **TodoItem** 資料表所做的所有操作都需要一位通過驗證的使用者。這亦可簡化下一堂教學課程的指令碼，因為他們將無需容許匿名使用者的可能性。

    ![][6]

3.  在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成[開始使用行動服務][]教學課程時所建立的專案。

4.  按 F5 鍵執行此快速入門型應用程式；確認發生狀態代碼 401 (未經授權) 的例外狀況。

    因為應用程式以未驗證的使用者身分存取行動服務才會發生此情況，但 *TodoItem* 資料表現在需要驗證。

接下來，您將更新應用程式，先使用 Live Connect 驗證使用者再向行動服務要求資源。

## <a name="add-authentication"></a><span class="short-header">新增驗證</span>將驗證新增至應用程式

1.  下載並安裝 [Live SDK for Windows and Windows Phone][]。

2.  在 Visual Studio 的 [專案] 功能表中，按一下 [加入參考]，然後展開 [組件]，按一下 [擴充功能]，勾選 [Microsoft.Live]，然後按一下 [確定]。

    ![][7]

    這會在專案中新增 Live SDK 的參照。

3.  開啟專案檔案 mainpage.xaml.cs 並新增下列 using 陳述式：

        using Microsoft.Live;      

4.  將下列程式碼片段新增至 MainPage 類別：

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

5.  使用向 Live Connect 註冊應用程式時所產生的用戶端識別碼值，來更新上一個步驟中的字串 *\<\< INSERT CLIENT ID HERE \>\>*。

    <div class="dev-callout"><b>注意</b>
<p>在 Windows Phone 8 應用程式中，您可透過將此用戶端識別碼值傳遞至類別建構函式來建立 <strong>LiveAuthClient</strong> 類別的執行個體。在 <a href="/zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">Windows 市集應用程式</a>中，您可透過傳遞重新導向網域 URI 來具現化相同類別。</p>
</div>

6.  刪除或註解化現有的 **OnNavigatedTo** 方法，這將會以下列處理頁面上 **Loaded** 事件的方法覆寫和取代該現有方法。

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    此方法會呼叫新的 **Authenticate** 方法。

7.  以下列程式碼取代 MainPage 建構函式：

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    此建構函式也會註冊 Loaded 事件的處理常式。

8.  按 F5 鍵執行應用程式，並以您的 Microsoft 帳戶登入 Live Connect。

    當您成功登入之後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者][]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。如需關於如何使用其他識別提供者以進行驗證的詳細資訊，請參閱[開始使用驗證][8]。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 市集 C#]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/develop/mobile/tutorials/single-sign-on-wp8/ "Windows Phone"
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-wp8/
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [Live SDK for Windows and Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-wp8
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [Windows 市集應用程式]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
  [使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /zh-tw/develop/mobile/tutorials/get-started-with-users-wp8
