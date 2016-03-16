<properties pageTitle="從 Web 應用程式使用 Azure 金鑰保存庫 | Microsoft Azure" description="使用此教學課程來幫助您了解如何從 Web 應用程式使用 Azure 金鑰保存庫。" services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016" 
	ms.author="adhurwit"/>

# 從 Web 應用程式使用 Azure 金鑰保存庫 #

## 簡介  
使用此教學課程來幫助您了解如何從 Azure 中的 Web 應用程式使用 Azure 金鑰保存庫。它會引導您完成從 Azure 金鑰保存庫存取密碼的程序，以便在 Web 應用程式中使用該密碼。

**預估完成時間：**15 分鐘。


如需 Azure 金鑰保存庫的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## 必要條件 

若要完成本教學課程，您必須具備下列項目：

- Azure 金鑰保存庫中密碼的 URI
- 已在 Azure Active Directory 註冊，且有權存取您金鑰保存庫之 Web 應用程式的用戶端識別碼和用戶端密碼
- Web 應用程式。我們將會說明 ASP.NET MVC 應用程式在 Azure 中做為 Web 應用程式部署的步驟。 

> [AZURE.NOTE]  在本教學課程中，完成在[開始使用 Azure 金鑰保存庫](key-vault-get-started.md)中所列步驟是很重要的，這樣您才會有 Web 應用程式的密碼 URI 和用戶端識別碼和用戶端密碼。

已在 Azure Active Directory 註冊、且已獲權存取金鑰保存庫的 Web 應用程式將會被用來存取金鑰保存庫。如果情況不是這樣，請回到開始使用教學課程中的註冊應用程式，並重複列出的步驟。

本教學課程是針對 Web 開發人員所設計，這些開發人員必須了解在 Azure 上建立 Web 應用程式的基本概念。如需有關 Azure Web Apps 的詳細資訊，請參閱 [Web Apps 概觀](../app-service-web-overview.md)。



## <a id="packages"></a>新增 Nuget 封裝 ##
有二個 Web 應用程式必須已安裝的封裝。

- Active Directory 驗證程式庫 - 包含與 Azure Active Directory 互動及管理使用者身分識別的方法
- Azure 金鑰保存庫資源庫 - 包含與 Azure 金鑰保存庫互動的方法


您可以使用 Package Manager Console 的 Install-Package 命令安裝這二個封裝。

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault 


## <a id="webconfig"></a>修改 Web.Config ##
有三個必須加入至 web.config 檔案的應用程式設定，如下所示。

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


如果您不打算將您的應用程式做為 Azure Web 應用程式裝載，則您應該將實際的 ClientId、用戶端密碼和密碼 URI 值加入 web.config。因為我們將在 Azure 入口網站中新增實際值以取得額外的安全性層級，不然，您可以保留這些虛擬值。


## <a id="gettoken"></a>新增方法以取得存取權杖 ##
為了能夠使用金鑰保存庫 API，您需要存取權杖。金鑰保存庫用戶端會處理金鑰保存庫 API 的呼叫，但是您必須提供具有取得存取權杖的函式。

以下是從 Azure Active Directory 取得存取權杖的程式碼。此程式碼可以放置在應用程式的任何位置。我想要新增 Utils 或 EncryptionHelper 類別。

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

> [AZURE.NOTE] 透過用戶端識別碼和密碼，使用用戶端密碼來驗證 Azure AD 應用程式是最簡單的方法。此外，如果在 Web 應用程式中使用該密碼，您將能夠區分職責，並充分掌控您的金鑰管理。但是，要這樣做就必須將用戶端密碼保存在組態設定中。在某種程度上，這樣的做法與將您要保護的密碼保存在組態設定中的方法相較而言，兩者的風險是一樣的。如需關於如何使用用戶端識別碼與憑證 (而非用戶端識別碼與用戶端密碼) 來驗證 Azure AD 應用程式的討論，請參閱以下內容。



## <a id="appstart"></a>在應用程式啟動時擷取密碼 ##
現在我們需要呼叫金鑰保存庫 API，並擷取密碼的程式碼。下列程式碼可以放置在任何位置，只要在您需要使用它之前呼叫即可。我已將程式碼放入 Global.asax 的應用程式啟動事件中，因此它在啟動時會執行一次，並讓密碼可在應用程式中使用。

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>(選擇性) 在 Azure 入口網站中新增應用程式設定 ##
如果您已有 Azure Web Apps，您現在可以在 Azure 入口網站中為 AppSettings 新增實際值。如此一來，實際值將不會存在於 web.config 中，但會透過您有個別存取控制功能的入口網站受到保護。這些值會被您在 web.config 中輸入的值取代。請確定名稱都相同。

