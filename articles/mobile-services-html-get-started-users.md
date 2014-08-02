<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

在行動服務中開始使用驗證
========================

[Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows 市集 C#")[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows 市集 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

本主題顯示如何在 Azure 行動服務中從 HTML 應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊應用程式以進行驗證和設定行動服務](#register)
2.  [對通過驗證的使用者限制資料表權限](#permissions)
3.  [將驗證新增至應用程式](#add-authentication)

本教學課程以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-html)。

註冊應用程式註冊應用程式以進行驗證和設定行動服務
------------------------------------------------

您必須向身分識別提供者註冊應用程式，才能驗證使用者。然後，您必須向行動服務註冊由提供者產生的用戶端密碼。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

     ![](./media/mobile-services-html-get-started-users/mobile-services-selection.png)

2.  按一下 **[儀表板]** 索引標籤，記下 **[Mobile Service URL]** 值。

     ![](./media/mobile-services-html-get-started-users/mobile-service-uri.png)

    註冊應用程式時，您可能需要提供此值給身分識別提供者。

3.  從下列清單中選擇支援的身分識別提供者，並依照步驟向該提供者註冊應用程式：

	-   [Microsoft 帳戶](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Facebook 登入](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Twitter 登入](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Google 登入](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	請記下提供者所產生的用戶端身分識別和密碼值。

    	**安全性注意事項**

    	提供者產生的密碼是很重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。

1.  回到管理入口網站，按一下 **[識別]** 索引標籤，輸入您從身分識別提供者取得的應用程式識別碼和共用密碼值，然後按一下 **[儲存]**。

     ![](./media/mobile-services-html-get-started-users/mobile-identity-tab.png)

現在，行動服務和應用程式已設定為使用您選擇的驗證提供者。

限制權限只將權限授與已驗證的使用者
----------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

     ![](./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png)

2.  按一下 **[權限]** 索引標籤，將所有權限設為 **[Only authenticated users]**，然後按一下 **[儲存]**。如此可確保 **TodoItem** 資料表上的所有作業只能由已驗證的使用者執行。這也簡化下一個教學課程中的指令碼，讓指令碼不需要顧慮可能有匿名使用者。

     ![](./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png)

3.  在應用程式目錄中，從 **server** 子資料夾啟動下列其中一個命令檔。

    -   **launch-windows** (Windows 電腦)
    -   **launch-mac.command** (Mac OS X 電腦)
    -   **launch-linux.sh** (Linux 電腦)

    **注意**

    在 Windows 電腦上，當 PowerShell 要求您確認要執行指令碼時，請輸入 \`R\`。因為指令碼是從網際網路下載，網頁瀏覽器可能會警告您不要執行指令碼。發生此狀況時，您必須要求瀏覽器繼續載入指令碼。

    這樣會在本機電腦上啟動 Web 伺服器來裝載新的應用程式。

4.  在網頁瀏覽器中開啟 URL <http://localhost:8000/> 來啟動應用程式。

    資料載入失敗。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

5.  (選用) 開啟網頁瀏覽器的指令碼偵錯工具，重新載入頁面。確認發生拒絕存取錯誤。

接下來，您要將應用程式更新為在要求行動服務的資源之前允許驗證。

新增驗證將驗證新增至應用程式
----------------------------

**注意**

因為是在快顯中執行登入，您應該從按鈕的 click 事件中叫用 **login** 方法。否則，許多瀏覽器會阻擋登入視窗。

1.  開啟專案檔案 index.html，找出 H1 元素，在它下面加入下列程式碼片段：

        <div id="logged-in">
        	You are logged in as <span id="login-name"></span>.
        	<button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
        	You are not logged in.
        	<button>Log in</button>
        </div>

    這樣可讓您從網頁登入行動服務。

2.  在 app.js 檔案中，找出檔案最尾端呼叫 refreshTodoItems 函數的那一行程式碼，換成下列程式碼：

        function refreshAuthDisplay() {
        	var isLoggedIn = client.currentUser !== null;
        	$("#logged-in").toggle(isLoggedIn);
        	$("#logged-out").toggle(!isLoggedIn);

        	if (isLoggedIn) {
        		$("#login-name").text(client.currentUser.userId);
        		refreshTodoItems();
            }
        }

        function logIn() {
        	client.login("facebook").then(refreshAuthDisplay, function(error){
        		alert(error);
             });
        }

        function logOut() {
        	client.logout();
        	refreshAuthDisplay();
        	$('#summary').html('<strong>You must login to access data.</strong>');
        }

        // On page init, fetch the data and set up event handlers
        $(function () {
        	refreshAuthDisplay();
        	$('#summary').html('<strong>You must login to access data.</strong>');            
        	$("#logged-out button").click(logIn);
        	$("#logged-in button").click(logOut);
        });

    這會建立一組函數來處理驗證程序。使用者透過 Facebook 登入來驗證。

    **注意**

    如果您使用的身分識別提供者不是 Facebook，請將傳給上述 **login** 方法的值變更為下列其中一個：*microsoftaccount*、*facebook*、*twitter* 或 *google*。

3.  返回正在執行應用程式的瀏覽器，重新整理頁面。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

    **注意**

    使用 Internet Explorer 時，登入之後可能會出現錯誤：`Cannot reach window opener.It may be on a different Internet Explorer zone`。這是因為快顯是在不同於 localhost (內部網路) 的安全性區域 (網際網路) 中執行。這只會在開發期間使用 localhost 時影響到應用程式。解決方法是開啟 **[網際網路選項]** 的 **[安全性]** 索引標籤，按一下 **[近端內部網路]**，按一下 **[網站]**，然後停用 **[自動偵測內部網路]**。完成測試時，請記得恢復此設定。

後續步驟
--------

在下一個教學課程[使用指令碼來授權使用者](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html)中，您將根據驗證的使用者來取得行動服務所提供的使用者識別碼，並用來篩選行動服務所傳回的資料。深入了解如何透過 HTML/JavaScript 來使用行動服務，請參閱[行動服務 HTML/JavaScript 作法概念性參考](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

