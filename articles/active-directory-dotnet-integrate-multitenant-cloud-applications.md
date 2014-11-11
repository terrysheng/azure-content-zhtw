<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# 整合多租用戶雲端應用程式與 Azure Active Directory

## <a name="introduction"></a>簡介

Azure Active Directory (Azure AD) 是以 REST 為基礎的新式服務，可為雲端應用程式提供身分識別管理和存取控制功能。Azure AD 可輕鬆地與雲端服務以及 Azure、Microsoft Office 365、Dynamics CRM Online 和 Windows Intune 整合。現有的內部部署 Active Directory 部署也可以充分運用 Azure AD。若要深入了解，請參閱 windowsazure.com 上的[身分識別頁面][身分識別頁面]。

本逐步解說適用於想要整合多租用戶應用程式與 Azure AD 的 .NET 開發人員。您將了解如何：

-   讓客戶使用 Azure AD 來註冊您的應用程式
-   使用 Azure AD 來啟用單一登入 (SSO)
-   使用 Azure AD Graph API 來查詢客戶的目錄資料

本逐步解說隨附的範例應用程式在[這裡下載][這裡下載]。範例不需要變更就可直接使用，但您可能需要變更 [Visual Studio 中的連接埠指派][Visual Studio 中的連接埠指派]來使用 https。請依連結中的指示進行，但要在 ApplicationHost.config 檔案的 bindings 區段中將連結通訊協定設為 "https"。以下步驟中的所有程式碼片段取自於範例。

> [WACOM.NOTE]
> 多租用戶目錄應用程式範例僅供說明用途。請勿於生產環境中使用此範例 (包括其協助程式庫類別)。

### 必要條件

本逐步解說需要有下列開發人員必備元件：

-   [Visual Studio 2012][Visual Studio 2012]
-   [WCF Data Services for OData][WCF Data Services for OData]

### 目錄

-   [簡介][簡介]
-   [第 1 部分：取得用來存取 Azure AD 的用戶端識別碼][第 1 部分：取得用來存取 Azure AD 的用戶端識別碼]
-   [第 2 部分：讓客戶使用 Azure AD 來註冊][第 2 部分：讓客戶使用 Azure AD 來註冊]
-   [第 3 部分：啟用單一登入][第 3 部分：啟用單一登入]
-   [第 4 部分：存取 Azure AD Graph][第 4 部分：存取 Azure AD Graph]
-   [第 5 部分：發行您的應用程式][第 5 部分：發行您的應用程式]
-   [摘要][摘要]

## <a name="getclientid"></a>第 1 部分：取得用來存取 Azure AD 的用戶端識別碼

本節說明建立 Microsoft 賣方儀表板帳戶之後，如何取得用戶端識別碼和用戶端密碼。用戶端識別碼是應用程式的唯一識別碼，而用戶端密碼是以用戶端識別碼提交要求時所需的相關密碼。兩者都是整合應用程式與 Azure AD 時的必要項目。

### 步驟 1：使用 Microsoft 賣方儀表板建立帳戶

若要開發並發行與 Azure AD 整合的應用程式，您必須註冊 [Microsoft 賣方儀表板][Microsoft 賣方儀表板]帳戶。將會提示您以公司或個人身分[建立帳戶個人檔案][建立帳戶個人檔案]。此個人檔案用於將應用程式發行到 Azure Marketplace 或其他市集，也是產生用戶端識別碼和用戶端密碼所需的項目。

新的帳戶會進入「帳戶等待核准」狀態。此狀態不妨礙您著手開發，您仍然可以建立用戶端識別碼和草稿應用程式清單。不過，帳戶本身必須先經過核准後，才能提交應用程式清單來等待核准。提交的應用程式清單必須經過核准後，才能在 Azure 市集讓客戶看見。

### 步驟 2：取得應用程式的用戶端識別碼

