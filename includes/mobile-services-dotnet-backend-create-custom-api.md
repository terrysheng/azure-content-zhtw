

1. 在 Visual Studio [方案總管] 中，於行動服務專案的 Controllers 資料夾上按一下滑鼠右鍵，展開 **[新增]**，然後按 **[New Scaffolded Item]**。

	這會顯示 [新增 Scaffold] 對話方塊。

2. 展開 **[Azure 行動服務]**，按一下 **[Azure 行動服務自訂控制器]**，然後按一下 **[新增]**，提供  `CompleteAllController` 的 **[控制器名稱]**，然後再按一次 **[新增]**。

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	這會建立名為 **CompleteAllController** 的新空白控制器類別。

>[AZURE.NOTE]如果對話方塊沒有行動服務的特定 Scaffold，請改為建立新的 **Web API 控制器 - 空白**。在此新控制器類別中加入公用 **Services** 屬性，該屬性可傳回 **ApiServices** 類型。此屬性可用來存取控制器內部的伺服器專屬設定。

3. 在新的 CompleteAllController.cs 專案檔案，新增下列 **using** 陳述式：

		using System.Threading.Tasks;
		using todolistService.Models;

	在上述程式碼中，以行動服務專案的命名空間取代  `todolistService`，該名稱必須是附加  `Service` 的行動服務名稱。 

4. 在 CompleteAllController.cs 中，在命名空間中新增下列類別定義。此類別會包裝傳送給用戶端的回應。

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. 將下列程式碼新增至新控制器：

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

	在上述程式碼中，以資料模型之 DbContext 的名稱取代  `todolistContext`，該名稱必須是附加  `Context` 的行動服務名稱。也會將 UPDATE 陳述式中的結構描述名稱取代為您的行動服務名稱。 

	此程式碼使用 [Database 類別](http://msdn.microsoft.com/library/system.data.entity.database.aspx) (英文) 直接存取 **TodoItems** 資料表，以在所有項目上設定完成旗標。此方法支援 POST 要求，且系統會以整數值將變更的列數傳回給用戶端。

	> [AZURE.NOTE] 已設定預設權限，這表示應用程式的任何使用者均可呼叫自訂 API。不過，應用程式金鑰並未安全地發佈或儲存，所以無法視為安全的認證。因此，您應考慮僅限通過驗證的使用者存取可修改資料或影響行動服務的操作。 

接著，您將會修改快速入門應用程式，以加入新按鈕和非同步呼叫新自訂 API 的程式碼。

<!--HONumber=42-->
