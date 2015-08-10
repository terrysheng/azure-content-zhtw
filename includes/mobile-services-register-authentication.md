
* 按一下 Azure 入口網站 > [**行動服務**] > 您的行動服務 > [**儀表板**]，然後記下 [**行動服務 URL**] 值。

* 向 [Google](mobile-services-how-to-register-google-authentication.md)、[Facebook](mobile-services-how-to-register-facebook-authentication.md)、[Twitter](mobile-services-how-to-register-twitter-authentication.md)、[Microsoft](mobile-services-how-to-register-microsoft-authentication.md) 或 [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md) 註冊應用程式。請記下提供者所產生的用戶端身分識別和用戶端密碼值。請勿散佈或共用用戶端密碼。

* 按一下 Azure 入口網站 > [行動服務] > 您的行動服務 > [身分識別] > 您的身分識別提供者設定。輸入您的提供者之應用程式 ID 和密碼值。現在，已設定您的應用程式和行動服務使用您的驗證提供者。您可以選擇性對想要支援的每個額外身分識別提供者重複所有步驟。

    > [AZURE.IMPORTANT]確認您已在身分識別提供者開發人員網站上設定正確的重新導向 URI。如上方每個提供者的連結指示所述，.NET 後端服務與 JavaScript 後端服務的重新導向 URI 可能會有所不同。設定錯誤的重新導向 URI 可能會導致無法正確顯示登入畫面，以及應用程式可能會以非預期的方式失去作用。

<!---HONumber=July15_HO5-->