您需要用戶端識別碼和用戶端密碼，才能整合應用程式與 Azure AD。用戶端識別碼是應用程式的唯一識別碼，主要用來識別應用程式的單一登入，或驗證 Azure AD Graph 的呼叫。如需有關取得用戶端識別碼和用戶端密碼的詳細資訊，請參閱＜[在 Microsoft 賣方儀表板中建立用戶端識別碼和密碼][在 Microsoft 賣方儀表板中建立用戶端識別碼和密碼]＞。

> [WACOM.NOTE]
> 本逐步解說稍後需要用到您的用戶端識別碼和用戶端密碼，請先記錄下來。

若要產生用戶端識別碼和用戶端密碼，您需要在 Microsoft 賣方儀表板中輸入下列屬性：

**應用程式網站**：應用程式的主機名稱，例如 "contoso.com"。此屬性不可包含任何連接埠號碼。在開發期間，此屬性應該設為 "localhost"。

**應用程式重新導向 URL**：在使用者登入之後及組織授權您的應用程式之後，供 Azure AD 傳送回應的重新導向 URL，例如："<https://contoso.com/>"。開發期間，此屬性應該設為 "<https://localhost>:\<連接埠號碼\>"

### 步驟 3：設定應用程式來使用用戶端識別碼和用戶端密碼

此步驟需要有在賣方儀表板註冊時所產生的用戶端識別碼和用戶端密碼。用戶端識別碼用於 SSO，而用戶端識別碼和用戶端密碼兩者稍後會用來取得存取權杖，以呼叫 Azure AD Graph API。

範例應用程式已預先設定好使用 Azure AD，並從 config 載入用戶端識別碼和用戶端密碼。在範例應用程式的 **Web.config** 檔案中，進行下列變更：

1.  在 **appSettings** 節點中，將 "clientId" 和 "SymmetricKey" 的值改成您的用戶端識別碼、用戶端密碼和應用程式網域：

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  在 **system.identityModel** 的 **audienceUris** 節點中，在 "spn:" 後面插入用戶端識別碼：

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>第 2 部分：讓客戶使用 Azure AD 來註冊

本節說明如何讓客戶使用 Azure AD 來註冊您的應用程式。因為客戶可以使用與 Azure AD 整合的應用程式，租用戶系統管理員必須授權應用程式。此授權程序開始時是從應用程式發送同意要求給 Azure，而這會產生您的應用程式必須處理的回應。下列步驟說明如何產生同意要求和處理回應。

本節的步驟使用範例應用程式中的協助類別。這些類別都在範例的 *Microsoft.IdentityModel.WAAD.Preview* 程式庫中，可讓開發人員輕鬆地專心處理應用程式碼，而非身分識別和通訊協定細節。

### 步驟 1：要求同意您的應用程式

下列範例互動示範要求應用程式同意的過程：

1.  客戶在應用程式的網頁上按一下 [使用 Azure AD 來註冊] 連結。
2.  您將客戶重新導向至 Azure AD 授權頁面，並將應用程式的資訊附加至要求。
3.  客戶同意或拒絕您的應用程式。
4.  Azure AD 將客戶重新導向至指定的應用程式重新導向 URL。您在 Microsoft 賣方儀表板上產生用戶端識別碼和用戶端密碼時會指定此 URL。重新導向要求會指出同意要求的結果，包含其租用戶的相關資訊 (若表示同意的話)。

若要在上述步驟 2 中產生重新導向要求，您必須將查詢字串參數附加至下列 Azure AD 授權頁面 URL：*<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*

查詢字串參數的說明如下：

**ApplicationID**：(必要) 您在賣方儀表板中收到的 **ClientID** 值。

**RequestedPermissions**：(選用) 租用戶必須授與應用程式的權限。
在開發期間，這些權限用來測試未發行的應用程式。對於已發行的應用程式，則會忽略此參數。取而代之，將會使用應用程式清單中要求的權限。如需此清單的詳細資訊，請參閱第 5 部分。
此參數有三個可能的值：

