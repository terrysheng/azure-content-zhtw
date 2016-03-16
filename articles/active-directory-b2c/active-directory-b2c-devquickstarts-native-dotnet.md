<properties
	pageTitle="Azure Active Directory B2C 預覽版 | Microsoft Azure"
	description="如何使用 Azure Active Directory B2C 來建置包含登入、註冊及設定檔管理功能的 Windows 桌面應用程式。"
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

# Azure AD B2C 預覽版：建置 Windows 桌面應用程式


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

如果您利用 Azure Active Directory (Azure AD) B2C，只要幾個簡短的步驟，就在您的桌面應用程式中新增功能強大的自助式身分識別管理功能。本文章說明如何建立 .NET Windows Presentation Foundation (WPF)「待辦事項清單」應用程式，其中包含使用者註冊、登入和設定檔管理的功能。該應用程式將支援以使用者名稱或電子郵件來註冊及登入的功能。它也會支援以社交帳戶 (例如 Facebook 和 Google) 來註冊及登入。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄是適用於所有使用者、app、群組等項目的容器。如果您還沒有此資源，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續進行本指南。

## 建立應用程式

接著，您必須在 B2C 目錄中建立應用程式。這會提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必：

- 在應用程式中加入**原生用戶端**。
- 複製 [重新導向 URI] `urn:ietf:wg:oauth:2.0:oob`。這是此程式碼範例的預設 URL。
- 複製指派給您應用程式的 [應用程式識別碼]。稍後您將會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆由[原則](active-directory-b2c-reference-policies.md)所定義。此程式碼範例包含三種身分識別體驗：註冊、登入和編輯設定檔。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)中所述。建立這三個原則時，請務必：

- 在身分識別提供者刀鋒視窗中，選擇 [使用者識別碼註冊] 或 [電子郵件註冊]。
- 在註冊原則中，選擇 [顯示名稱] 和其他註冊屬性。
- 針對每個原則選擇 [顯示名稱] 和 [物件識別碼] 宣告來做為應用程式宣告。您也可以選擇其他宣告。
- 在您建立每個原則之後，請複製原則的 [名稱]。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立這三個原則之後，就可以開始建置您的 app。

## 下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet) 上。如要依照指示建置範例，請[下載 .zip 檔案格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)。您也可以複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

完整的應用程式也[以 .zip 檔案格式提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)，或是放在相同存放庫的 `complete` 子目錄中。

下載範例程式碼之後，請開啟 Visual Studio .sln 檔案開始進行。方案中有兩個專案：`TaskClient` 專案和 `TaskService` 專案。`TaskClient` 是使用者會互動的 WPF 桌面應用程式。`TaskService` 是應用程式的後端 Web API，會儲存每位使用者的待辦事項清單。在此案例中，由於 `TaskClient` 及 `TaskService` 會構成一個邏輯應用程式，因此兩者都是由單一應用程式識別碼來表示。

## 設定工作服務

當 `TaskService` 收到 `TaskClient` 的要求時，它會尋找有效的存取權杖來驗證要求。如要驗證存取權杖，您必須提供有您應用程相關資訊的 `TaskService`。請在 `TaskService` 專案中，開啟專案根目錄中的 `web.config` 檔案，然後取代 `<appSettings>` 區段中的值：

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

