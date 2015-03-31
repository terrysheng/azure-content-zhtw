<properties 
	pageTitle="註冊 Microsoft 驗證 - 行動服務" 
	description="了解如何在 Azure Mobile Services 應用程式中註冊 Microsoft 驗證。" 
	authors="ggailey777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# 註冊應用程式來使用 Microsoft 帳戶登入

本主題說明如何註冊應用程式，以使用 Live Connect 做為 Azure 行動服務的驗證提供者。 

>[AZURE.NOTE]若要設定通用 Windows 應用程式的 Microsoft 帳戶驗證，或為 Windows 市集應用程式提供單一登入體驗，請參閱[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證](/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication)。

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">[我的應用程式]</a> 頁面，並視需要使用您的 Microsoft 帳戶登入。 

2. 按一下 [**建立應用程式**]、然後輸入 [**應用程式名稱**]，並按一下 [**我接受**]。

   	![][1] 

   	這會向 Live Connect 註冊應用程式。

3. 按一下 [**API 設定**]，在 [**重新導向 URL**] 中提供  `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` 值 ，然後按一下 [**儲存**]。

	>[AZURE.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 後面附加路徑 _signin-microsoft_，而您的行動服務為 .NET 服務，例如 <code>https://todolist.azure-mobile.net/signin-microsoft</code>。  

	![][3]

	這會為您的應用程式啟用 Microsoft 帳戶驗證。

	>[AZURE.NOTE]針對現有的 Live Connect 應用程式註冊，您可能必須先啟用 [**增強型重新導向安全性**]。

4. 按一下 [**應用程式設定**]，記下 [**用戶端 ID**] 和 [**用戶端密碼**] 的值。 

   	![][2]

    > [AZURE.NOTE] 用戶端密碼是重要的安全性認證。請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。

現在，您可以在應用程式中提供用戶端識別碼和用戶端密碼值給行動服務，以使用 Microsoft 帳戶進行驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[提交應用程式頁面]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure 管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=47-->