**DirectoryReader**：授與權限來讀取目錄資料，例如使用者帳戶、群組及組織的相關資訊。啟用 SSO。

**UserAccountAdministrator**：授與權限來讀取和寫入資料，例如使用者、群組及組織的相關資訊。啟用 SSO。

**None**：啟用單一登入，但禁止存取目錄資料。

如果此參數未指定或指定不正確，則使用預設值 "None"。

下列是有效的同意要求 URL 範例：
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

在範例應用程式中，[註冊] 連結包含類似的同意要求 URL，如下所示：

![login][login]

> [WACOM.NOTE]
> 當您測試未發行的應用程式時，您會經歷與客戶相似的同意操作過程。不過，未發行的應用程式和已發行的應用程式各有不同的授權頁面外觀。已發行的應用程會顯示應用程式名稱、標誌和發行者詳細資料，而未發行的應用程式不會顯示這些資料。

### 步驟 2：處理同意回應

在客戶同意或拒絕您的應用程式之後，Azure AD 會傳送回應至應用程式的應用程式重新導向 URL。此回應包含下列查詢字串參數：

**TenantId**：Azure AD 租用戶的唯一 GUID，且此租用戶已授權您的應用程式。只有當客戶已授權您的應用程式時，才會指定此參數。

**Consent**：如果已授權應用程式，此值會設為 "Granted"，如果已拒絕要求，則會設為 "Denied"。

下列是同意要求的有效回應範例，指出已授權應用程式：
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

您的應用程式必須維護內容，使傳送至 Azure AD 授權頁面的要求繫結至回應 (且會拒絕無相關要求的任何回應)。

<div class="dev-callout"><strong>注意</strong><p>表示同意之後，Azure AD 經過一些時間才會佈建 SSO 和 Graph 存取。在佈建完成之前，每個組織中第一位註冊您應用程式的使用者可能會看到登入錯誤。</p></div>

當客戶表示同意您的應用程式之後，必須將新建立的租用戶儲存在應用程式中，並與同意回應所傳回的 TenantId 建立關聯。範例應用程式包含 *Microsoft.IdentityModel.WAAD.Preview.Consent* 命名空間中的 *HttpModule*，在所有成功的同意回應上，可將 TenantId 自動記錄到 customer/TenantId「資料存放區」。以下為相關的程式碼，由 *TrustedIssuers.Add* 方法將 TenantId 記錄到 customer/TenantId「資料存放區」：

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

您必須先取得 Azure AD 目錄租用戶，才能測試應用程式的同意要求/回應程式碼。

### 步驟 3：取得 Azure AD 租用戶來測試應用程式

若要測試應用程式與 Azure AD 整合的能力，您需要有 Azure AD 租用戶。如果您已有一個租用戶用來測試其他應用程式，則可重複使用。建議至少取得兩個租用戶，以確保應用程式可由多個租用戶來測試和使用。不建議在此用途上使用正式租用戶。[取得 Azure AD 租用戶][取得 Azure AD 租用戶]。

取得 Azure AD 租用戶之後，您可以按 **F5** 來建立和執行應用程式。此外，您也可以嘗試使用新的租用戶來註冊應用程式。

<div class="dev-callout"><strong>注意</strong><p>如果客戶註冊新的 Azure AD 租用戶，則需要經過一些時間，才會完整佈建該租用戶。在佈建完成之前，使用者可能會在同意頁面上看到錯誤。</p></div>

## <a name="enablesso"></a>第 3 部分：啟用單一登入

本節說明如何啟用單一登入 (SSO)。程序一開始是建構登入要求給 Azure AD，以便向應用程式驗證使用者，然後在登入回應中，驗證客戶是屬於已授權您應用程式的租用戶。登入要求需要從賣方儀表板取得您的用戶端識別碼，也需要客戶組織的租用戶識別碼。

