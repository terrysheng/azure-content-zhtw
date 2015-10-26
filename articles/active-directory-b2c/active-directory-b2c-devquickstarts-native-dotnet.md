<properties
	pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
	description="如何使用 Azure AD B2C 建置支援登入、註冊和設定檔管理的 Windows 桌面應用程式。"
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

# Azure AD B2C 預覽：建置 Windows 桌面應用程式

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至桌面應用程式，只要幾個簡短步驟即可完成。本文將說明如何建立 .NET WPF「待辦事項清單」應用程式，其中包含使用者註冊、登入和設定檔管理。它將支援以使用者名稱或電子郵件及社交帳戶 (例如 Facebook 和 Google) 來註冊和登入。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。目錄為所有使用者、應用程式、群組等項目的容器。如果您尚未擁有目錄，請先[建立 B2C 目錄](active-directory-b2c-get-started.md)，再繼續下一個步驟。

## 2\.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。若要建立應用程式，請遵循[這些指示](active-directory-b2c-app-registration.md)。請務必

- 在應用程式中加入**原生用戶端**。
- 複製**重新導向 Uri** `urn:ietf:wg:oauth:2.0:oob` - 它是此程式碼範例的預設 URL。
- 複製指派給應用程式的**應用程式識別碼**。稍後您將會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\.建立您的原則

在 Azure AD B2C 中，每個使用者經驗皆是由某個[**原則**](active-directory-b2c-reference-policies.md)定義的。此程式碼範例包含三種身分識別體驗 - 註冊、登入和編輯設定檔。您必須為每個類型建立一個原則，如[原則參考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。建立您的三個原則時，請務必：

- 在身分識別提供者刀鋒視窗中，選擇 [使用者識別碼註冊] 或 [電子郵件註冊]。
- 在註冊原則中選擇 [顯示名稱] 和其他一些註冊屬性。
- 在每個原則中選擇 [顯示名稱] 和 [物件識別碼] 宣告做為應用程式宣告。您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的**名稱**。其前置詞應該為 `b2c_1_`。稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

## 4\.下載程式碼

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet) 上。若要遵循指示建立範例，您可以[下載 .zip 格式的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)，或複製基本架構：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

完整的應用程式也提供 [.zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)；您也可以在相同儲存機制的 `complete` 分支取得。

下載範例程式碼後，請開啟 Visual Studio `.sln` 檔案開始進行。您會看到方案中有兩個專案：`TaskClient` 專案和 `TaskService` 專案。`TaskClient` 是使用者與之互動的 WPF 桌面應用程式。`TaskService` 是應用程式的後端 Web API，可儲存每位使用者的待辦事項清單。在此案例中，因為 `TaskClient` 和 `TaskService` 會組成一個邏輯應用程式，所以將以單一**應用程式識別碼**表示。

## 5\.設定工作服務

當 `TaskService` 收到 `TaskClient` 的要求時，它會檢查存取權杖是否有效，進而驗證要求。為了驗證存取權杖，您必須將應用程式的一些資訊提供給 `TaskService`。在 `TaskService` 專案中，開啟專案根目錄中的 `web.config` 檔案，並取代 `<appSettings>` 區段中的值：

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

