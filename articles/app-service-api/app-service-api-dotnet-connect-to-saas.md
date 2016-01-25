<properties 
	pageTitle="在 Azure App Service 中自訂 SaaS 連接器 ASP.NET API 應用程式" 
	description="了解如何撰寫從 API 應用程式連接至 SaaS 平台的程式碼，以及如何從 .NET 用戶端呼叫 API 應用程式。" 
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
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# 從 Azure App Service 中的 ASP.NET API 應用程式連接至 SaaS 平台

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概觀

本教學課程示範如何撰寫及設定 [API 應用程式](app-service-api-apps-why-best-platform.md)的程式碼，其使用 [App Service API App SDK for .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) 連接至[軟體即服務 (SaaS) 平台](../app-service/app-service-authentication-overview.md#obotosaas)。本教學課程也會示範如何使用 [App Service SDK for .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService) 從 .NET 用戶端呼叫 API 應用程式。在本教學課程結尾，您將擁有 .NET 主控台應用程式用戶端，可呼叫在 Azure App Service 執行的 .NET API 應用程式。API 應用程式會呼叫 Dropbox API，並傳回使用者 Dropbox 帳戶中的檔案和資料夾清單。

撰寫可直接從自訂 API 應用程式呼叫 SaaS API 之程式碼的替代方式為，呼叫預先封裝的[連接器 API 應用程式](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)。如需如何執行這項作業的相關資訊，請參閱[部署及設定 SaaS 連接器 API 應用程式](app-service-api-connnect-your-app-to-saas-connector.md)。

本教學課程將逐步引導您完成下列步驟：

* 在 Visual Studio 中建立 API 應用程式專案。 
* 設定 *apiapp.json* 檔案，讓 API 應用程式連接至 Dropbox 服務。
* 加入呼叫 Dropbox 並傳回結果的程式碼。
* 在 Azure 中建立新的 API 應用程式。
* 將專案部署至 API 應用程式。
* 設定 API 應用程式。
* 設定閘道。
* 建立測試用戶端。
* 執行測試用戶端。

## 必要條件

本教學課程進行下列假設：

* 您已完成[建立 API 應用程式](app-service-dotnet-create-api-app.md)和[部署 API 應用程式](app-service-dotnet-deploy-api-app.md)教學課程。
  
* 您已經對進行驗證的 Azure App Service 閘道器架構有基本了解 (相關資訊如 [API 應用程式與行動應用程式的驗證](app-service-authentication-overview.md)所述)。

* 您已知道如何使用 Azure Preview 入口網站中的 API 應用程式 (相關資訊如 [如何巡覽至 API 應用程式和閘道刀鋒視窗](app-service-api-manage-in-portal.md#navigate)所述)。

## 建立 API 應用程式專案
 
當指示要求您為專案輸入名稱時，請輸入 *SimpleDropbox*。

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## 設定 *apiapp.json* 檔案

為了讓 API 應用程式對 SaaS 平台發出傳出呼叫，必須在 *apiapp.json* 檔案中指定 SaaS 平台。

1. 請如此處所示，開啟 *apiapp.json* 檔案並加入 `authentication` 屬性 (您也必須在前面的屬性後加入逗號)：

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	完整 apiapp.json 檔案將與此範例類似：

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. 儲存檔案。

設定 `authentication` 屬性有幾種效果：

* 它會讓入口網站在 API 應用程式刀鋒視窗中顯示 UI，以便您輸入 SaaS 平台的用戶端識別碼和用戶端密碼值。

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* 它可讓 API 應用程式從閘道擷取 SaaS 提供者的存取 Token，以用於呼叫 SaaS 提供者的 API。

`authentication` 屬性是一種陣列，但此預覽版本不支援指定多個提供者。

如需所支援平台的清單，請參閱[取得使用者的同意以存取其他 SaaS 平台](../app-service/app-service-authentication-overview.md#obotosaas)。

您也可以指定範圍 (如這個範例所示)：

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

可用範圍是由每個 SaaS 提供者所定義，而且可以在提供者的開發人員入口網站中找到。

## 加入呼叫 Dropbox 的程式碼

1. 在 SimpleDropbox 專案中，安裝 [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) NuGet 封裝。

	* 從 [**工具**] 功能表中按一下 **[NuGet 封裝管理員] > [封裝管理員主控台]**。

	* 在 [**封裝管理員主控台**] 視窗中，輸入此命令：
	 
			install-package DropboxRestAPI  

1. 開啟 *Controllers\\ValuesController.cs*，並以下列程式碼取代檔案中的所有程式碼。

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	在用戶端呼叫這個方法之前，使用者已登入 Dropbox，並同意 API 應用程式存取使用者的 Dropbox 帳戶。Dropbox 藉由提供 App Service 閘道的存取 Token 來認可該同意。此程式碼會從閘道擷取 Token，並將它包括在 Dropbox API 的呼叫中 (如下圖的步驟 6 和 7 所示)。

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. 建置專案。

## 在 Azure 中建立 API 應用程式

在此區段中，您將使用 Visual Studio **發佈 Web** 精靈在 Azure 中建立 API 應用程式。指示要求您為 API 應用程式輸入名稱時，請輸入 *SimpleDropbox*。

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## 部署程式碼

您可使用同一個**發佈 Web** 精靈將程式碼部署到新的 API 應用程式。

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## 設定傳入呼叫的驗證

若要讓 Azure App Service 允許來自 API 應用程式的已驗證傳出呼叫，API 應用程式也必須要求傳入呼叫來自已驗證的使用者。這不是一般 OAuth 2.0 需求，而是目前實作的 App Service 閘道架構需求。

本區段中的螢幕擷取畫面顯示 ContactsList API 應用程式，但處理序也適用於您將在本教學課程中建立的 SimpleDropbox API 應用程式。

### 設定 API 應用程式以要求驗證傳入呼叫

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### 在閘道中設定識別提供者

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## 設定傳出呼叫的驗證

若要讓您的 API 應用程式呼叫 Dropbox API，則必須在您 API 應用程式與您在 Dropbox 開發人員網站建立的 Dropbox 應用程式之間交換設定。

### 建立 Dropbox.com 網站上的 Dropbox 應用程式

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### 在 Dropbox 與您 API 應用程式之間交換設定

下列步驟參考 Dropbox 連接器 API 應用程式，但程序和 UI 也適用於您將在本教學課程中建立的 SimpleDropbox API 應用程式。

> **注意：**若您在 SimpleDropbox API 應用程式的 [**驗證**] 刀鋒視窗上沒有看到 Dropbox 用戶端識別碼和用戶端密碼的欄位 (如螢幕擷取畫面中所示)，請確認您將 API 應用程式專案部署至 API 應用程式之後，是否有依照指示重新啟動閘道器。您先前部署的 *apiapp.json* 檔案之 `authentication` 屬性中的「dropbox」值會觸發入口網站顯示這些欄位。

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## 建立測試用戶端

在本區段中，您將建立主控台應用程式專案，以使用 Visual Studio 所產生的用戶端程式碼呼叫 SimpleDropbox API 應用程式。主控台應用程式會具現化 Windows Form 瀏覽器控制項，以處理與閘道和 Dropbox 登入網頁的使用者互動。

### 建立專案

1. 在 Visual Studio 中，建立主控台應用程式專案，並將其命名為 *SimpleDropboxTest*。

2. 設定對 System.Windows.Forms 的參考。
 
	* 在 [方案總管] 中，以滑鼠右鍵按一下 [參考]，然後按一下 [加入參考]。

	* 選取 [**System.Windows.Forms**] 左側的核取方塊，然後按一下 [**確定**]。
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	主控台應用程式將使用 Windows Form 組件，在瀏覽器控制項需要讓使用者登入至閘道和 Dropbox 時，加以具現化。

### 加入產生的用戶端程式碼

此區段中的螢幕擷取畫面顯示 ContactsList 專案和 API 應用程式，但在本教學課程中，請選取 SimpleDropboxTest 專案和 SimpleDropbox API 應用程式。

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### 加入呼叫 API 應用程式的程式碼

3. 開啟 *Program.cs*，並以下列程式碼取代其中的程式碼。
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. 將 {閘道 URL} 取代為您閘道的實際 URL。
 
	您可以從入口網站中的 [**閘道**] 刀鋒視窗取得閘道 URL。

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **重要**：請確定閘道 URL 的開頭為 `https://`，而非 `http://`。**如果您從入口網站複製 http://，在將其貼入程式碼時，必須變更為 https://。**

### 程式碼的說明

此主控台應用程式的設計目的是使用最少量的程式碼，說明用戶端應用程式必須經過的步驟。生產應用程式通常不會是主控台應用程式，而將實作錯誤處理和記錄。

以下是程式碼作用的概觀：

* 將瀏覽器開啟為已設定識別提供者 (在此情況下是 Azure Active Directory) 的閘道登入 URL。 
	 
* 在使用者登入後處理預期的回應 URL：擷取使用者識別碼和 Zumo Token，並將其提供給 App Service 用戶端物件。

* 使用 App Service 用戶端物件擷取閘道 URL，以重新導向至 Dropbox 的登入和同意連結。圖表中的步驟 1。

* 將瀏覽器開啟為閘道同意 URL。瀏覽器會重新導向至 Dropbox 登入和同意連結。圖表中的步驟 2。
	 
* 當使用者在 Dropbox.com 登入並同意之後，請關閉瀏覽器。圖表中的步驟 3。
 
* 呼叫 API 應用程式。圖表中的步驟 5。(步驟 4 是在背景發生於 Dropbox.com 與閘道之間，而步驟 6 和 7 是從 API 應用程式完成，而非用戶端。)

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

其他注意事項：

* `Main` 方法上的 `STAThread` 屬性是網頁瀏覽器控制項的必要項目，而且與設定或呼叫 API 應用程式無關。

* 針對 Azure Active Directory，所顯示的閘道器登入 URL 結尾為 `/aad`。

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	以下是用於其他提供者的值：* "microsoftaccount" * "facebook" * "twitter" * "google" <br/><br/>

* `GetConsentLinkAsync()` 方法的第二個參數是回呼 URL，而同意伺服器會在使用者登入 Dropbox 並同意存取其帳戶之後，重新導向至該回呼 URL。

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	在這個參數中，您通常會指定使用者在用戶端應用程式中應該移至的下一個網頁。因為此示範程式碼是在主控台應用程式中，所以沒有要移至的應用程式頁面，而且程式碼會指定閘道 URL 就像便利的登陸頁面。

	用戶端應用程式應該確認它會重新導向至此 URL，而且沒有任何錯誤訊息。如果登入/同意程序失敗，則重新導向 URL 可能會包含查詢字串中的錯誤訊息。如需詳細資訊，請參閱[疑難排解](#troubleshooting)一節。

## 測試

1. 執行 SimpleDropboxTest 主控台應用程式。

2. 在第一個登入頁面中，請使用您的 Azure Active Directory 認證 (或您在閘道中設定之其他識別提供者 (例如 Google 或 Twitter) 的認證) 登入。

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. 在 Dropbox.com 登入頁面中，請使用您的 Dropbox 認證登入。

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. 在 Dropbox 同意頁面中，請授與應用程式權限以存取您的資料。

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	主控台應用程式接著呼叫 API 應用程式，它會傳回 Dropbox 帳戶中的檔案清單。

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## 疑難排解

本區段包含下列主題：

* [閘道登入後的 HTTP 錯誤 405](#405)
* [HTTP 錯誤 400 而非 Dropbox 登入頁面](#400)
* [呼叫 API 應用程式時的 HTTP 錯誤 403](#403)

### <a id="405"></a> 閘道器登入後發生的 HTTP 錯誤 405

如果您在程式碼呼叫 GetConsentLinkAsync 時收到 HTTP 錯誤 405，請確認您針對閘道 URL 使用 https:// 而非 http://。

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

收到錯誤訊息「405 不允許方法」，因為用戶端嘗試進行非 SSL HTTP POST 要求。閘道器會重新導向至 **https://*，而該重新導向動作將導致發出 GET 要求。擷取同意連結的 URL 只接受 POST 要求。

### <a id="400"></a>出現 HTTP 錯誤 400 而非 Dropbox 登入頁面

請確認您在 API 應用程式的 [**驗證**] 刀鋒視窗是否輸入正確的 [**用戶端識別碼**]，並且開頭或結尾沒有任何空格。

### <a id="403"></a> 呼叫 API 應用程式時發生 HTTP 錯誤 403

* 請確認 API 應用程式的 [**存取層級**] 是否設為 [**公用 (已驗證)**]，而非 [**內部**]。

* 請確認您在 API 應用程式的 [**驗證**] 刀鋒視窗是否輸入正確的 [**用戶端密碼**]，並且開頭或結尾沒有任何空格。

Dropbox 登入之後的重新導向 URL 可能與此範例類似：

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

從 `error` 查詢字串值的結尾移除 %3d%3d，便成為有效的 base64 編碼字串。解碼字串以取得錯誤訊息：

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## 後續步驟

您已了解如何為連接至 SaaS 平台的 API 應用程式編寫程式碼及進行設定。如需如何處理 API 應用程式中的驗證作業之其他教學課程的連結，請參閱 [API 應用程式和行動應用程式的驗證 - 後續步驟](../app-service/app-service-authentication-overview.md#next-steps)。

[Azure preview portal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0114_2016-->