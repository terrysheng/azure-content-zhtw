1.  在 [Azure 管理入口網站][] 中，按一下目錄頁面上的 [應用程式] 索引標籤。

2.  按一下整合的應用程式註冊。

3.  按一下應用程式頁面的 [設定]，然後向下捲動到頁面的 [金鑰] 區段。
4.  按一下 [1 年] 期以取得新的金鑰。然後按一下 [儲存]，入口網站便會隨即顯示新您的金鑰值。
5.  儲存後，請複製顯示的 [用戶端識別碼] 和 [金鑰]。請注意，金鑰值在儲存後只會顯示一次。

    ![][]

6.  向下捲動到整合的應用程式組態頁面底部，並啟用應用程式的 [Read directory data] 權限，然後按一下 [儲存]。

    ![][1]

7.  在 [Azure 管理入口網站][]中，向後巡覽至您的行動服務，然後按一下 [設定] 索引標籤。向下捲動到 [應用程式設定] 區段，並新增下列應用程式設定，然後按一下 [儲存]。

    | 應用程式設定名稱    | 說明                                                          |
    |---------------------|---------------------------------------------------------------|
    | AAD\_CLIENT\_ID     | 在上述步驟中，您從整合的應用程式複製的用戶端識別碼。          |
    | AAD\_CLIENT\_KEY    | 在上述步驟中，您在 AAD 整合的應用程式中所產生的應用程式金鑰。 |
    | AAD\_TENANT\_DOMAIN | 您的 AAD 網域名稱。應該類似 "mydomain.onmicrosoft.com"        |

    ![][2]

  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png
