<properties 
	pageTitle="在 Logic Apps 中呼叫自訂 API" 
	description="將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags ms.service="app-service-logic" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na"
	
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="stepsic"/>
	
# 將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用

雖然邏輯應用程式有一系列 40 餘個各式各樣的連接器，但您可能會想要呼叫自訂的 API 以執行自己的程式碼。是裝載您自己的自訂 Web API，最簡單且最具擴充性的方法之一是使用 App Service。本文說明如何呼叫裝載在 App Service Web 應用程式中的任何 Web API。

## 部署 Web 應用程式

首先，您必須將 API 部署為 App Service 中的 Web 應用程式。此處的指示說明的是基本部署：[建立 ASP.NET Web 應用程式](web-sites-dotnet-get-started.md)。

請確實取得 Web 應用程式的 **URL** - 出現在 Web 應用程式頂端的 **Essentials** 中。

## 呼叫 API

首先應建立新的空白邏輯應用程式。建立空白的邏輯應用程式之後，請按一下 [編輯] 或 [觸發程序和動作]，然後選取 [從頭建立]。

首先，您可能會想要使用週期觸發程序，或按一下 [手動執行此邏輯]。接下來，您會實際呼叫您的 API。若要這樣做，請在右手邊按一下綠色的 [HTTP] 動作。

1. 選擇 [方法] - 這將會定義在您的 API 程式碼中
2. 在 [URL] 區段中，貼入您已部署的 Web 應用程式的 [URL]
3. 如果您需要任何 [標頭]，請將它們包含在 JSON 格式中，如下所示：`{"Content-type" : "application/json", "Accept" : "application/json" }`
4. 如果您的 API 是公用的，您可以將 [驗證] 保留為空白。如果要保護您 API 的呼叫，請參閱下列各節。
5. 最後，納入您的 API 中定義之問題的**本文**。

按一下命令列中的 [儲存]。如果您按一下 [立即執行]，您應該會在執行清單中看到 API 的呼叫和回應。

如果您有公用 API，此方式將可順利運作，但如果您想要保護 API，則有幾種不同的方式可供執行：

1. *無需變更程式碼* - Azure Active Directory 可用來保護您的 API，而不需要任何程式碼變更或重新部署。
2. 在 API 的程式碼中強制執行基本驗證、AAD 驗證或憑證驗證。 

## 保護您 API 的呼叫而不變更程式碼 

在本節中，您將建立兩個 Azure Active Directory 應用程式 – 一個用於邏輯應用程式，一個用於 Web 應用程式。您將會使用與邏輯應用程式的 AAD 應用程式相關聯的服務主體 (用戶端識別碼和密碼)，驗證對您 Web 應用程式的呼叫。最後，您將會在邏輯應用程式定義中納入應用程式識別碼。

### 第 1 部分：設定邏輯應用程式的應用程式身分識別碼

這將是邏輯應用程式用來對 Active Directory 進行驗證的項目。您只*需要*為您的目錄執行此動作一次；例如，您可以選擇讓您所有的邏輯應用程式使用相同的身分識別碼，儘管只要您願意，您也可以為每個邏輯應用程式建立唯一的身分識別碼。您可以在 UI 中執行這項操作，或使用 PowerShell。

#### 使用 Azure 傳統入口網站建立應用程式身分識別碼

