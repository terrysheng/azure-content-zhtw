<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# 在行動服務中開始使用驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

本主題顯示如何在 Azure 行動服務中從 HTML 或 PhoneGap 應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a> 限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  在應用程式目錄中，從 **server** 子資料夾啟動下列其中一個命令檔。

    -   **launch-windows** (Windows 電腦)
    -   **launch-mac.command** (Mac OS X 電腦)
    -   **launch-linux.sh** (Linux 電腦)

    > [WACOM.NOTE]在 Windows 電腦上，當 PowerShell 要求您確認想要執行指令碼時，請輸入 `R`。因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。發生此情況時，您必須要求瀏覽器繼續載入指令碼。

    這樣會在本機電腦上啟動 Web 伺服器來裝載新的應用程式。

2.  在網頁瀏覽器中開啟 URL <http://localhost:8000/> 來啟動應用程式。

    資料載入失敗。這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

3.  (選用) 開啟網頁瀏覽器的指令碼偵錯工具，重新載入頁面。確認發生拒絕存取錯誤。

接下來，您要將應用程式更新為在要求行動服務的資源之前允許驗證。

## <a name="add-authentication"></a>新增驗證至應用程式

> [WACOM.NOTE]因為是在快顯中執行登入，所以您應該從按鈕的 click 事件叫用 **login** 方法。否則，許多瀏覽器會阻擋登入視窗。

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

    這會建立一組函數來處理驗證程序。使用者透過 Facebook 登入來驗證。如果您使用的身分識別提供者不是 Facebook，請將傳遞至上述 **login** 方法的值，變更為下列其中一個值：*microsoftaccount*、*facebook*、*twitter*、*google* 或 *aad*。

    > [WACOM.NOTE]在 PhoneGap 應用程式中，您必須也將下列外掛程式新增至專案：
    >
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git`
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git`
    >
    > </p>

3.  返回正在執行應用程式的瀏覽器，重新整理頁面。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

    > [WACOM.NOTE]使用 Internet Explorer 時，登入之後可能會出現錯誤：`Cannot reach window opener. It may be on a different Internet Explorer zone`。這是因為快顯是在不同於 localhost (內部網路) 的安全性區域 (網際網路) 中執行。這只會在開發期間使用 localhost 時影響到應用程式。解決方法是開啟 [網際網路選項] 的 [安全性] 索引標籤，按一下 [近端內部網路]，按一下 [網站]，然後停用 [自動偵測內部網路]。完成測試時，請記得恢復此設定。

## <a name="next-steps"> </a>後續步驟

在下一堂教學課程[使用指令碼授權使用者][]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。深入了解如何透過 HTML/JavaScript 來使用行動服務，請參閱[行動服務 HTML/JavaScript 作法概念性參考][]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-html-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-html-authorize-users-in-scripts
  [行動服務 HTML/JavaScript 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-html-how-to-use-client-library
