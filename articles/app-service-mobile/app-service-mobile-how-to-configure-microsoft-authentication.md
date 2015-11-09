<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證"
	description="了解如何為您的應用程式服務應用程式設定 Microsoft 帳戶驗證。"
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
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

# 如何設定應用程式以使用 Microsoft 帳戶登入

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何設定 Azure 行動應用程式，以使用 Microsoft 帳戶做為驗證提供者。

## <a name="register"> </a>使用 Microsoft 帳戶註冊您的應用程式

1. 登入 [Azure 管理入口網站]，並瀏覽至您的行動應用程式。

2. 按一下 [設定]、[使用者驗證]，然後按一下 [Microsoft 帳戶]。複製 [重新導向 URL]。您將使用此 URL 為您的 Microsoft 帳戶設定新的應用程式。

3. 瀏覽到 Microsoft 帳戶開發人員中心的 [我的應用程式]頁面，並視需要使用您的 Microsoft 帳戶登入。

4. 按一下 [建立應用程式]、然後輸入 [應用程式名稱]，並按一下 [I accept]。

5. 按一下 [**API 設定**]。針對 [**行動或桌面用戶端應用程式**]，選取 [**是**]。在 [重新導向 URL] 欄位中輸入您之前複製的 [重新導向 URL]。這是附加 _/signin-microsoft_ 的行動應用程式閘道。例如，`https://contosogateway.azurewebsites.net/signin-microsoft`。請確實使用 HTTPS 配置。輸入重新導向 URL 之後，按一下 [**儲存**]。

	![][0]

	>[AZURE.NOTE]針對現有的 Microsoft 帳戶應用程式註冊，您可能必須先啟用 [**增強型重新導向安全性**]。

4. 按一下 [應用程式設定]，記下 [用戶端 ID] 和 [用戶端密碼] 的值。

    > [AZURE.NOTE]用戶端密碼是重要的安全性認證。請勿與任何人共用此用戶端密碼，或在用戶端應用程式中加以散發。


## <a name="secrets"> </a>將 Microsoft 帳戶資訊新增至行動應用程式

1. 回到行動應用程式的 Microsoft 帳戶設定刀鋒視窗上的 [Azure 管理入口網站]，貼上您先前取得的用戶端識別碼和用戶端機密值。然後按一下 [儲存]。

    ![][1]

現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 管理入口網站]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO1-->