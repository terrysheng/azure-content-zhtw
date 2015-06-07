

若要驗證使用者，請向身分識別提供者註冊應用程式，然後向 Azure 行動服務註冊提供者產生的用戶端認證。

1. 登入 Azure 管理入口網站，按一下 [行動服務]，然後按一下您的行動服務。

2. 按一下 [儀表板] 索引標籤，並記下 [行動服務 URL] 值。註冊應用程式時，您可能需要提供此值給身分識別提供者。

3. 從下列清單中選擇支援的身分識別提供者。請依照步驟向該提供者註冊您的應用程式。請記下提供者所產生的用戶端身分識別和密碼值。

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Microsoft 帳戶</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google </a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT]提供者產生的密碼是很重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。

4. 回到管理入口網站，按一下 [識別] 索引標籤，輸入您從身分識別提供者取得的應用程式識別碼和共用密碼值，然後按一下 [儲存]。您的行動服務和您的應用程式現在都已設定成使用您所選擇的驗證提供者。

    > [AZURE.IMPORTANT]確認您已在身分識別提供者開發人員網站上設定正確的重新導向 URI。如上方每個提供者的連結指示所述，.NET 後端服務與 JavaScript 後端服務的重新導向 URI 可能會有所不同。設定錯誤的重新導向 URI 可能會導致無法正確顯示登入畫面，以及應用程式可能會以非預期的方式失去作用。

5. (選擇性) 重複步驟 3 與 4，以設定您要應用程式支援的任何其他身分識別提供者。

<!--HONumber=54-->