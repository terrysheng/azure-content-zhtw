<properties
	pageTitle="如何使用 Azure Active Directory 與 API 管理保護 Web API 後端"
	description="了解如何使用 Azure Active Directory 與 API 管理保護 Web API 後端。" 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="sdanie"/>

# 如何使用 Azure Active Directory 與 API 管理保護 Web API 後端

下列視訊示範如何使用 OAuth 2.0 通訊協定搭配 Azure Active Directory 與 API 管理建置 Web API 後端並加以保護。本文提供概觀以及視訊中步驟的其他資訊。這段 24 分鐘的視訊示範如何：

-	使用 AAD 建置 Web API 後端以及保護安全 - 從 1:30 開始
-	將 API 匯入 API 管理 - 從 7:10 開始
-	設定開發人員入口網站呼叫 API - 從 9:09 開始
-	設定桌面應用程式呼叫 API - 從 18:08 開始
-	設定 JWT 驗證原則來預先授權要求 - 從 20:47 開始

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## 建立 Azure AD 目錄

若要使用 Azure Active Directory 保護您的 Web API 後端，您必須先具備 AAD 租用戶。在這段視訊中，使用名為 **APIMDemo** 的租用戶。若要建立 AAD 租用戶，請登入 [Azure 傳統入口網站](https://manage.windowsazure.com)，按一下 [新增] -> [應用程式服務] -> [Active Directory] -> [目錄] -> [自訂建立]。

![Azure Active Directory][api-management-create-aad-menu]

此範例會一同建立名為 **APIMDemo** 的目錄與名為 **DemoAPIM.onmicrosoft.com** 的預設網域。在整段視訊中都會使用這個目錄。

![Azure Active Directory][api-management-create-aad]

## 建立由 Azure Active Directory 保護的 Web API 服務

在此步驟中，Web API 後端會使用 Visual Studio 2013 建立。這個步驟的視訊從 1:30 開始。若要在 Visual Studio 中建立 Web API 後端專案，請按一下 [檔案] -> [新增] -> [專案]，然後從 [Web] 範本清單中選擇 [ASP.NET Web 應用程式]。在這段視訊中，專案的名稱為 **APIMAADDemo**。按一下 [確定] 以建立專案。

![Visual Studio][api-management-new-web-app]

從 [選取範本清單] 按一下 [Web API] 來建立 Web API 專案。若要設定 Azure Directory 驗證，請按一下 [變更驗證]。

![新增專案][api-management-new-project]

按一下 [組織帳戶]，並指定您 AAD 租用戶的 [網域]。此範例中的網域是 **DemoAPIM.onmicrosoft.com**。您可以從目錄的 [網域] 索引標籤取得您目錄的網域。

![網域][api-management-aad-domains]

在 [變更驗證] 對話方塊中設定所需的設定，並按一下 [確定]。

![變更驗證][api-management-change-authentication]

按下 [確定] 之後，Visual Studio 將會嘗試使用您的 Azure AD 目錄註冊您的應用程式，Visual Studio 會提示您登入。使用您目錄的系統管理帳戶登入。

![登入 Visual Studio][api-management-sign-in-vidual-studio]

若要設定此專案為 Azure Web API，請核取 [在雲端中主控] 的方塊，然後按一下 [確定]。

![新增專案][api-management-new-project-cloud]

系統可能會提示您登入 Azure，接著您就可以設定 Web 應用程式。

![設定][api-management-configure-web-app]

此範例指定了一個名為 **APIMAADDemo** 的新 **App Service 方案**。

按一下 [確定] 設定 Web 應用程式和建立專案。

## 將程式碼加入 Web API 專案

視訊中的下一個步驟會將程式碼加入 Web API 專案。這個步驟從 4:35 開始。

此範例中的 Web API 使用模型和控制器實作基本的計算機服務。若要加入服務的模型，請以滑鼠右鍵按一下 [方案總管] 中的 [模型]，然後選擇 [新增] -> [類別]。將類別命名為 `CalcInput`，然後按一下 [新增]。

在 `CalcInput.cs` 檔案的開頭處新增下列 `using` 陳述式。

	using Newtonsoft.Json;

 將產生的類別取代為下列程式碼。

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

以滑鼠右鍵按一下 [方案總管] 中的 [控制器]，然後選擇 [新增] -> [控制器]。按一下 [Web API 2 控制器 - 空白]，然後按一下 [新增]。輸入 **CalcController** 做為控制器名稱，然後按一下 [新增]。

![新增控制器][api-management-add-controller]

在 `CalcController.cs` 檔案的開頭處新增下列 `using` 陳述式。

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

將產生的控制器類別取代為下列程式碼。此程式碼實作基本計算機 API 的 `Add`、`Subtract`、`Multiply` 以及 `Divide` 運算。

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
HttpResponseMessage response = Request.CreateResponse(); response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml"); return response; } }


按下 **F6** 來建置和驗證解決方案。

## 將專案發佈到 Azure

在此步驟中，Visual Studio 專案會發佈到 Azure。這個步驟的視訊從 5:45 開始。

若要將專案發佈到 Azure，請以滑鼠右鍵按一下 Visual Studio 中的 [APIMAADDemo] 專案，然後選擇 [發佈]。保留 [發佈 Web] 對話方塊中的預設值，然後按一下 [發佈]。

![Web 發佈][api-management-web-publish]

## 授與權限給 Azure AD 後端服務應用程式

設定和發佈您的 Web API 專案時，您的 Azure AD 目錄中會建立一個新的應用程式用於後端服務。這個步驟會授與權限給 Web API 後端，從視訊的 6:13 開始。

![應用程式][api-management-aad-backend-app]

按一下要設定必要權限的應用程式名稱。瀏覽到 [設定] 索引標籤，向下捲動到 [其他應用程式的權限] 區段。按一下 [Windows Azure Active Directory] 旁邊的 [應用程式權限] 下拉式清單，核取 [讀取目錄資料] 的方塊，然後按一下 [儲存]。

![新增權限][api-management-aad-add-permissions]

>[AZURE.NOTE]如果 [Windows Azure Active Directory] 並未列在 [其他應用程式的權限] 之下，請按一下 [加入應用程式] 從清單將其加入。

請記下 [應用程式識別碼 URI] 供後續為 API 管理開發人員入口網站設定 Azure AD 應用程式的步驟時使用。

![App 識別碼 URI][api-management-aad-sso-uri]

## 將 Web API 匯入 API 管理

API 是從 API 發佈者入口網站所設定，您可以透過 Azure 傳統入口網站存取此入口網站。若要存取發佈者入口網站，請在 Azure 傳統入口網站中，針對您的 API 管理服務按一下 [管理]。如果您尚未建立 API 管理服務執行個體，請參閱[管理您的第一個 API][] 教學課程中的[建立 API 管理服務執行個體][]。

![發行者入口網站][api-management-management-console]

您可以將運算[手動加入 API](api-management-howto-add-operations.md)，也可以匯入運算。在這段視訊中從 6:40 開始，運算會以 Swagger 格式匯入。

以下列內容建立名為 `calcapi.json` 的檔案，然後將檔案儲存到您的電腦。確定 `host` 屬性指向您的 Web API 後端。在此範例中使用 `"host": "apimaaddemo.azurewebsites.net"`。

{ "swagger": "2.0", "info": { "title": "Calculator", "description": "Arithmetics over HTTP!", "version": "1.0" }, "host": "apimaaddemo.azurewebsites.net", "basePath": "/api", "schemes": [ "http" ], "paths": { "/add?a={a}&b={b}": { "get": { "description": "Responds with a sum of two numbers.", "operationId": "Add two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand.Default value is <code>51</code>.", "required": true, "default": "51", "enum": [ "51" ] }, { "name": "b", "in": "query", "description": "Second operand.Default value is <code>49</code>.", "required": true, "default": "49", "enum": [ "49" ] } ], "responses": {} } }, "/sub?a={a}&b={b}": { "get": { "description": "Responds with a difference between two numbers.", "operationId": "Subtract two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand.Default value is <code>100</code>.", "required": true, "default": "100", "enum": [ "100" ] }, { "name": "b", "in": "query", "description": "Second operand.Default value is <code>50</code>.", "required": true, "default": "50", "enum": [ "50" ] } ], "responses": {} } }, "/div?a={a}&b={b}": { "get": { "description": "Responds with a quotient of two numbers.", "operationId": "Divide two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand.Default value is <code>100</code>.", "required": true, "default": "100", "enum": [ "100" ] }, { "name": "b", "in": "query", "description": "Second operand.Default value is <code>20</code>.", "required": true, "default": "20", "enum": [ "20" ] } ], "responses": {} } }, "/mul?a={a}&b={b}": { "get": { "description": "Responds with a product of two numbers.", "operationId": "Multiply two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand.Default value is <code>20</code>.", "required": true, "default": "20", "enum": [ "20" ] }, { "name": "b", "in": "query", "description": "Second operand.Default value is <code>5</code>.", "required": true, "default": "5", "enum": [ "5" ] } ], "responses": {} } } } }