如要了解 Web API 如何利用 Azure AD B2C 來安全地驗證要求，請查看 [Web API 開始使用文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## 執行原則
當 `TaskService` 已準備好驗證要求時，您就可以實作 `TaskClient`。您的應用程式會藉由傳送 HTTP 驗證要求，來與 Azure AD B2C 通訊。這些要求會指定自己想要執行的原則，來做為要求的一部分。對於 .NET 桌面應用程式，您可以使用 Active Directory Authentication Library (ADAL) 來傳送 OAuth 2.0 驗證訊息、執行原則，並取得會呼叫 Web API 的權杖。

### 安裝 ADAL
使用 Visual Studio Package Manager Console 來把 ADAL 新增到 `TaskClient` 專案中。

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### 輸入 B2C 詳細資料
開啟檔案 `Globals.cs`，然後用您自己的值來取代每個屬性值。整個 `TaskClient` 都會使用這個類別來參考常用的值。

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### 建立 AuthenticationContext
ADAL 的主要類別是 `AuthenticationContext`，這代表您的應用程式與您 B2C 目錄的連線。當應用程式啟動時，請在 `MainWindow.xaml.cs` 中建立 `AuthenticationContext` 的執行個體。這可用在整個視窗上。

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### 起始註冊流程
當使用者選擇要註冊時，您會想要起始利用您所建立註冊原則的註冊流程。藉由使用 ADAL，您只要呼叫 `authContext.AcquireTokenAsync(...)` 即可。您傳遞給 `AcquireTokenAsync(...)` 的參數決定您會收到哪個權杖、用於驗證要求中的原則等等。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}

			MessageBox.Show(message);
		}

		return;
	}
}
```

### 起始登入流程
您可以用您起始註冊流程的相同方式來起始登入流程。當使用者登入時，請用同樣的方式呼叫 ADAL，但這次是利用您的登入原則：

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### 起始編輯設定檔流程
同樣地，您可以用相同的方式執行編輯設定檔原則：

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

在所有這些案例中，ADAL 會傳回 `AuthenticationResult` 中的權杖，或是擲回例外狀況。每次從 ADAL 取得權杖時，您都可以使用 `AuthenticationResult.UserInfo` 物件來更新應用程式中的使用者資料，例如 UI。ADAL 也會快取權杖，以供應用程式的其他部分使用。

## 呼叫 API
您已經使用 ADAL 來執行原則並取得權杖。不過，在許多情況下，您會只想要查看已快取的現有權杖，而不執行任何原則。例如，當應用程式嘗試從 `TaskService` 擷取使用者的待辦事項清單時。您可以使用相同的 `authContext.AcquireTokenAsync(...)` 方法來這麼做，方法同樣是把 `clientId` 當做範圍參數，但這次也使用 `PromptBehavior.Never`：

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}
			MessageBox.Show(message);
		}

		return;
	}
	...
```

當您成功呼叫 `AcquireTokenAsync(...)`，且在快取中找到權杖之後，便可以將該權杖加入 HTTP 要求的 `Authorization` 標頭中。如此一來， `TaskService` 就能驗證要求，以便讀取使用者的待辦事項清單：

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

每當您想要查看權杖快取中的權杖時，但不想要提示使用者登入時，就可以採用這種模式。舉例來說，當應用程式啟動時，您可能想要查看 `FileCache` 來尋找現有的權杖。如此一來，應用程式每次執行時，使用者的登入工作階段就會持續下去。您可以在 `MainWindow` 的 `OnInitialized` 事件中看到相同的程式碼。`OnInitialized` 會處理這個初次執行的案例。

## 登出使用者
您可以在使用者選取 [登出] 時，使用 ADAL 來結束使用者的應用程式工作階段。藉由使用 ADAL，您只要清除權杖快取中的所有權杖即可：

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## 執行範例應用程式

最後，請建置並執行 `TaskClient` 和 `TaskService`。使用電子郵件地址或使用者名稱來註冊應用程式。登出，再以相同的使用者身分重新登入。編輯該使用者的設定檔。請登出，然後以不同的使用者身分登入。

## 新增社交 IDP

目前，應用程式僅支援使用者以**本機帳戶**來註冊及登入。這些是儲存在 B2C 目錄中使用使用者名稱和密碼的帳戶。藉由使用 Azure AD B2C，您可以新增對其他身分識別提供者 (IDP) 的支援，但不需變更任何程式碼。

若要將社交 IDP 加入至應用程式，請依照下列文章中的詳細指示開始。針對您想要支援的每個 IDP，您必須在該系統中註冊應用程式並取得用戶端識別碼。

- [將 Facebook 設定為 IDP](active-directory-b2c-setup-fb-app.md)
- [將 Google 設定為 IDP](active-directory-b2c-setup-goog-app.md)
- [將 Amazon 設定為 IDP](active-directory-b2c-setup-amzn-app.md)
- [將 LinkedIn 設定為 IDP](active-directory-b2c-setup-li-app.md)

當您將身分識別提供者新增到 B2C 目錄之後，就必須分別編輯您的三個原則來包含新的 IDP，如[原則參考文章](active-directory-b2c-reference-policies.md)中所述。儲存您的原則之後，重新執行應用程式。在每一次的身分識別體驗中，您應該會看到新的 IDP 已加入成為登入和註冊選項。

您可以在範例應用程式上對原則進行試驗，並觀察試驗結果。新增或移除 IDP、管理應用程式宣告，或變更註冊屬性。請一直試驗，直到您了解原則、驗證要求及 ADAL 之間的緊密關係為止。

為供您參考，我們提供 [.zip 檔案格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)的完整範例。您也可以從 Github 複製它：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-------->