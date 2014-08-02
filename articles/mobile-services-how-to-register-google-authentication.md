<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Register your apps for Google login with Mobile Services" authors="" solutions="" manager="" editor="" />

在行動服務中註冊您的應用程式以進行 Google 登入
==============================================

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Google 驗證。

**注意**

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。

1.  瀏覽到 [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 網站，使用您的 Google 帳戶認證登入，然後按一下 **[建立專案...]**。

	![][1]   

2.  按一下 **[API Access]**，然後按一下 **[Create an OAuth 2.0 client ID]**。

	![][2]

3.  在 **[Branding Information]** 下輸入您的 **[產品名稱]**，然後按 **[下一步]**。

	![][3]

4.  在 **[Client ID Settings]** 下選取 **[Web 應用程式]**，在 **[Your site or hostname]** 中輸入您的行動服務 URL，按一下 **[more options]**，以您的行動服務 URL 附加路徑 */login/google* 來取代 **[Authorized Redirect URIs]** 中所產生的 URL，然後按一下 **[Create client ID]**。

	![][4]

    > [WACOM.NOTE]如需使用 Visual Studio 來將 .NET 後端行動服務發行至 Azure，則重新導向 URL 會是您行動服務的 URL 附加路徑 *signin-google*，您的行動服務即為 .NET 服務，例如 `https://todolist.azure-mobile.net/signin-google`。

5.  在 **[Client ID for web applications]** 下，記下 **[用戶端識別碼]** 和 **[用戶端密碼]** 的值。

	![][5]

    **安全性注意事項**

	用戶端密碼是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。

現在您已準備好提供用戶端識別碼和用戶端密碼值給行動服務，以在您的應用程式中採用 Google 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/