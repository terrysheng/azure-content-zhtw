<properties
	pageTitle="Azure AD B2C 預覽：使用圖形 API | Microsoft Azure"
	description="如何對 B2C 租用戶呼叫圖形 API 並使用應用程式身分識別將過程自動化。"
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

# Azure AD B2C 預覽：使用圖形 API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Azure AD B2C 租用戶通常會很龐大，這表示許多常見的租用戶管理工作需要以程式設計方式執行。一個主要的例子是使用者管理 - 您可能需要將現有的使用者存放區移轉到 B2C 租用戶，也可能想要在自己的網頁上主控使用者註冊，在幕後於 Azure AD 中建立使用者帳戶。這類工作需要能夠建立、讀取、更新和刪除使用者帳戶 - 這些都能透過 Azure AD 圖形 API 達成。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

對於 B2C 租用戶，與圖形 API 通訊主要有兩種模式。

- 對於互動式、執行一次的工作，您可能想要以 B2C 租用戶中的系統管理員帳戶來執行管理工作。此模式需要系統管理員先以認證登入，才能對圖形 API進行任何呼叫。
- 對於自動化、持續的工作，您可以使用您授與必要權限的某種服務帳戶來執行管理工作。在 Azure AD 中，作法上您可以使用 [OAuth 2.0 用戶端認證授與](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)，註冊應用程式並利用「應用程式身分識別」向 Azure AD 驗證。在此情況下，應用程式會親自呼叫圖形 API，而不以任何特定的使用者身分。  

在本文中，我們將示範如何執行後者的自動化使用案例。為了示範，我們會建置 .NET 4.5 "B2CGraphClient" 來執行使用者 CRUD 作業。為了方便試驗，用戶端會有 Windows 命令列介面讓您叫用各種方法。不過，程式碼會撰寫成以非互動、自動化的方式運作。現在就開始吧。

## 取得 Azure AD B2C 租用戶

您需要一個 Azure AD B2C 租用戶和該租用戶中的全域系統管理員帳戶，才可建立應用程式、使用者，或與 Azure AD 進行任何互動。如果您還沒有此目錄，請依照[開始使用 Azure AD B2C](active-directory-b2c-get-started.md) 指南進行。

## 在租用戶中註冊服務應用程式

