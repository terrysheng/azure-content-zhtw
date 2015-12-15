<properties
	pageTitle="在 Azure App Service 中開始使用 API Apps 和 ASP.NET | Microsoft Azure"
	description="了解如何在 Azure App Service 使用 Visual Studio 2015 建立、部署和取用 ASP.NET API 應用程式。"
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
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# 在 Azure App Service 中開始使用 API Apps 和 ASP.NET

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概觀

在本教學課程中，您會建立 [App Service API 應用程式](app-service-api-apps-why-best-platform.md)、將 ASP.NET Web API 部署到 API 應用程式，並從 ASP.NET MVC 用戶端取用 API 應用程式。本教學課程假設您已熟悉 ASP.NET，但先前沒有使用 Microsoft Azure 的經驗。完成此教學課程後，您將有個 Web API 和用戶端應用程式已在雲端中啟動並執行。

範例應用程式是簡單的連絡人清單。下圖顯示 MVC 應用程式如何顯示從 API 接收的資料。

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## 您將學到什麼

Azure App Service 有三個功能特別適合用來開發和裝載 API：

* 整合的 API 中繼資料支援
* CORS 支援
* 驗證和授權支援
 
這是一系列教學課程中的第一個，專門負責介紹這些功能。本教學課程聚焦在 API 中繼資料，第二個聚焦在 CORS，第三和第四個則聚焦在驗證和授權。

在這些教學課程中，您將學會：

* 如何安裝 Azure SDK for .NET 好讓電腦準備好用於 Azure 開發。
* 如何透過 Visual Studio 2015 的內建工具，在 Azure App Service 中使用 API 應用程式和 Web 應用程式。
* 如何使用 Swashbuckle NuGet 封裝來動態產生 Swagger API 定義 JSON，以便自動進行 API 探索。
* 如何使用自動產生的用戶端程式碼，從 .NET 用戶端取用 API 應用程式。
* 如何使用 Azure 入口網站設定 API 應用程式中繼資料的端點。
* 當 JavaScript 用戶端來自與 API 不同的網域時，如何使用 CORS 從該用戶端呼叫 API 應用程式。
* 如何使用 Azure Active Directory 防止未經驗證存取 API。
* 如何為已登入 Azure Active Directory 的使用者取用受保護的 API。
* 如何透過服務主體取用受保護的 API。

## 必要條件

### ASP.NET Web API

