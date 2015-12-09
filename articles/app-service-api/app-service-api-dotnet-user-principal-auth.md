<properties
	pageTitle="Azure App Service 中 API Apps 的使用者驗證 | Microsoft Azure"
	description="了解如何藉由僅允許經過驗證的使用者存取，保護 Azure App Service 中的 API 應用程式。"
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
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Azure App Service 中 API Apps 的使用者驗證

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀

本教學課程示範如何使用 Azure App Service 的驗證和授權功能來保護 API 應用程式，以及如何代表一般使用者取用 API 應用程式。教學課程中所顯示的驗證提供者為 Azure Active Directory，而範例用戶端是在瀏覽器中執行的 AngularJS 單一頁面應用程式。

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
這是一系列教學課程中的第三個，說明如何在 Azure App Service 中使用 API 應用程式。如需系列的詳細資訊，請參閱[在 Azure App Service 中開始使用 API Apps 和 ASP.NET](app-service-api-dotnet-get-started.md)。

## Azure App Service 中的驗證與授權

Azure App Service 提供內建的服務進行驗證和授權。您可以在自己的程式碼中隨意處理驗證，但如果想要將必須撰寫和維護的程式碼數量降到最低，請使用 App Service 提供的完整解決方案。

您可以使用 App Service 支援的任何驗證提供者 (共有五個)：Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶。而且，您可以保護以 App Service 支援的任何語言撰寫的 API：不需要在您的 API 中撰寫任何程式碼，您可以要求使用者登入或用來存取的權杖。

Azure App Service 會處理驗證，並將授權留給您的程式碼處理。您可以設定 App Service，只允許經過驗證的使用者呼叫您的 API，也可以允許所有的呼叫端。在任一情況下，App Service 會以 HTTP 標頭將驗證資訊傳遞給您的應用程式，您的程式碼即可使用該資訊來做出授權選擇。

* 在 .NET API 中，您可以使用 `Authorize` 屬性，而如需微調的授權，您可以輕鬆地根據宣告撰寫程式碼。系統會為您在 .NET 類別中填入宣告資訊。

* 對於以其他語言撰寫的 API，App Service 會在 HTTP 要求的授權標頭中傳遞 JWT 權杖。此外，Azure 會設定某些特殊標頭 (例如，`x-ms-client-principal-id`)，讓您更容易存取最重要的宣告。

如需 Azure App Service 中驗證和授權的詳細資訊，請參閱[展開 App Service 驗證 / 授權](/blog/announcing-app-service-authentication-authorization/)和 [App Service API Apps - 變更的內容](app-service-api-whats-changed.md)。

## ContactsList.Angular.AAD 範例專案

在本教學課程中，您會使用在[本系列第一個教學課程](app-service-api-dotnet-get-started.md)中下載的範例專案，以及您在第一個教學課程中建立的 Azure 資源 (API 應用程式和 Web 應用程式)。

ContactsList.Angular.AAD 專案是一個 AngularJS 用戶端，其包含可搭配 Azure Active Directory 使用的程式碼。此程式碼是以可在 [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) 儲存機制中找到的 AAD 範例為基礎。

ContactsList.Angular.AAD 專案中的程式碼結構與較簡單的 ContactsLists.Angular 專案不同。呼叫 API 的程式碼位於 ContactsList.Angular.AAD 專案的 *app/scripts/contactsSvc.js* 檔案中。

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

在這裡，`Get` 方法會標示為 `getItems`，而在控制器 (*app/scripts/contactsCtrl.js*) 中，`getItems` 會連接至 `$scope.populate`。

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

在檢視 (*app/views/Contacts.html*) 中，初始化時會呼叫 $scope.populate。

		<div ng-init="populate()">

## 在 Azure 中設定驗證與授權

