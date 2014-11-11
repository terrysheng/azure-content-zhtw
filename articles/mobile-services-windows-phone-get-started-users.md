<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# 在行動服務中開始使用驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">上午 10:50:00</span></div>

</div>

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][註冊您的應用程式以驗證與設定行動服務]
2.  [對通過驗證的使用者限制資料表權限][對通過驗證的使用者限制資料表權限]
3.  [將驗證新增至應用程式][將驗證新增至應用程式]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][開始使用行動服務]。

> [WACOM.NOTE]本教學課程示範行動服務透過各種身分識別提供者來管理的驗證流程。此方法設定起來很簡單，而且可支援多個提供者。若要改用可於用戶端管理驗證的 Live Connect，並在您的 Windows Phone 應用程式中提供單一登入體驗，請參閱[使用 Live Connect 在 Windows Phone 應用程式提供單一登入功能][使用 Live Connect 在 Windows Phone 應用程式提供單一登入功能]主題。藉由使用用戶端管理的驗證，您的應用程式即可存取身分識別提供者保留的其他使用者資料。您可藉由呼叫伺服器指令碼中的 **user.getIdentities()** 函數，在行動服務中取得相同的使用者資料。如需詳細資訊，請參閱 [這篇文章][這篇文章]。

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a> 限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成[開始使用行動服務][開始使用行動服務]教學課程時所建立的專案。

2.  按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a>新增驗證至應用程式

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>將授權權杖儲存在用戶端上

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權][行動服務使用者的伺服器端授權]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started
  [使用 Live Connect 在 Windows Phone 應用程式提供單一登入功能]: /zh-tw/documentation/articles/mobile-services-windows-phone-single-sign-on
  [這篇文章]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-windows-phone-authenticate-app]: ../includes/mobile-services-windows-phone-authenticate-app.md
  [mobile-services-windows-phone-authenticate-app-with-token]: ../includes/mobile-services-windows-phone-authenticate-app-with-token.md
  [行動服務使用者的伺服器端授權]: /zh-tw/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
