<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="" />

註冊應用程式來使用 Microsoft 帳戶登入
=====================================

本主題說明如何註冊應用程式以使用 Live Connect 做為 Azure 行動服務的驗證提供者。

> [WACOM.NOTE]當您也想要從 Windows 市集應用程式中提供單一登入 (SSO) 或推播通知的用戶端導向驗證時，請同時考慮向 Windows 市集註冊您的應用程式。如需詳細資訊，請參閱[註冊 Windows 市集應用程式來使用 Windows Live Connect 驗證](/en-us/develop/mobile/how-to-guides/register-for-single-sign-on)。

1.  瀏覽到 Live Connect 開發人員中心的 [[我的應用程式]](http://go.microsoft.com/fwlink/p/?LinkId=262039) 頁面，必要的話以您的 Microsoft 帳戶登入。

2.  按一下 **[建立應用程式]**，輸入 **[應用程式名稱]**，然後按一下 **[我接受]**。

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png) 

   	這會向 Live Connect 註冊應用程式。

3.  按一下 **[應用程式設定]** 頁面，再按一下 **[API 設定]**，記下 **[用戶端識別碼]** 和 **[用戶端密碼]** 的值。

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Security Note</b>
	<p>The client secret is an important security credential.Do not share the client secret with anyone or distribute it with your app.</p>
    </div>

1.  在 **[重新導向網域]** 中，輸入行動服務的 URL，然後按一下 **[儲存]**。

現在，您可以在應用程式中提供用戶端識別碼和用戶端密碼值給行動服務，以使用 Microsoft 帳戶進行驗證。

