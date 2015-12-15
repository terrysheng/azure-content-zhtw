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
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# 使用 CORS 從 JavaScript 取用 API 應用程式

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀

本教學課程示範如何在與 API 應用程式不同的網域所提供的網站中，從 JavaScript 程式碼取用 API 應用程式。範例用戶端會使用 AngularJS。

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
這是一系列教學課程中的第二個，內容是關於在 Azure App Service 中使用 API 應用程式。若要前往此系列課程中的第一個，請選擇頁面頂端的 [主題] 下拉式清單中的第一個主題。

## Azure App Service 中的 CORS 支援

基於安全性理由，瀏覽器的預設行為會防止 JavaScript 對與 JavaScript 原始網域不同的網域進行 API 呼叫。例如，您可以從 contoso.com 網頁對 contoso.com API 端點進行呼叫，但不能對 fabrikam.com 端點進行呼叫。跨原始來源資源共用 (CORS) 是一個網際網路通訊協定，其設計用來啟用您需要進行這類跨網域 API 呼叫的案例。在 Azure App Service 中，您的 JavaScript 用戶端在網頁中執行，而您的 API 在 API 應用程式中執行，這就是這類案例的範例之一。

Azure App Service 可讓您輕鬆設定允許呼叫 API 應用程式的網域，而且在 Azure Apps Service 所支援的所有語言 (例如 Java 和 Node.js) 中，CORS 功能都能以同樣的方式運作。

## 如何遵循本教學課程

