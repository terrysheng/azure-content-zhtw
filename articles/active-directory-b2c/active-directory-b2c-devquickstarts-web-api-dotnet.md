<properties
	pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
	description="如何建置使用 Azure AD B2C 呼叫 Web API 的 Web 應用程式。"
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C 預覽：從 .NET Web 應用程式呼叫 Web API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至 Web 應用程式和 Web API，只要幾個簡短步驟即可完成。本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 .NET Web API 的 .NET MVC「待辦事項清單」Web 應用程式。Web 應用程式與 Web API 將會使用 Azure AD B2C 管理使用者身分識別與驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。而會著重在如何在使用者已通過驗證後呼叫 Web API。您應該先從 [.NET Web App 使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始 (如果還沒有進行)，以了解 Azure AD B2C 的基本概念。

## 1\.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您尚未擁有目錄，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續下一個步驟。

## 2\.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，因為 Web 應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必

- 在應用程式中加入 **Web 應用程式/Web API**
- 輸入 `https://localhost:44316/` 作為**回覆 URL** -它是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製下來。稍後您將會用到此資訊。
- 複製指派給應用程式的**應用程式識別碼**。稍後您也會用到此資訊。

    > [AZURE.IMPORTANT]您無法為此使用已在 [Azure 入口網站](https://manage.windowsazure.com/)上的 [應用程式] 索引標籤中登錄的應用程式。

## 3\.建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆是由某個[**原則**](active-directory-b2c-reference-policies.md)所定義。此 Web 應用程式包含三種身分識別體驗 - 註冊、登入和編輯設定檔。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在註冊原則中選擇 [顯示名稱] 和其他一些註冊屬性。
- 在每個原則中選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的 [名稱]。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。 

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。若您想要了解 Azure AD B2C 中的原則如何運作，您應該從 [.NET Web 應用程式使用者入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 4\.下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet) 上。若要遵循指示建立範例，您可以[下載 .zip 格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)或複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

完整的應用程式也提供 [.zip 格式](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)；您也可以在相同儲存機制的 `complete` 分支取得。

下載範例程式碼後，請開啟 Visual Studio `.sln` 檔案開始進行。您會看到方案中有兩個專案：`TaskWebApp` 專案和 `TaskService` 專案。`TaskWebApp` 是與使用者互動的 WPF Web 應用程式前端。`TaskService` 是應用程式的後端 Web API，可儲存每個使用者的待辦事項清單。

## 5\.設定工作服務

當 `TaskService` 收到 `TaskWebApp` 的要求時，它會檢查存取權杖是否有效可驗證要求。為了驗證存取權杖，您必須提供應用程式的一些資訊給 `TaskService`。在 `TaskService` 專案中，開啟專案根目錄中的 `web.config` 檔案，取代 `<appSettings>` 區段中的值：

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
  ```

這篇文章不會介紹保護 `TaskService` 的詳細資料。如果您想要了解 Web API 如何安全地使用 Azure AD B2C 驗證要求，請查看 [Web API 使用者入門文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## 6.設定工作 Web 應用程式

為了讓 `TaskWebApp` 與 Azure AD B2C 通訊，您必須提供一些通用參數。在 `TaskWebApp` 專案中，開啟專案根目錄中的 `web.config` 檔案，取代 `<appSettings>` 區段中的值。這些值將用於整個 Web 應用程式。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```     

另外有兩個 `[PolicyAuthorize]` 裝飾項目，需要在其中提供您的登入原則名稱。當使用者嘗試存取應用程式中需要驗證的頁面時，`[PolicyAuthorize]` 屬性會用來叫用特定原則。

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## 7\.取得存取權杖和呼叫工作 API

本節將示範如何在 Web 應用程式中使用 Microsoft 的程式庫和架構完成 OAuth 2.0 權杖交換。如果您不熟悉**授權碼**和**存取權杖**，建議您瀏覽 [OpenID Connect 通訊協定參考](active-directory-b2c-reference-protocols.md)。

#### 取得授權碼

呼叫 `TaskService` Web API 的第一個步驟是驗證使用者並從 Azure AD 接收**授權碼**。在任何原則成功執行之後 ，包括登入、註冊和編輯設定檔原則，您可以從 Azure AD 收到授權碼。

首先，請使用 Visual Studio Package Manager Console 安裝 OWIN OpenID Connect 中介軟體。我們將使用 OWIN 將驗證要求傳送至 Azure AD 並處理其回應：

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

開啟檔案 `App_Start\Startup.Auth.cs`。我們將在這裡設定 OWIN 驗證管線，並提供 B2C 目錄和您所建立的應用程式的詳細資料：

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

#### 使用授權碼取得存取權杖

您的 Web 應用程式現在已設定為使用您的 B2C 目錄驗證使用者，並從 Azure AD 取回授權碼。下一步是使用授權碼向 Azure AD 兌換存取權杖。

每當您的 .NET Web 應用程式需要從 Azure AD 取得存取權杖時，您就可以使用 **Active Directory 驗證程式庫 (ADAL)**。您不一定要使用 ADAL 執行此程序，但 ADAL 會處理許多細節，例如傳送 OAuth 2.0 驗證訊息、快取和重新整理權杖，讓您更輕鬆完成此程序。

首先，再一次使用 Package Manager Console，將 ADAL 安裝到 `TaskWebApp` 專案中：

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

現在您需要將授權碼傳遞給 ADAL，讓它為您取得權杖。OWIN OpenID Connect 中介軟體會通知您使用此授權碼 - 每次您的應用程式收到 Azure AD 的授權碼時，就會引發通知。在 `App_Start\Startup.Auth.cs` 中，使用 ADAL 實作 `OnAuthorizationCodeReceived` 通知處理常式：

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default.  In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache, for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
``` 

#### 在控制器中取得存取權杖

既然我們已獲得 `TaskService` 後端的存取權杖並儲存在 ADAL 的權杖快取中，我們必須實際使用它。`TasksController` 負責與 `TaskService` API 通訊，並傳送 HTTP 要求給 API 來讀取、建立和刪除工作。傳送 HTTP 要求前，請先從 ADAL 取得存取權杖：

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;
		
		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache, and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
``` 

ADAL 會負責快取權杖、在快取權杖過期時重新整理，並經由擲回例外狀況，讓您知道使用者必須重新登入。每當應用程式中需要權杖時，您只需要呼叫 `AuthenticationContext.AcquireTokenSilentAsync(...)` 即可。

#### 從 Web API 讀取工作

既然您已經有權杖，您可以將它附加至 HTTP GET 要求的 `Authorization` 標頭，以安全地呼叫 `TaskService`：

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	... 
	
	try 
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache, 
			// and show the user an error indicating they might need to sign-in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

#### 在 Web API 上建立和刪除工作

您可以遵循完全相同的模式來傳送 POST 和 DELETE 要求給 `TaskService`。只要呼叫 `AuthenticationContext.AcquireTokenSilentAsync(...)`，並將產生的權杖附加至要求中的 `Authorization` 標頭即可。我們已經為您實作 `Create` 動作。請嘗試親自完成 `TasksController.cs` 中的 `Delete` 動作。

## 8\.登出使用者

最後的細節處理。當使用者登出 Web 應用程式時，您應該清除 ADAL 權杖快取，以移除使用者已驗證的工作階段的任何殘餘部分：

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string> 
			{ 
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## 9\.執行範例應用程式

最後，建置並執行 `TaskClient` 和 `TaskService`。註冊或登入應用程式，並為登入的使用者建立工作。登出後，再以不同使用者重新登入，並為該使用者建立工作。請注意每位使用者的工作如何儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

為了方便參考，您可以[在此處取得 .zip 格式](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)的完整範例，或者從 GitHub 中複製：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->