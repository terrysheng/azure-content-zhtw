<properties
	pageTitle="使用 CORS 從 JavaScript 取用 API 應用程式 | Microsoft Azure"
	description="了解如何在 Azure App Service 中從 JavaScript 用戶端並使用 CORS 取用 API 應用程式"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/04/2016"
	ms.author="tdykstra"/>

# 使用 CORS 從 JavaScript 取用 API 應用程式

## 概觀

App Service 提供內建的跨原始來源資源共用 (CORS) 支援，可讓 JavaScript 用戶端對 App Service API 應用程式進行跨網域呼叫。

基於安全性理由，瀏覽器會防止 JavaScript 對與 JavaScript 程式碼原始網域不同的網域進行 API 呼叫。例如，您可以從 contoso.com 網頁對 contoso.com API 端點進行呼叫，但不能對 fabrikam.com 端點進行呼叫。CORS 是一個網際網路通訊協定，其設計用來啟用您需要進行這類跨網域 API 呼叫的案例。在 Azure App Service 中，您的 JavaScript 用戶端在 Web 應用程式中執行，而您的 API 在 API 應用程式中執行，這就是這類案例的範例之一。

本文包含兩個部分：

* [如何設定 CORS](#corsconfig)一節大致說明如何為 API 應用程式設定 CORS，且一體適用於 App Service 支援的所有架構，包括 .NET、Node.js 和 Java。 

* 從[繼續 .NET 快速入門教學課程](#tutorialstart)一節開始，會引導您部署 .NET 範例應用程式和設定 CORS，讓 JavaScript 前端可以呼叫 Web API 後端。

## <a id="corsconfig"></a>如何在 Azure App Service 中設定 CORS

您可以在 Azure 入口網站中設定 CORS，或使用 [Azure Resource Manager](../resource-group-overview.md) 工具設定。

#### 在 Azure 入口網站中設定 CORS

8. 在瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [應用程式服務]，然後按一下您 API 應用程式的名稱。

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. 在 [API 應用程式] 右側開啟的 [設定] 刀鋒視窗中，尋找 [API] 區段，再按一下 [CORS]。

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. 在文字方塊中，輸入您想要允許的 JavaScript 呼叫來源 URL。


	例如，如果您已將 JavaScript 應用程式部署至名為 todolistangular 的 Web 應用程式，請輸入 "https://todolistangular.azurewebsites.net"。或者，您也可以輸入星號 (*) 來指定接受所有的原始網域。


13. 按一下 [儲存]。

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	按一下 [儲存] 之後，API 應用程式會接受來自指定 URL 的 JavaScript 呼叫。

### 使用 Azure 資源管理員工具設定 CORS

您也可以使用 [Azure PowerShell](../powershell-install-configure.md) 和 [Azure CLI](../xplat-cli-install.md) 等命令列工具中的 [Azure Resource Manager 範本](../resource-group-authoring-templates.md)，設定 API 應用程式的 CORS。

如需可設定 CORS 屬性之 Azure Resource Manager 範本的範例，請開啟[本教學課程的範例應用程式儲存機制中的 azuredeploy.json 檔案](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)。找出如下列範例所示的範本區段：

		"cors": {
		    "allowedOrigins": [
		        "todolistangular.azurewebsites.net"
		    ]
		}

## <a id="tutorialstart"></a>繼續進行 .NET 入門教學課程

如果您要遵循適用於 API 應用程式的 Node.js 或 Java 入門系列，請跳至下一節 [App Service API 應用程式的驗證](app-service-api-authentication.md)。

本文其餘部分是 .NET 入門系列的延續，並假設您已成功完成[第一個教學課程](app-service-api-dotnet-get-started.md)。

## 將 ToDoListAngular 專案部署到新的 Web 應用程式

在[第一個教學課程](app-service-api-dotnet-get-started.md)中，您建立了中介層 API 應用程式和資料層 API 應用程式。在本教學課程中，您將建立單一頁面應用程式 (SPA) Web 應用程式來呼叫中介層 API 應用程式。為了讓 SPA 正常運作，您必須在中介層 API 應用程式上啟用 CORS。

在 [ToDoList 範例應用程式](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list)中，ToDoListAngular 專案是簡單的 AngularJS 用戶端，並且會呼叫中介層 ToDoListAPI Web API 專案。app/scripts/todoListSvc.js 檔案中的 JavaScript 程式碼會使用 AngularJS HTTP 提供者來呼叫 API。

		angular.module('todoApp')
		.factory('todoListSvc', ['$http', function ($http) {
		    var apiEndpoint = "http://localhost:46439";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems : function(){
		            return $http.get(apiEndpoint + '/api/TodoList');
		        },

		        /* Get by ID, Put, and Delete methods not shown */

		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/TodoList', item);
		        }
		    };
		}]);

