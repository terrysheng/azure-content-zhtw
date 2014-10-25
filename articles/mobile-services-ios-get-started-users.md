<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/23/2014" ms.author="krisragh"></tags>

# 在行動服務中開始使用驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

本主題說明如何從您的 iOS 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]
4.  [將驗證權杖儲存在您的應用程式中][]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

完成本教學課程需使用 XCode 4.5 及 iOS 5.0 或更新版本。

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a> 限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  在 Xcode 中，開啟您完成教學課程[開始使用行動服務][1]時建立的專案。

2.  按下 [執行] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a>新增驗證至應用程式

[WACOM.INCLUDE [mobile-services-ios-authenticate-app][]]

## <a name="store-authentication"></a>將驗證權杖儲存在您的應用程式中

[WACOM.INCLUDE [mobile-services-ios-authenticate-app-with-token][]]

## <a name="next-steps"></a>後續步驟

在下一個[行動服務使用者的伺服器端授權][]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [將驗證權杖儲存在您的應用程式中]: #store-authentication
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-ios
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /zh-tw/documentation/articles/mobile-services-ios-get-started
  [mobile-services-ios-authenticate-app]: ../includes/mobile-services-ios-authenticate-app.md
  [mobile-services-ios-authenticate-app-with-token]: ../includes/mobile-services-ios-authenticate-app-with-token.md
  [行動服務使用者的伺服器端授權]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-ios
