
根據預設，可以匿名方式叫用行動應用程式後端中的 API。接下來，您必須限制只有經過驗證的用戶端才有存取權。

1. 在您電腦上的 Visual Studio 中開啟伺服器專案，然後瀏覽至 [控制器] > [TodoItemController.cs]。

2. 將 `[Authorize]` 屬性加入 **TodoItemController** 類別，如下所示。必須是通過驗證的使用者，才能對 TodoItem 資料表進行所有操作。若要限制只有特定方法才能存取，也可以將此屬性套用至這些方法，而不是類別。


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    必須是通過驗證的使用者，才能對 TodoItem 資料表進行所有操作。若要限制只有特定方法才能存取，也可以將此屬性套用至這些方法，而不是類別。
   
3. 重新發佈您的伺服器專案。

<!---HONumber=August15_HO8-->