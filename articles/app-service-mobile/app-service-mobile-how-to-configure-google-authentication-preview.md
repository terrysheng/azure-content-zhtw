<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Google 驗證"
	description="了解如何為您的應用程式服務應用程式設定 Google 驗證。"
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="mahender"/>

# 如何設定應用程式以使用 Google 登入

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

本主題說明如何設定行動應用程式使用 Google 作為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

1. 登入 [Azure 管理入口網站]，並瀏覽至您的行動應用程式。複製您的 **URL**。您稍後將使用此 URL 搭配您的 Google 應用程式。
 
2. 按一下 [設定]、[使用者驗證]，然後按一下 **Google**。複製**重新導向 URI**。您將使用此 URI 設定您的 Google 應用程式。

3. 瀏覽至 [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) (英文) 網站，以您的 Google 帳戶認證登入，按一下 [Create Project]，提供「專案名稱」，然後按一下 [Create]。

4. 在左側的導覽列中按一下 [API & Auth]，然後在 [Social APIs] 底下按一下 [Google + API] > [啟用 API]。

5. 按一下 [API & Auth] > [認證] > [OAuth 同意畫面]，然後選取您的**電子郵件地址**，輸入**產品名稱**，再按一下 [儲存]。

6. 在 [認證] 索引標籤中，按一下 [新增認證] > [OAuth 2.0 用戶端識別碼]，然後選取 [Web 應用程式]。

7. 貼上您先前複製的 Mobile Apps **URL** 到 [授權 JavaScript 來源]，然後貼上您先前複製的**重新導向 URI** 到 [授權重新導向 URI]。重新導向 URI 是加上路徑 _/signin-google_ 的行動應用程式閘道器。例如：`https://contosogateway.azurewebsites.net/signin-google`。請確實使用 HTTPS 配置。然後按一下 [建立]。

8. 在下一個畫面上，記下用戶端識別碼和用戶端密碼的值。

    > [AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。

9. 回到行動應用程式的 Google 帳戶設定刀鋒視窗上的 [Azure 管理入口網站]，貼上您先前取得的用戶端識別碼和用戶端密碼值。然後按一下 [儲存]。

     ![][1]

現在，您已可在應用程式中使用 Google 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 管理入口網站]: https://portal.azure.com/
 

<!---HONumber=Oct15_HO3-->