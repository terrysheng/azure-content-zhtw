<properties
	pageTitle="如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器"
	description="涵蓋如何執行無訊息安裝 Azure AD 應用程式 Proxy 連接器，為內部部署的應用程式提供安全的遠端存取。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="steven.powell"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="rkarlin"/>

# 如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器

您想要能傳送安裝指令碼至多部伺服器，或傳送至未啟用使用者介面的 Windows Server。本主題說明如何建立 Windows PowerShell 指令碼以啟用自動安裝，同時安裝並註冊您的 Azure AD 應用程式 Proxy 連接器。

## 啟用存取
應用程式 Proxy 的運作方式是透過在網路內部安裝一個稱為連接器的精簡型 Windows Server 服務。應用程式 Proxy 連接器必須使用全域系統管理員和密碼向 Azure AD 目錄註冊後才能運作。通常，這是在連接器安裝期間於一個快顯對話方塊中輸入的。但是，您可以使用 Windows PowerShell 來建立認證物件，並使用其建立的語彙基元來輸入您的註冊資訊，或者您可以建立自己的語彙基元來輸入註冊資訊。

## 步驟 1：安裝連接器，但不註冊


下列是安裝連接器但不註冊連接器的方式：


1. 開啟命令提示字元。
2. 執行下列命令，其中的 /q 表示無訊息安裝，即安裝不會提示您接受使用者授權合約。

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## 步驟 2：向 Azure Active Directory 註冊連接器
這可以使用下列方法之一來完成：- 使用 Windows PowerShell 認證物件註冊連接器 - 使用離線時建立的語彙基元註冊連接器

### 使用 Windows PowerShell 認證物件註冊連接器


1. 執行下列命令以建立 Windows PowerShell 認證物件，其中的 <username> 和 <password> 應取代為您的目錄的使用者名稱和密碼：

        $User = "<username>" 
        $PlainPassword = '<password>' 
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force 
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword 
    
2. 	移至 C:\\Program Files\\Microsoft AAD 應用程式 Proxy 連接器，並執行 Windows PowerShell 中的檔案 **Register Connector.PS1**。
3. 執行下列命令，以使用您建立的 PowerShell 認證物件在您的指令碼中輸入連接器註冊使用者名稱和密碼，其中的 $cred 是您建立的 PowerShell 認證物件的名稱：

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred 


### 使用離線時建立的語彙基元註冊連接器

1. 使用 AuthenticationContext 類別建立一個離線語彙基元，例如：

        #region constants /// /// The AAD authentication endpoint uri /// static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");
        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
		static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");
		
		/// <summary>
		/// The AppIdUri of the registration service in AAD
		/// </summary>
		static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

		#endregion


		public static void GetAuthenticationToken()
		{
    		AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);
	    AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);


	        if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
    	    {
          Trace.TraceError("Authentication result, token or tenant id returned are null");
          throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
    	}

    	string token = authResult.AccessToken;
    	string tenantID = authResult.TenantId;
		}

2. 建立語彙基元後，請使用該語彙基元建立一個 SecureString：<br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. 執行下列 Windows PowerShell 命令，其中的 SecureToken 是您剛才建立的語彙基元的名稱：<br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## 後續步驟
應用程式 Proxy 還有其他更多用途：


- [使用您自己的網域名稱發佈應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)


### 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源
* [以組織方式註冊 Azure](../sign-up-organization.md)
* [Azure 身分識別](../fundamentals-identity.md)

<!---HONumber=Oct15_HO3-->