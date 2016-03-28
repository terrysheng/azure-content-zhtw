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
	ms.date="03/11/2016"
	ms.author="wesmc"/>

# 使用 Mobile Engagement REST API 進行驗證

## 概觀

本文件說明如何取得有效的 AAD Oauth 權杖，以使用 Mobile Engagement REST API 進行驗證。

已假設您具備有效的 Azure 訂用帳戶，也已使用其中一個[開發人員教學課程](mobile-engagement-windows-store-dotnet-get-started.md)建立 Mobile Engagement 應用程式。

## 驗證

使用 Microsoft Azure Active Directory 型的 OAuth 權杖進行驗證。

為了驗證 API 要求，必須將授權標頭加入至每個要求，其格式如下：

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory 權杖會在 1 小時內過期。

有幾種方式可以取得權杖。因為通常會從雲端服務呼叫 API，所以您會想要使用 API 金鑰。在 Azure 術語中，API 金鑰稱為「服務主體密碼」。下列程序說明一種手動設定的方法。

### 單次設定 (使用指令碼)

您應該遵循下列這組指示，使用 PowerShell 指令碼來執行安裝程式，該指令碼會利用最少時間進行安裝，但會使用最多允許的預設值。或者，您也可以依照[手動安裝](mobile-engagement-api-authentication-manual.md)中的指示來執行，以便從 Azure 入口網站直接執行此動作並進行更細微的設定。

1. 從[此處](http://aka.ms/webpi-azps)取得最新版的 Azure PowerShell。如需下載指示的詳細資訊，您可以查看這個[連結](../powershell-install-configure.md)。  

2. 安裝 Azure PowerShell 之後，使用下列命令，以確保您已安裝 **Azure 模組**︰

    a.確定 Azure PowerShell 模組可在可用模組清單中取得。
    
		Get-Module –ListAvailable 

	![可用的 Azure 模組][1]
    	
    b.如果您在上述清單中找不到 Azure PowerShell 模組，則必須執行下列命令︰
    	
		Import-Module Azure 
    	
3. 從 PowerShell 登入 Azure Resource Manager，方法是執行下列命令，並提供您 Azure 帳戶的使用者名稱和密碼︰
    	
		Login-AzureRmAccount

4. 如果您擁有多個訂用帳戶，則您應該執行下列動作︰

	a.取得所有訂用帳戶的清單，並複製您想要使用的訂用帳戶的 SubscriptionId。確定此訂用帳戶與含有您要使用 API 進行互動的 Mobile Engagement 應用程式的訂用帳戶相同。

		Get-AzureRmSubscription

	b.執行下列命令，提供 SubscriptionId，以設定要使用的訂用帳戶。

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. 將 [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 指令碼的文字複製到本機電腦、將它儲存為 PowerShell Cmdlet (例如 `APIAuth.ps1`)，然後執行它 `.\APIAuth.ps1`。
	
6. 指令碼將會要求您提供 **principalName** 的輸入。在此處提供您想要用來建立 Active Directory 應用程式 (例如 APIAuth) 的適當名稱。

7. 指令碼完成之後，它將會顯示下列四個值，您將需要以程式設計方式利用 AD 進行驗證，因此請確定會複製它們。
		
	**TenantId**、**SubscriptionId**、**ApplicationId** 及 **Secret**。

	您將使用 TenantId 做為 `{TENANT_ID}`、使用 ApplicationId 做為 `{CLIENT_ID}`，並使用 Secret 做為 `{CLIENT_SECRET}`。

	> [AZURE.NOTE] 預設的安全性原則可能會阻止您執行 PowerShell 指令碼。如果是這樣，請使用下列命令，暫時設定您的執行原則來允許執行指令碼：

    	> Set-ExecutionPolicy RemoteSigned

8. PS Cmdlet 組合看起來應如下所示。

	![][3]

9. 查看 Azure 管理入口網站，會有一個新的 AD 應用程式是使用您提供給 [顯示我公司所擁有的應用程式] 下方名為 **principalName** 的指令碼的名稱所建立。

	![][4]

#### 取得有效權杖的步驟

1. 使用下列參數呼叫 API，且務必取代 TENANT\_ID、CLIENT\_ID 與 CLIENT\_SECRET：

	- **要求 URL** 為 **https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
- **HTTP Content-Type 標頭**為 *application/x-www-form-urlencoded*
	- **HTTP 要求主體**為 *grant\_type=client\_credentials&client\_id={CLIENT\_ID}&client\_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

	以下是範例要求：

		POST /{TENANT_ID}/oauth2/token HTTP/1.1
		Host: login.microsoftonline.com
		Content-Type: application/x-www-form-urlencoded
		grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
		urce=https%3A%2F%2Fmanagement.core.windows.net%2F

	以下是範例回應：

		HTTP/1.1 200 OK
		Content-Type: application/json; charset=utf-8
		Content-Length: 1234
	
		{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
		5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

	這個範例包含 POST 參數的 URL 編碼，`resource` 值實際上是 `https://management.core.windows.net/`。請注意也要在 URL 中將 `{CLIENT_SECRET}` 編碼，因為它可能包含特殊字元。

	> [AZURE.NOTE] 如需測試，您可以使用像是 [Fiddler](http://www.telerik.com/fiddler) 或 [Chrome Postman 延伸模組](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)的 HTTP 用戶端工具

2. 現在，在每個 API 呼叫中加入授權要求標頭：

		Authorization: Bearer {ACCESS_TOKEN}

	如果傳回 401 狀態碼，請檢查回應本文，其中可能指出權杖已過期。在此情況下，請取得新的權杖。

##使用 API

既然您已取得有效的權杖，您可以開始執行 API 呼叫。

1. 在每個 API 要求中，您必須傳遞您在上一節取得的有效、未過期的權杖。

2. 您需要在用來識別應用程式的要求 URI 中插入一些參數。要求 URI 看起來如下所示

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	若要取得參數，請按一下您的應用程式名稱，再按一下 [儀表板]，您將會看到如下的頁面和所有 3 個參數。

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`
	- **4** 除非您建立一個新的，否則您的資源群組名稱應該是 **MobileEngagement**。 

	![Mobile Engagement API URI 參數][2]

>[AZURE.NOTE] <br/>
>1. 忽略 API 根位址，因為這適用於舊版 API。<br/> 2.您需要使用不同於應用程式名稱本身的應用程式資源名稱。 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png

<!---HONumber=AcomDC_0316_2016-->