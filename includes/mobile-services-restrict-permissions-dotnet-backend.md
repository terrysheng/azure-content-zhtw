

根據預設，所有對行動服務資源的要求都會限定於提供應用程式金鑰的用戶端 (此金鑰並不會嚴格保護對資源的存取)。若要保護您的資源，您必須限定只有經過驗證的用戶端可進行存取。

1. 在 Visual Studio 中開啟行動專案，展開 Controllers 資料夾，然後開啟 **TodoItemController.cs**。**TodoItemController** 類別會實作 TodoItem 資料表的資料存取。新增下列 `using` 陳述式：

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. 將下列 _AuthorizeLevel_ 屬性套用至 **TodoItemController** 類別：這可確保針對 _TodoItem_ 資料表所做的所有作業都需要一位通過驗證的使用者。

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]將 AuthorizeLevel 屬性套用至個別方法，會對控制器公開的方法設定特定的授權層級。

3. 如果您想要在本機偵測驗證錯誤，請展開 `App_Start` 資料夾，開啟 **WebApiConfig.cs**，然後將下列程式碼新增至 **Register** 方法：  

		config.SetIsHosted(true);

	這會指示本機行動服務專案依循在 Azure 中受到代管的型態執行，包括接受 *AuthorizeLevel* 設定。如果沒有此設定，則無論是否有 *AuthorizeLevel* 設定，所有對 localhost 的 HTTP 要求都會在未經驗證的情況下受到允許。 

4. 重新發佈您的專案。

<!--HONumber=47-->
