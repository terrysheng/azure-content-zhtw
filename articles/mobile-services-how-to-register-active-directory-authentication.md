<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="" />

註冊應用程式以使用 Azure Active Directory 帳戶登入
==================================================

本主題說明如何註冊應用程式以使用 Azure Active Directory 作為 Azure 行動服務的驗證提供者。

> [WACOM.NOTE] 行動服務的 Azure Active Directory 驗證提供者目前為預覽版。如果您想註冊預覽版，請連絡 <mobileservices@microsoft.com>。否則，行動服務的 [身分識別]`` 索引標籤中就不會顯示驗證提供者。

> [WACOM.NOTE]當您也想要從 Windows 市集應用程式中提供單一登入 (SSO) 或推播通知的用戶端導向驗證時，請同時考慮向 Windows 市集註冊您的應用程式。如需詳細資訊，請參閱[註冊 Windows 市集應用程式來使用 Windows Live Connect 驗證](/en-us/develop/mobile/how-to-guides/register-for-single-sign-on)。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png)

2.  按一下行動服務的 **[身分識別]** 索引標籤。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png)

3.  向下捲動至 **[Azure Active 目錄]** 身分識別提供者區段，然後複製此處所列的 **APP URL**。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png)

4.  導覽至管理入口網站中的 **Active Directory**，然後按一下您的目錄。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png)

5.  按一下頂端的 **[應用程式]** 索引標籤，然後按一下以**新增**應用程式。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png)

6.  按一下 **[Add an application my organization is developing]**。

7.  在 [新增應用程式精靈] 中，輸入應用程式的 **[名稱]**，然後按一下 **[Web Application And/Or Web API]** 類型。接著，按一下以繼續。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png)

8.  在 **[登入 URL]** 方塊中，貼上您從行動服務的 Active Directory 身分識別提供者設定中複製的應用程式 ID。同時，請在 **[應用程式 ID URI]** 方塊中輸入唯一的資源識別碼。應用程式會使用此 URI 提交對 Azure Active Directory 的單一登入要求。接著，按一下以繼續。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png)

9.  按一下以啟用應用程式的 **[單一登入]** 存取類型。按一下以完成 [新增應用程式精靈]。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png)

10. 在新增應用程式後，捲動至底部。接著，按一下 **[Enable your app to read or write directory data]** 區段加以展開。按一下以複製應用程式的 **[用戶端 ID]**。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png)

11. 回到行動服務的 **[身分識別]** 索引標籤。在底部，貼上 Azure Active Directory 身分識別提供者的 **[用戶端 ID]** 設定。然後按一下 **[儲存]**。

    ![](./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png)

現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。

