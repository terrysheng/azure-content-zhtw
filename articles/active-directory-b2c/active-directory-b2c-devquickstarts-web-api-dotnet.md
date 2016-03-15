<properties
	pageTitle="Azure Active Directory B2C 預覽 | Microsoft Azure"
	description="如何利用 Azure Active Directory B2C 來建置會呼叫 Web API 的 Web 應用程式。"
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C 預覽版：從 .NET Web 應用程式呼叫 Web API


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

如果您利用 Azure Active Directory (Azure AD) B2C，只要幾個簡短的步驟，就在您的 Web 應用程式及 Web API 中新增功能強大的自助式身分識別管理功能。本文章探討如何建立會利用 OAuth 2.0 持有人權杖來呼叫 .NET Web API 的 .NET Model-View-Controller (MVC)「待辦事項清單」Web 應用程式。Web 應用程式及 Web API 都會使用 Azure AD B2C 來管理使用者身分識別，以及驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。而會著重在如何在使用者已通過驗證後呼叫 Web API。如果您還沒進行 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)，請您先進行該教學課程，以便了解 Azure AD B2C 的基本概念。

## 取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄就是您所有使用者、應用程式、群組等項目的容器。如果您還沒有目錄，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續進行本指南。

## 建立應用程式

接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。在此案例中，由於 Web 應用程式及 Web API 會構成一個邏輯應用程式，因此兩者都是由單一**應用程式識別碼**來表示。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

- 在應用程式中加入 **Web 應用程式/Web API**。
- 在 [回覆 URL] 中輸入 `https://localhost:44316/`。這是此程式碼範例的預設 URL。
- 為您的應用程式建立 [應用程式密碼]，然後複製該密碼。稍後您將會用到此資訊。請注意，在您使用這個值之前，必須先讓該值經過 [XML 逸出](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)。
- 複製指派給您應用程式的 [應用程式識別碼]。稍後您也會需要此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則

在 Azure AD B2C 中，每個使用者經驗都藉由[原則](active-directory-b2c-reference-policies.md)來定義。這個 Web 應用程式包含三種身分識別體驗：註冊、登入及編輯設定檔。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)中所述。建立這三個原則時，請務必：

- 在註冊原則中，選擇 [顯示名稱] 和其他註冊屬性。
- 在每個原則中，選擇 [顯示名稱] 和 [物件識別碼] 應用程式宣告。您也可以選擇其他宣告。
- 在您建立每個原則之後，請複製原則的 [名稱]。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則後，就可以開始建置您的應用程式。

請注意，本文不涵蓋如何使用您剛才建立的原則。如需了解 Azure AD B2C 中原則的運作方式，請從 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)開始。

## 下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet) 上。如要遵循指示建置範例，請[下載 .zip 檔案格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)。您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

完整的應用程式也[以 .zip 檔案格式提供](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)，或是放在相同存放庫的 `complete` 子目錄中。

下載範例程式碼之後，請開啟 Visual Studio .sln 檔案開始進行。您會看到方案中有兩個專案：`TaskWebApp` 專案和 `TaskService` 專案。`TaskWebApp` 是使用者會互動的 Windows Presentation Foundation (WPF) Web 應用程式。`TaskService` 是應用程式的後端 Web API，會儲存每位使用者的待辦事項清單。

## 設定工作服務

當 `TaskService` 收到 `TaskWebApp` 的要求時，它會尋找有效的存取權杖來驗證要求。如要驗證存取權杖，您必須提供有您應用程相關資訊的 `TaskService`。請在 `TaskService` 專案中，開啟專案根目錄中的 `web.config` 檔案，然後取代 `<appSettings>` 區段中的值：

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

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


