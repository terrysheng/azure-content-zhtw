<properties
	pageTitle="如何為您的應用程式服務應用程式設定 Twitter 驗證"
	description="了解如何為您的應用程式服務應用程式設定 Twitter 驗證。"
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

# 如何設定應用程式以使用 Twitter 登入

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

本主題說明如何設定 Azure 行動應用程式，以使用 Twitter 做為驗證提供者。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Twitter 帳戶。若要建立新的 Twitter 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

## <a name="register"> </a>向 Twitter 註冊您的應用程式


1. 登入 [Azure 管理入口網站]，並瀏覽至您的行動應用程式。複製您的 [URL]。您將使用此 URL 設定您的 Twitter 應用程式。

2. 按一下 [設定]、[使用者驗證]，然後按一下 [Twitter]。複製**回呼 URL**。您將使用此 URL 設定您的 Twitter 應用程式。

3. 瀏覽至 [Twitter Developers] 網站，使用您的 Twitter 帳戶認證登入，然後按一下 [**建立新的應用程式**]。

4. 針對您新的應用程式輸入 [Name] 和 [Description]。貼上您的 [行動應用程式 URL] 做為 [網站] 值。然後，針對 [Callback URL]，貼上您之前複製的 [Callback URL]。這是您附加路徑 _/signin-twitter_ 的行動應用程式閘道。例如，`https://contosogateway.azurewebsites.net/signin-twitter`。請確實使用 HTTPS 配置。

    ![][0]

3.  在頁面底部，閱讀並接受條款。然後按一下 [**Create your Twitter application**]。這會註冊應用程式並顯示應用程式詳細資料。

4. 按一下 [ **Settings**] 索引標籤，勾選 [**Allow this application to be used to sign in with Twitter**]，然後按一下 [**Update Settings**]。

5. 選取 [**Keys and Access Tokens**] 索引標籤。記下 [**Consumer Key (API Key)**] 和 [**Consumer secret (API Secret)**] 的值。

    > [AZURE.NOTE]消費者密碼是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。


## <a name="secrets"> </a>將 Twitter 資訊新增至行動應用程式

1. 回到行動應用程式 Twitter 設定刀鋒視窗上的 [Azure 管理入口網站]，貼上您先前取得的 API 金鑰與 API 機密值。然後按一下 [儲存]。

    ![][1]

現在，您已可在應用程式中使用 Twitter 進行驗證。

## <a name="related-content"> </a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure 管理入口網站]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=August15_HO8-->