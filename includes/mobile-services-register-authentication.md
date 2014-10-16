

您必須向身分識別提供者註冊應用程式，才能驗證使用者。然後，您必須向行動服務註冊由提供者產生的用戶端密碼。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2.  按一下 **[儀表板]** 索引標籤，記下 **[Mobile Service URL]** 值。

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    註冊應用程式時，您可能需要提供此值給身分識別提供者。

3.  從下列清單中選擇支援的身分識別提供者，並依照步驟向該提供者註冊應用程式：

 -   [Microsoft 帳戶](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
 -   [Facebook 登入](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
 -   [Twitter 登入](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
 -   [Google 登入](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
 -   [Azure Active Directory](/zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    請記下提供者所產生的用戶端身分識別和密碼值。

	<div class="dev-callout"><b>安全性注意事項</b>
	<p>提供者產生的密碼是很重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。</p>
    </div>

1.  回到管理入口網站，按一下 **[識別]** 索引標籤，輸入您從身分識別提供者取得的應用程式識別碼和共用密碼值，然後按一下 **[儲存]**。

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

    您的行動服務和您的應用程式現在都已設定成使用您所選擇的驗證提供者。


