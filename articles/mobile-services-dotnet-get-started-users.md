<properties  linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Get Started with Users" pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

# 開始使用行動服務中的驗證

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows 市集 C#" class="current">Windows 市集 C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-js" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

 
<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content">
<p>本主題說明如何從您的應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用行動服務所支援的身份識別提供者將驗證新增至快速入門專案。使用者 ID 值在成功經過行動服務的驗證和授權後，就會顯示出來。</p>
<p>按一下右方的迴紋針可以觀看本教學課程的影片版。</p>
</div>

 
<div  class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> (英文) <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span  class="icon">播放影片</span></a> (英文) <span  class="time">10:04</span></div>

 本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1.  [註冊應用程式進行驗證，並設定行動服務](#register)
2.  [限制只有經驗證的使用者具有資料表的權限](#permissions)
3.  [新增驗證至應用程式](#add-authentication)

本教學課程以行動服務快速入門為基礎。您也必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程。
<div class="dev-callout"><b>注意</b>
	<p>本教學課程示範行動服務所提供以各種身分識別提供者來驗證使用者的基本方法。此方法設定起來很簡單，而且可支援多個提供者。不過，此方法也需要使用者在每次啟用應用程式時進行登入。若要改用 Live Connect 在您的 Windows 市集應用程式中提供單一登入體驗，請參閱<a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet">使用 Live Connect 在 Windows 市集應用程式提供單一登入功能</a>。</p>
</div>

 <h2><a name="register"></a><span  class="short-header">註冊應用程式</span>註冊應用程式進行驗證，並設定行動服務</h2>


為了能夠驗證使用者，您必須向身分識別提供者註冊您的應用程式。然後，您必須在行動服務中註冊提供者所產生的用戶端密鑰。

1.  登入 **Azure 管理入口網站][1]，按一下 [行動服務**，然後按一下您的行動服務。
    
	![][4]

2.  按一下 **儀表板** 索引標籤，記下 **Mobile Service URL** 值。
    
	![][5]
    
    當您註冊應用程式時，可能需要提供此值給身分識別提供者。

3.  從以下清單中選擇一個支援的身分識別提供者，並依照步驟向該提供者註冊您的應用程式：

	* [Microsoft  帳戶](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	* [Facebook  登入](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	* [Twitter  登入](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	* [Google  登入](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	* [Azure Active  Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)
  
  	請務必記下提供者所產生的用戶端身分識別與密鑰值。
	<div class="dev-callout"><b>安全性注意事項</b>
	<p>提供者所產生的密鑰是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。</p>
	</div>


1.  回到管理入口網站，按一下 **身分識別** 索引標籤，輸入從身分識別提供者那裡取得的應用程式識別碼與共用密鑰，然後按一下 **儲存**。
    
	![][13]

2.  (選用) 完成[註冊 Windows 市集應用程式套件以採用 Microsoft 驗證](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package)中的步驟。
<div class="dev-callout"><b>注意</b>
<p>這個步驟僅適用於「Microsoft 帳戶」登入提供者，所以是選用步驟。當您向行動服務註冊您的 Windows 市集應用程式套件資訊時，用戶端能夠重複使用「Microsoft 帳戶」登入認證來達到單一登入體驗。若您沒有這麼做，您的「Microsoft 帳戶」登入使用者將在每次呼叫登入方法時都看到登入提示。若您打算使用「Microsoft 帳戶」身分識別提供者，請完成這個步驟。</p>
</div>



您的行動服務和您的應用程式現在都已設定成使用您所選擇的驗證提供者。

<h2><a name="permissions"></a><span  class="short-header">限制權限</span>限制只有經驗證使用者具有權限</h2>


1.  在管理入口網站中，按一下 **資料** 索引標籤，然後按一下 **TodoItem** 資料表。
    
	![][14]

2.  按一下 **權限** 索引標籤，將所有的權限設為 **Only authenticated users**，然後按一下 **儲存**。如此可確保對 **TodoItem** 資料表的所有操作都必須由經過驗證的使用者進行。如此也可簡化下一個教學課程中的指令碼，因為那些指令碼將不需要顧及有匿名使用者的可能性。
    
	![][15]

3.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started)教學課程時所建立的專案。

4.  按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。
    
	發生這個情況是因為應用程式嘗試以未經驗證的使用者身分存取行動服務，但 _TodoItem_ table 現在要求要經過驗證。

接下來，您將更新應用程式，使其先驗證使用者再向行動服務要求資源。

<h2><a name="add-authentication"></a><span  class="short-header">新增驗證</span>新增驗證至應用程式</h2>


1.  開啟專案檔案 mainpage.xaml.cs 並新增下列 using 陳述式：
    
         using Windows.UI.Popups;

2.  新增下列程式碼片段至 MainPage 類別：
    
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    
    如此會建立一個成員變數來存放目前使用者，並建立一個方法來處理驗證程序。使用者會透過 Facebook 登入受到驗證。如果您是使用
    Facebook 以外的身分識別提供者，請將上面的 **MobileServiceAuthenticationProvider** 值變更為您的提供者。
	<div class="dev-callout"><b>注意</b>
	<p>如果您已向行動服務註冊您的 Windows 市集應用程式套件資訊，則應該為 <em>useSingleSignOn</em> 參數提供 <strong>true</strong> 值來呼叫 <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> 方法。若您沒有這麼做，您的使用者將在每次呼叫登入方法時都看到登入提示。</p>
	</div>


3.  將現有 **OnNavigatedTo** 方法覆寫，取代為以下這個會呼叫新 **Authenticate** 方法的方法：
    
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

4.  按 F5 鍵執行應用程式，並以您選擇的身分識別提供者登入應用程式。
    
	當您登入成功後，應用程式應該會毫無錯誤地正常執行，而您應該能夠查詢行動服務並更新資料。

## <a name="next-steps"> </a>後續步驟

在下一個教學課程[以指令碼授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)中，您將使用由行動服務根據經驗證的使用者而提供的使用者 ID 值，來篩選行動服務傳回的資料。如需深入了解如何使用行動服務與 .NET，請參閱[行動服務 .NET 作法概念參考](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[新增驗證至應用程式]: #add-authentication
[後續步驟]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-users-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package