1. 在 [Azure 入口網站](https://portal.azure.com/) 中，瀏覽至您在第一個教學課程中建立的 API 應用程式的 [API 應用程式] 刀鋒視窗，然後按一下 [設定]。

2. 尋找 [功能] 區段，然後按一下 [驗證/授權]。

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. 在 [驗證/授權] 刀鋒視窗中，按一下 [開啟]。

4. 在 [要求未經驗證時所採取的動作] 下拉式清單中，選取 [登入 Azure Active Directory]。

5. 在 [驗證提供者] 底下，按一下 [Azure Active Directory]。

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. 在 [Azure Active Directory 設定] 刀鋒視窗中，按一下 [快速]。

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure 會自動在 AAD 租用戶中建立 AAD 應用程式。請記下新的 AAD 應用程式的名稱，因為您稍後在移至 Azure 傳統入口網站以取得其用戶端識別碼時需要選取它。

7. 按一下 [確定]。

10. 在 [驗證/授權] 刀鋒視窗中，按一下 [儲存]。

8. 若要驗證 API 應用程式現已受到保護，請移至 API 應用程式的 URL + `/swagger`如同您在第一個教學課程中一樣使用 Swagger UI。

	這次系統會將您重新導向至登入頁面。

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，移至 [Azure Active Directory]。

12. 在 [目錄] 索引標籤中，選取您的 AAD 租用戶。

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. 按一下 [應用程式] > [我的公司擁有的應用程式]，然後按一下核取記號。

	您可能還必須重新整理頁面，才能看見新的應用程式。

15. 在應用程式清單中，按一下當您針對 API 應用程式啟用驗證時 Azure 為您建立的應用程式名稱。

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. 按一下 [設定]。

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. 在頁面底部，按一下 [管理資訊清單] > [下載資訊清單]，並將檔案儲存在您可以編輯它的位置。

16. 在下載的資訊清單檔案中，搜尋 `oauth2AllowImplicitFlow` 屬性。將這個屬性的值從 `false` 變更為 `true`，然後儲存檔案。

16. 按一下 [管理資訊清單] > [上傳資訊清單]，然後上傳您在前述步驟中更新的檔案。

17. 一直保持頁面開啟，讓您可以複製並貼上值，並且在稍後的教學課程步驟中更新頁面上的值。

## 設定使用 https 的 Visual Studio 專案

1. 在 [方案總管] 中，按一下 ContactList.API 專案，然後在 [屬性] 視窗中將 [已啟用 SSL] 變更為 [True]。

2. 複製 [SSL URL]。

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. 以滑鼠右鍵按一下 ContactsList.API 專案，然後按一下 [屬性]。

5. 按一下 [Web] 索引標籤，將 SSL URL 貼到 [專案 Url] 欄位中，然後儲存您的變更。

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. 請遵循相同的程序，對 ContactsList.Angular.AAD 專案啟用 SSL。

2. 將 ContactsList.API 專案和 ContactsList.Angular.AAD 專案設定為起始專案，並將 ContactsList.API 專案設為先開始。

## 更新 ContactsList.Angular.AAD 專案中的端點 URL 和 AAD 設定

7. 在 ContactsList.Angular.AAD 專案中，開啟 *app/scripts/app.js* 檔案。

8. 在設定 `endpoints` 變數的程式碼中，確定它具有 ContactsList.API 專案的正確 SSL URL，並從傳統入口網站中 AAD 應用程式的 [設定] 索引標籤，以 AAD 應用程式的用戶端識別碼的實際值取代 "yourclientid" 的兩個執行個體。確定端點 URL 是以斜線結尾。

	程式碼如下列範例所示：

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. 此外，在 *app.js* 的 `adalProvider.init` 程式碼中，以實際值取代 "{您的租用戶 url}" 和 "{您的用戶端識別碼}"。

	程式碼如下列範例所示：

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

10. 在 *app/scripts/contactsSvc.js* 中，確定 apiEndpoint 已設定為 ContactsList.API 專案的正確 SSL URL。

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## 更新 ContactsList.API 專案中的 AAD 設定

1. 在 ContactsList.API 專案中，開啟應用程式 *Web.config* 檔案。

2. 在 appSettings 元素中，將 ida:Authority 設定為值 "https://login.windows.net/{您的租用戶 url}"，並將 ida:ClientId 設定為 AAD 應用程式的用戶端識別碼，如下列範例所示。

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## 針對 localhost 設定 AAD 應用程式 

1. 在傳統入口網站中 AAD 應用程式的 [設定] 索引標籤中，於 [登入 URL] 欄位中貼上 ContactsList.Angular.AAD 專案 SSL URL 並移除尾端斜線。

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. 在 [設定] 索引標籤底部附近的 [回覆 URL] 欄位中，貼上 ContactsList.Angular.AAD 專案 SSL URL，取代現有的值並保留尾端斜線。

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. 按一下 [儲存]。

## 在本機執行 API 和用戶端專案

在本機執行 HTTPS 時，您會收到有關 localhost 的 IIS Express SSL 憑證的警告訊息。若要在本機執行，您可以略過這些訊息。如果想要的話，您可以略過本節，直接移至下一節以準備在 Azure 中執行應用程式和 API。

如果您無法登入，請嘗試使用不同的瀏覽器，或將 Incognito 或 InPrivate 視窗開啟至 AngularJS 專案的 URL，例如 `https://localhost:44301`。

5. 在 Visual Studio 中，按 F5 可在本機執行 API 和 AngularJS 專案。

	AngularJS 應用程式的首頁索引標籤隨即出現。

10. 按一下 [登入] 索引標籤。

	系統會提示您登入。

7. 使用 AAD 租用戶中使用者的使用者認證進行登入。

10. 按一下 [連絡人] 索引標籤。

	[連絡人] 頁面隨即出現。

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. 關閉瀏覽器視窗。

ContactsList.Angular.AAD 專案程式碼會要求您先登入，然後才會呼叫 API 並顯示連絡人。不過，沒有任何事物可防止未經驗證的呼叫端呼叫 API。您可以在開啟至 ContactsList.API 專案 SSL URL 的瀏覽器視窗中執行 Swagger UI，以確認這一點。只有當 API 在 Azure App Service 中執行時，才能防止未經驗證的呼叫端存取。

在下列各節中，您會設定專案和 AAD 以便在 Azure App Service 中執行用戶端和 API、將專案部署至 Azure，然後在 Azure 中進行測試。

## 設定 ContactsList.Angular.AAD 專案以呼叫 Azure API 應用程式

1. 在 ContactsList.Angular.AAD 專案中，開啟 *app/scripts/app.js* 檔案。

2. 註解化 localhost 端點、取消註解 Azure 端點，並以您的 API 應用程式名稱取代 "{您的 api 應用程式名稱}"。

	程式碼現在類似下列範例。

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. 在 *app/scripts/contactsSvc.js* 檔案中進行相同的端點 URL 變更。

	程式碼現在類似下列範例。

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## 針對 Azure Web 應用程式設定 AAD 應用程式

1. 在傳統入口網站中 AAD 應用程式的 [設定] 索引標籤中，於 [登入 URL] 欄位中刪除 ContactsList.Angular.AAD 專案 SSL URL，並以 Web 應用程式的基底 URL (不含尾端斜線) 加以取代 (請注意，這是 Web 應用程式的 URL，而非 API 應用程式的 URL)。

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. 在 [回覆 URL] 欄位中，以 Web 應用程式的基底 URL 取代 ContactsList.Angular.AAD 專案 SSL URL，並保留尾端斜線。

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. 按一下 [儲存]。

## 將 ContactsList.API 專案部署至 Azure

8. 在 [方案總管] 中，以滑鼠右鍵按一下 ContactsList.API 專案，然後按一下 [發佈]。

	[發佈 Web] 精靈隨即開啟至此專案最後使用的發佈設定檔，這用於部署到您先前建立的 API 應用程式。

7. 按一下 [發佈]。

8. 關閉自動開啟的瀏覽器視窗。

## 將 ContactsList.Angular.AAD 專案部署至 Azure

8. 在 [方案總管] 中，以滑鼠右鍵按一下 ContactsList.Angular.API 專案，然後按一下 [發佈]。

9. 按一下 [Microsoft Azure App Service]。

10. 在 [App Service] 對話方塊的 [訂用帳戶] 下拉式清單中，選擇您的訂用帳戶。

11. 展開您為本教學課程建立的資源群組，然後選取您在第二個教學課程中建立的 Web 應用程式。

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. 按一下 [確定]。

12. 在 [發佈 Web] 精靈中，按一下 [連接] 索引標籤，然後在 [目的地 URL] 方塊中將 `http://` 變更為 `https://`

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. 在 [發佈 Web] 精靈中，按一下 [設定] 索引標籤，展開 [檔案發佈選項]，然後選取 [移除目的地的其他檔案] 核取方塊。

7. 按一下 [發佈]。

	Visual Studio 會部署專案並將瀏覽器開啟至應用程式的首頁。

## 在 Azure 中測試 AngularJS Web 應用程式

8. 按一下 [連絡人] 索引標籤。

	系統會提示您登入。

9. 使用 AAD 租用戶中使用者的認證進行登入。

10. [連絡人] 頁面隨即出現。

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. 若要驗證 API 應用程式是否受保護，請在 InPrivate 或 Incognito 瀏覽器視窗中移至其 Swagger UI URL。

	系統會將您重新導向至登入頁面。

	前端現在可以代表經過驗證的使用者呼叫 API，但未經驗證的使用者無法呼叫 API。

## 後續步驟

在本教學課程中，您使用 App Service 的驗證和授權功能來限制 API 應用程式存取，讓只有經過驗證的使用者才可以呼叫它。在本系列的下一個教學課程中，您將學習如何[對於服務對服務的案例限制您的 API 應用程式存取](app-service-api-dotnet-service-principal-auth.md)。

<!---HONumber=AcomDC_1203_2015-->