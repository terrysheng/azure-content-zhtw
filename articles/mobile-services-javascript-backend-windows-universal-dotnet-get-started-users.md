<properties pageTitle="開始使用驗證 (Windows 市集) | 行動開發人員中心" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="了解如何使用行動服務透過眾多識別提供者驗證 Windows 市集應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn Gailey" />

# 在您的行動服務應用程式中新增驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

本主題說明如何從您的通用 Windows 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]
5. [將驗證權杖儲存在用戶端]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。 

>[WACOM.NOTE]本教學課程說明如何在 Windows 市集與 Windows Phone 市集 8.1 應用程式中驗證使用者。有關 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請檢視此版本的[開始使用行動服務中的驗證](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users).

>本教學課程示範行動服務如何管理不同的身分識別提供者的驗證流程。此方法設定起來很簡單，而且可支援多個提供者。若要改用 Live Connect 與用戶端管理驗證方式，在您的 Windows Phone 應用程式中提供單一登入體驗，請參閱[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]主題。透過用戶端管理的驗證，您的應用程式就能存取身分識別提供者所維護的其他使用者資料。您可以呼叫伺服器指令碼中的 **user.getIdentities()** 函數，取得行動服務中的相同使用者資料。如需詳細資訊，請參閱[本文章](http://go.microsoft.com/fwlink/p/?LinkId=506605)。

##<a name="register"></a> 註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(選用) 完成<a href="/zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證</a>中的步驟。</p>

    <div class="dev-callout"><b>注意</b>
	<p>這個步驟僅適用於「Microsoft 帳戶」登入提供者，所以是選用步驟。向行動服務註冊 Windows 市集應用程式封裝資訊之後，用戶端就能夠重複使用 Microsoft 帳戶登入認證來享受單一登入的方便性。如果您沒有執行此動作，Microsoft 帳戶登入使用者會在每次呼叫登入方法時j都會看到登入提示。若您打算使用「Microsoft 帳戶」身分識別提供者，請完成這個步驟。</p>
    </div>
</li>
</ol>

##<a name="permissions"></a> 限制只有通過驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>在 Visual Studio 中，以滑鼠右鍵按一下 TodoList 應用程式的 Windows 市集專案，然後按一下 [<strong>設定為啟始專案</strong>]。</p></li>
<li><p>在共用專案中，開啟 App.xaml.cs 專案檔案並找到 <a href="http://msdn.microsoft.com/zh-tw/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> 的定義，然後確保該定義已設定為連接至 Azure 中執行的行動服務。</p>
<div class="dev-callout"><strong>注意</strong><p>當您使用 Visual Studio 工具將您的應用程式連接到行動服務時，此工具會分別針對個別用戶端平台需要產生共兩組 <strong>MobileServiceClient</strong> 定義。這時您可以順勢將使用 <code>#if...#endif</code> 包裝的 <strong>MobileServiceClient</strong> 定義統一至單一未包裝的定義以供這兩個應用程式版本使用，藉此簡化產生的程式碼。如果您是從 Azure 管理入口網站下載快速入門應用程式，則不需要這麼做。</p></div>
</li> 
<li><p>按 F5 鍵執行 Windows 市集應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a> 將驗證新增至應用程式

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>將授權權杖儲存在用戶端上

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>後續步驟

在下一個教學課程[行動服務使用者的伺服器端授權][使用指令碼授權使用者]中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，以篩選行動服務傳回的資料。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[對通過驗證的使用者限制資料表權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[將驗證權杖儲存在用戶端]: #tokens
[後續步驟]:#next-steps


<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 單一登入驗證 Windows 市集應用程式]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript 和 HTML]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念性參考]: /zh-tw/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: /zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
