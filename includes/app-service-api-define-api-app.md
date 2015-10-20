## 新增 Web API 程式碼

在下列步驟中，您為簡單 HTTP Get 方法新增程式碼，以傳回寫在程式碼的連絡人清單。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [**模型**] 資料夾，然後選取 [**新增 > 類別**]。 

	![](./media/app-service-api-define-api-app/03-add-new-class-v3.png)

2. 將新檔案命名為 *Contact.cs*。

	![](./media/app-service-api-define-api-app/0301-add-new-class-dialog-v3.png)

3. 按一下 [新增]。

4. 一旦建立了 *Contact.cs* 檔案，即會將檔案的整個內容取代為下列程式碼。

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. 以滑鼠右鍵按一下 [**控制器**] 資料夾，然後選取 [**新增 > 控制器**]。

	![](./media/app-service-api-define-api-app/05-new-controller-v3.png)

6. 在 [**新增 Scaffold**] 對話方塊中，選取 [**Web API 2 控制器 - 空的**] 選項，然後按一下 [**新增**]。

	![](./media/app-service-api-define-api-app/06-new-controller-dialog-v3.png)

7. 將控制器命名為 **ContactsController**，然後按一下 [**新增**]。

	![](./media/app-service-api-define-api-app/07-new-controller-name-v2.png)

8. 一旦建立了 ContactsController.cs 檔案，即會將檔案的整個內容取代為下列程式碼。

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
						new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
						new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
	                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		    }
		}

## 啟用 Swagger UI

預設會啟用 API 應用程式專案，並自動產生 [Swagger](http://swagger.io/ "官方 Swagger 資訊") 中繼資料，因此使用 [新增 API 應用程式 SDK] 功能表項目來轉換 Web API 專案時，依預設也會啟用 API 測試頁面。

不過，Azure API 應用程式的新專案範本會停用 API 測試頁面。使用 API 應用程式專案範本建立 API 應用程式專案時，請執行下列步驟來啟用測試頁面。

**注意：**如果您啟用 Swagger UI，並將 API 應用程式部署為「匿名公用」，則任何人都能夠使用 Swagger UI 探索及呼叫您的 API。

1. 開啟 *App\_Start/SwaggerConfig.cs* 檔案，並搜尋 **EnableSwaggerUI**：

	![](./media/app-service-api-define-api-app/12-enable-swagger-ui-with-box.png)

2. 將下列幾行程式碼取消註解：

	        })
	    .EnableSwaggerUi(c =>
	        {

3. 完成時，檔案應該如下所示：

	![](./media/app-service-api-define-api-app/13-enable-swagger-ui-with-box.png)

## 測試 Web API

若要檢視 API 測試頁面，請執行下列步驟。

1. 在本機執行應用程式 (CTRL+F5)。

	瀏覽器會開啟並顯示 HTTP 403 錯誤，因為基底 URL 不是有效的網頁，或不是此專案的 API 方法 URL。
 
3.  將 `/swagger` 新增至基底 URL 的結尾，以瀏覽至 Swagger 頁面。

	![](./media/app-service-api-define-api-app/swaggerhome.png)

2. 按一下 **[連絡人] > [取得] > [立即試用]**，您會看見 API 運作中並傳回預期的結果。

	![](./media/app-service-api-define-api-app/swaggertry.png)

3. 在 Visual Studio 中，按一下 [偵錯] > [停止偵錯]。

<!---HONumber=Oct15_HO3-->