![Azure 入口網站中顯示的應用程式設定][1]


## 使用憑證 (而非用戶端密碼) 進行驗證。 
若要驗證 Azure AD 應用程式，另一種方式是使用用戶端識別碼和憑證 (而非用戶端識別碼和用戶端密碼)。以下是在 Azure Web 應用程式中使用憑證的步驟：

1. 取得或建立憑證
2. 將憑證與 Azure AD 應用程式產生關聯
3. 將程式碼加入 Web 應用程式以使用憑證
4. 將憑證加入 Web 應用程式


**取得或建立憑證** 基於本文的目的，我們將測試憑證。以下幾個命令可讓您在開發人員命令提示字元中用來建立憑證。變更您要建立憑證檔案的目錄位置。

	makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
	pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

記下 .pfx 的結束日期和密碼 (在此範例中為：07/31/2016 和 test123)。您將需要在下面使用這些資訊。

如需如何建立測試憑證的詳細資訊，請參閱[做法：自行建立測試憑證](https://msdn.microsoft.com/library/ff699202.aspx) (英文)


**將憑證與 Azure AD 應用程式產生關聯** 有了憑證之後，您需要將其與 Azure AD 應用程式產生關聯。但是，Azure 管理入口網站目前不支援這項作業。您必須改用 Powershell。以下是您需要執行的命令：

	$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
	
	PS C:\> $x509.Import("C:\data\KVWebApp.cer")
	
	PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())
	
	PS C:\> $now = [System.DateTime]::Now
	
	# this is where the end date from the cert above is used
	PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31") 
	
	PS C:\> $adapp = New-AzureADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow
	
	PS C:\> $sp = New-AzureADServicePrincipal -ApplicationId $adapp.ApplicationId
	
	PS C:\> Set-AzureKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToKeys all -ResourceGroupName 'contosorg'
	
	# get the thumbprint to use in your app settings
	PS C:\>$x509.Thumbprint

執行這些命令之後，您就可以在 Azure AD 中看到應用程式。如果您一開始沒有看到該應用程式，請改用「我公司所擁有的應用程式」進行搜尋，不要使用「我公司所使用的應用程式」。

若要深入了解 Azure AD 應用程式物件和 ServicePrincipal 物件，請參閱[應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)



**將程式碼加入 Web 應用程式以使用憑證** 現在我們將程式碼加入您的 Web 應用程式，以存取憑證並使用其進行驗證。

首先，使用程式碼存取憑證。

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint, 
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


請注意，StoreLocation 是 CurrentUser，而不是 LocalMachine。另外，由於我們使用測試憑證，因此我們必須提供 "false" 憑證給 Find 方法。


接著是使用 CertificateHelper 並建立驗證所需之 ClientAssertionCertificate 的程式碼。

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


以下是取得存取 Token 的新程式碼。此程式碼將會取代上述的 GetToken 方法。為方便起見，我已將其改名。

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

為了方便使用，我已將所有此類程式碼放入我的 Web 應用程式專案的公用程式類別。

最後的程式碼變更位在 Application\_Start 方法中。首先，我們需要呼叫 GetCert() 方法以載入 ClientAssertionCertificate。接著，在建立新的 KeyVaultClient 時，變更我們所提供的回呼方法。請注意，這會取代我們上面的程式碼。

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**將憑證加入 Web 應用程式** 將憑證加入您的 Web 應用程式的程序相當簡單，只需兩個步驟。首先，請移至 Azure 入口網站並瀏覽至您的 Web 應用程式。在 Web 應用程式的 [設定] 刀鋒視窗中，按一下 [自訂網域及 SSL] 的項目。在開啟的刀鋒視窗中，您將能夠上傳先前建立的憑證 KVWebApp.pfx，並請確定您記得 pfx 的密碼。

![在 Azure 入口網站中將憑證加入 Web 應用程式][2]


您需要做的最後一件事，就是將應用程式設定新增至名為WEBSITE\_LOAD\_CERTIFICATES 與值為 * 的 Web 應用程式。如此可確保載入所有憑證。如果您只想載入已上傳的憑證，則可輸入其憑證指紋的逗號分隔清單。

若要深入了解將憑證加入 Web 應用程式的程序，請參閱[在 Azure 網站應用程式中使用憑證](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/) (英文)



## <a id="next"></a>接續步驟 ##


如需程式設計參考，請參閱 [Azure 金鑰保存庫 C# 用戶端 API 參考](https://msdn.microsoft.com/library/azure/dn903628.aspx)。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 

<!---HONumber=AcomDC_0204_2016-->