若要匯入計算機 API，請從左邊的 [**API 管理**] 功能表中按一下 [**API**]，然後按一下 [**匯入 API**]。

![匯入 API 按鈕][api-management-import-api]

執行下列步驟以設定計算機 API。

1. 按一下 [從檔案]，瀏覽到您儲存的 `calculator.json` 檔案，然後按一下 [Swagger] 選項按鈕。
2. 在 [**Web API URL 尾碼**] 文字方塊中輸入 **calc**。
3. 按一下 [**產品 (選擇性)**] 方塊，然後選擇 [**入門**]。
4. 按一下 [**儲存**] 匯入 API。

![Add new API][api-management-import-new-api]

匯入 API 之後，API 的摘要頁面隨即會顯示在發行者入口網站中。

## 從開發人員入口網站無法成功呼叫 API

現在，API 已經匯入 API 管理，但是還無法從開發人員入口網站成功呼叫，因為使用 Azure AD 驗證保護後端服務。這會從視訊的 7:40 開始使用下列步驟示範。

從發佈者入口網站的右上角，按一下 [開發人員入口網站]。

![開發人員入口網站][api-management-developer-portal-menu]

按一下 [API]，然後按一下 [計算機] API。

![開發人員入口網站][api-management-dev-portal-apis]

按一下 [試試看]。