本文章沒有如何保護 `TaskService` 的詳細資料。如要了解 Web API 如何利用 Azure AD B2C 來安全地驗證要求，請查看 [Web API 開始使用文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## 設定工作 Web 應用程式

如要讓 `TaskWebApp` 與 Azure AD B2C 通訊，您必須提供部分常用的參數。在 `TaskWebApp` 專案中，開啟專案根目錄中的 `web.config` 檔案，然後取代 `<appSettings>` 區段中的值：這些值將用於整個 Web 應用程式。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

還有兩個 `[PolicyAuthorize]` 裝飾項目需要您提供您的登入原則名稱。當使用者嘗試存取應用程式中需要驗證的頁面時，可使用 `[PolicyAuthorize]` 屬性來叫用特定的原則。

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

## 取得存取權杖和呼叫工作 API

本節將探討如何在 Web 應用程式中使用 Microsoft 的程式庫和架構來完成 OAuth 2.0 權杖交換。如果您不熟悉授權碼及存取權杖，請參閱 [OpenID Connect 通訊協定參考](active-directory-b2c-reference-protocols.md)來進一步了解。

### 取得授權碼

`TaskService` Web API 呼叫中的第一個步驟，就是驗證使用者，然後接收來自 Azure AD 的授權碼。只要任何原則執行成功，您就會接到來自 Azure AD 的授權碼。這些原則包括登入、註冊及編輯設定檔。

首先，請利用 Visual Studio Package Manager Console 來安裝 OWIN OpenID Connect 中介軟體。請使用 OWIN 來把驗證要求傳送到 Azure AD，並處理這些要求的回應：

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

開啟檔案 `App_Start\Startup.Auth.cs`。這就是您藉由提供 B2C 目錄及您所建立應用程式的詳細資料，來設定 OWIN 驗證管線的地方：

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
			// endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
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

### 使用授權碼來取得存取權杖

您的 Web 應用程式已設定成會使用您的 B2C 目錄來驗證使用者，然後從 Azure AD 取回授權碼。下一個步驟是交換存取權杖，來存取從 Azure AD 取得的授權碼。

當您的 .NET Web 應用程式需要從 Azure AD 取得存取權杖時，您可以使用 Active Directory Authentication Library (ADAL)。您不必在這個程序中使用 ADAL，但 ADAL 能讓您更輕易地處理許多細節，包括傳送 OAuth 2.0 驗證訊息、快取，以及重新整理權杖。

首先，請利用 Package Manager Console，在 `TaskWebApp` 專案中安裝 ADAL：

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

接下來，您需要將授權碼傳遞給 ADAL，讓 ADAL 能為您取得權杖。OWIN OpenID Connect 中介軟體會提供通知，讓您能使用這個授權碼。每次應用程式從 Azure AD 收到授權碼時，您就會收到通知。請在 `App_Start\Startup.Auth.cs` 中，利用 ADAL 來實作 `OnAuthorizationCodeReceived` 通知處理常式：

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and it is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default. In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

### 在控制器中取得存取權杖

在您為 `TaskService` 後端取得存取權杖，並將權杖存放在 ADAL 的權杖快取中之後，您必須使用該權杖。`TasksController` 負責與 `TaskService` API 通訊，而它會把 HTTP 要求傳送到 API 來讀取、建立及刪除工作。請在 HTTP 要求傳送出去之前，先從 ADAL 取得存取權杖：

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

		// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache and throw an exception if it cannot do so.
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

ADAL 會快取權杖、在權杖過期時重新整理權杖，以及透過擲回例外狀況來讓您知道使用者何時必須重新登入。每當應用程式需要權杖時，您就只需要呼叫 `AuthenticationContext.AcquireTokenSilentAsync(...)` 即可。

### 從 Web API 讀取工作

當您擁有權杖之後，就可以把權杖附加 HTTP `GET` 要求的 `Authorization` 標頭中，以便安全地呼叫 `TaskService`：

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
			// and show the user an error that indicates that they might need to sign in again.
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

### 在 Web API 上建立及刪除工作

當您傳送 `POST` 及 `DELETE` 要求給 `TaskService` 時，請依照相同的模式來進行。只要呼叫 `AuthenticationContext.AcquireTokenSilentAsync(...)`，然後把收到的權杖附加到要求的 `Authorization`標頭即可。我們已經為您實作了建立動作，您可以嘗試在 `TasksController.cs` 中完成刪除動作。

## 登出使用者

當使用者登出 Web 應用程式時，您想要清除 ADAL 權杖快取，來徹底移除使用者的已驗證工作階段：

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

## 執行範例應用程式

最後，請建置並執行 `TaskClient` 和 `TaskService`。請註冊並登入應用程式，然後為已登入使用者建立工作。請登出應用程式，再以不同的使用者身分登入，然後為該使用者建立工作。您會發現，這些工作在 API 上是依照使用者來儲存的 ，因為 API 會從自己收到的存取權杖中擷取使用者的身分識別。

為供您參考，我們提供 [.zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)的完整範例。您也可以從 Github 複製它：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-------->