登入要求是針對特定的目錄租用戶，必須包含 TenantID。從 Azure AD 目錄租用戶的網域名稱中可決定 TenantID。有兩種常見的方式可從登入的使用者取得此網域名稱：

-   如果應用程式的 URL 是 *<https://contoso.myapp.com>* 或 *<https://myapp.com/contoso.com>*，則 *[contoso][contoso]* 和 *[contoso.com][contoso]* 代表 Azure AD 網域名稱，*[myapp.com][contoso]* 代表應用程式的 URL。
-   應用程式可以提示使用者輸入電子郵件地址或 Azure AD 網域名稱。範例應用程式中採用此方法，使用者必須輸入 Azure AD 網域名稱，如下所示：

![login][login]

### 步驟 1：查閱租用戶識別碼

您可以使用客戶提供的 Azure AD 網域名稱，經由剖析 Azure AD 同盟中繼資料，以查閱租用戶識別碼。在範例應用程式中，此程序由 *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals* 類別的 *Domain2TenantId* 方法來處理。

下列步驟使用 contoso.com 網域名稱來示範此程序。

1.  取得 Azure AD 租用戶的 **FederationMetadata.xml** 檔案。例如：
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  在 **FederationMetadata.xml** 檔案中，找出 **Entity Descriptor** 項目。租用戶識別碼放在 **entityID** 屬性中，且緊接在 "<https://sts.windows.net>" 後面，如下所示：

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    在此例子中，TenantID 值為 **a7456b11-6fe2-4e5b-bc83-67508c201e4b**。

3.  您應該將網域及其相關的 TenantID 儲存在應用程式的 customer/TenantId「資料存放區」中。這兩個值可一起用於未來的登入要求，不必每次都要取得 **FederationMetadata.xml**。範例應用程式並未使用這種最佳化設計。

### 步驟 2：產生登入要求

當客戶登入您的應用程式時，例如按一下登入按鈕，就必須使用客戶的租用戶識別碼和應用程式的用戶端識別碼來產生登入要求。在範例應用程式中，由 *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils* 類別的 *GenerateSignInMessage* 方法產生此要求。此方法可驗證客戶的 TenantID 代表已授權您應用程式的組織，還會產生登入按鈕的目的地 URL，如下所示：

![login][login]

按一下按鈕會將使用者的瀏覽器導向 Azure AD 登入頁面。登入之後，Azure AD 會將登入回應傳回給應用程式。

### 步驟 3：在登入回應中驗證傳入權杖的簽發者

當客戶登入您的應用程式時，您需要驗證其租用戶已授權您的應用程式。他們的登入回應包含權杖，而權杖包含可由您的應用程式來檢查的屬性和宣告。

若要執行此驗證，您必須從權杖的 Issuer 屬性中取得 TenantID，並確定它存在於 customer/TenantId「資料存放區」。在範例應用程式中，此驗證的作法是建立自訂權杖處理常式類別，而此類別延伸 Windows Identity Foundation 的 *Saml2SecurityTokenHandler*。自訂權杖處理常式會驗證傳入的安全性權杖，並提供其宣告和屬性給應用程式，如此就可驗證 TenantID。以下顯示此類別的程式碼片段。

在範例應用程式中，原始程式碼位於 *Microsoft.IdentityModel.WAAD.Preview.WebSSO* 命名空間下。權杖處理常式也使用 *Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers* 類別的 Contains 方法，用來驗證 TenantID 保存在 customer/TenantId「資料存放區」。

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

驗證權杖之後，使用者就登入應用程式。請執行應用程式，並使用您稍早建立的同意租用戶中的 Azure AD 帳戶來嘗試登入。

## <a name="accessgraph"></a>第 4 部分：存取 Azure AD Graph

本節說明如何取得存取權杖，並呼叫 Azure AD Graph API 來存取租用戶的目錄資料。例如，雖然登入期間取得的權杖包含使用者資訊，例如名稱和電子郵件地址，但您的應用程式可能還需要其他資訊，例如使用者管理員的群組成員資格或名稱。可利用 Graph API 從租用戶的目錄中取得此資訊。如需 Graph API 的詳細資訊，請參閱[本主題][本主題]。

