<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="開始使用驗證 (Android) | 行動開發人員中心" metaKeywords="" description="了解如何使用行動服務透過眾多識別提供者驗證 Android 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# 在您的行動服務應用程式中新增驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">10:42</span></div>
</div> 

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊應用程式進行驗證，並設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]
4. [將驗證權杖儲存在用戶端]
5. [重新整理過期的語彙基元]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務]。 

>[AZURE.NOTE] 如果您想要查看已完成的應用程式的原始程式碼，請到<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithAuth/Android" target="_blank">這裡</a>。

完成本教學課程需要有 Eclipse 和 Android 4.2 或更新版本。 

<h2><a name="register"></a>註冊應用程式進行驗證，並設定行動服務</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>限制只有通過驗證的使用者具有權限</h2>

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. 在 Eclipse 中，開啟您完成教學課程[開始使用行動服務]時建立的專案。 

4. 從 [**執行**] 功能表，按一下 [**執行**] 來啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。 

	 這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 _TodoItem_ 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

<h2><a name="add-authentication"></a>將驗證新增至應用程式</h2>

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>在用戶端快取驗證權杖

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>重新整理權杖快取

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>後續步驟

在下一個教學課程[使用指令碼授權使用者]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。 

<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]: #register
[限制只有通過驗證的使用者具有權限]: #permissions
[將驗證新增至應用程式]: #add-authentication
[將驗證權杖儲存在用戶端]: #cache-tokens
[重新整理過期權杖]: #refresh-tokens
[後續步驟]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows (英文)]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 單一登入驗證 Windows 市集應用程式]: /zh-tw/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started-android
[開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-android
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-android
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-android
[使用指令碼授權使用者]: /zh-tw/develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure 管理入口網站]: https://manage.windowsazure.com/
