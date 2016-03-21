<properties 
	pageTitle="使用 Mobile Engagement REST API 進行驗證"
	description="描述如何使用 Azure Mobile Engagement REST API 進行驗證" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="03/07/2016"
	ms.author="wesmc"/>

# 使用 Mobile Engagement REST API 進行驗證

## 概觀

本文件詳細說明如何讓驗證機制為新的 API 運作。

已假設您具備有效的 Azure 訂用帳戶，也已使用其中一個[開發人員教學課程](mobile-engagement-windows-store-dotnet-get-started.md)建立 Mobile Engagement 應用程式。

## 驗證

必須使用 Microsoft Azure Active Directory 型的 OAuth 權杖進行驗證。

為了驗證 API 要求，必須將授權標頭加入至每個要求。它必須是下列格式：

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory 權杖會在 1 小時內過期。

有幾種方式可以取得權杖。因為通常會從雲端服務呼叫 API，您可能會想要使用 API 金鑰。在 Azure 術語中，API 金鑰稱為「服務主體密碼」。下列程序說明一種手動設定的方法。

> [AZURE.WARNING] Azure Active Directory 中顯示的金鑰「不是」入口網站中顯示的 Mobile Engagement API 金鑰。Mobile Engagement API 金鑰的概念已經過時，由本文中所述的 AAD 驗證取而代之。

#### 單次設定 (手動)

這樣做時，請注意下列資訊，稍後需要用到：
	
1. 使用[本指南](../resource-group-create-service-principal-portal.md)建立 Azure Active Directory 應用程式。

	- 您選擇的應用程式名稱，在本文件中以 `{AD_APP_NAME}` 表示。
	- 顯示在設定功能表中的用戶端識別碼，在本文件中以 `{CLIENT_ID}` 表示。
	- 儲存之後只顯示一次的金鑰，在本文件中以 `{CLIENT_SECRET}` 表示。
	- 按一下底部列的 [檢視端點] 按鈕，然後複製 [OAUTH 2.0 權杖端點 URL]，在本文件中以 `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` 表示。<br/>                                    
2. 使用 [Azure CLI](../xplat-cli-install.md) 將角色指派給服務主體，例如讀者或擁有者。

	如果您是在 Windows 上，請修改 `PATH` 環境變數來包含 `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin`，才能夠使用 Azure 命令。

	使用 Azure 命令列介面 (CLI) 執行下列命令來設定您的帳戶：

		azure config mode arm 
		azure login

	然後，使用此命令尋找您的應用程式的物件識別碼。

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	請記下輸出中的 `Object Id`。

	然後，使用此命令指派 `Owner` 角色給服務主體：
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### 單次設定 (使用指令碼)

這是使用 PowerShell 指令碼來完成前述步驟的另一種方式。

1. 取得最新版的 Azure PowerShell。請參閱此[連結](../powershell-install-configure.md)以取得下載指示。 

2. 以系統管理員模式開啟 Windows PowerShell，並確定您已安裝 [Azure Resource Manager Cmdlet](https://msdn.microsoft.com/library/mt125356.aspx)。

		Install-Module AzureRM
		Install-AzureRM

3. 執行以下命令：

		Import-Module AzureRM.profile

4. 執行下列命令來啟動登入對話方塊。登入之後，該命令會顯示「作用中」訂用帳戶，這表示您執行的任何命令都會影響此訂用帳戶。

		Add-AzureRmAccount

5. 執行下列命令來列出您的所有訂用帳戶。複製您想要使用的訂用帳戶的 guid。

		Get-AzureRmSubscription

6. 執行下列命令並提供訂用帳戶 guid，以設定要使用的訂用帳戶。當您有多個訂用帳戶時，這特別有用。

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. 將 [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 指令碼的文字複製到本機電腦，並執行它。

	>[Azure.Note] 預設的安全性原則可能會阻止您執行 PowerShell 指令碼。如果是這樣，請使用下列命令，暫時設定您的執行原則來允許執行指令碼：

	>	Set-ExecutionPolicy RemoteSigned

	指令碼將會提示您輸入要指派給 ServicePrincipal 的 「名稱」。您在這裡可以提供任何您想要的名稱。

	指令碼完成之後，它會顯示四個需要以程式設計方式向 AD 驗證的值：**TenantId**、**SubscriptionId**、**ApplicationId** 和 **Secret**。

	複製這些值當作參考。現在要取得存取權杖，您將使用 TenantId 做為 `{TENANT_ID}`、使用 ApplicationId 做為 `{CLIENT_ID}`，並使用 Secret 做為 `{CLIENT_SECRET}`。

8. 在 Azure 管理入口網站中，檢查新的 AD 應用程式出現在 [顯示我公司所擁有的應用程式] 之下。

#### 取得有效權杖的步驟

若要取得新的權杖，呼叫此 API：

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

以下是範例要求：

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

以下是範例回應：

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

這個範例包含 POST 參數的 URL 編碼，`resource` 值實際上是 `https://management.core.windows.net/`。請注意也要在 URL 中將 `{CLIENT_SECRET}` 編碼，因為它可能包含特殊字元。

現在，在每個 API 呼叫中加入授權要求標頭：

	Authorization: Bearer {ACCESS_TOKEN}

如果傳回 401 狀態碼，請檢查回應本文，其中可能指出權杖已過期。在此情況下，請取得新的權杖。

##使用 API

既然您已取得有效的權杖，您可以開始執行 API 呼叫。

1. 在每個 API 要求中，您必須傳遞您在上一節取得的有效、未過期的權杖。

2. 您需要在用來識別應用程式的要求 URI 中插入一些參數。要求 URI 看起來如下所示

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	若要取得參數，請按一下您的應用程式名稱，再按一下 [儀表板]，您將會看到如下的頁面和所有 3 個參數。

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/>
>1. 忽略 API 根位址，因為這是用於舊版 API。<br/> 2.您需要使用不同於應用程式名稱本身的應用程式資源名稱。 

<!---HONumber=AcomDC_0309_2016-->