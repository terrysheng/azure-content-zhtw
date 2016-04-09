<properties
	pageTitle="Azure Active Directory B2C 預覽：使用圖形 API | Microsoft Azure"
	description="如何使用應用程式身分識別對 B2C 租用戶呼叫圖形 API，以將程序自動化。"
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="dastrock"/>

# Azure AD B2C 預覽：使用圖形 API


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure Active Directory (Azure AD) B2C 租用戶通常會很龐大。這表示許多常見的租用戶管理工作需要以程式設計方式執行。使用者管理是主要範例。您可能需要將現有的使用者存放區移轉到 B2C 租用戶。您希望在自己的頁面上裝載使用者註冊，並在幕後的 Azure AD 中建立使用者帳戶。這類工作需要能夠建立、讀取、更新和刪除使用者帳戶。這些工作都可以透過 Azure AD 圖形 API 達成

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

對於 B2C 租用戶，與圖形 API 通訊有兩種主要模式。

- 對於互動式、執行一次的工作，您應以 B2C 租用戶中的系統管理員帳戶來執行管理工作。此模式需要系統管理員先使用認證登入，系統管理員才能對圖形 API進行任何呼叫。
- 對於自動化、持續的工作，您應使用您提供必要權限的某種服務帳戶來執行管理工作。在 Azure AD 中，作法上您可以註冊應用程式並向 Azure AD 驗證。使用採用 [OAuth 2.0 用戶端認證授與](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)的**應用程式識別碼**即可完成。在此情況下，應用程式會以本身 (而非使用者的身分) 呼叫圖形 API。

在本文中，我們將討論如何執行自動化使用案例。為了示範，我們會建置 .NET 4.5 `B2CGraphClient` 來執行使用者建立、讀取、更新和刪除 (CRUD) 作業。用戶端會有 Windows 命令列介面讓您叫用各種方法。不過，程式碼會撰寫成以非互動、自動化的方式運作。

## 取得 Azure AD B2C 租用戶

您需要一個 Azure AD B2C 租用戶和該租用戶中的全域系統管理員帳戶，才可建立應用程式或使用者，或與 Azure AD 進行互動。如果您還沒有租用戶，請參閱[開始使用 Azure AD B2C](active-directory-b2c-get-started.md)。

## 在租用戶中註冊服務應用程式