![試試看][api-management-dev-portal-try-it]

按一下 [傳送]，注意回應狀態為 [401 未授權]。

![傳送][api-management-dev-portal-send-401]

因為後端 API 受到 Azure Active Directory 的保護，所以要求未獲授權。在成功呼叫 API 之前，必須先使用 OAuth 2.0 設定開發人員入口網站以授權開發人員。下列各節將說明這個程序。

## 將開發人員入口網站註冊為 AAD 應用程式

使用 OAuth 2.0 設定開發人員入口網站來授權開發人員的第一個步驟是將開發人員入口網站註冊為 AAD 應用程式。這會從視訊的 8:27 開始示範。

這段視訊第一個步驟是瀏覽到 Azure AD 租用戶，在此範例中是 **APIMDemo**，然後瀏覽到 [應用程式] 索引標籤。

![新增應用程式][api-management-aad-new-application-devportal]

按一下 [**加入**] 按鈕來建立新 Azure Active Directory 應用程式，並選擇 [**加入我的組織正在開發的應用程式**]。

![新增應用程式][api-management-new-aad-application-menu]

選擇 [Web 應用程式和/或 Web API]、輸入名稱，然後按下一步箭頭。在此範例中使用 **APIMDeveloperPortal**。

![新增應用程式][api-management-aad-new-application-devportal-1]