本教學課程會使用您在[此系列的 ASP.NET 版本的第一個教學課程](app-service-api-dotnet-get-started.md)中所下載並為其建立 API 應用程式的範例應用程式。如果您想要使用 Java 或 Node.js，請參閱下方的 [CORS 組態區段](#corsconfig)，其內容中有提供適用於所有 API 應用程式的一般指示。

## ContactsList.Angular 範例專案

在 [ContactsList 範例應用程式](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)中，ContactsList.Angular 專案是適用於 ContactsList.API Web API 專案的簡單 AngularJS 用戶端。

呼叫 API 的 AngularJS JavaScript 程式碼位於 ContactsList.Angular 專案的 *index.html* 檔案中。此程式碼會定義函式並將它們加入至 `$scope` 物件，如下所示 API 的 Get 方法已定義為 `$scope.refresh()`。

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

當頁面載入 (在上述程式碼片段的結尾) 並連接至 UI 中的 [重新整理] 按鈕時，程式碼就會呼叫 $scope.refresh() 方法。

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## 在本機執行 AngularJS 專案

在本節中，您會確認您可以在本機執行用戶端，並可於 API 在本機執行時予以呼叫。

1. 將 ContactsList.API 和 ContactsList.Angular 專案設定為起始專案，而 ContactsList.API 開始於 ContactsList.Angular 之前。 

2. 按 F5 啟動專案。

	AngularJS UI 會顯示本機儲存的連絡人，而您可以使用 UI 來新增及刪除連絡人。

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. 關閉瀏覽器視窗。

## 將 AngularJS 專案變更為指向 Azure API 應用程式 

接下來，您將在雲端執行 AngularJS 前端並呼叫在雲端執行的 API 後端。將前端部署至 Azure 之前，您必須在 AngularJS 專案中變更 API 端點，以便程式碼呼叫您稍早建立的 Azure API 應用程式。

1. 在 ContactsList.Angular 專案中，開啟 *index.html*。

2. 註解化可將 `baseUrl` 設定為 localhost URL 的這一行程式碼，取消註解將 `baseUrl` 設定為 azurewebsites.net URL 的這一行程式碼，並以您稍早建立的 API 應用程式的實際名稱取代預留位置。如果您將 API 應用程式命名為 ContactsListAPI，則程式碼現在看起來如下列範例所示。

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### 將 ContactsList.Angular 專案部署到 Web 應用程式

您可以建立用來將 AngularJS 專案部署到的新 Web 應用程式，但在本教學課程中，您將部署至上一個教學課程中所建立的相同 Web 應用程式。Web 應用程式名稱可能會反映您原先將 ASP.NET MVC 專案部署至該應用程式的這個事實，但在此部署之後，該應用程式將會執行 AngularJS 程式碼。

8. 在 [方案總管] 中，以滑鼠右鍵按一下 ContactsList.Angular 專案，然後按一下 [發佈]。

9. 按一下 [設定檔] 索引標籤。

3.  在 [發佈 Web] 精靈的 [設定檔] 步驟中，按一下 [Microsoft Azure App Service]。

4. 在 [App Service] 對話方塊中，選擇您的訂用帳戶。

5. 將 [檢視] 設為預設值 [資源群組]，展開您為本教學課程系列建立的資源群組。

7. 選取您在第一個教學課程中建立的 Web 應用程式 (請確定您未選取 API 應用程式)，然後按一下 [確定]。

8. 按一下 [設定] 索引標籤。

9. 展開 [檔案發佈選項]，然後選取 [移除目的地的其他檔案]。

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	當您將 Web 專案部署至現有的 App Service Web 應用程式時，您通常不想使用「移除其他檔案」選項，因為變更通常會是更新或新的檔案。在此情況下，您會將不同的專案部署到相同的 Web 應用程式，因此先前部署中可能有許多檔案不需要位於新的 Web 應用程式中。

10. 按一下 [發佈]。

	Visual Studio 會將 ContactsList.Angular 專案部署到 Web 應用程式，並將瀏覽器開啟至 Web 應用程式的 URL。瀏覽器會顯示您看到在本機執行的相同 AngularJS UI，但現在會顯示失敗，因為您的前端與後端 (API 應用程式 URL) 是在不同的網域 (Web 應用程式 URL) 中執行。

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a>在 Azure 中為目標 API 應用程式設定 CORS

8. 在另一個瀏覽器視窗中，移至 [Azure 入口網站](https://portal.azure.com/)。

9. 按一下 [瀏覽] > [API Apps]，然後選取目標 API 應用程式。在本教學課程中，這是指您在第一個教學課程中針對 ContactsList.API 專案所建立的 API 應用程式。

10. 在 [API 應用程式] 刀鋒視窗中，按一下 [設定]。

11. 尋找 [API] 區段，然後按一下 [CORS]。

12. 在文字方塊中，輸入您想要允許來自其中之呼叫的 URL。例如，如果您已將 JavaScript 應用程式部署至名為 ContactsListMVC 的 Web 應用程式，請輸入 "http://contactslistmvc.azurewebsites.net"。

	請注意，除了輸入 URL，您也可以輸入星號 (*) 來指定接受所有的原始網域。

13. 按一下 [儲存]。

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. 前往顯示 AngularJS 用戶端的瀏覽器視窗，然後重新整理頁面或按一下 [重新整理] 按鈕。

	此頁面現在會顯示儲存在 Azure API 應用程式的檔案系統中的連絡人。

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### Azure 資源管理員工具中的 CORS

您也可以使用 Azure PowerShell、CLI 或[資源總管](https://resources.azure.com/)等 Azure 資源管理員工具，來設定 API 應用程式的 CORS。

在您的 <site name>/web 資源的 Microsoft.Web/sites/config 資源類型上設定 `cors` 屬性。例如，在 [資源總管] 中移至 [訂用帳戶] > {您的訂用帳戶} > [resourceGroups] > {您的資源群組} > [提供者] > [Microsoft.Web] > [網站] > {您的網站} > [組態] > [web]，您就會看到 cors 屬性：

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

### App Service CORS 與 Web API CORS

針對 ASP.NET Web API 專案，也可以很容易地在程式碼中設定 CORS，詳情請見下一節。不過，如果您同時使用 App Service CORS 和 Web API CORS，則會優先使用 App Service CORS，至於 Web API CORS 則不會有任何作用。例如，如果您在 App Service 中啟用一個原始網域，並在您的 Web API 程式碼中啟用所有的原始網域，則 Azure API 應用程式僅接受來自您在 Azure 中指定之網域的呼叫。


## 如何在 Web API 程式碼中設定 CORS

在 Web API 專案中，您可以安裝 [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet 封裝，以便在程式碼中指定您的 API 將會接受來自哪些網域的 JavaScript 呼叫。此程序已詳載於[在 ASP.NET Web API 2 中啟用跨原始來源要求](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)。若為使用 ASP.NET Web API 建置的 API 應用程式，此程序完全一樣，但會在以下摘要說明。

1. 在 Web API 專案中，於 **WebApiConfig** 的 **Register** 方法中加入 `config.EnableCors()` 這行程式碼，如下列範例所示。 

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

1. 在 Web API 控制器中，於 `ContactsController` 類別或個別動作方法中加入 `EnableCors` 屬性。在下列範例中，整個控制器都適用 CORS 支援。

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **注意**：將萬用字元使用於具有 `EnableCors` 屬性的所有參數僅供示範之用，並且將您的 API 開啟至所有來源和所有 HTTP 要求 。請謹慎使用這個屬性。

## 後續步驟 

在本教學課程中，您已看到如何啟用 App Service CORS 支援，以便用戶端 JavaScript 程式碼可以呼叫不同網域中的 API。在下一篇 API Apps 入門系列文章中，您將了解 [App Service API 應用程式的驗證](app-service-api-authentication.md)。

<!---HONumber=AcomDC_1210_2015-->