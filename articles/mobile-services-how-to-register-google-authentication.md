<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 在行動服務中註冊您的應用程式以進行 Google 登入

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Google 驗證。

<div class="dev-callout"><b>注意</b>
<p>若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Google 帳戶。若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。</p>
</div>

1.  瀏覽至 [Google apis][Google apis] (英文) 網站，以您的 Google 帳戶認證登入，按一下 [Create Project]，提供「專案名稱」，然後按一下 [Create]。

    ![][0]

2.  依序按一下 [API & Auth]、[Credentials] 和 [Create new Client ID]。

    ![][1]

3.  選取 [Web application]，在 [Authorized JavaScript Origins] 中輸入您的行動服務 URL，將 [Authorized Redirect URI] 中所產生的 URL，取代為行動服務的 URL，後面附加路徑 */login/google*，然後按一下 [Create client ID]。

    > [WACOM.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 後面附加路徑 *signin-google*，而您的行動服務為 .NET 服務，例如 <code>https://todolist.azure-mobile.net/signin-google</code>.

    ![][2]

4.  在 [Client ID for web applications] 下，記下 [用戶端識別碼] 和 [用戶端密碼] 的值。

    ![][3]

    <div class="dev-callout"><b>安全性注意事項</b>
<p>用戶端密碼是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。</p>
</div>

現在您已準備好提供用戶端識別碼和用戶端密碼值給行動服務，以在您的應用程式中採用 Google 登入驗證。

 
 


  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