對 [登入 URL] 輸入您 API 管理服務的 URL，並且附加 `/signin`。在此範例中使用 ****https://contoso5.portal.azure-api.net/signin**。

對 [應用程式識別碼 URL] 輸入您 API 管理服務的 URL，並且附加一些唯一字元。這些字元可以是任何想要的字元，在此範例中使用 ****https://contoso5.portal.azure-api.net/dp**。設定了想要的 [應用程式屬性] 之後，按一下核取記號以建立應用程式。

![新增應用程式][api-management-aad-new-application-devportal-2]

## 設定 API 管理 OAuth 2.0 授權伺服器

下一步是在 API 管理中設定 OAuth 2.0 授權伺服器。這個步驟會從視訊的 9:43 開始示範。

從左側的 [API 管理] 功能表按一下 [安全性]，然後依序按一下 [OAuth 2.0] 和 [新增授權伺服器]。

![新增授權伺服器][api-management-add-authorization-server]

在 [名稱] 和 [說明] 欄位中輸入名稱和選擇性的說明。這些欄位是用來在 API 管理服務執行個體中識別 OAuth 2.0 授權伺服器。在此範例中使用 **Authorization server demo**。稍後當您指定要用於 API 驗證的 OAuth 2.0 伺服器時，您要選取這個名稱。

為 [用戶端註冊頁面 URL] 輸入如 `http://localhost` 的預留位置值。[**用戶端註冊頁面 URL**] 指向使用者可用來建立和設定其對 OAuth 2.0 提供者之專屬帳戶的頁面，而提供者支援使用者帳戶管理。在此範例中，使用者沒有建立和設定自己的帳戶，因此使用預留位置。

![新增授權伺服器][api-management-add-authorization-server-1]

接著，指定 [授權端點 URL] 和 [權杖端點 URL]。

![授權伺服器][api-management-add-authorization-server-1a]

這些值可以從您為開發人員入口網站建立的 AAD 應用程式的 [App 端點] 頁面擷取。若要存取端點，請瀏覽到 AAD 應用程式的 [設定] 索引標籤，然後按一下 [檢視端點]。

![應用程式][api-management-aad-devportal-application]

![檢視端點][api-management-aad-view-endpoints]

複製 [OAuth 2.0 授權端點] 並貼到 [授權端點 URL] 文字方塊中。

![新增授權伺服器][api-management-add-authorization-server-2]

複製 [OAuth 2.0 權杖端點] 並貼到 [權杖端點 URL] 文字方塊中。

![新增授權伺服器][api-management-add-authorization-server-2a]

除了貼上權杖端點，請新增名為 **resource** 的其他主體參數，值則使用發佈 Visual Studio 專案時建立的後端服務的 AAD 應用程式的 [App 識別碼 URI]。

![App 識別碼 URI][api-management-aad-sso-uri]

接著，指定用戶端認證。這些是您想要存取的資源的認證，在此案例中是後端服務。

![用戶端認證][api-management-client-credentials]

若要取得 [用戶端識別碼]，請瀏覽到後端服務的 AAD 應用程式的 [設定] 索引標籤，然後複製 [用戶端識別碼]。

若要取得 [用戶端密碼]，請按一下 [金鑰] 區段中的 [選取持續時間] 下拉式清單，然後指定間隔。在此範例中使用 1 年。

![用戶端識別碼][api-management-aad-client-id]

按一下 [**儲存**] 以儲存組態並顯示金鑰。

>[AZURE.IMPORTANT]記下此金鑰。關閉 Azure Active Directory 組態視窗之後，即無法再次顯示金鑰。

將金鑰複製到剪貼簿、切換回發佈者入口網站、將金鑰貼入 [用戶端密碼] 文字方塊中，然後按一下 [儲存]。

