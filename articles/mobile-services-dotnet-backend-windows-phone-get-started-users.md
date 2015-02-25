<properties pageTitle="開始使用驗證 (Windows Phone) | 行動開發人員中心" description="了解如何使用行動服務透過眾多識別提供者驗證 Windows Phone 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題說明如何從您的 Windows Phone 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

>[AZURE.NOTE] 本主題僅支援 Windows Phone 8.0 和 Windows Phone 8.1 Silverlight 應用程式。如果您有 Windows Phone 市集 8.1 或通用 Windows 應用程式，請改為依照[本主題的通用 Windows 應用程式版本](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)進行。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證:

1. [註冊應用程式進行驗證，並設定行動服務]
2. [限制只有經驗證的使用者具有資料表的權限]
3. [將驗證新增到應用程式]
4. [將驗證權杖儲存在用戶端]

本教學課程會以行動服務快速入門為基礎。您也必須先完成教學課程[開始使用行動服務]。 


## <a name="register"></a> 註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>在 Visual Studio 中，開啟用戶端應用程式專案，確定 App.xaml.cs 中的 <strong>MobileServiceClient</strong> 執行個體設定為使用雲端 URL 至行動服務。</li> 
<li><p>按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>
   
   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a>將驗證新增到應用程式

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>將驗證權杖儲存在用戶端上

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權][以指令碼授權使用者]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。您可以在[行動服務 .NET 作法概念參考資料]中深入了解如何使用搭配 .NET 的行動服務。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有經驗證的使用者具有資料表的權限]: #permissions
[將驗證新增到應用程式]: #add-authentication
[將驗證權杖儲存在用戶端]: #tokens
[後續步驟]: #next-steps


<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 單一登入驗證 Windows Phone 應用程式]: /zh-tw/documentation/articles/mobile-services-windows-phone-single-sign-on
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[以指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
[JavaScript 和 HTML]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念參考資料]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: /zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


<!--HONumber=42-->
