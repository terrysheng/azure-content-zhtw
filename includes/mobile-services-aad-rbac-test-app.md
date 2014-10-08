1.  在 Visual Studio 中執行用戶端應用程式，並嘗試驗證名為 Dave 的已建立使用者帳戶。

    ![][]

2.  Dave 沒有 Sales 群組的成員資格。因此以角色為基礎的存取檢查將會拒絕存取資料表作業。關閉用戶端應用程式。

    ![][1]

3.  在 Visual Studio 中重新執行用戶端應用程式。這一次驗證名為 Bob 的已建立使用者帳戶。

    ![][2]

4.  Bob 確實具有 Sales 群組的成員資格。因此以角色為基礎的存取檢查將會允許存取資料表作業。

    ![][3]

  []: ./media/mobile-services-aad-rbac-test-app/dave-login.png
  [1]: ./media/mobile-services-aad-rbac-test-app/unauthorized.png
  [2]: ./media/mobile-services-aad-rbac-test-app/bob-login.png
  [3]: ./media/mobile-services-aad-rbac-test-app/success.png