如果您想要了解 Web API 如何安全地使用 Azure AD B2C 驗證要求，請查看 [Web API 使用者入門文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## 6\.執行原則
既然 `TaskService` 已準備好驗證要求，我們便可以實作 `TaskClient`。您的應用程式與 Azure AD B2C 通訊時會傳送 HTTP 驗證要求，並指定它想要在要求中執行的原則。對於 .NET 桌面應用程式，您可以使用 **Active Directory Authentication Library (ADAL)** 來傳送 OAuth 2.0 驗證訊息、執行原則，並取得呼叫 Web API 的權杖。

#### 安裝 ADAL
首先，使用 Visual Studio Package Manager Console 將 ADAL 新增到 TaskClient 專案中。

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### 輸入 B2C 詳細資料
開啟檔案 `Globals.cs`，並使用您自己的值來取代每個屬性值。整個 `TaskClient` 都會使用這個類別來參考常用的值。

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


#### 建立 AuthenticationContext
ADAL 的主要類別是 `AuthenticationContext`它代表您的應用程式與 B2C 目錄的連接。當應用程式啟動時，在 `MainWindow.xaml.cs` 中建立 `AuthenticationContext` 的執行個體，這可用於整個視窗。

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### 起始註冊流程
當使用者按一下 [註冊] 按鈕時，我們要使用您所建立的註冊原則來起始註冊流程。有了 ADAL，您只需要呼叫 `authContext.AcquireTokenAsync(...)` 即可。您傳給 `AcquireTokenAsync(...)` 的參數可決定您會收到哪一個權杖、用於驗證要求中的原則等等。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
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

#### 起始登入流程
登入流程的起始方式與註冊流程相同。當使用者按一下 [登入] 按鈕時，對 ADAL 發出相同的呼叫，但這次使用登入原則：

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

#### 起始設定檔編輯流程
同樣地，您可以用相同的方式執行您的編輯設定檔原則：

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

在這些案例中，ADAL 會在其 `AuthenticationResult` 中傳回權杖，或擲回例外狀況。每次從 ADAL 取得權杖時，您都可以使用 `AuthenticationResult.UserInfo` 物件來更新應用程式中的使用者資料，例如 UI。ADAL 也會快取權杖，供應用程式的其他部分使用。

## 7\.呼叫 API
我們已經使用 ADAL 來執行原則並取得權杖。不過，在許多情況下，您只想要檢查現有快取的權杖，而不執行任何原則。例如，當應用程式嘗試從 `TaskService` 擷取使用者的待辦事項清單時。您可以使用相同的 `authContext.AcquireTokenAsync(...)` 方法來執行此動作，同樣是使用 `clientId` 做為範圍參數，但這次使用 `PromptBehavior.Never`：

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
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
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

成功呼叫 `AcquireTokenAsync(...)`，且在快取中找到權杖後，您便可以將該權杖加入 HTTP 要求的 `Authorization` 標頭，以便讓 `TaskService` 驗證讀取使用者待辦事項清單的要求：

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

每當您想要檢查權杖快取中的權杖時，就可以採用這種相同的模式，而不需要提示使用者登入。例如，當應用程式啟動時，我們想要檢查 `FileCache` 中是否有任何現有的權杖，以便每次應用程式執行時，都能維持使用者的登入工作階段。在 `MainWindow` 的 `OnInitialized` 事件中 (處理首次執行)，您會看到相同的程式碼。

## 8\.登出使用者
最後，當使用者按一下 [登出] 按鈕時，您可以使用 ADAL 來結束使用者與應用程式之間的工作階段。有了 ADAL，就只是從權杖快取中清除所有權杖這麼簡單：

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
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

## 9\.執行範例應用程式

最後，建置並執行 `TaskClient` 和 `TaskService`。使用電子郵件地址或使用者名稱來註冊應用程式。登出，再以相同的使用者身分重新登入。編輯該使用者的設定檔。登出，再以不同的使用者身分登入。

## 10\.新增社交 IDP

目前，應用程式只支援以所謂的**本機帳戶** (連同使用者名稱與密碼一起儲存在 B2C 目錄中的帳戶) 進行使用者註冊和登入。透過 Azure AD B2C，您可以增加支援其他**識別提供者** (簡稱 IDP)，而不需要變更任何程式碼。

若要將社交 IDP 加入至應用程式，請依照下面一篇或兩篇文章中的詳細指示開始。針對您想要支援的每個 IDP，您必須在其系統中註冊應用程式並取得用戶端識別碼。

- [將 Facebook 設定為 IDP](active-directory-b2c-setup-fb-app.md)
- [將 Google 設定為 IDP](active-directory-b2c-setup-goog-app.md)
- [將 Amazon 設定為 IDP](active-directory-b2c-setup-amzn-app.md)
- [將 LinkedIn 設定為 IDP](active-directory-b2c-setup-li-app.md) 

將識別提供者加入 B2C 目錄之後，您必須回來編輯所有三個原則以包含新的 IDP，如[原則參考文章](active-directory-b2c-reference-policies.md)所述。儲存您的原則之後，重新執行應用程式。在每一次的身分識別體驗中，您應該會看到新的 IDP 已加入成為登入和註冊選項。

您可以自由試驗您的原則，並觀察範例應用程式上有何效果 - 新增/移除 IDP、操作應用程式宣告、變更註冊屬性。試驗到您開始了解原則、驗證要求和 ADAL 如何結合在一起為止。

為了方便參考，您可以[在此處取得 .zip 格式](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)的完整範例，或者從 GitHub 中複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO3-->