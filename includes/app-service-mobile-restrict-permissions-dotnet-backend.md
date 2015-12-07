
根據預設，可以匿名方式叫用行動應用程式後端中的 API。接下來，您必須限制只有經過驗證的用戶端才有存取權。

+ **Node.js 後端 (透過入口網站)**：  
	
	在行動應用程式的 [**設定**] 中，按一下 [**簡單資料表**]，然後選取您的資料表。按一下 [**變更權限**]，選取所有權限的 [**僅驗證存取**]，然後按一下 [**儲存**]。

+ **.NET 後端 (C#)**：

	在伺服器專案中，瀏覽至 [**控制器**] > [**TodoItemController.cs**]。將 `[Authorize]` 屬性加入 **TodoItemController** 類別，如下所示。若要限制只有特定方法才能存取，也可以將此屬性套用至這些方法，而不是類別。發佈伺服器專案。


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js 後端 (透過 Node.js 程式碼)**：
	
	如需要求資料表存取驗證，請將下行加入 Node.js 伺服器指令碼：


        table.access = 'authenticated';

	如需詳細資訊，請參閱[如何使用 Azure Mobile Apps Node.js SDK](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md) 中的[需經過驗證才可存取資料表](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)。

<!---HONumber=AcomDC_1125_2015-->