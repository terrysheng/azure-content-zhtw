<properties 
	pageTitle="註冊 Google 驗證 - 行動服務" 
	description="了解如何在 Azure 行動服務中註冊您的應用程式以使用 Google 進行驗證。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# 在行動服務中註冊您的應用程式以進行 Google 登入

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Google 驗證。

>[AZURE.NOTE]本教學課程有關 [Azure 行動服務](http://azure.microsoft.com/services/mobile-services/)，此方案可協助您建置適用於任何平台的可擴充行動應用程式。行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。此頁面是<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">開始使用驗證</a>教學課程的輔助，說明如何將使用者登入您的應用程式。如果這是您第一次使用行動服務，請完成<a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">開始使用行動服務</a>教學課程。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> (英文) 網站，以您的 Google 帳戶認證登入，按一下 [建立專案]****，提供「專案名稱」****，然後按一下 [建立]****。

   	![][1]

2. 按一下 [同意] 畫面****，選取您的電子郵件地址****，輸入產品名稱****，然後按一下 [儲存]****。

3. 依序按一下 [API & Auth]****、[Credentials]**** 和 [Create new Client ID]****。

   	![][2]

4. 選取 [Web application]****，在 [Authorized JavaScript Origins]**** 中輸入您的行動服務 URL，將 [Authorized Redirect URI]**** 中所產生的 URL，取代為行動服務的 URL，後面附加路徑 _/login/google_，然後按一下 [Create client ID]****。

	>[AZURE.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 並附加路徑 _signin-google_，而您的行動服務為 .NET 服務，例如 <code>https://todolist.azure-mobile.net/signin-google</code>。

   	![][3]

5. 在 [Client ID for web applications]**** 下，記下 [用戶端識別碼]**** 和 [用戶端密碼]**** 的值。

   	![][4]

    > [AZURE.IMPORTANT]用戶端密碼是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。

現在您已準備好提供用戶端識別碼和用戶端密碼值給行動服務，以在您的應用程式中採用 Google 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54--> 