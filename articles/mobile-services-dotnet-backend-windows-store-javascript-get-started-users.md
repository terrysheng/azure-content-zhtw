<properties pageTitle="開始使用驗證 (Windows 市集) | 行動開發人員中心" metaKeywords="authentication, FAcebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# 在您的行動服務應用程式中新增驗證 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題說明如何從您的 Windows 市集應用程式，在 Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

>[WACOM.NOTE]使用行動服務 JavaScript 用戶端程式庫時，Windows Phone 市集 8.1 應用程式目前尚不支援驗證。如果您有使用 JavaScript 用戶端的通用 Windows 應用程式專案，此時將不能驗證 Windows Phone 上的使用者。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]
4. [將驗證權杖儲存在用戶端]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。 

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>限制只有通過驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="3">
<li><p>在 Visual Studio 2013 中，開啟您完成<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/">開始使用行動服務</a>教學課程時所建立的專案。</p></li> 
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>將授權權杖儲存在用戶端上

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

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
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts
[JavaScript 和 HTML]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: /zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