![新增授權伺服器][api-management-add-authorization-server-3]

緊接在用戶端認證後面的是授權碼授與。複製此授權碼並切換回 Azure AD 開發人員入口網站應用程式設定頁面，將授權授與貼入 [回覆 URL] 欄位，然後再按一下 [儲存]。

![回覆 URL][api-management-aad-reply-url]

下一步是設定開發人員入口網站 AAD 應用程式的權限。按一下 [應用程式權限]，然後核取 [讀取目錄資料] 的方塊。按一下 [儲存] 以儲存這項變更，然後按一下 [加入應用程式]。

![新增權限][api-management-add-devportal-permissions]

按一下搜尋圖示，在 [開頭為] 方塊中輸入 **APIM**，選取 [APIMAADDemo]，然後按一下核取記號儲存。

![新增權限][api-management-aad-add-app-permissions]

按一下 [APIMAADDemo] 的 [委派權限]，核取 [存取 APIMAADDemo] 的方塊，然後按一下 [儲存]。這樣就允許開發人員入口網站應用程式存取後端服務。

![新增權限][api-management-aad-add-delegated-permissions]

## 啟用計算機 API 的 OAuth 2.0 使用者授權

現在已經設定好 OAuth 2.0 伺服器，您可以在安全性設定中為您的 API 指定此伺服器。這個步驟會從視訊的 14:30 開始示範。

按一下左側功能表中的 [API]，然後按一下 [計算機] 來檢視和設定其設定。

![計算機 API][api-management-calc-api]

瀏覽到 [安全性] 索引標籤，核取 [OAuth 2.0] 核取方塊，從 [授權伺服器] 下拉式清單選取想要的授權伺服器，然後按一下 [儲存]。

![計算機 API][api-management-enable-aad-calculator]

## 從開發人員入口網站成功呼叫計算機 API

現在已經在 API 上設定好 OAuth 2.0 授權，就可以從開發人員中心成功呼叫其運算。這個步驟會從視訊的 15:00 開始示範。

瀏覽回到開發人員入口網站中計算機服務的 [相加兩個整數] 運算，然後按一下 [試試看]。請注意，在 [授權]區段中的新項目對應到您剛才加入的授權伺服器。

![計算機 API][api-management-calc-authorization-server]

從授權下拉式清單選取 [授權碼]，然後輸入要使用的帳戶認證。如果您已經使用帳戶登入，就不會提示您。

![計算機 API][api-management-devportal-authorization-code]

按一下 [傳送]，注意 [回應狀態] 為 [200 OK]，以及回應內容中的運算結果。

![計算機 API][api-management-devportal-response]

## 設定桌面應用程式呼叫 API

視訊中的下一個程序從 16:30 開始，設定簡單的桌面應用程式呼叫 API。第一個步驟是在 Azure AD 中註冊桌面應用程式，並且讓它能夠存取目錄和後端服務。在 18:25 處示範桌面應用程式呼叫計算機 API 的運算。

## 設定 JWT 驗證原則來預先授權要求

最後的程序從視訊的 20:48 開始，示範如何使用[驗證 JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) 原則，藉由驗證每個傳入要求的存取權杖來預先授權要求。如果要求沒有經過「驗證 JWT」原則驗證，要求就會被 API 管理封鎖而不會傳入後端。

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

如需設定和使用此原則的另一個示範，請觀賞 [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)，向前快轉到 13:50。向前快轉到 15:00 可看到在原則編輯器中設定的原則，再到 18:50 可以看到一個示範，從開發人員入口網站 (使用和不使用必要的授權權杖) 呼叫運算。

## 後續步驟
-	查看更多有關 API 管理的[視訊](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
-	如需其他保護後端服務的方式，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)和[透過 VPN 或 ExpressRoute 連線](api-management-howto-setup-vpn)。

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[管理您的第一個 API]: api-management-get-started.md

<!-------HONumber=AcomDC_1210_2015--->