<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

開始使用行動服務中的驗證
========================
<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/zh-tw/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/" title="..NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/"  title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何從您的應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用行動服務所支援的身份識別提供者將驗證新增至快速入門專案。使用者 ID 值在成功經過行動服務的驗證和授權後，就會顯示出來。

本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1.  [註冊應用程式進行驗證，並設定行動服務](#register)
2.  [限制只有經驗證的使用者具有資料表的權限](#permissions)
3.  [新增驗證至應用程式](#add-authentication)

本教學課程是以行動服務快速入門為基礎。您也必須先完成[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-windows-store-get-started/)教學課程。

<div class="dev-callout"><b>注意</b>
	<p>本教學課程示範行動服務所提供以各種身分識別提供者來驗證使用者的基本方法。此方法設定起來很簡單，而且可支援多個提供者。不過，此方法也需要使用者在每次啟用應用程式時進行登入。若要改用 Live Connect 在您的 Windows 市集應用程式中提供單一登入體驗，請參閱<a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js">使用 Live Connect 在 Windows 市集應用程式提供單一登入功能</a>。</p>
</div>

註冊應用程式進行驗證，並設定行動服務
------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (選用) 完成[註冊 Windows 市集應用程式套件以採用 Microsoft 驗證](/zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/)中的步驟。

	<div class="dev-callout"><b>注意</b>
	<p>這個步驟僅適用於「Microsoft 帳戶」登入提供者，所以是選用步驟。向行動服務註冊 Windows 市集應用程式封裝資訊之後，用戶端就能夠重複使用 Microsoft 帳戶登入認證來享受單一登入的方便性。如果您沒有執行此動作，Microsoft 帳戶登入使用者會在每次呼叫登入方法時j都會看到登入提示。若您打算使用「Microsoft 帳戶」身分識別提供者，請完成這個步驟。</p>
    </div>

您的行動服務和您的應用程式現在都已設定成使用您所選擇的驗證提供者。

限制只有經驗證的使用者具有權限
------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/)教學課程時所建立的專案。

2.  按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

    發生這個情況是因為應用程式嘗試以未經驗證的使用者身分存取行動服務，但 *TodoItem* 資料表現在要求要經過驗證。

接下來，您將更新應用程式，使其先驗證使用者再向行動服務要求資源。

新增驗證至應用程式
------------------

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

後續步驟
--------

在下一個[行動服務使用者的伺服器端授權](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts)教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