本教學課程假設您已熟悉 ASP.NET Web API。如果您需要相關簡介，請參閱[開始使用 ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。

## Visual Studio 2015

這些指示和螢幕擷取畫面均假設您是使用 Visual Studio 2015，但同樣的指引也適用於 Visual Studio 2013。

## Azure 帳戶

您需要有 Azure 帳戶，才能完成本教學課程。您可以：

* [免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)。您將獲得能用來試用 Azure 付費服務的額度。即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務和功能，例如 Azure App Service 中的 Web Apps 功能。
* [啟用 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

如果您想要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)。您可以於該處，在 App Service 中立即建立短期的入門應用程式 — 不需信用卡，不需任何承諾。

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

本教學課程需要 2.8.1 版或更新版本的 Azure SDK for.NET。

## 範例應用程式概觀

您將在本教學課程中部署至 API 應用程式和 Web 應用程式的程式碼位於 [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) GitHub 儲存機制中。ContactsList Visual Studio 解決方案包含下列專案：

* **ContactsList.API** - 可傳回名稱和電子郵件地址清單的 ASP.NET Web API 專案。初始呼叫 Get 方法會傳回 3 個硬式編碼的連絡人，而後續呼叫 Put、Post 和 Delete 方法則會在本機 JSON 檔案中儲存變更。
* **ContactsList.MVC** -ContactsList API 的 ASP.NET MVC 用戶端。
* **ContactsList.Angular** -ContactsList API 的簡單 AngularJS UI 用戶端。示範如何呼叫未受保護 (不需驗證) 的 API 應用程式。
* **ContactsList.Angular.AAD** - AngularJS 用戶端，可顯示如何使用 Azure Active Directory 驗證使用者。
* **CompanyContacts.API** - ASP.NET Web API 專案，可傳回硬式編碼的連絡人清單以回應 Get 要求。由 **ContactsList.API** Get 方法呼叫，以示範如何使用服務對服務 (服務主體) 驗證呼叫 API。
 
## 下載範例應用程式 

1. 下載 [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) 儲存機制。

	您可以[下載 .zip 檔案](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip)或複製您的本機電腦上的儲存機制。

2. 在 Visual Studio 2015 或 2013 中開啟 ContactsList 解決方案。

2. 建置解決方案以還原 NuGet 封裝。

## 使用 Swagger 中繼資料和 UI

Azure App Service 內建支援 [Swagger 2.0](http://swagger.io/) API 中繼資料。每個 API 應用程式都可以定義會以 Swagger JSON 格式傳回 API 中繼資料的 URL 端點。從該端點傳回的中繼資料可以用來產生用戶端程式碼，讓您更輕鬆地使用 API。

本節教學課程將讓您了解如何自動產生 ASP.NET Web API 專案的中繼資料，並且您將會執行 API 測試工具。這些工作還不會用到 Azure App Service，稍後您將會了解 API Apps 如何利用中繼資料。

若要提供 ASP.NET Web API 專案的 Swagger 2.0 中繼資料，您可以安裝 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 封裝。Swashbuckle 使用「反射」來動態產生中繼資料。Swashbuckle NuGet 封裝已安裝在您所下載的 ContactsList.API 專案中，而當您使用 [Azure API 應用程式] 專案範本來建立新專案時便已安裝 (在 Visual Studio 中：[檔案] > [新增] > [專案] > [ASP.NET Web 應用程式] > [Azure API 應用程式])。

在教學課程的這一節中，請看一下所產生的 Swagger 2.0 中繼資料，然後試用以 Swagger 中繼資料為基礎的測試 UI。

2. 將 ContactsList.API 專案設定為起始專案 (而非 CompanyContacts.API 專案，此專案會用於稍後的其中一個教學課程)。
 
4. 按 F5 以在偵錯模式中執行專案。

	瀏覽器會開啟並顯示 [403 禁止] 頁面。

	![](./media/app-service-api-dotnet-get-started/403.png)

12. 在瀏覽器的位址列中，於 URL 結尾加入 `swagger/docs/v1`，然後按 Enter 鍵 (URL 會是 `http://localhost:51864/swagger/docs/v1`)。

	這是 Swashbuckle 用來傳回 API 的 Swagger 2.0 JSON 中繼資料的預設 URL。如果您是使用 Internet Explorer，瀏覽器會提示您下載 v1.json 檔案。

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	如果您是使用 Chrome 或 Edge，瀏覽器會在瀏覽器視窗中顯示 JSON。

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	下列範例顯示 API 的 Swagger 中繼資料的第一個區段 (包含 Get 方法的定義)。此中繼資料可驅動您將在下列步驟中使用的 Swagger UI，而您將在本教學課程稍後的章節中使用它來自動產生用戶端程式碼。

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. 關閉瀏覽器。

3. 在 [方案總管] 的 ContactsList.API 專案中，開啟 *App\_Start\\SwaggerConfig.cs* 檔案，然後向下捲動至下列程式碼並予以取消註解。

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	SwaggerConfig.cs 檔案建立於您在專案中安裝 Swashbuckle 封裝時。此檔案會提供數種方式來設定 Swashbuckle。

	您已取消註解的程式碼會啟用您將使用於下列步驟中的 Swagger UI。當您使用 API 應用程式專案範本建立 Web API 專案時，基於安全性考量，預設會註解化此程式碼。

5. 再次執行此專案。

3. 在瀏覽器的位址列中，於 URL 結尾加入 `swagger`，然後按 Enter 鍵 (URL 會是 `http://localhost:51864/swagger`)。

4. 當 Swagger UI 頁面出現時，按一下 [連絡人] 以查看可用的方法。

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. 按一下 [Get] > [立即試用]。

	Swagger UI 會呼叫 ContactsList Get 方法並顯示 JSON 結果。

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. 按一下 [Post]，然後按一下 [模型結構描述] 之下的方塊。

	按一下模型結構描述會預先填入輸入方塊，而您可以在其中指定 Post 方法的參數值。

	![](./media/app-service-api-dotnet-get-started/post.png)

7. 在 `contact` 參數輸入方塊中變更 JSON，讓它看起來如同下列範例，或以您自己的名稱和電子郵件地址替代：

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. 按一下 [立即試用]。

	ContactsList API 會傳回 HTTP 200 以及可確認新增內容的回應本文。

11. 按一下 [Get] > [立即試用]。

	Get 方法回應現在包含新的連絡人。

12. 也請試用 Put、Delete 和 Get by ID方法，然後關閉瀏覽器。

Swashbuckle 可搭配任何 ASP.NET Web API 專案使用。如果您要將 Swagger 中繼資料產生新增至現有的專案，只需安裝 Swashbuckle 封裝。如果您要建立將部署為 App Service API 應用程式的新專案，請使用 ASP.NET **Azure API 應用程式**專案範本，如下圖所示。

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

此範本會建立已安裝 Swashbuckle 的 Web API 專案。

**附註：**根據預設，Swashbuckle 可能會為控制器方法產生重複的 Swagger 作業識別碼。如果控制器有多載的 HTTP 方法，例如 `Get()` 和 `Get(id)`，就會發生此情況。如需如何處理多載的相關資訊，請參閱[自訂 Swashbuckle 產生的 API 定義](app-service-api-dotnet-swashbuckle-customize.md)。如果您在 Visual Studio 中使用 Azure API 應用程式範本建立 Web API 專案，*SwaggerConfig.cs* 檔案中就會自動新增用來產生唯一作業識別碼的程式碼。

## 在 Azure 中建立 API 應用程式並將 ContactsList.API 專案部署至該應用程式

在本節中，您會使用已整合至 Visual Studio 的 [發佈 Web] 精靈中的 Azure 工具，在 Azure 中建立新的 API 應用程式。然後您會將 ContactsList.API 專案部署到新的 API 應用程式，並藉由再次執行 Swagger UI 來呼叫 API，而這次則是在雲端執行。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 ContactsList.API 專案，然後按一下 [發佈]。

3.  在 [發佈 Web] 精靈的 [設定檔] 步驟中，按一下 [Microsoft Azure App Service]。

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. 如果您尚未登入，請登入您的 Azure 帳戶；或者如果過期，請重新整理您的認證。

4. 在 [App Service] 對話方塊中，選擇您想要使用的 Azure [訂用帳戶]，然後按一下 [新增]。

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. 在 [建立 App Service] 對話方塊的 [主控] 索引標籤中，按一下 [變更類型]，然後按一下 [API 應用程式]。

	![](./media/app-service-api-dotnet-get-started/apptype.png)

4. 輸入在 *azurewebsites.net* 網域中唯一的 [API 應用程式名稱]。

	Visual Studio 會將日期時間字串附加至專案名稱，藉此提議唯一的名稱。如果想要的話，您可以接受該名稱。

	如果您輸入了其他人已使用的名稱，您就會在右邊看到紅色驚嘆號，而不是綠色勾號，這代表您需要輸入不同的名稱。

	Azure 將使用這個名稱做為應用程式 URL 的前置詞。完整的 URL 組合是這個名稱加上 *.azurewebsites.net*。例如，若名稱為 `ContactsListAPI`，URL 會是 `contactslistapi.azurewebsites.net`。

6. 在 [資源群組] 下拉式清單中，輸入 "ContactsListGroup" 或其他您偏好使用的名稱。

	此方塊可讓您選取現有的[資源群組](../azure-preview-portal-using-resource-groups.md)，或藉由輸入與您的訂用帳戶中任何現有的資源群組不同的名稱，以建立新的資源群組。

	在本教學課程中，最好建立新的資源群組，因為這麼做即可在一個步驟中輕鬆刪除您為本教學課程建立的所有 Azure 資源。

4. 按一下 [App Service 方案] 下拉式清單旁邊的 [新增] 按鈕。

	![](./media/app-service-api-dotnet-get-started/createas.png)

	如需 App Service 方案的詳細資訊，請參閱 [App Service 方案概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

5. 在 [設定 App Service 方案] 對話方塊中，輸入 "ContactsListPlan" 或其他您偏好使用的名稱。

5. 在 [位置] 下拉式清單中，選擇最接近您的位置。

	這個設定會指定應用程式將執行所在的 Azure 資料中心。您可以在本教學課程中選取任何區域，這不會造成顯著的差異。但是對於生產用的應用程式，您的伺服器應盡可能靠近存取該應用程式的用戶端，以將[延遲](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)降到最低。

5. 在 [大小] 下拉式清單中，按一下 [免費]。

	在本教學課程中，免費定價層會提供足夠的效能。

6. 在 [設定 App Service 方案] 對話方塊中，按一下 [確定]。

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. 在 [建立 App Service] 對話方塊中，按一下 [建立]。

	Visual Studio 會建立 API 應用程式，並建立發佈設定檔，其中包含新的 API 應用程式所需要的所有設定。在下列步驟中，您可以使用新的發佈設定檔來部署專案。
 
	**注意：**有其他方法可在 Azure App Service 中建立 API 應用程式。在 Visual Studio 中，當您建立新專案時可使用相同的對話方塊。您也可以使用 Azure 入口網站、[適用於 Windows PowerShell 的 Azure Cmdlet](../powershell-install-configure.md) 或[跨平台命令列介面](../xplat-cli.md)來建立 API 應用程式。

8. 在 [發佈 Web] 精靈的 [連接] 索引標籤中，按 [發佈]。

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio 會將 ContactsList.API 專案部署到新的 API 應用程式，並將瀏覽器開啟至 API 應用程式的 URL。[已成功建立] 頁面隨即出現在瀏覽器中。

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. 在瀏覽器的位址列中將 "swagger" 新增至 URL，然後按 Enter 鍵(URL 會是 `http://{apiappname}.azurewebsites.net/swagger`)。

	瀏覽器會顯示您稍早看到的相同 Swagger UI，但該 UI 現在在雲端執行。試用 Get 方法，而您會看見您已還原為預設的 3 個連絡人，因為您稍早所做的變更已儲存在本機檔案中；您現在所做的變更將會儲存在 Azure API 應用程式的檔案系統中。

12. 開啟 [Azure 入口網站](https://portal.azure.com/)。
 
14. 按一下 [瀏覽] > [API Apps] > {新的 API 應用程式}。

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. 按一下 [設定]，然後在 [設定] 刀鋒視窗中尋找 API 區段並按一下 [API 定義]。

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	[API 定義] 刀鋒視窗可讓您指定會以 JSON 格式傳回 Swagger 2.0 中繼資料的 URL。當 Visual Studio 建立 API 應用程式時，它會將 API 定義 URL 設定為您稍早所見的預設值，也就是 API 應用程式的基底 URL 加上 `/swagger/docs/v1`。

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	當您選取要對其產生用戶端程式碼的 API 應用程式時，Visual Studio 會從這個 URL 擷取中繼資料。

### Azure 資源管理員工具中的 API 定義 URL

您也可以使用 Azure PowerShell、CLI 或[資源總管](https://resources.azure.com/)等 Azure 資源管理員工具，來設定 API 應用程式的 API 定義 URL。

在您的 <site name>/web 資源的 Microsoft.Web/sites/config 資源類型上設定 `apiDefinition` 屬性。例如，在 [資源總管] 中移至 [訂用帳戶] > {您的訂用帳戶} > [resourceGroups] > {您的資源群組} > [提供者] > [Microsoft.Web] > [網站] > {您的網站} > [組態] > [web]，您就會看到 cors 屬性：

		"apiDefinition": {
		  "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
		}

## <a id="codegen"></a> 使用所產生的用戶端程式碼從 .NET 用戶端取用 

將 Swagger 整合到 Azure API 應用程式的優點之一，就是自動產生程式碼。產生的用戶端類別讓您能更容易地撰寫會呼叫 API 應用程式的程式碼。

在這一節中，您將了解如何從 ASP.NET MVC Web 應用程式取用 API 應用程式。您會先在本機執行 MVC 用戶端和 Web API，然後將此用戶端部署至 Azure App Service 中的新 Web 應用程式並且在雲端執行。

### 產生用戶端程式碼

您可以利用 Visual Studio，或從命令列來為 API 應用程式產生用戶端程式碼。在本教學課程中，您將使用 Visual Studio。如需了解如何從命令列進行這項作業，請參閱 GitHub.com 上 [Azure/autorest](https://github.com/azure/autorest) 儲存機制的讀我檔案。

ContactsList.MVC 專案已有產生的用戶端程式碼，但是您要將其刪除並重新產生，以讓自己的 API 應用程式 URL 成為預設目標 URL。

1. 在 Visual Studio [方案總管] 中，在 ContactsList.MVC 專案中刪除 ContactsList.API 資料夾。

	此資料夾先前已透過您即將進行的程式碼產生程序加以建立。

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. 以滑鼠右鍵按一下 ContactsList.MVC 專案，然後按一下 [新增] > [REST API 用戶端]。

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. 在 [加入 REST API 用戶端] 對話方塊中，按一下 [從 Microsoft Azure API 應用程式下載]，然後按一下 [瀏覽]。

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. 在 [App Service] 對話方塊中，展開 **ContactsListGroup** 資源群組，並選取您的 API 應用程式，然後按一下 [確定]。

	此對話方塊可讓您用數種方法組織清單中的 API 應用程式，以免您有太多 API 應用程式而無法捲動。此外，也可讓您輸入搜尋字串來依名稱篩選 API 應用程式。

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	如果在清單中沒有看到 API 應用程式，可能是因為您在建立 API 應用程式時，不小心略過了將類型從 Web 應用程式變更為 API 應用程式的步驟。在此情況下，您可以藉由重複進行稍早的步驟建立新的 API 應用程式。除非您先前往入口網站刪除 Web 應用程式，否則您必須為 API 應用程式選擇不同的名稱。

	請注意，當您返回 [加入 REST API 用戶端] 對話方塊時，文字方塊中已填入您稍早在入口網站中看到的 API 定義 URL 值。

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	或者，您可以直接輸入 URL，而不需透過瀏覽對話方塊。例如，如果您將 API 部署至 Web 應用程式，但它未顯示在瀏覽對話方塊中，您可以在此手動輸入可傳回 Swagger 中繼資料的 URL。

	另外，也請注意 [選取現有的 Swagger 中繼資料檔案] 選項。如果您要在部署至 Azure 之前產生程式碼，您可以在本機執行、下載 Swagger JSON 檔案，並在此處予以選取。

9. 在 [加入 REST API 用戶端] 對話方塊中，按一下 [確定]。

	Visual Studio 會建立以 API 應用程式命名的資料夾，並且產生用戶端類別。

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. 開啟 *Controllers\\ContactsController.cs*，查看可使用所產生的用戶端呼叫 API 的程式碼。

	下列程式碼片段示範如何具現化用戶端物件和呼叫 Get 方法。

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	此程式碼會將 API 專案的本機 IIS Express URL 傳遞至用戶端類別建構函式，以便您在本機執行 MVC Web 專案和 API 專案。如果您省略建構函式參數，預設端點會是您產生程式碼的 URL。

6. 將會根據您的 API 應用程式名稱，以不同的名稱產生您的用戶端類別；變更此程式碼，讓類型名稱符合您的專案中產生的內容。例如，如果您將 API 應用程式命名為 ContactsListAPIContoso，程式碼看起來會像下面的範例：

		private ContactsListAPIContoso db = new ContactsListAPIContoso(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

7. 建置方案。

MVC 專案的控制器和檢視看起來類似針對 Entity Framework 所建構的控制器和檢視，因為其建立方式如下：藉由建構 Entity Framework 資料模型，然後稍做變更以使用 REST API 用戶端，而非使用 Entity Framework 資料庫內容。

### 在本機執行

1. 將 ContactsList.API 和 ContactsList.MVC 專案設定為起始專案，而 ContactsList.API 開始於 ContactsList.MVC 之前(以滑鼠右鍵按一下解決方案，按一下 [屬性]，按一下 [多個起始專案]，並將每個專案設定為 [啟動]。使用向上/向下箭號圖示，確保 ContactsList.API 是清單中的第一項)。 

2. 按 F5 啟動專案。

	有一個瀏覽器會顯示 API 的 403 頁面，還有一個瀏覽器會顯示 MVC 應用程式的首頁。

3. 在可顯示 MVC 應用程式首頁的瀏覽器中，按一下功能表列中的 [連絡人]。

	MVC UI 會顯示本機儲存的連絡人，而您可以使用 UI 來新增及刪除連絡人。

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### 在 Azure 中建立 Web 應用程式並將 ContactsList.MVC 專案部署至該應用程式

在這一節中，您會使用先前用來建立 API 應用程式的相同方法來建立 Web 應用程式，以及使用相同的方法將 Web 專案部署至 Azure Web 應用程式。

#### 將 MVC 專案變更為指向 Azure API 應用程式 

在部署至 Azure 之前，請在 MVC 專案中變更 API 端點，以便程式碼在部署時呼叫您稍早建立的 Azure API 應用程式 (而不是 localhost)。

1. 在 ContactsList.MVC 專案中，開啟 *Controllers\\ContactsController.cs*。

2. 註解化可將 API 基底 URL 設定為 localhost URL 的那一行，並取消註解沒有任何建構函式參數的那一行。除了這兩行中反映您用來產生程式碼之 API 應用程式名稱的類別名稱以外，程式碼現在如下列範例所示。

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	預設目標 URL 是您的 Azure API 應用程式，因為您從該處產生程式碼；如果您使用不同的方法來產生程式碼，您可能必須用您指定本機 URL 的相同方式來指定 Azure API 應用程式 URL。

#### 建立 Web 應用程式以裝載 MVC 網站

1. 在 [方案總管] 中，以滑鼠右鍵按一下 ContactsList.MVC 專案，然後按一下 [發佈]。

2. 在 [發佈 Web] 精靈中，按一下 [設定檔] 索引標籤。

3.  在 [發佈 Web] 精靈的 [設定檔] 索引標籤中，按一下 [Microsoft Azure App Service]。

5. 在 [App Service] 對話方塊中，按一下 [新增]。

3. 在 [建立 App Service] 對話方塊的 [主控] 索引標籤中，按一下 [變更類型] 並確定類型是 [Web 應用程式]。

4. 輸入在 *azurewebsites.net* 網域中唯一的 [Web 應用程式名稱]。

5. 選擇您要使用的 Azure [訂用帳戶]。

6. 在 [資源群組] 下拉式清單中，選擇您稍早建立的資源群組。

4. 在 [App Service 方案] 下拉式清單中，選擇您稍早建立的同一個方案。

7. 按一下 [建立]。

	Visual Studio 會建立 Web 應用程式、建立其發佈設定檔，並顯示 [發佈 Web] 精靈的 [連接] 步驟。

### 將 ContactsList.Web 專案部署到新的 Web 應用程式

3.  在 [發佈 Web] 精靈的 [連接] 步驟中，按一下 [發佈]。

	Visual Studio 會將 ContactsList.MVC 專案部署到新的 Web 應用程式，並將瀏覽器開啟至 Web 應用程式的 URL。如您所見在本機執行的相同 MVC UI 會出現，但現在它會顯示儲存在 Azure API 應用程式的檔案系統中的連絡人。

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## 後續步驟

在本教學課程中，您已了解如何建立 API 應用程式、將程式碼部署到它們，以及從 .NET 用戶端加以取用。API Apps 入門系列中的下一個教學課程示範如何[使用 CORS 從 JavaScript 用戶端取用 API 應用程式](app-service-api-cors-consume-javascript.md)。

<!---HONumber=AcomDC_1210_2015-->