有了 B2C 租用戶以後，您需要使用 Azure AD Powershell Cmdlet 建立服務應用程式。首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。接著下載並安裝[適用於 Windows PowerShell 的 64 位元 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。

> [AZURE.NOTE]
若要使用圖形 API 搭配 B2C 租用戶，您必須使用 Powershell 註冊專用的應用程式。請依照本文中的指示執行該項操作。您不能重複使用已經在 Azure 入口網站中註冊的現有 B2C 應用程式。這是 Azure AD B2C 預覽的一項限制，預計在不久的將來移除。我們計劃屆時會更新本文章。

安裝 Powershell 模組之後，請開啟 Powershell 並連線到 B2C 租用戶。執行 `Get-Credential` 之後，系統將提示您輸入使用者名稱和密碼。請輸入 B2C 租用戶系統管理員帳戶的使用者名稱和密碼。

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

建立應用程式之前，您必須產生新的**用戶端密碼**。您的應用程式將使用此用戶端密碼向 Azure AD 驗證，並取得存取權杖。您可以在 PowerShell 中產生有效的密碼：

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

最後一個命令應該會印出新的用戶端密碼。將它複製到安全的地方。稍後您將會用到此資訊。提供新的用戶端密碼作為應用程式的認證，現在即可建立應用程式：

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

如果您成功建立應用程式，應該會印出應用程式的某些屬性，如上所示。您將需要 `ObjectId` 和 `AppPrincipalId`，因此也請複製這些值。

在 B2C 租用戶中建立應用程式之後，您需要將執行使用者 CRUD 作業所需的權限指派給它。將三個角色指派給應用程式：目錄讀取者 (用於讀取使用者)、目錄寫入者 (用於建立及更新使用者)，以及使用者帳戶管理員 (用於刪除使用者)。這些角色具有已知的識別碼，所以您可以用上述的 `ObjectId` 取代 `-RoleMemberObjectId` 參數並執行下列命令。若要查看所有目錄角色的清單，請嘗試執行 `Get-MsolRole`。

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

您現在的應用程式有權從 B2C 租用戶建立、讀取、更新和刪除使用者。

## 下載、設定和建置範例程式碼

首先，下載範例程式碼並開始執行。然後我們會仔細查看。您可以[將範例程式碼下載為 .zip 檔案](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)。您可以將此檔案複製到您選擇的目錄中：

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

在 Visual Studio 中開啟 `B2CGraphClient\B2CGraphClient.sln` Visual Studio 方案。在 `B2CGraphClient` 專案中，開啟 `App.config` 檔案。使用您自己的值取代三個應用程式設定：

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下來，以滑鼠右鍵按一下 `B2CGraphClient` 方案並重建範例。如果成功，您現在應該有一個 `B2C.exe` 可執行檔位於 `B2CGraphClient\bin\Debug`。

## 使用圖形 API 來建置使用者 CRUD 作業

若要使用 B2CGraphClient，請開啟 `cmd` Windows 命令提示字元，將您的目錄變更為 `Debug` 目錄。然後執行 `B2C Help` 命令。

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

這會顯示每個命令的簡短描述。您每次叫用上述其中一個命令時，`B2CGraphClient` 會對 Azure AD 圖形 API 發出要求。

### 取得存取權杖

對圖形 API 發出任何要求時，需要有存取權杖進行驗證。`B2CGraphClient` 會使用開放原始碼 Active Directory 驗證程式庫 (ADAL) 來協助取得存取權杖。ADAL 提供簡單的 API 並處理一些重要的細節，例如快取存取權杖，可讓您輕鬆取得權杖。不過，您不必使用 ADAL 來取得權杖。您也可以藉由製作 HTTP 要求來取得權杖。

> [AZURE.NOTE]
	此程式碼範例使用 ADAL v2 (ADAL 公開上市版本)。不使用 ADAL v4，因為這是用於 Azure AD B2C 的預覽版本。在 Azure AD B2C 預覽中，您必須使用 ADAL v2 才能與圖形 API 進行通訊。未來，我們預計會使用 ADAL v4 提供圖形 API 存取權，您不需要在完整的 Azure AD B2C 方案中使用兩個版本的 ADAL。

`B2CGraphClient` 執行時會建立 `B2CGraphClient` 類別的執行個體。這個類別的建構函式會設定 ADAL 驗證架構：

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are
	// provided to Azure AD in order to receive an access_token by using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

我們將以 `B2C Get-User` 命令為例。叫用 `B2C Get-User` 而沒有任何其他輸入時，CLI 會呼叫 `B2CGraphClient.GetAllUsers(...)` 方法。這個方法會呼叫 `B2CGraphClient.SendGraphGetRequest(...)`，後者會送出 HTTP GET 要求給圖形 API。在 `B2CGraphClient.SendGraphGetRequest(...)` 傳送 GET 要求之前，它會先使用 ADAL 取得存取權杖：

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

您可以呼叫 ADAL `AuthenticationContext.AcquireToken(...)` 方法，以取得圖形 API 的存取權杖。然後 ADAL 會傳回 `access_token` 表示應用程式的身分識別。

### 讀取使用者

當您想要從圖形 API 取得使用者清單或取得特定的使用者時，您可以傳送 HTTP `GET` 要求給 `/users` 端點。要求取得租用戶中所有使用者時，情況如下：

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看此要求，請執行：

 ```
 > B2C Get-User
 ```

有兩個重點值得注意：

- 透過 ADAL 取得的存取權杖已利用 `Bearer` 配置加入至 `Authorization` 標頭。
- 對於 B2C 租用戶，您必須使用查詢參數 `api-version=1.6`。

這兩個細節都在 `B2CGraphClient.SendGraphGetRequest(...)` 方法中處理：

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the 1.6 Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### 建立取用者的使用者帳戶

在 B2C 租用戶中建立使用者帳戶時，您可以傳送 HTTP `POST` 要求給 `/users` 端點：

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"signInNames": [                            // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "LocalAccount",            // always set to 'LocalAccount'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

此要求中的這些屬性，在建立取用者使用者時，大部分都是必要的屬性。若要深入了解，請按一下[這裡](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)。請注意，加上 `//` 註解是為了講解，請不要放入實際的要求中。

若要查看此要求，請執行下列其中一個命令：

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令會以 .json 檔案做為輸入參數。這包含使用者物件的 JSON 表示法。範例程式碼中有兩個範例 .json 檔案：`usertemplate-email.json` 和 `usertemplate-username.json`。您可以修改這些檔案以符合您的需求。除了上述必要欄位以外，這些檔案包含一些您可以使用的選擇性欄位。[Azure AD 圖形 API 實體參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity)提供選擇性欄位的詳細資訊。

您可以在 `B2CGraphClient.SendGraphPostRequest(...)` 中看到如何建構 POST 要求。

- 它會將存取權杖附加至要求的 `Authorization` 標頭。
- 它會設定 `api-version=1.6`。
- 它會將 JSON 使用者物件加入要求主體中。

> [AZURE.NOTE]
如果您想要從現有使用者存放區移轉的帳戶密碼強度比[由 Azure AD B2C 強制執行的強式密碼強度](https://msdn.microsoft.com/library/azure/jj943764.aspx)還低，您可以使用 `passwordPolicies` 屬性中的 `DisableStrongPassword` 值來停用強式密碼需求。例如，您可以修改上述提供的建立使用者要求，如下所示︰`"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`。

### 更新取用者的使用者帳戶

當您更新使用者物件時，此程序類似於您用來建立使用者物件的程序。但此程序會使用 HTTP `PATCH` 方法：

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

嘗試以新資料更新 JSON 檔案，進而更新使用者。然後您可以使用 `B2CGraphClient` 來執行下列其中一個命令：

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphPatchRequest(...)` 方法。

### 搜尋使用者

您可以透過數種方式在 B2C 租用戶中搜尋使用者。一個是利用使用者的物件識別碼，另一個則是使用者的登入識別碼 (亦即 `signInNames` 屬性)。

執行下列其中一個命令來搜尋特定的使用者︰

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

以下是一些範例︰

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### 刪除使用者

刪除使用者的程序很簡單。使用 HTTP `DELETE` 方法並以正確的物件識別碼建構 URL：

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看範例，請輸入此命令並檢視主控台印出的 Delete 要求：

```
> B2C Delete-User <object-id-of-user>
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphDeleteRequest(...)` 方法。

除了使用者管理，您還可以使用 Azure AD 圖形 API 執行其他許多動作。[Azure AD 圖形 API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)會提供每個動作的詳細資訊以及範例要求。

## 使用自訂屬性

大部分消費者應用程式都需要儲存某種自訂使用者設定檔資訊。作法之一是在 B2C 租用戶中定義自訂屬性。然後，您可將該屬性視同使用者物件的任何其他屬性一樣處理。該屬性就像登入權杖中的宣告，您可以更新、刪除、查詢、傳送該屬性。

若要在 B2C 租用戶中定義自訂屬性，請參閱 [B2C 預覽自訂屬性參考](active-directory-b2c-reference-custom-attr.md)。

您可以使用 `B2CGraphClient` 來檢視 B2C 租用戶中定義的自訂屬性：

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

這些函式的輸出會顯示每個自訂屬性的詳細資料，例如：

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

您可以使用完整名稱做為使用者物件的屬性，例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`。以新的屬性和該屬性的值更新 .json 檔案，然後執行：

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

使用 `B2CGraphClient`，您會有一個服務應用程式可利用程式設計方式來管理 B2C 租用戶使用者。`B2CGraphClient` 會使用自己的應用程式身分識別，向 Azure AD 圖形 API 進行驗證。它也會使用用戶端密碼來取得權杖。將這項功能納入您的應用程式時，請記住 B2C 應用程式的幾個重點：

- 您需要將租用戶中的適當權限授與應用程式。
- 現在，您需要使用 ADAL v2 取得存取權杖(也可以直接傳送通訊協定訊息，而不使用程式庫)。
- 當您呼叫圖形 API 時，請使用 `api-version=1.6`。
- 當建立和更新取用者使用者，有幾個必要的屬性，如上所述。

對於您想要使用圖形 API 在 B2C 租用戶上執行的動作，如有任何問題或要求，請在本文上留言，或在 GitHub 程式碼範例儲存機制中提出問題。

<!---HONumber=AcomDC_0323_2016-->