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
	ms.date="11/20/2015"
	ms.author="mahender"/>

# 如何設定 App Service 應用程式以使用 Google 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure App Service，以使用 Google 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。

> [AZURE.NOTE]本主題示範 App Service 驗證/授權功能的用法。這會取代大部分應用程式的 App Service 閘道器。整個主題中使用閘道器所產生的差異都列在注意事項中。


## <a name="register"> </a>向 Google 註冊您的應用程式

1. 登入 [Azure 入口網站]，然後瀏覽到您的應用程式。複製您的 **URL**。您將使用此 URI 設定您的 Google 應用程式。
 
2. 瀏覽至 [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) (英文) 網站，以您的 Google 帳戶認證登入，按一下 [Create Project]，提供「專案名稱」，然後按一下 [Create]。

3. 在左側的導覽列中按一下 [API & Auth]，然後在 [Social APIs] 底下按一下 [Google + API] > [啟用 API]。

4. 按一下 [API & Auth] > [認證] > [OAuth 同意畫面]，然後選取您的**電子郵件地址**，輸入**產品名稱**，再按一下 [儲存]。

5. 在 [認證] 索引標籤中，按一下 [新增認證] > [OAuth 2.0 用戶端識別碼]，然後選取 [Web 應用程式]。

6. 將您先前複製的 App Service **URL** 貼到 [授權 JavaScript 來源]，然後將您先前複製的**重新導向 URI** 貼到 [授權重新導向 URI]。重新導向 URI 是您的應用程式 URL 加上路徑 _/.auth/login/google/callback_。例如：`https://contoso.azurewebsites.net/.auth/login/google/callback`。請確實使用 HTTPS 配置。然後按一下 [建立]。


	> [AZURE.NOTE]如果您使用的是 App Service 閘道器，而不是App Service 驗證/授權功能，重新導向 URL 會改用閘道器 URL 加上 _/signin-google_ 路徑。


7. 在下一個畫面上，記下用戶端識別碼和用戶端密碼的值。


    > [AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。


## <a name="secrets"> </a>將 Google 資訊新增至應用程式

> [AZURE.NOTE]如果您使用 App Service 閘道器，請忽略此章節，並改為在入口網站中瀏覽至您的閘道器。依序選取 [設定]、[身分識別]，[Google]。貼入您之前取得的值，然後按一下 [儲存]。


8. 請回到 [Azure 入口網站]，並瀏覽到您的應用程式。依序按一下 [設定] 及 [驗證/授權]。

9. 如果 [驗證/授權] 功能未啟用，請切換到 [開]。

10. 按一下 [Google]。貼上先前取得的應用程式識別碼與應用程式密碼值，然後選擇性啟用應用程式需要的任何範圍。然後按一下 [確定]。

    ![][1]

	App Service 預設會提供驗證，但不會限制對您網站內容和 API 的已授權存取。您必須在應用程式程式碼中授權使用者。

17. (選擇性) 若要限制只有透過 Google 授權的使用者可以存取您的網站，請將 [要求未經驗證時所採取的動作] 設為 [Google]。這會要求所有要求都需經過驗證，且所有未經驗證的要求都會重新導向至 Google 以進行驗證。

12. 按一下 [儲存]。

現在，您已可在應用程式中使用 Google 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 入口網站]: https://portal.azure.com/
 

<!---HONumber=AcomDC_1203_2015-->