

您必須向身分識別提供者註冊應用程式，才能驗證使用者。然後，您必須向行動服務註冊由提供者產生的用戶端密碼。

1. 登入 [Azure 管理入口網站]，按一下 **[行動服務]**，然後按一下您的行動服務。

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. 按一下 **[儀表板]** 索引標籤，並記下 **[行動服務 URL]** 值。

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    註冊應用程式時，您可能需要提供此值給身分識別提供者。

3. 從下列清單中選擇支援的身分識別提供者，並依照步驟向該提供者註冊應用程式：

 - <a href="/zh-tw/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft 帳戶</a>
 - <a href="/zh-tw/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook 登入</a>
 - <a href="/zh-tw/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter 登入</a>
 - <a href="/zh-tw/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google 登入</a>
 - <a href="/zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    請記下提供者所產生的用戶端身分識別和密碼值。

    > [AZURE.IMPORTANT] 提供者產生的密碼是很重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。

4. 回到管理入口網站，按一下 **[識別]** 索引標籤，輸入您從身分識別提供者取得的應用程式識別碼和共用密碼值，然後按一下 **[儲存]**。

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	現在，行動服務和應用程式已設定為使用您選擇的驗證提供者。

<!-- URLs. -->
[Azure 管理入口網站]: https://manage.windowsazure.com/
\<!--HONumber=42-->