既然您已經有 B2C 租用戶，您需要使用 Azure AD Powershell Cmdlet 建立服務應用程式。首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。接著可以下載並安裝[適用於 Windows PowerShell 的 64 位元 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。

> [AZURE.NOTE]
若要使用圖形 API 搭配 B2C 租用戶，您必須使用 Powershell 註冊專用的應用程式，並遵循下列指示。您不能重複使用已經在 Azure 入口網站中註冊的現有 B2C 應用程式。這是 Azure AD B2C 預覽的一項限制，將在不久的將來移除，屆時我們將會更新本文章。

安裝 Powershell 模組後，開啟 Powershell 並連線到 B2C 租用戶。執行 `Get-Credential` 之後，將提示您輸入使用者名稱和密碼，請輸入您 B2C 租用戶系統管理員帳戶的使用者名稱和密碼。

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

建立應用程式之前，您必須產生新的「用戶端密碼」。您的應用程式將使用此用戶端密碼向 Azure AD 驗證，並取得存取權杖。您可以在 Powershell 中產生有效的密碼：

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

上述最後一個命令應該會印出新的用戶端密碼。將它複製到某個安全的地方，稍後需要用到。現在您可以建立應用程式，並提供新的用戶端密碼作為應用程式的認證：

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

如果成功建立應用程式，應該會印出應用程式的某些屬性，如上所示。您將需要 `ObjectId` 和 `AppPrincipalId`，因此也請抄下這些值。

既然您已經在 B2C 租用戶中建立應用程式，您需要將執行使用者 CRUD 作業所需的權限指派給它。您需要將三個不同的角色指派給應用程式：目錄讀取者 (用於讀取使用者)、目錄寫入者 (用於建立及更新使用者)，以及使用者帳戶管理員 (用於刪除使用者)。這些角色具有已知的識別碼，所以您可以執行下列命令，使用上述的 `ObjectId` 取代 `-RoleMemberObjectId` 參數。若要查看所有目錄角色的清單，請嘗試執行 `Get-MsolRole`。

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

您現在的應用程式有權從 B2C 租用戶建立、讀取、更新和刪除使用者，我們就撰寫一些程式碼來使用它。

## 下載、設定和建置範例程式碼

首先，下載範例程式碼並開始執行。然後，我們可以看看幕後運作情形。您可以[下載 .zip 格式的範例程式碼](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)，或將它複製到您所選擇的目錄：

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

在 Visual Studio 中開啟 `B2CGraphClient\B2CGraphClient.sln` Visual Studio 方案。在 `B2CGraphClient` 專案中，開啟 `App.config` 檔案。使用您自己的值取代三個應用程式設定，如下所示：

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

現在，以滑鼠右鍵按一下 `B2CGraphClient` 方案並重建範例。如果成功，您現在應該有一個可執行檔 `B2C.exe` 位於 `B2CGraphClient\bin\Debug`。

## 使用圖形 API 執行使用者 CRUD

若要使用 B2CGraphClient，請開啟 cmd Windows 命令提示字元，cd 到 `Debug` 目錄。然後執行 `B2C Help` 命令。

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

這會顯示每個命令的簡短描述。當您叫用任何這些命令時，B2CGraphClient 會對 Azure AD 圖形 API 發出要求。

### 取得存取權杖

對圖形 API 發出任何要求時，需要有存取權杖進行驗證。B2CGraphClient 使用開放原始碼 Active Directory 驗證程式庫 (ADAL) 來協助取得存取權杖。您當然不一定要使用 ADAL 來取得權杖，您可以製作自己的 HTTP 要求來取得權杖。ADAL 提供簡單的 API 並處理一些重要的細節，例如快取存取權杖，可讓您輕鬆取得權杖。

> [AZURE.NOTE]
	此程式碼範例刻意使用 ADAL v2 (ADAL 公開上市版本)。「不」使用 ADAL v4，因為這是專門用於 Azure AD B2C 的預覽版本。在 Azure AD B2C 預覽中，您必須使用 ADAL v2 才能與圖形 API 進行通訊。未來，我們將支援以 ADAL v4 存取圖形 API，您不需要在完整的 Azure AD B2C 方案中使用兩個不同版本的 ADAL。

B2CGraphClient 執行時會建立 `B2CGraphClient` 類別的執行個體。這個類別的建構函式會設定 ADAL 的驗證架構：

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
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

讓我們以 `B2C Get-User` 命令為例。叫用 `Get-User` 而沒有任何其他輸入時，CLI 會呼叫 `B2CGraphClient.GetAllUsers(...)` 方法。這個方法會呼叫 `B2CGraphClient.SendGraphGetRequest(...)`，後者會送出 HTTP GET 要求給圖形 API。在傳送 GET 要求之前，它會先使用 ADAL 取得存取權杖：

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

如您所見，您可以呼叫 ADAL 的 `AuthenticationContext.AcquireToken(...)` 方法，取得圖形 API 的存取權杖。ADAL 會傳回代表應用程式身分識別的 access\_token。

### 讀取使用者

當您想要從圖形 API 取得使用者清單或取得特定的使用者時，您可以傳送 HTTP GET 要求給 `/users` 端點。要求取得租用戶中所有使用者時，情況如下：

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

若要查看此要求的運作情形，請嘗試執行：

 ```
 > B2C Get-User
 ```

這裡有兩個重點值得注意：

- 透過 ADAL 取得的存取權杖已利用 `Bearer` 配置加入 `Authorization` 標頭。
- 對於 B2C 租用戶，您必須使用查詢參數 `api-version=beta`。


> [AZURE.NOTE]
	Azure AD 圖形 API 的 Beta 版提供預覽功能。如需 Beta 版的詳細資訊，請參閱[這篇圖形 API 小組部落格文章](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx)。

這兩個細節都在 `B2CGraphClient.SendGraphGetRequest(...)` 方法中處理：

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### 建立取用者的使用者帳戶

在 B2C 租用戶中建立使用者帳戶時，您可以傳送 HTTP POST 要求給 `/users` 端點：

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

建立取用者使用者時，需要以上要求中包含的每個屬性。加上 `//` 註解是為了講解，請不要放入實際的要求中。

若要查看此要求的運作情形，請嘗試執行下列其中一個命令：

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` 命令接受 `.json` 檔案作為輸入參數，其中包含使用者物件的 JSON 表示法。範例程式碼包含兩個範例 `.json` 檔案 - `usertemplate-email.json` 和 `usertemplate-username.json` - 可修改成符合您的需求。除了上述必要欄位，這些檔案中還有一些您可以使用的選擇性欄位。[Azure AD 圖形 API 實體參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity)提供這些其他欄位的詳細資訊。

您可以在 `B2CGraphClient.SendGraphPostRequest(...)`中看到如何建構此 POST 要求，它會：

- 將存取權杖附加至要求的 `Authorization` 標頭。
- 設定 `api-version=beta`。
- 將 JSON 使用者物件加入要求主體中。

### 更新取用者的使用者帳戶

更新使用者物件非常類似於建立使用者物件，但使用 HTTP PATCH 動詞命令：

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

您可以嘗試以新資料更新 JSON 檔案，並使用 B2CGraphClient 執行下列其中一個命令，以更新使用者：

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphPatchRequest(...)` 方法。

### 刪除使用者

刪除使用者很簡單 - 只要使用 HTTP DELETE 動詞命令，並以正確的物件識別碼建構 URL：

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看範例，請嘗試下列命令並檢視主控台印出的 DELETE 要求：

```
> B2C Delete-User <object-id-of-user>
```

如需有關如何傳送此要求的詳細資訊，請檢閱 `B2CGraphClient.SendGraphDeleteRequest(...)` 方法。

除了使用者管理，Azure AD 圖形 API 還可讓您執行其他許多動作。[Azure AD 圖形 API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)搭配範例要求，提供每個動作的詳細資訊。


## 使用自訂屬性

幾乎每個消費者應用程式都需要儲存某種自訂使用者設定檔資訊。作法之一是在 B2C 租用戶中定義自訂屬性，讓您將該屬性視同使用者物件的任何其他內容一樣。該屬性就像登入權杖中的宣告，您可以更新、刪除、查詢、傳送該屬性。

若要在 B2C 租用戶中定義自訂屬性，請參閱 [B2C 預覽自訂屬性參考](active-directory-b2c-reference-custom-attr.md)。

您可以使用 B2CGraphClient 來檢視 B2C 租用戶中定義的自訂屬性：

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

您可以使用完整名稱作為使用者物件的屬性，例如 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`。只需要以新的屬性和該屬性的值更新 `.json` 檔案，然後執行：

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

就這麼簡單！ 透過 B2CGraphClient，您現在有一個服務應用程式可以用程式設計方式管理 B2C 租用戶使用者。它使用自己的應用程式身分識別向 Azure AD 圖形 API 驗證，並使用 client\_secret 取得權杖。將這項功能納入您自己的應用程式時，請記住 B2C 應用程式的幾個重點：

- 您需要將租用戶中的適當權限授與應用程式
- 現在，您需要使用 ADAL v2 取得存取權杖 (也可以直接傳送通訊協定訊息，而不使用程式庫)
- 呼叫圖形 API 時，請使用 [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx)。
- 當建立和更新取用者使用者，有幾個必要的屬性，如上所述。

對於您想要使用圖形 API 在 B2C 租用戶上執行的動作，如果您有任何問題或要求，我們洗耳恭聽！ 請在文章上留言，或在程式碼範例 GitHub 儲存機制中提出問題。

<!---HONumber=AcomDC_0224_2016-->