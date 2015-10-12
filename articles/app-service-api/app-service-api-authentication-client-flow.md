<properties 
	pageTitle="從驗證的用戶端呼叫 API 應用程式" 
	description="了解如何從經過 Azure Active Directory 驗證的 Web 應用程式用戶端呼叫 Azure API 應用程式。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="tdykstra"/>

# 從經過 Azure Active Directory 驗證的 Web 應用程式用戶端呼叫 Azure API 應用程式

## 概觀

本教學課程示範如何呼叫受到 Azure Active Directory (AAD) 保護的 API 應用程式 (透過也受到 AAD 保護的 Web 應用程式)。完成本教學課程時，您的 Azure 訂用帳戶中將執行一個 Web 應用程式和一個 API 應用程式。Web 應用程式會顯示藉由呼叫 API 應用程式取得的資料，如下列螢幕擷取畫面所示。

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

您將學習如何從 Web 應用程式傳入 AAD 認證至 API 應用程式，以便 API 應用程式不會再次提示使用者進行登入。

您會執行下列步驟：

- 建立 API 應用程式，並將它設定為使用 AAD 驗證。
- 建立 Web 應用程式，並將它設定為使用 AAD 驗證。
- 加入程式碼至 Web 應用程式，以呼叫受保護的 API 應用程式。
- 將 Web 應用程式和 API 應用程式部署至 Azure。
- 測試以驗證 Web 應用程式可以呼叫 API 應用程式。

### 用戶端流程驗證

