<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your apps for Twitter login with Mobile Services" authors="" solutions="" manager="" editor="" />

在行動服務中註冊您的應用程式以進行 Twitter 登入
===============================================

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Twitter 驗證。

**注意**

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Twitter 帳戶。若要建立新的 Twitter 帳戶，請前往 [twitter.com](http://go.microsoft.com/fwlink/p/?LinkID=268287)。

1.  瀏覽到 [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300) 網站，使用您的 Twitter 帳戶認證登入，然後按一下 **[建立新應用程式]**。

	![][1]

2.  輸入您應用程式的 **[名稱]**、**[描述]** 和 **[網站]** 值，然後在 **[回呼 URL]** 中輸入行動服務的 URL。

	![][2]

    > [WACOM.NOTE]如需使用 Visual Studio 來將 .NET 後端行動服務發行至 Azure，則重新導向 URL 會是您行動服務的 URL 附加路徑 *signin-google*，您的行動服務即為 .NET 服務，例如 `https://todolist.azure-mobile.net/signin-twitter`。
    > **[網站]** 值為必要但不會用到。

3.  在頁面底部，請閱讀並接受條款，輸入正確的 CAPTCHA 文字，然後按一下 **[Create your Twitter application]**。

	![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png)

	這會註冊應用程式並顯示應用程式詳細資料。

1.  記下 **[消費者金鑰]** 和 **[消費者密碼]** 的值。

	![][4]

    **安全性注意事項**

	消費者密碼是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。

2.  按一下 **[設定]** 索引標籤，向下捲動並勾選 **[Allow this application to be used to sign in with Twitter]**，然後按一下 **[Update this Twitter application's settings]**。

    ![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png)

現在您已準備好提供消費者金鑰和消費者密碼值給行動服務，以在您的應用程式中採用 Twitter 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/