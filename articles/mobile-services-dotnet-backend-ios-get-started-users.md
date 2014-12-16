<properties pageTitle="開始使用驗證 (Windows 市集) | 行動開發人員中心" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 在您的行動服務應用程式中新增驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]
4. [將驗證語彙基元儲存至您的應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>在 Xcode 中，開啟用戶端應用程式快速入門專案。</li>
<li><p>按 [<strong>執行</strong>] 按鈕，以在 iPhone 模擬器中建立專案並啟動應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。<p>

   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>將驗證語彙基元儲存至您的應用程式

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>後續步驟

在下一個教學課程[行動服務使用者的伺服器端授權][使用指令碼授權使用者]中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，以篩選行動服務傳回的資料。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有通過驗證的使用者具有權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[後續步驟]:#next-steps
[將驗證權杖儲存在您的應用程式]:#store-authentication

<!-- URLs. -->
[開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[開始使用驗證]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[開始使用推播通知]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[使用指令碼授權使用者]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: /zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