您加入至 Web 應用程式的程式碼會實作稱為[用戶端流程](../app-service/app-service-authentication-overview.md#client-flow)驗證的程序。下圖說明取得 AAD 存取權杖，並交換該存取權杖以取得 API 應用程式 (Zumo) 權杖的程序。

![](./media/app-service-api-authentication-client-flow/clientflow.png)

如果您不熟悉 API 應用程式閘道器在 API 應用程式的驗證中的角色，請參閱 [API 應用程式和行動應用程式的驗證](../app-service/app-service-authentication-overview.md)。

## 必要條件

開始本教學課程之前，請確定您已安裝 [Visual Studio 2015](https://www.visualstudio.com/) 和 [Azure SDK for .NET](http://go.microsoft.com/fwlink/?linkid=518003)。相同的指示也適用於 Visual Studio 2013。

本教學課程假設您知道如何在 Visual Studio 中使用 Web 專案。

## 使用 AAD 建立和保護 API 應用程式

1. 建立或下載 API 應用程式專案。
 
	* 若要建立專案，請遵照[建立 API 應用程式](app-service-dotnet-create-api-app.md)中的指示。

	* 若要下載專案，請從 [ContactsList GitHub 儲存機制](https://github.com/tdykstra/ContactsList)取得。

	您現在有可傳回連絡資料的 Web API 專案。

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. 將 API 應用程式專案部署到 Azure 中的新 API 應用程式。

	遵循[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)的指示。

	在 [Azure Preview 入口網站]中，API 應用程式的 [API 定義] 刀鋒視窗現在會顯示您部署的 Web API 專案的 Get 和 Post 方法。

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. 藉由使用 Azure Active Directory (AAD) 做為身分識別提供者來保護 API 應用程式。
 
	遵循[保護 API 應用程式](app-service-api-dotnet-add-authentication.md)中 AAD 的指示。您不必進行教學課程的**使用 Postman 傳送 Post 要求**一節。

	完成時，[Azure Preview 入口網站]中，API 應用程式的 [Azure Active Directory] 刀鋒視窗會有 AAD 應用程式的用戶端識別碼和 AAD 租用戶。

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	而 [Azure 入口網站] AAD 應用程式的 [設定] 索引標籤會有 API 應用程式的應用程式識別碼 URL 和回覆 URL。

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## 使用 AAD 建立和保護 Web 應用程式

在本節中，您可以下載並設定為 AAD 驗證設定的 Web 專案。專案具有**關於**頁面，顯示所登入使用者的宣告資訊。

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

稍後您會透過加入區段用於顯示從 API 應用程式擷取的連絡資訊來修改此頁面。

1. 從 [WebApp-GroupClaims-DotNet 儲存機制](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)下載 Web 專案
 
2. 遵循[讀我檔案](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md)中**如何執行範例**的指示，但有下列例外狀況：
 
	* 雖然讀我檔案說明要使用 Visual Studio 2013，但您可以使用 Visual Studio 2015。 

	* 使用您已建立的 AAD 應用程式而不是建立一個新應用程式。
 
	* 保持您的 AAD 應用程式已經擁有的相同**應用程式識別碼 URI**。(不要將它變更為讀我檔案中指定的格式。)
	
	* 依照指示變更其他 AAD 應用程式設定；特別是將登入和回覆 URL 設為範例應用程式的基底 URL。

您會保留為 API 應用程式建立的相同的應用程式識別碼 URI，以便您可以對 Web 應用程式和 API 應用程式使用相同的 AAD 存取權杖。如果您將應用程式識別碼 URI 變更為讀我檔案所規定的格式，如此能存取 Web 應用程式，但無法存取 API 應用程式。您將無法將 AAD 權杖傳遞至 API 應用程式閘道器來取得 Zumo 權杖，因為閘道器會預期應用程式識別碼 URI 的權杖是由閘道器 URL 加上 "/login/aad" 組成。

## 為 API 應用程式加入產生的用戶端程式碼

在本節中，您會為呼叫 API 應用程式的具型別介面加入自動產生的程式碼。

8.	在 Visual Studio 的 [方案總管] 中，在 WebApp-GroupClaims-DotNet 專案上按一下滑鼠右鍵，然後按一下 [加入] > [Azure API 應用程式用戶端]。

9.	在 [加入 Microsoft Azure API 應用程式用戶端] 對話方塊中，選取以 AAD 保護的 API 應用程式。

	程式碼產生完成之後，您會在 [**方案總管**] 中看到該 API 應用程式名稱的新資料夾。此資料夾包含實作用戶端類別和資料模型的程式碼。

10. 修正 *ContactsList/ContactsExtensions.cs* 中產生的程式碼造成的不明確參考：將 `Task.Factory.StartNew` 的兩個執行個體變更為 `System.Threading.Tasks.Task.Factory.StartNew`。
 
## 加入程式碼來交換 Zumo 權杖的 AAD 權杖

1.	在 HomeController.cs 中，為 App Service SDK 和 JSON 序列化程式加入 `using` 陳述式。

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. 加入閘道器 URL 和 AAD 應用程式的應用程式識別碼 URI 的常數。請確定閘道器 URL 是 https 不是 http。

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	加入會取得用於呼叫 API 應用程式的用戶端物件的方法。

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	在此程式碼中，`ConfigHelper.Authority` 會解析為 "https://login.microsoftonline.com/{your tenant}"，例如："https://login.microsoftonline.com/contoso.onmicrosoft.com"。

2.	在 `About` 方法的結尾、緊接著 `return View()` 陳述式之前，加入程式碼來呼叫 API 應用程式。(在下一個步驟中，您將加入程式碼到 `About` 檢視來顯示傳回的資料。)

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. 在 *Views/Home/About.cshtml* 中，將程式碼加入到 `h2` 標題之後，以顯示連絡資訊。

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. 在應用程式 Web.config 檔案中，於開啟 `<assemblyBinding>` 標記之後加入下列繫結重新導向。

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	若沒有這個繫結重新導向，應用程式將會失敗，因為 App Service SDK 包含 System.Net.Http.Primitives 1.5.0.0 版的相依性，但專案所使用的版本為 4.2.28.0。
 
3. 遵循[讀我檔案](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)中**部署此範例至 Azure** 的指示進行。

5. 執行應用程式。

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. 按一下 [登入]，然後輸入您正用於此應用程式的 AAD 網域中的使用者認證。

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. 按一下 [關於]。

	您加入到 [關於] 控制項和檢視的程式碼會執行並顯示您已成功向 API 應用程式驗證並呼叫其 Get 方法。

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## 疑難排解

### HTTP 400 錯誤 

請確定閘道器 URL 是 https 不是 http。直接從 Azure Preview 入口網站複製時，它可能會指定 http。
 
### SQL Server 錯誤

應用程式需要以 GroupClaimContext 連接字串識別的 SQL Server 資料庫的存取權。請確定已部署之網站中的連接字串指向 SQL 資料庫執行個體。您可以將正確的連接字串放在已部署的 Web.config 或是在 Azure 執行階段組態設定中。

## 通知

感謝 Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) 協助開發本教學課程。

## 後續步驟

您已了解如何為 App Service API 應用程式執行用戶端流程驗證。如需關於在 API 應用程式中處理驗證的其他方式的資訊，請參閱 [API 應用程式與行動應用程式的驗證](../app-service/app-service-authentication-overview.md)。

[Azure 入口網站]: https://manage.windowsazure.com/
[Azure Preview 入口網站]: https://portal.azure.com/

<!---HONumber=Oct15_HO1-->