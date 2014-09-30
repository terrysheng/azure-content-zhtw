1.  按一下 [Azure 管理入口網站][]之目錄頁面上的 [應用程式] 索引標籤。

2.  按一下整合的應用程式登錄。

3.  按一下應用程式頁面上的 [設定]，並向下捲動至頁面中的 [金鑰] 區段。
4.  針對新金鑰的期限，按一下 [1 年]。然後按一下 [儲存]，入口網站將會顯示您的新金鑰值。
5.  複製儲存後顯示的**用戶端識別碼**和**金鑰**。請注意，儲存後金鑰值只會顯示一次。

    ![][]

6.  向下捲動整合應用程式設定頁面至底部，並啟用應用程式的 [讀取目錄資料] 權限，然後按一下 [儲存]。

    ![][1]

7.  在 [Azure 管理入口網站][]中，往回瀏覽至您的行動服務，並按一下 [設定] 索引標籤。向下捲動至 [應用程式設定] 區段，並加入下列應用程式設定，然後按一下 [儲存]。

    | 應用程式設定名稱    | 說明                                                 |
    |---------------------|------------------------------------------------------|
    | AAD\_CLIENT\_ID     | 在上述步驟中，從整合應用程式中複製的用戶端識別碼。   |
    | AAD\_CLIENT\_KEY    | 在上述步驟中，AAD 整合應用程式中產生的應用程式金鑰。 |
    | AAD\_TENANT\_DOMAIN | AAD 網域名稱。應類似 "mydomain.onmicrosoft.com"      |
    | AAD\_GROUP\_ID      | 在前面章節針對業務群組所記下的群組識別碼。           |

    ![][2]

  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png
