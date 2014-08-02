<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="" />

註冊應用程式以使用 Azure Active Directory 帳戶登入
==================================================

本主題說明如何註冊應用程式以使用 Azure Active Directory 作為 Azure 行動服務的驗證提供者。

**注意**

當您也想要從 Windows 市集應用程式中提供單一登入 (SSO) 或推播通知的用戶端導向驗證時，請同時考慮向 Windows 市集註冊您的應用程式。如需詳細資訊，請參閱[註冊 Windows 市集應用程式來使用 Windows Live Connect 驗證](/en-us/develop/mobile/how-to-guides/register-for-single-sign-on)。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。

2.  導覽至管理入口網站中的 **Active Directory**，然後按一下您的目錄。

    ![](./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png)

3.  按一下 **[應用程式]** 索引標籤，然後按一下 **[加入應用程式]**。

    ![](./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png)

4.  依照新增應用程式精靈的指示，對 XXX 選擇 **[WEB 應用程式和/或 WEB API]**。啟用單一登入。提示您輸入 **[應用程式 URL]** 時，請貼上行動服務應用程式 URL。

5.  *** 更多內容 ***

現在，您可以在應用程式中提供用戶端識別碼和用戶端密碼值給行動服務，以使用 Azure Active Directory 進行驗證。