### 為 ToDoListAngular 專案建立新的 Web 應用程式

建立新的 Web 應用程式並對其部署專案的程序，和您在本系列的第一個教學課程中看到的相同，差別只在類型為 [Web 應用程式] 而不是 [API 應用程式]。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 ToDoListAngular 專案，然後按一下 [發佈]。

3.  在 [發佈 Web] 精靈的 [設定檔] 索引標籤中，按一下 [Microsoft Azure App Service]。

5. 在 [App Service] 對話方塊中，按一下 [新增]。

3. 在 [建立 App Service] 對話方塊的 [主控] 索引標籤中，輸入 [Web 應用程式名稱]，而該名稱在 azurewebsites.net 網域中必須是唯一名稱。

5. 選擇您要使用的 Azure [訂用帳戶]。

6. 在 [資源群組] 下拉式清單中，選擇您稍早建立的資源群組。

4. 在 [App Service 方案] 下拉式清單中，選擇您稍早建立的同一個方案。

7. 按一下 [建立]。

	Visual Studio 會建立 Web 應用程式、建立其發佈設定檔，並顯示 [發佈 Web] 精靈的 [連接] 步驟。

	在按一下 [發佈 Web] 精靈中的 [發佈] 之前，您會設定新的 Web 應用程式以呼叫在 App Service 中執行的中介層 API 應用程式。

### 在 Web 應用程式設定中設定中介層 URL

1. 移至 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至您建立以裝載 TodoListAngular (前端) 專案的 Web 應用程式的 [Web 應用程式] 刀鋒視窗。

2. 按一下 **[設定] > [應用程式設定]**。

3. 在 [應用程式設定] 區段中，新增下列金鑰和值：

	|金鑰|值|範例
	|---|---|---|
	|toDoListAPIURL|https://{your 中介層 API 應用程式名稱}.azurewebsites.net|https://todolistapi0121.azurewebsites.net|

4. 按一下 [儲存]。

	在 Azure 中執行程式碼時，這個值現在會覆寫 Web.config 檔案中的 localhost URL。

	取得設定值的程式碼位於 index.cshtml：

		<script type="text/javascript">
		    var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
		</script>
		<script src="app/scripts/todoListSvc.js"></script>

	todoListSvc.js 中的程式碼會使用設定：

		return {
		    getItems : function(){
		        return $http.get(apiEndpoint + '/api/TodoList');
		    },
		    getItem : function(id){
		        return $http.get(apiEndpoint + '/api/TodoList/' + id);
		    },
		    postItem : function(item){
		        return $http.post(apiEndpoint + '/api/TodoList', item);
		    },
		    putItem : function(item){
		        return $http.put(apiEndpoint + '/api/TodoList/', item);
		    },
		    deleteItem : function(id){
		        return $http({
		            method: 'DELETE',
		            url: apiEndpoint + '/api/TodoList/' + id
		        });
		    }
		};

### 將 ToDoListAngular Web 專案部署到新的 Web 應用程式

*  在 Visual Studio 的 [發佈 Web] 精靈的 [連接] 步驟中，按一下 [發佈]。

	Visual Studio 會將 ToDoListAngular 專案部署到新的 Web 應用程式，並將瀏覽器開啟至 Web 應用程式的 URL。

### 在不啟用 CORS 的情況下測試應用程式 

2. 在瀏覽器開發人員工具中，開啟 [主控台] 視窗。

