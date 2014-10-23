<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 註冊應用程式來使用 Microsoft 帳戶登入

本主題說明如何註冊應用程式，以使用 Live Connect 做為 Azure 行動服務的驗證提供者。

> [WACOM.NOTE]若要設定適用於 Windows 8.1 或 Windows Phone 8.1 應用程式的 Microsoft 帳戶驗證，請參閱[註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證][]。

1.  瀏覽到 Live Connect 開發人員中心的[我的應用程式][]頁面，並視需要使用您的 Microsoft 帳戶登入。

2.  按一下 [建立應用程式]、然後輸入 [應用程式名稱]，並按一下 [I accept]。

    ![][]

    這會向 Live Connect 註冊應用程式。

3.  按一下 [API 設定]，在 [重新導向 URL] 中提供值 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount`，然後按一下 [儲存]。

    > [WACOM.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 後面附加路徑 *signin-microsoft*，而您的行動服務為 .NET 服務，例如 <code>https://todolist.azure-mobile.net/signin-microsoft</code>.

    ![][1]

    如此即可為您的應用程式啟用 Microsoft 帳戶驗證。

    > [WACOM.NOTE]針對現有的 Live Connect 應用程式註冊，您可能必須先啟用 [增強型重新導向安全性]。

4.  按一下 [應用程式設定]，記下 [用戶端 ID] 和 [用戶端密碼] 的值。

    ![][2]

    <div class="dev-callout"><b>安全性注意事項</b>
<p>用戶端密碼是重要的安全性認證。請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。</p>
</div>

現在，您可以在應用程式中提供用戶端識別碼和用戶端密碼值給行動服務，以使用 Microsoft 帳戶進行驗證。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證]: /zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  []: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