您的應用程式必須先自我驗證並取得存取權杖，才能呼叫 Azure AD Graph。需要以戶端識別碼和用戶端密碼來驗證應用程式，以取得存取權杖。下列步驟說明如何：

1.  使用產生的 Proxy 類別來呼叫 Azure AD Graph
2.  使用 Azure Authentication Library (AAL) 來取得存取權杖
3.  呼叫 Azure AD Graph 來取得租用戶使用者清單

<div class="dev-callout"><strong>注意</strong><p>範例應用程式協助程式庫 Microsoft.IdentityModel.WAAD.Preview 已包含一個自動產生的 Proxy 類別 (將一個稱為 GraphicService 的服務參考加入至 https://graph.windows.net/your-domain-name 而建立)。應用程式會使用此 Proxy 類別來呼叫 Azure AD Graph 服務。</p></div>

### 步驟 1：使用 Proxy 類別來呼叫 Azure AD Graph

在此步驟中，我們將使用範例應用程式來說明如何：

1.  建立租用戶專用的 Azure AD Graph 端點
2.  使用端點來具現化 Proxy 以呼叫 Graph
3.  將授權標頭加入至要求並取得權杖。

在範例應用程式中，由 *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* 類別的 GraphInterface 方法來處理這些 API 呼叫，如下列程式碼所示。

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### 步驟 2：使用 Azure Authentication Library 來取得存取權杖

範例應用程式使用 Azure Authentication Library (AAL) 取得權杖，以存取 Graph API。由 *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* 類別中的 *GetAuthorizationHeader* 方法來管理權杖取得過程，如下所示。

<div class="dev-callout"><strong>注意</strong><p>AAL 以 NuGet 套件形式提供，可安裝在 Visual Studio 內。</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

如以上程式碼所示：下列資訊可用來取得取得權杖：

1.  The application's information (ClientID, ServicePrincipalKey and AppHostname)
2.  目標資訊，就是 Graph，即上述的 ServiceRealm
3.  您稍早取得的 TenantDomainName

### 步驟 3：呼叫 Azure AD Graph 來取得使用者清單

*Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* 類別中的下列方法會使用稍早產生的 *DataService* Proxy，以取得租用戶的完整使用者清單。

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

**HomeController.cs** 檔案會呼叫此方法，將使用者清單顯示在 Web 應用程式的 [使用者] 索引標籤上。

## <a name="publish"></a>第 5 部分：發行您的應用程式

完整測試您的應用程式之後，您可以建立應用程式清單，並將應用程式清單發行到 Azure 市集。這些步驟是在 Microsoft 賣方儀表板上執行。

<div class="dev-callout"><strong>注意</strong><p>您的應用程式負責管理客戶的任何計費關係。Azure 市集只提供應用程式網站及相關資訊的連結。</p></div>

### 步驟 1：建立應用程式資訊清單和應用程式清單

在建立應用程式清單之前，您必須為應用程式的生產版本產生新的用戶端識別碼和用戶端密碼。在本逐步解說的第 1 部分，您已針對應用程式的測試版本產生用戶端識別碼和用戶端密碼。請重複這些步驟，設定應用程式來使用新的值，並確保您設定產生應用程式網域和應用程式重新導向 URL。

接下來，您必須建立應用程式資訊清單，列出應用程式要求客戶同意時所需的權限。此資訊清單是以 XSD 檔案所規定的 XML 格式來撰寫。資訊清單必須隨附於您建立的應用程式清單一起上傳。

有三種不同的權限層級，如本逐步解說的第 1 部分所述：

**DirectoryReader**：授與權限來讀取目錄資料，例如使用者帳戶、群組及組織的相關資訊。啟用 SSO。

**UserAccountAdministrator**：授與權限來讀取和寫入資料，例如使用者、群組及組織的相關資訊。啟用 SSO。

**None**：啟用單一登入，但禁止存取目錄資料。

以下是兩個應用程式資訊清單範例。第一個範例示範限用 SSO 應用程式的權限，第二個範例示範唯讀應用程式的權限：

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="zh-TW" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

上述範例中的 *Policy* 屬性說明所要求的應用程式權限類型。目前，僅支援 "AppOnly" 權限屬性。此權限類型指出應用程式只存取其本身的 Directory。

選用的 *Reason* 元素可指定 (在多種文化特性中) 您要求必要權限層級的理由。此文字會顯示在同意頁面上，協助客戶決定要核准或拒絕您的應用程式。

您可以使用新的用戶端識別碼和應用程式資訊清單，依照＜[在 Microsoft 賣方儀表板中新增應用程式][在 Microsoft 賣方儀表板中新增應用程式]＞中的指示來建立應用程式清單。在建立應用程式清單時，請記得選取 Azure AD 應用程式類型。當應用程式清單建立完成之後，按一下 [提交] 將應用程式發行至 Azure 市集。在發行完成之前，需要等待您的應用程式通過核准。

<div class="dev-callout"><strong>注意</strong><p>如果提示您「加入稅金和付款資訊」，請略過此步驟，因為您是直接將應用程式銷售給客戶，而不是透過 Microsoft。</p></div>

### 步驟 2：完成測試並公開發行您的應用程式

當您的應用程式清單經過核准之後，您應該重新對應用程式進行端對端測試。例如，請確定已使用生產 ClientID 和用戶端密碼來更新您的應用程式。最後一次瀏覽測試檢查清單，確定同意頁面現在顯示您已加入應用程式清單中的資訊。

## <a name="summary"></a>摘要

在本逐步解說中，您學到如何整合多租用戶應用程式與 Azure AD。過程包含三個步驟：

-   讓客戶使用 Azure AD 來註冊您的應用程式
-   使用 Azure AD 來啟用單一登入 (SSO)
-   使用 Azure AD Graph API 來查詢客戶的目錄資料

與 Azure AD 整合可讓客戶使用他們已維護的身分識別管理系統來註冊並登入您的應用程式，如此可減少或不需要對您的應用程式進行身分識別管理工作。此功能可讓您的客戶更順暢地使用您的應用程式，而不浪費時間來處理管理工作。

  [身分識別頁面]: http://www.windowsazure.com/zh-TW/home/features/identity/
  [這裡下載]: http://go.microsoft.com/fwlink/?LinkId=271213
  [Visual Studio 中的連接埠指派]: http://msdn.microsoft.com/zh-TW/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [WCF Data Services for OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [簡介]: #introduction
  [第 1 部分：取得用來存取 Azure AD 的用戶端識別碼]: #getclientid
  [第 2 部分：讓客戶使用 Azure AD 來註冊]: #enablesignup
  [第 3 部分：啟用單一登入]: #enablesso
  [第 4 部分：存取 Azure AD Graph]: #accessgraph
  [第 5 部分：發行您的應用程式]: #publish
  [摘要]: #summary
  [Microsoft 賣方儀表板]: https://sellerdashboard.microsoft.com/
  [建立帳戶個人檔案]: http://msdn.microsoft.com/zh-TW/library/jj552460.aspx
  [在 Microsoft 賣方儀表板中建立用戶端識別碼和密碼]: http://msdn.microsoft.com/zh-TW/library/jj552461.aspx
  [login]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [取得 Azure AD 租用戶]: http://g.microsoftonline.com/0AX00en/5
  [contoso]: https://contoso.myapp.com
  [本主題]: http://msdn.microsoft.com/zh-TW/library/windowsazure/hh974476.aspx
  [在 Microsoft 賣方儀表板中新增應用程式]: http://msdn.microsoft.com/zh-TW/library/jj552465.aspx
