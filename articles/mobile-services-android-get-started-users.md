<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/23/2014" ms.author="ricksal"></tags>

# 在行動服務中開始使用驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">10:42</span></div>

</div>

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]
4.  [在用戶端儲存驗證語彙基元][]
5.  [重新整理過期的語彙基元][]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

完成本教學課程需要有 Eclipse 和 Android 4.2 或更新版本。

## <a name="register"></a><span class="short-header">註冊應用程式</span>註冊應用程式以進行驗證和設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a><span class="short-header">限制權限</span>對通過驗證的使用者限制權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  在 Eclipse 中，開啟您完成教學課程[開始使用行動服務][]時建立的專案。

2.  從 [執行] 功能表，按一下 [執行] 來啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a><span class="short-header">新增驗證</span>將驗證新增至應用程式

[WACOM.INCLUDE [mobile-services-android-authenticate-app][]]

## <a name="cache-tokens"></a>在用戶端快取驗證語彙基元

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][]]

## <a name="refresh-tokens"></a>重新整理語彙基元快取

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][]]

## <a name="next-steps"></a>後續步驟

在下一堂教學課程[使用指令碼授權使用者][]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [觀看教學課程]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [在用戶端儲存驗證語彙基元]: #cache-tokens
  [重新整理過期的語彙基元]: #refresh-tokens
  [開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-android
