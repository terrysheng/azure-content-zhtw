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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# 如何設定應用程式以使用 Google 登入

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

本主題說明如何設定行動應用程式使用 Google 作為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

## <a name="register"> </a>向 Google 註冊您的應用程式


1. 登入 [Azure 管理入口網站]，並瀏覽至您的行動應用程式。複製您的 [URL]。您稍後將使用此 URL 搭配您的 Google 應用程式。
 
2. 按一下 [設定]、[使用者驗證]，然後按一下 [Google]。複製 [重新導向 URI]。您將使用此 URI 設定您的 Google 應用程式。

3. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> 網站，以您的 Google 帳戶認證登入，按一下 [**建立專案**]，提供 [**名稱**]，然後按一下 [**建立**]。

4. 在左側導覽列中，按一下 [**API 和驗證**]。然後按一下 [**同意畫面**]。選取您的 [**電子郵件地址**]，並輸入 [**產品名稱**]。然後按一下 [儲存]。

5. 同時在 [**API 和驗證**] 下選取 [**API**]，並啟用 [**Google+ API**]。它位於 [Social API] 底下。您也可以只搜尋 **Google+ API**。

6. 再次於 [**API 和驗證**] 下選取 [**憑證**]，然後選取 [**建立新的用戶端 ID**]。

7. 選取 [**網路應用程式**]。貼上您稍早在 [授權 JavaScript 來源] 中複製的 [URL]，然後將 [授權重新導向 URI] 中產生的 URL 取代為您稍早複製的行動應用程式 [重新導向 URI]。此 URI 是附加路徑 _/signin-google_ 的行動應用程式閘道。例如，`https://contosogateway.azurewebsites.net/signin-google`。請確實使用 HTTPS 配置。然後按一下 [**建立用戶端 ID**]。

     ![][0]

8. 在下一個畫面上，在 [**Web 應用程式的用戶端識別碼**] 下，記下 [**用戶端識別碼**] 和 [**用戶端密鑰**] 的值。

    > [AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿與任何人共用此密碼，或在用戶端應用程式中加以散發。


## <a name="secrets"> </a>將 Google 資訊新增至行動應用程式

7. 回到行動應用程式的 Google 帳戶設定刀鋒視窗上的 [Azure 管理入口網站]，貼上您先前取得的用戶端識別碼和用戶端機密值。然後按一下 [儲存]。

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
 

<!---HONumber=August15_HO8-->