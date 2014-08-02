

根據預設，所有對行動服務資源的要求都會限定於提供應用程式金鑰的用戶端 (此金鑰並不會嚴格保護對資源的存取)。若要保護您的資源，您必須限定只有已驗證的用戶端可進行存取。

1.  在 Visual Studio 中，開啟包含您的行動服務的專案。

2.  在 [方案總管] 中展開 Controllers 資料夾，然後開啟 TodoItemController.cs 專案檔案。

    **TodoItemController** 類別會實作 TodoItem 資料表的資料存取。

3.  在程式碼頁面頂端新增下列 `using` 陳述式：

         using Microsoft.WindowsAzure.Mobile.Service.Security;

4.  將下列 AuthorizeLevel 屬性套用至 **TodoItemController** 類別：

         [AuthorizeLevel(AuthorizationLevel.User)] 

    如此可確保對 **TodoItem** 資料表的所有操作都必須由經過驗證的使用者進行。

    > [WACOM.NOTE]將 AuthorizeLevel 屬性套用至個別方法，會對控制器公開的方法設定特定的授權層級。

5.  展開 App\_Start 資料夾，開啟 WebApiConfig.cs 專案檔案，然後將下列程式碼新增至 **Register** 方法：

         config.SetIsHosted(true);

    這會指示本機行動服務專案依循在 Azure 中受到代管的型態執行，包括接受 AuthorizeLevel 設定。如果沒有此設定，則無論是否有 AuthorizeLevel 設定，所有對 *localhost* 的 HTTP 要求都會在未經驗證的情況下受到允許。

6.  重新發佈您的服務專案。


