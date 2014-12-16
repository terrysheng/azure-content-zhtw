<properties urlDisplayName="Get Started with Authentication (iOS)" pageTitle="開始使用驗證 (iOS) | 行動開發人員中心" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 在您的行動服務應用程式中新增驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主題說明如何在 Azure 行動服務從 iOS 應用程式驗證使用者。  在本教學課程中，您會使用行動服務支援的身分識別提供者，將驗證新增至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。  

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]
4. [將驗證語彙基元儲存至您的應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。

完成本教學課程需使用 XCode 4.5 及 iOS 5.0 或更新版本。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> 限制只有通過驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>在 Xcode 中，開啟您完成教學課程<a href="/zh-tw/documentation/articles/mobile-services-ios-get-started">開始使用行動服務</a>時建立的專案。</p></li>
<li><p>按 [<strong>執行</strong>] 按鈕，以在 iPhone 模擬器中建立專案並啟動應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。<p>

   	<p>這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 <em>TodoItem</em> 資料表現在需要驗證。</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>將驗證語彙基元儲存至您的應用程式

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>後續步驟

在下一個教學課程[行動服務使用者的伺服器端授權][使用指令碼授權使用者]中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，以篩選行動服務傳回的資料。

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[對通過驗證的使用者限制資料表權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[後續步驟]:#next-steps
[將驗證語彙基元儲存至您的應用程式]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 單一登入驗證 Windows 市集應用程式]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-ios
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure 管理入口網站]: https://manage.windowsazure.com/