3. 在顯示 AngularJS UI 的瀏覽器視窗中，按一下 [待辦事項清單] 連結。

	JavaScript 程式碼會嘗試呼叫中介層 API 應用程式，但呼叫將會失敗，因為前端執行所在的網域 (Web 應用程式 URL) 與後端 (API 應用程式 URL) 不同。瀏覽器的 [開發人員工具主控台] 視窗會顯示跨原始來源錯誤訊息。

	![](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## 為中介層 API 應用程式設定 CORS 。

在本節中，您將會設定ToDoListAPI API 應用程式，以允許來自您為 ToDoListAngular 專案所建立之 Web 應用程式的 JavaScript 呼叫。
 
8. 在瀏覽器中，移至 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [應用程式服務]，再按一下 ToDoListAPI (中介層) API 應用程式。

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. 在 [API 應用程式] 右側開啟的 [設定] 刀鋒視窗中，尋找 [API] 區段，再按一下 [CORS]。

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. 在文字方塊中輸入 ToDoListAngular (前端) Web 應用程式的 URL。例如，如果您將 ToDoListAngular 專案部署到名為 todolistangular0121 的 Web 應用程式，則允許來自 URL `https://todolistangular0121.azurewebsites.net` 的呼叫。

	或者，您也可以輸入星號 (*) 來指定接受所有的原始網域。

13. 按一下 [儲存]。

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	按一下 [儲存] 之後，API 應用程式會接受來自指定 URL 的 JavaScript 呼叫。在這個螢幕擷取畫面中，ToDoListAPI0223 API 應用程式會接受來自 ToDoListAngular Web 應用程式的 JavaScript 用戶端呼叫。

### 在啟用 CORS 的情況下測試應用程式

* 開啟瀏覽器至 Web 應用程式的 HTTPS URL。 

	這一次，應用程式會讓您檢視、新增、編輯和刪除待辦事項項目。

	![](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## App Service CORS 與 Web API CORS

在 Web API 專案中，您可以安裝 [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet 封裝，以便在程式碼中指定您的 API 將會接受來自哪些網域的 JavaScript 呼叫。
 
Web API CORS 支援比 App Service CORS 支援更有彈性。例如，在程式碼中您可以為不同動作方法指定不同的可接受原始來源，但對於 App Service CORS，您只能為所有 API 應用程式的方法指定一組可接受的原始來源。

> [AZURE.NOTE] 請勿嘗試在一個 API 應用程式中同時使用 Web API CORS 和 App Service CORS。App Service CORS 會優先獲得採用，而 Web API CORS 不會有任何作用。例如，如果您在 App Service 中啟用一個原始網域，並在您的 Web API 程式碼中啟用所有的原始網域，則 Azure API 應用程式僅接受來自您在 Azure 中指定之網域的呼叫。

### 如何在 Web API 程式碼中啟用 CORS

下列步驟概述啟用 Web API CORS 支援的程序。如需詳細資訊，請參閱[在 ASP.NET Web API 2 中啟用跨原始來源要求](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)。

1. 在 Web API 專案中，安裝 [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet 封裝。

1. 在 **WebApiConfig** 類別的 **Register** 方法中加入 `config.EnableCors()` 這行程式碼，如下列範例所示。

		public static class WebApiConfig
		{
		    public static void Register(HttpConfiguration config)
		    {
		        // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
		        config.EnableCors();
	
		        // Web API routes
		        config.MapHttpAttributeRoutes();
	
		        config.Routes.MapHttpRoute(
		            name: "DefaultApi",
		            routeTemplate: "api/{controller}/{id}",
		            defaults: new { id = RouteParameter.Optional }
		        );
		    }
		}

1. 在 Web API 控制器中，加入 `System.Web.Http.Cors` 命名空間的 `using` 陳述式，並將 `EnableCors` 屬性加入至控制器類別或個別的動作方法。在下列範例中，整個控制器都適用 CORS 支援。

		namespace ToDoListAPI.Controllers 
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ToDoListController : ApiController
 
	> **注意**：將萬用字元使用於具有 `EnableCors` 屬性的所有參數僅供示範之用，並且將您的 API 開啟至所有來源和所有 HTTP 要求 。請謹慎使用這個屬性。

## 後續步驟 

在本教學課程中，您已看到如何啟用 App Service CORS 支援，以便用戶端 JavaScript 程式碼可以呼叫不同網域中的 API。在下一篇 API Apps 入門系列文章中，您將了解 [App Service API 應用程式的驗證](app-service-api-authentication.md)。

<!---HONumber=AcomDC_0309_2016-->