1. 瀏覽至 [Azure 傳統入口網站中的 Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，然後選取您要用於 Web 應用程式的目錄
2. 按一下 [應用程式] 索引標籤。
3. 在頁面底部的命令列中按一下 [新增]
4. 為您的身分識別碼指定使用名稱，按 [下一步] 箭號
5. 在兩個文字方塊中放入格式化為網域的唯一字串，然後按一下核取記號
6. 按一下此應用程式的 [設定] 索引標籤
7. 複製 [用戶端識別碼]，這將會用於您的邏輯應用程式
8. 在 [金鑰] 區段中按一下 [選取持續時間]，然後選擇 1 年或 2 年
9. 在畫面底部按一下 [儲存] 按鈕 (您可能需要等候數秒)
10. 現在，請確實將金鑰複製到方塊中。這也會用於您的邏輯應用程式

#### 使用 PowerShell 建立應用程式身分識別碼
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. 請確實複製 [租用戶識別碼]、[應用程式識別碼] 和您所使用的密碼

### 第 2 部分：使用 AAD 應用程式身分識別碼保護您的 Web 應用程式

如果 Web 應用程式已部署，您可以直接在入口網站中加以啟用。否則，您可以啟用 Azure 資源管理員部署的授權部分。

#### 在 Azure 入口網站中啟用授權

1. 瀏覽至 Web 應用程式，然後在命令列中按一下 [設定]。
2. 按一下 [授權/驗證]。 
3. 加以 [開啟]。

此時，應用程式將會自動為您建立。在第 3 部分將需要此應用程式的用戶端識別碼，因此您將需要：

1. 移至 [Azure 傳統入口網站中的 Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，然後選取您的目錄。 
2. 在搜尋方塊中搜尋應用程式
3. 在清單中加以點按
4. 按一下 [設定] 索引標籤
5. 您應會看見 [用戶端識別碼]

#### 使用 ARM 範本部署 Web 應用程式

首先，您必須為 Web 應用程式建立應用程式。這應與用於邏輯應用程式的應用程式不同。首先請遵循第 1 部分中的前述步驟，但是現在對於 **HomePage** 和 **IdentifierUris**，請使用 Web 應用程式的實際 https://**URL**。

>[AZURE.NOTE]當您建立 Web 應用程式的應用程式時，您必須使用 [Azure 傳統入口網站途徑](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，因為 PowerShell Cmdlet 不會設定讓使用者登入網站的必要權限。

在您具備用戶端識別碼和租用戶識別碼後，請在部署範本中納入下列項目做為 Web 應用程式的子資源：

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

若要自動執行部署以同時部署使用 AAD 的空白 Web 應用程式和邏輯應用程式，請按一下下列按鈕：[![部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

如需完整範本，請參閱 [以邏輯應用程式呼叫裝載於 App Service 上且受到 AAD 保護的自訂 API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)。


### 第 3 部分：填入邏輯應用程式中的授權區段

在 [HTTP] 動作的 [授權] 區段中：`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 元素 | 說明 |
|---------|-------------|
| 類型 | 驗證類型。針對 ActiveDirectoryOAuth 驗證，值為 ActiveDirectoryOAuth。 |
| tenant | 用來識別 AD 租用戶的租用戶識別碼。 |
| audience | 必要。您要連接到的資源。 |
| clientID | Azure AD 應用程式的用戶端識別碼。 |
| secret | 必要。要求權杖之用戶端的密碼。 | 

上述範本已設定此項目，但如果您要直接撰寫邏輯應用程式，您必須包含完整的授權區段。

## 保護您在程式碼中的 API

### 憑證驗證

您可以使用用戶端憑證來驗證對您 Web 應用程式的傳入要求。請參閱[如何設定 Web 應用程式的 TLS 相互驗證](app-service-web-configure-tls-mutual-auth.md)，以了解如何設定您的程式碼。

在 [授權] 區段中，您應提供：`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`。

| 元素 | 說明 |
|---------|-------------|
| 類型 | 必要。驗證類型。若為 SSL 用戶端憑證，值必須是 ClientCertificate。 |
| pfx | 必要。Base64 編碼的 PFX 檔案內容。 |
| password | 必要。存取 PFX 檔案的密碼。 |

### 基本驗證

您可以使用基本驗證 (例如使用者名稱和密碼) 來驗證傳入要求。基本驗證是常見模式，你可以使用您用來建置應用程式的任何語言執行此驗證。

在 [授權] 區段中，您應提供：`{"type": "basic","username": "test","password": "test"}`。

| 元素 | 說明 |
|---------|-------------|
| 類型 | 必要。驗證類型。若為基本驗證，值必須是 Basic。 |
| username | 必要。要驗證的使用者名稱。 |
| password | 必要。要驗證的密碼。 |
 
### 在程式碼中處理 AAD 驗證

根據預設，您在入口網站中啟用的 Azure Active Directory 驗證並不會執行精細的授權。例如，它不會鎖定您的 API 給特定使用者或應用程式使用，但只是鎖定給特定租用戶。

如果您要將 API 限定給邏輯應用程式使用 (例如在程式碼中)，您可以擷取包含 JWT 的標頭，並檢查呼叫端為何，而拒絕任何不相符的要求。

再進一步，如果您想要在自己的程式碼中加以完整實作，且不利用入口網站功能，您可以參閱這篇文章：[在 Azure App Service 中使用 Active Directory 進行驗證](web-sites-authentication-authorization.md)。

您仍然必須遵循前述步驟建立邏輯應用程式的應用程式身分識別碼，並用它來呼叫 API。

<!-------HONumber=AcomDC_1210_2015--->