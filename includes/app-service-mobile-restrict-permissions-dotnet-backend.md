

根據預設，行動應用程式中定義的端點會公開。若要保護您的資源，您必須限定只有已驗證的用戶端可進行存取。

1. 在 Visual Studio 中，開啟包含您的行動應用程式程式碼的專案。 

2. 在 [方案總管] 中展開 Controllers 資料夾，然後開啟 TodoItemController.cs 專案檔案。

	**TodoItemController** 類別會實作 TodoItem 資料表的資料存取。

3. 將 `Authorize` 屬性套用至 **TodoItemController** 類別：

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	如此可確保對 **TodoItem** 資料表的所有操作都必須由經過驗證的使用者進行。

	>[AZURE.NOTE]將 Authorize 屬性套用至個別方法，會對控制器公開的方法設定特定的授權層級。

4. 重新發佈您的行動應用程式專案。

<!---HONumber=August15_HO6-->