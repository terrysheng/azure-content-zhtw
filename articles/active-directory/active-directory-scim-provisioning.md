<properties
	pageTitle="使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建 | Microsoft Azure"
	description="Azure Active Directory 會利用 SCIM 通訊協定規格中定義的介面，自動佈建使用者和群組到 Web 服務前端的任何應用程式或身分識別存放區"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="asmalser-msft"/>

#使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建

##概觀

Azure Active Directory 會利用 [SCIM 2.0 通訊協定規格](https://tools.ietf.org/html/draft-ietf-scim-api-19)中定義的介面，自動佈建使用者和群組到 Web 服務前端的任何應用程式或身分識別存放區。Azure Active Directory 可以傳送要求給此 Web 服務來建立、修改和刪除指派的使用者與群組，Web 服務接著可將這些要求轉譯為目標身分識別存放區的作業。

![][1] *圖：透過 Web 服務從 Azure Active Directory 佈建至身分識別存放區*

這項功能可搭配 Azure AD 中的[「自備應用程式」](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)功能，以為提供 SCIM Web 服務或位於該服務後端的應用程式啟用單一登入和自動使用者佈建。

Azure Active Directory 中的 SCIM 有兩個使用案例：

* **將使用者與群組佈建至支援 SCIM 的應用程式** - 應用程式若支援 SCIM 2.0，而且能夠接受來自 Azure AD 的 OAuth 持有人權杖，將可直接與 Azure AD 搭配運作。

* **為支援其他 API 型佈建的應用程式建置您自己的佈建解決方案** - 對於非 SCIM 應用程式，您可以建立能夠在 Azure AD 的 SCIM 端點與應用程式為使用者佈建支援的任何 API 之間進行轉譯的 SCIM 端點。為了促進 SCIM 端點的開發，我們連同程式碼範例提供了 CLI 程式庫，為您說明如何提供 SCIM 端點及轉譯 SCIM 訊息。

##將使用者與群組佈建至支援 SCIM 的應用程式

Azure Active Directory 可以設定為將已指派的使用者和群組佈建至實作 [System for Cross-domain Identity Management 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) Web 服務、並接受以 OAuth 持有人權杖進行驗證的應用程式。在 SCIM 2.0 規格中，應用程式必須符合下列需求：

* 支援根據 SCIM 通訊協定 3.3 小節建立使用者和 (或) 群組的作業。  

* 支援根據 SCIM 通訊協定 3.5.2 小節修改具有修補要求的使用者和 (或) 群組的作業。

* 支援根據 SCIM 通訊協定 3.4.1 小節擷取已知資源的作業。

*  支援根據 SCIM 通訊協定 3.4.2 小節查詢使用者和 (或) 群組的作業。依預設會依 externalId 來查詢使用者，並依 displayName 查詢群組。

* 支援根據 SCIM 通訊協定 3.4.2 小節，依 ID 和管理員查詢使用者的作業。

* 支援根據 SCIM 通訊協定 3.4.2 小節，依 ID 和成員查詢群組的作業。

* 接受根據 SCIM 通訊協定 2.1 小節以 OAuth 持有人權杖進行授權的作法。

* 支援以 Azure AD 作為 OAuth 權杖的身分識別提供者 (外部身分識別提供者的支援將於近期推出)

您應洽詢應用程式提供者，或參閱應用程式提供者文件中的相關陳述，以了解是否符合這些需求。
 
###開始使用

支援上述 SCIM 設定檔的應用程式，可使用 Azure AD 應用程式庫中的「自訂」應用程式功能連接到 Azure Active Directory。連接之後，Azure AD 會每隔 5 分鐘執行一次同步處理程序，此程序會為指派的使用者和群組查詢應用程式的 SCIM 端點，並根據指派詳細資料加以建立或修改。

**若要連接支援 SCIM 的應用程式：**

1.	在網頁瀏覽器中，在 https://manage.windowsazure.com 啟動 Azure 管理入口網站。
2.	瀏覽至 [Active Directory] > [目錄] > [您的目錄] > [應用程式]，然後選取 [新增] > [從資源庫中新增應用程式]。
3.	選取左側的 [自訂] 索引標籤，輸入應用程式的名稱，然後按一下核取記號圖示以建立應用程式物件。

![][2]

4.	在產生的畫面中，選取第二個 [設定帳戶佈建] 按鈕。
5.	在對話方塊中，輸入應用程式SCIM 端點的 URL。  
6.	按 [下一步]，然後按一下 [開始測試] 按鈕，讓 Azure Active Directory 嘗試連接到 SCIM 端點。如果嘗試失敗，就會顯示診斷資訊。  
7.	如果嘗試連接到應用程式成功，請在其餘畫面上按 [下一步]，然後按一下 [完成] 以結束對話方塊。
8.	在產生的畫面中，選取第三個 [指派帳戶] 按鈕。在產生的使用者和群組區段中，指派您想要佈建到應用程式的使用者或群組。
9.	指派使用者和群組之後，按一下畫面頂端附近的 [設定] 索引標籤。
10.	在 [帳戶佈建] 下，確認狀態設為開啟。 
11.	在 [工具] 下，按一下 [重新啟動帳戶佈建] 以開始進行佈建程序。

請注意，可能需要 5-10 分鐘的時間，佈建程序才會開始將要求傳送至 SCIM 端點。應用程式的 [儀表板] 索引標籤上提供的連接嘗試的摘要和佈建活動的報表和佈建的任何錯誤就可以從下載目錄的 [報表] 索引標籤。

##為任何應用程式建置您自己的佈建解決方案

建立可與 Azure Active Directory 互動的 SCIM Web 服務後，您即可為提供 REST 或 SOAP 使用者佈建 API 的絕大多數應用程式啟用單一登入和自動使用者佈建。

其運作方式如下：

1.	Azure AD 提供名為 [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/) 的通用語言基礎結構程式庫。系統整合業者和開發人員可以使用此程式庫來建立及部署能夠將 Azure AD 連線到任何應用程式的身分識別存放區的 SCIM 式 Web 服務端點。
2.	對應會在 Web 服務中實作，以將標準的使用者結構描述對應到使用者結構描述和應用程式所需的通訊協定。
3.	端點 URL 會在 Azure AD 中註冊，作為應用程式資源庫中自訂應用程式的一部分。
4.	使用者和群組會在 Azure AD 中指派給此應用程式。指派時，它們會被放入佇列，以同步處理至目標應用程式。同步處理程序會每隔 5 分鐘處理佇列的執行。

###程式碼範例

為了讓這個程序更簡單，我們提供了一組[程式碼範例](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)，該範例會建立 SCIM Web 服務端點並示範自動佈建。其中一個範例是維護代表使用者和群組、具有逗號分隔值資料列檔案的提供者。另一個是在 Amazon Web 服務身分識別與存取管理服務上運作的提供者。


**先決條件** * Visual Studio 2013 或更新版本 * [Azure SDK for .NET](https://azure.microsoft.com/zh-TW/downloads/) * 支援將 ASP.NET Framework 4.5 作為 SCIM 端點的 Windows 電腦。這台電腦必須能夠從雲端存取 * [具有 Azure AD Premium 試用版或授權版的 Azure 訂用帳戶](https://azure.microsoft.com/zh-TW/services/active-directory/) * Amazon AWS 範例需要來自於 [AWS Toolkit for Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html) 的程式庫。請參閱範例隨附的讀我檔案，以取得其他詳細資料

###開始使用

實作可以接受來自 Azure AD 的佈建要求的 SCIM 端點的最簡單的方式是建置和部署會將佈建的使用者輸出至以逗號分隔值 (CSV) 檔案的程式碼範例。

**若要建立範例 SCIM 端點：**

1.	在 [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) 下載程式碼範例封裝
2.	將封裝解壓縮，並將它放在 Windows 電腦上的位置，例如 C:\\AzureAD-BYOA-Provisioning-Samples。
3.	在此資料夾中，於 Visual Studio 中啟動 FileProvisioningAgent 方案。
4.	選取 [工具] > [程式庫封裝管理員] > [封裝管理員主控台]，並執行以下命令，讓 FileProvisioningAgent 專案解析方案參考：

    Install-Package Microsoft.SystemForCrossDomainIdentityManagement Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory Install-Package Microsoft.Owin.Diagnostics Install-Package Microsoft.Owin.Host.SystemWeb

5.	建置 FileProvisioningAgent 專案。
6.	在 Windows 中 (以系統管理員身分) 啟動命令提示字元應用程式，並使用 **cd** 命令來將目錄變更為您的 **\\AzureAD-BYOA-Provisioning-Samples\\ProvisioningAgent\\bin\\Debug** 資料夾。
7.	執行以下命令，將 <ip-address> 取代為 Windows 電腦的 IP 或網域名稱。

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.	在 Windows 中，於 [Windows 設定] > [網路和網際網路設定] 下，選取 [Windows 防火牆] > [進階設定]，並建立允許對連接埠 9000 進行輸入存取的 [輸入規則]。
9.	如果 Windows 電腦位於路由器背後，必須將路由器設定為在公開到網際網路的連接埠 9000 和 Windows 電腦上的連接埠 9000 之間執行網路存取轉譯。為了讓 Azure AD 能夠在雲端中存取這個端點，這是必要的。


**若要在 Azure AD 中註冊範例 SCIM 端點：**

1.	在網頁瀏覽器中，在 https://manage.windowsazure.com 啟動 Azure 管理入口網站。
2.	瀏覽至 [Active Directory] > [目錄] > [您的目錄] > [應用程式]，然後選取 [新增] > [從資源庫中新增應用程式]。
3.	選取左側的 [自訂] 索引標籤，輸入「SCIM 測試應用程式」之類的名稱，並按一下核取記號圖示來建立應用程式物件。請注意，建立的應用程式物件要代表您要佈建和實作登一登入的目標應用程式，而不只是 SCIM 端點。

![][2]

4.	在產生的畫面中，選取第二個 [設定帳戶佈建] 按鈕。
5.	在對話方塊中，輸入網際網路公開的 URL 和 SCIM 端點的連接埠。這看起來應該像 http://testmachine.contoso.com:9000 或 http://<ip-address>:9000/，其中 <ip-address> 是網際網路公開 IP 位址。  
6.	按 [下一步]，然後按一下 [開始測試] 按鈕，讓 Azure Active Directory 嘗試連接到 SCIM 端點。如果嘗試失敗，就會顯示診斷資訊。  
7.	如果嘗試連接到您的 Web 服務成功，請在其餘畫面上按 [下一步]，然後按一下 [完成] 以結束對話方塊。
8.	在產生的畫面中，選取第三個 [指派帳戶] 按鈕。在產生的使用者和群組區段中，指派您想要佈建到應用程式的使用者或群組。
9.	指派使用者和群組之後，按一下畫面頂端附近的 [設定] 索引標籤。
10.	在 [帳戶佈建] 下，確認狀態設為開啟。 
11.	在 [工具] 下，按一下 [重新啟動帳戶佈建] 以開始進行佈建程序。

請注意，可能需要 5-10 分鐘的時間，佈建程序才會開始將要求傳送至 SCIM 端點。應用程式的 [儀表板] 索引標籤上提供的連接嘗試的摘要和佈建活動的報表和佈建的任何錯誤就可以從下載目錄的 [報表] 索引標籤。

確認此範例的最後一個步驟是開啟 Windows 電腦上 \\AzureAD-BYOA-Provisioning-Samples\\ProvisioningAgent\\bin\\Debug 資料夾中的 TargetFile.csv 檔案。一旦執行佈建程序，此檔案會顯示所有指派和佈建的使用者和群組的詳細資料。

###開發程式庫

若要開發自己符合 SCIM 規格的 Web 服務，請先熟悉下列 Microsoft 所提供、有助於加速開發程序的程式庫：

**1：**提供通用語言基礎結構程式庫以搭配基於該基礎結構的語言使用，例如 C#。這些程式庫的其中一個 [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/) 會宣告 Microsoft.SystemForCrossDomainIdentityManagement.IProvider 介面，如下圖所示。使用程式庫的開發人員會對某個類別實作該介面，一般稱為提供者。程式庫可讓開發人員輕鬆部署符合 SCIM 規格的 Web 服務，無論是託管於網際網路資訊服務，或任何可執行的通用語言基礎結構組件。對該 Web 服務的要求將會轉譯成對提供者的方法呼叫，該呼叫會由開發人員以程式方式設計，以對某些身分識別存放區進行操作。

![][3]

**2：**提供 [ExpressRoute 處理常式](http://expressjs.com/guide/routing.html) 用於剖析 node.js 要求物件，以代表對 node.js Web 服務的呼叫 (如 SCIM 規格所定義)。

###建置自訂 SCIM 端點

使用上述的程式庫，使用這些程式庫的開發人員可以將其服務託管在任何可執行的通用語言基礎結構組件內，或在網際網路資訊服務內。以下範例程式碼用於將服務託管在可執行組件內，於位址 http://localhost:9000：

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

請務必注意，這項服務必須具有 HTTP 位址，而其伺服器驗證憑證的根憑證授權單位是下列其中一項：

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

伺服器驗證憑證可以使用網路殼層公用程式繫結到 Windows 主機上的連接埠，就像這樣：

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
此處，對 certhash 引數提供的值為憑證指紋，而對 appid 引數提供的值為任意的全域唯一識別碼。

若要將服務託管在網際網路資訊服務內，開發人員會建置通用語言基礎結構程式碼程式庫組件，並在組件的預設命名空間中使用名為 Startup 的類別。以下是這種類別的範例：

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###處理端點驗證

來自 Azure Active Directory 的要求包括 OAuth 2.0 持有人權杖。接收要求的任何服務應該代表預期的 Azure Active Directory 租用戶，將簽發者驗證為 Azure Active Directory，以存取 Azure Active Directory 的圖形 Web 服務。在 Token 中，簽發者是由 iss 宣告，例如："iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"。在此範例中，宣告值的基礎位址 https://sts.windows.net 會將 Azure Active Directory 識別為簽發者，而相對位址區段 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 是唯一識別碼，代表核發權杖時的 Azure Active Directory 租用戶。如果發出的權杖要用於存取 Azure Active Directory 的圖形 Web 服務，則該服務的識別項 00000002-0000-0000-c000-000000000000，應該位於權杖的 aud 宣告中的值。

開發人員若使用 Microsoft 所提供的通用語言基礎結構程式庫來建置 SCIM 服務，可以依照下列步驟使用 Microsoft.Owin.Security.ActiveDirectory 封裝以驗證來自 Azure Active Directory 的要求：

**1：**在提供者中，透過每次服務啟動時讓服務傳回要呼叫的方法來實作 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 屬性：

    public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2：**加入下列程式碼至該方法，代表指定的租用戶，將對任何服務端點的任何要求驗證為承載 Azure Active Directory 所發出的權杖，以存取 Azure Active Directory 的圖形 Web 服務：

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##使用者和群組結構描述

Azure Active Directory 可以佈建兩種類型的資源至 SCIM Web 服務。這些類型的資源是使用者和群組。

使用者資源是由結構描述識別碼 urn:ietf:params:scim:schemas:extension:enterprise:2.0:User 識別，其隨附於此通訊協定規格：http://tools.ietf.org/html/draft-ietf-scim-core-schema。以下的表 1 提供相對於urn:ietf:params:scim:schemas:extension:enterprise:2.0:User 資源的屬性，Azure Active Directory 中使用者屬性的預設對應。

群組資源是由結構描述識別碼 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group 所識別。以下的表 2 顯示 Azure Active Directory 中群組屬性與 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group 資源屬性的預設對應。

###表 1：預設使用者屬性對應

| Azure Active Directory 使用者 | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | 作用中 |
| displayName | displayName |
| Facsimile-TelephoneNumber | phoneNumbers[type eq "fax"].value |
| givenName | name.givenName |
| jobTitle | title |
| mail | emails[type eq "work"].value |
| mailNickname | externalId |
| manager | manager |
| mobile | phoneNumbers[type eq "mobile"].value |
| objectId | id |
| postalCode | addresses[type eq "work"].postalCode |
| proxy-Addresses | emails[type eq "other"].Value |
| physical-Delivery-OfficeName | addresses[type eq "other"].Formatted |
| streetAddress | addresses[type eq "work"].streetAddress |
| surname | name.familyName |
| telephone-Number | phoneNumbers[type eq "work"].value |
| user-PrincipalName | userName |


###表 2：預設群組屬性對應

| Azure Active Directory 群組 | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | externalId |
| mail | emails[type eq "work"].value |
| mailNickname | displayName |
| members | members |
| objectId | id |
| proxyAddresses | emails[type eq "other"].Value |


##使用者佈建和取消佈建

下圖顯示 Azure Active Directory 會傳送至 SCIM 服務的訊息，以管理使用者在其他身分識別存放區中的生命週期。圖表也會示範使用 Microsoft 提供、用於建置此類服務的通用語言基礎結構程式庫所實作之 SCIM 服務如何將這些要求轉譯為對提供者的方法呼叫。

![][4] 圖：使用者佈建和取消佈建順序

**1：**Azure Active Directory 會查詢服務是否有使用者具有符合 Azure Active Directory 中使用者的 mailNickname 屬性值的 externalId 屬性值。查詢會以類似於此的超文字傳輸通訊協定要求表示，其中，jyoung 是 Azure Active Directory 中使用者的 mailNickname 範例：

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Query 方法的呼叫。以下是該方法的簽章：

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

以下是 Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 介面的定義：

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
	{
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
	  { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

在查詢具有給定值 externalId 屬性的使用者的前述範例中，傳遞至 Query 方法的引數值將會是如下：

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

**2：**如果對具有符合 Azure Active Directory 使用者中 mailNickname 屬性值的 externalId 屬性值的使用者服務查詢的回應未傳回任何使用者，則 Azure Active Directory 會要求服務佈建能與 Azure Active Directory 中的使用者相對應的使用者。以下是這類要求的範例：

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫，會將要求轉譯為對服務提供者的 Create 方法的呼叫。Create 方法具有此簽章：

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

如果是佈建使用者的要求，資源引數的值將會是 Microsoft.SystemForCrossDomainIdentityManagement 的執行個體。Microsoft.SystemForCrossDomainIdentityManagement.Schemas 程式庫中定義的 Core2EnterpriseUser 類別。如果佈建使用者的要求成功，則方法的實作應該會傳回 Microsoft.SystemForCrossDomainIdentityManagement 的執行個體。Core2EnterpriseUser 類別，且其識別碼屬性值設定為新佈建使用者的唯一識別碼。

**3：**為了更新已知存在 SCIM 前端的身分識別存放區中的使用者，Azure Active Directory 會藉由以類似此的要求向服務要求該使用者的目前狀態繼續：

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Retrieve 方法的呼叫。以下是 Retrieve 方法的簽章：

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

如果是要擷取使用者目前狀態的要求的前述範例，提供做為參數引數值的物件將有的些屬性值如下所示：

* 識別碼："54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4：**若要更新參考屬性，則 Azure Active Directory 會查詢服務以判斷服務前端的身分識別存放區中的參考屬性的目前值是否已符合 Azure Active Directory 中該屬性的值。如果是使用者，以這種方式可查詢目前值的唯一屬性會是管理員屬性。要判斷特定使用者物件的管理員屬性目前是否具有某個值之要求的範例如下：

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

屬性查詢參數 id 的值，表示如果滿足提供做為篩選查詢參數值的運算式的使用者物件存在，則服務應該以 urn:ietf:params:scim:schemas:core:2.0:User 或 urn:ietf:params:scim:schemas:extension:enterprise:2.0:User 資源回應，包僅括該資源 id 屬性的值。當然，要求者知道 id 屬性的值 — 它包含在篩選查詢參數的值中；要求它的目的只是實際要求滿足篩選運算式做為任何這類物件是否存在指示之資源的最小表示。

如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Query 方法的呼叫。提供物件的屬性值做為參數引數的值，將如下所示：

* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "id"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

在此處，索引 x 的值可能會是 0，而索引 y 的值可能是 1，或 x 值可能是 1 而 y 的值可能是 0，視篩選查詢參數運算式的順序而定。

**5：**以下來自 Azure Active Directory 對 SCIM 服務要求更新使用者的範例：

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

用於實作 SCIM 服務的 Microsoft 通用語言基礎結構程式庫，會將要求轉譯為對服務提供者的 Update 方法的呼叫。以下是該方法的簽章：

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



如果是要更新使用者的要求的前述範例，提供做為修補程式引數值的物件將有這些屬性值：

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

**6：**若要從 SCIM 服務前端的身分識別存放區解除佈建使用者，Azure Active Directory 將會傳送如下的要求：

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
	
如果是使用 Microsoft 所提供、用於實作 SCIM 服務的通用語言基礎結構程式庫建置服務，會將要求轉譯為對服務提供者的 Delete 方法的呼叫。該方法具有此簽章：

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
提供做為 resourceIdentifier 引數值的物件，在要解除佈建使用者之要求的前述範例中，將具有這些屬性值：

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

##群組佈建和取消佈建

下圖顯示 Azure Active Directory 會傳送至 SCIM 服務的訊息，以管理群組在其他身分識別存放區中的生命週期。這些訊息與使用者的訊息不同，有下列三個方面：

* 群組資源的結構描述會被識別為 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group。  
* 擷取群組的要求會規定將成員屬性從回應要求中提供的任何資源中排除。  
* 要求判斷參考屬性是否具有特定值，將會是有關成員屬性的要求。  

![][5] 圖：使用者佈建和取消佈建順序

	
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG

<!---HONumber=Nov15_HO4-->