
1. 按一下 Azure 入口網站 > [**行動服務**] > 您的行動服務 > [**儀表板**]，然後記下 [**行動服務 URL**] 值。

2. 向下列其中一個或多個驗證提供者註冊您的應用程式：
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md)。 
   
   請記下提供者所產生的用戶端身分識別和用戶端密碼值。請勿散佈或共用用戶端密碼。

3. 在 Azure 入口網站中，按一下 [行動服務] > 您的行動服務 > [身分識別] > 您的身分識別提供者設定，然後輸入您提供者的用戶端識別碼和密碼值。 
 
現在，已設定您的應用程式和行動服務使用您的驗證提供者。您可以選擇性對想要支援的每個額外身分識別提供者重複所有步驟。

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->