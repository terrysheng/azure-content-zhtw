<properties
   pageTitle="使用 Azure Resource Manager 驗證服務主體 | Microsoft Azure"
   description="描述如何透過角色存取控制授與服務主體的存取權，並驗證服務主體。顯示如何使用 PowerShell 和 Azure CLI 執行這些工作。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="tomfitz"/>

# 使用 Azure Resource Manager 驗證服務主體

本主題顯示如何允許服務主體 (例如自動化程序、應用程式或服務) 存取您訂用帳戶中的其他資源。使用 Azure Resource Manager，您可以使用角色存取控制將允許的動作授與服務主體，並驗證該服務主體。

本主題說明如何使用 Azure PowerShell 或適用於 Mac、Linux 和 Windows 的 Azure CLI，來建立應用程式和服務主體、將角色指派給服務主體，並驗證為服務主體。如果您未安裝 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](./powershell-install-configure.md)。如果您未安裝 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli-install.md)。如需使用入口網站執行這些步驟的詳細資訊，請參閱[使用入口網站建立 Active Directory 應用程式和服務主體](resource-group-create-service-principal-portal.md)

## 概念
1. Azure Active Directory - 雲端的身分識別與存取管理服務。如需詳細資訊，請參閱[什麼是 Azure Active Directory](active-directory/active-directory-whatis.md)
2. 服務主體 - 必須存取其他資源之目錄中應用程式的執行個體。
3. Active Directory 應用程式 - 向 AAD 識別應用程式的目錄記錄。

如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](active-directory/active-directory-authentication-scenarios.md)。

本主題說明建立 Active Directory 應用程式和驗證該應用程式的 4 個路徑。路徑依您是否要使用密碼或憑證進行驗證，以及偏好使用 PowerShell 或 Azure CLI 而有所不同。這些路徑為：

- [使用密碼驗證 - PowerShell](#authenticate-with-password---powershell)
- [使用憑證驗證 - PowerShell](#authenticate-with-certificate---powershell)
- [使用密碼驗證 - Azure CLI](#authenticate-with-password---azure-cli)
- [使用憑證驗證 - Azure CLI](#authenticate-with-certificate---azure-cli)

## 使用密碼驗證 - PowerShell

在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供應用程式識別碼和密碼驗證為服務主體。

1. 登入您的帳戶。

        PS C:\> Login-AzureRmAccount

1. 執行 **New-AzureRmADApplication** 命令，建立新的 Active Directory 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     檢查新的應用程式物件。需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得存取權杖。

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. 傳入 Active Directory 應用程式的應用程式識別碼來建立應用程式的服務主體。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

3. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您必須登入為服務主體，來執行服務主體的作業。本主題中展示三種選項：

- [透過 PowerShell 手動提供認證](#manually-provide-credentials-through-powershell)
- [透過自動化的 PowerShell 指令碼提供認證](#provide-credentials-through-automated-powershell-script)
- [透過應用程式中的程式碼提供認證](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### 透過 PowerShell 手動提供認證

當執行隨選指令碼或命令時，您可以手動提供服務主體的認證。

1. 執行 **Get-Credential** 命令，以建立含有您認證的新 **PSCredential** 物件。

        PS C:\> $creds = Get-Credential

2. 系統會提示您輸入認證。針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。針對密碼，使用您在建立帳戶時所指定的密碼。

     ![輸入認證][1]

3. 擷取用來建立角色指派的訂用帳戶。此訂用帳戶將會用來取得服務主體角色指派所在租用戶的 **TenantId**。

        PS C:\> $subscription = Get-AzureRmSubscription

     如果您已在非目前所選取訂用帳戶的訂用帳戶中建立角色指派，則可以指定 **SubscriptoinId** 或 **SubscriptionName** 參數來擷取不同的訂用帳戶。

4. 使用 **Login-AzureRmAccount** Cmdlet 登入為服務主體，但提供認證物件，並將此帳戶指定為服務主體。

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

<a id="provide-credentials-through-automated-powershell-script" />
### 透過自動化的 PowerShell 指令碼提供認證

本節說明如何登入為服務主體，而不需要手動輸入認證。您不必手動提供密碼，因為密碼會從金鑰保存庫中擷取。

> [AZURE.NOTE] 直接將密碼包含在您的 PowerShell 指令碼中並不安全，因為密碼會以文字公開。請改用如金鑰保存庫的服務來儲存密碼，並在執行指令碼時擷取密碼。

這些步驟假設您已設定金鑰保存庫並儲存密碼。若要透過範本部署金鑰保存庫和密碼，請參閱[金鑰保存庫範本格式]()。如需深入了解金鑰保存庫，請參閱[開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。

1. 從金鑰保存庫擷取您的密碼 (在下列範例中，以名稱 **appPassword** 儲存的密碼)。

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. 取得您的 Active Directory 應用程式。登入時，您將需要應用程式識別碼。

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. 藉由提供應用程式識別碼和密碼作為認證，建立新的 **PSCredential** 物件。

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. 擷取用來建立角色指派的訂用帳戶。稍後會使用此訂用帳戶，來取得服務主體角色指派所在租用戶的 **TenantId**。

        PS C:\> $subscription = Get-AzureRmSubscription

     如果您已在非目前所選取訂用帳戶的訂用帳戶中建立角色指派，則可以指定 **SubscriptoinId** 或 **SubscriptionName** 參數來擷取不同的訂用帳戶。

5. 使用 **Login-AzureRmAccount** Cmdlet 登入為服務主體，但提供認證物件，並將此帳戶指定為服務主體。
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

<a id="provide-credentials-through-code-in-an-application" />
### 透過應用程式中的程式碼提供認證

若要從 .NET 應用程式認證，請包含下列程式碼。您需要 Active Directory 應用程式的應用程式識別碼、密碼，以及訂用帳戶的租用戶識別碼。擷取存取權杖之後，您就可以使用訂用帳戶中的資源。

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## 使用憑證驗證 - PowerShell

在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供憑證驗證為服務主體。本主題假設您已簽發憑證。

其顯示兩種方法來使用憑證 - 金鑰認證和金鑰值。您可以使用其中一種方法。

首先，您必須在 PowerShell 中設定一些值，以便稍後建立應用程式時使用。

1. 登入您的帳戶。

        PS C:\> Login-AzureRmAccount

1. 針對這兩種方法，從您的憑證建立 X509Certificate2 物件並擷取金鑰值。使用憑證的路徑以及該憑證的密碼。

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. 如果使用金鑰認證，請建立金鑰認證物件並將其值設為上一個步驟中的 `$keyValue`。以下的範例包含呼叫 `Add-Type` 以從組件加入類型。範例所示的路徑應該會與您的路徑相似，但可能略有不同。

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. 在目錄中建立應用程式。第一個命令會顯示如何使用金鑰值。

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    或者，使用第二個範例來指派金鑰認證。

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    檢查新的應用程式物件。需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得存取權杖。

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. 傳入 Active Directory 應用程式的應用程式識別碼來建立應用程式的服務主體。

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

5. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您必須登入為服務主體，來執行服務主體的作業。本主題中展示兩種選項：

- [透過自動化的 PowerShell 指令碼提供憑證](#provide-certificate-through-automated-powershell-script)
- [透過應用程式中的程式碼提供憑證](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### 透過自動化的 PowerShell 指令碼提供憑證

1. 取得您的 Active Directory 應用程式。登入時，您將需要應用程式識別碼

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. 擷取用來建立角色指派的訂用帳戶。稍後會使用此訂用帳戶，來取得服務主體角色指派所在租用戶的 **TenantId**。

        PS C:\> $subscription = Get-AzureRmSubscription

     如果您已在非目前所選取訂用帳戶的訂用帳戶中建立角色指派，則可以指定 **SubscriptoinId** 或 **SubscriptionName** 參數來擷取不同的訂用帳戶。

3. 取得將用於驗證的憑證。

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. 若要在 PowerShell 中進行驗證，請提供憑證指紋、應用程式識別碼和租用戶識別碼。

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

<a id="provide-certificate-through-code-in-an-application" />
### 透過應用程式中的程式碼提供憑證

若要從 .NET 應用程式認證，請包含下列程式碼。擷取用戶端之後，您可以存取訂用帳戶中的資源。

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
        

## 使用密碼驗證 - Azure CLI

您將從建立服務主體開始。若要這麼做，我們必須在目錄中建立應用程式。本節將逐步引導如何在目錄中建立新的應用程式。

1. 切換至 Azure Resource Manager 模式，並登入您的帳戶。

        azure config mode arm
        azure login

2. 執行 **azure ad app create** 命令，建立新的 Active Directory 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    傳回 Active Directory 應用程式。需要有 AppId 屬性，才能建立服務主體、角色指派以及取得存取權杖。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

4. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您必須登入為服務主體，來執行服務主體的作業。本主題中展示三種選項：

- [透過 Azure CLI 手動提供認證](#manually-provide-credentials-through-azure-cli)
- [透過自動化的 Azure CLI 指令碼提供認證](#provide-credentials-through-automated-azure-cli-script)
- [透過應用程式中的程式碼提供認證](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### 透過 Azure CLI 手動提供認證

如果您想要手動登入為服務主體，您可以使用 **azure login** 命令。您必須提供租用戶識別碼、應用程式識別碼和密碼。直接將密碼包含在指令碼中並不安全，因為密碼會儲存在檔案中。請參閱下一節，以了解執行自動化指令碼時更佳的選項。

1. 找出包含服務主體之訂用帳戶的 **TenantId**。您必須移除從 JSON 輸出傳回的開頭及結尾雙引號，然後才將它作為參數傳遞。

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

2. 針對使用者名稱，使用您在建立服務主體時所使用的 **AppId**。如果您要擷取應用程式識別碼，請使用下列命令。在 **search** 參數中提供 Active Directory 應用程式的名稱。

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

3. 登入為服務主體。

        azure login -u "$appId" --service-principal --tenant "$tenantId"

顯示提示時，提供您建立帳戶時指定的密碼。

    info:    Executing command login
    Password: ********

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

<a id="provide-credentials-through-automated-azure-cli-script" />
### 透過自動化的 Azure CLI 指令碼提供認證

本節說明如何登入為服務主體，而不需要手動輸入認證。

> [AZURE.NOTE] 將密碼包含在您的 Azure CLI 指令碼中並不安全，因為密碼會以文字公開。請改用如金鑰保存庫的服務來儲存密碼，並在執行指令碼時擷取密碼。

這些步驟假設您已設定金鑰保存庫並儲存密碼。若要透過範本部署金鑰保存庫和密碼，請參閱[金鑰保存庫範本格式]()。如需深入了解金鑰保存庫，請參閱[開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。

1. 從金鑰保存庫擷取您的密碼 (在下列範例中，以名稱 **appPassword** 儲存的密碼)。您必須移除從 JSON 輸出傳回的開頭及結尾雙引號，然後才將它作為密碼參數傳遞。

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq '.value' | sed -e 's/^"//' -e 's/"$//')
    
2. 找出包含服務主體之訂用帳戶的 **TenantId**。

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

3. 針對使用者名稱，使用您在建立服務主體時所使用的 **AppId**。如果您要擷取應用程式識別碼，請使用下列命令。在 **search** 參數中提供 Active Directory 應用程式的名稱。

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

4. 藉由提供應用程式識別碼、金鑰保存庫中的密碼、租用戶識別碼，登入為服務主體。

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

<a id="provide-credentials-through-code-in-an-application-cli" />
### 透過應用程式中的程式碼提供認證

若要從 .NET 應用程式認證，請包含下列程式碼。您需要 Active Directory 應用程式的應用程式識別碼、密碼，以及訂用帳戶的租用戶識別碼。擷取存取權杖之後，您就可以使用訂用帳戶中的資源。

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## 使用憑證驗證 - Azure CLI

在本節中，您要執行的步驟是為 Azure Active Directory 應用程式建立可使用憑證進行驗證的服務主體。本主題假設您已簽發憑證，並已安裝 [OpenSSL](http://www.openssl.org/)。

1. 使用下列項目建立 **.pem** 檔案：

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. 開啟 **.pem** 檔案並複製憑證資料。尋找在 **-----BEGIN CERTIFICATE-----** 與 **-----END CERTIFICATE-----** 之間的一長串字元。

3. 執行 **azure ad app create** 命令，以建立新的 Active Directory 應用程式，並提供您在上一個步驟中複製的憑證資料以做為金鑰值。

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    傳回 Active Directory 應用程式。需要有 AppId 屬性，才能建立服務主體、角色指派以及取得存取權杖。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

您已經建立了 Active Directory 應用程式，和該應用程式的服務主體。您已經將服務主體指派給角色。現在，您必須登入為服務主體，來執行服務主體的作業。本主題中展示兩種選項：

- [透過自動化的 Azure CLI 指令碼提供憑證](#provide-certificate-through-automated-azure-cli-script)
- [透過應用程式中的程式碼提供憑證](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### 透過自動化的 Azure CLI 指令碼提供憑證

1. 您必須擷取憑證指紋，然後移除不必要的字元。

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     這會傳回指紋值，類似：

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. 找出包含服務主體之訂用帳戶的 **TenantId**。

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

3. 針對使用者名稱，使用您在建立服務主體時所使用的 **AppId**。如果您要擷取應用程式識別碼，請使用下列命令。在 **search** 參數中提供 Active Directory 應用程式的名稱。

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

4. 若要使用 Azure CLI 進行驗證，請提供憑證指紋、憑證檔案、應用程式識別碼和租用戶識別碼。

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

您現在驗證為您所建立之 Active Directory 應用程式的服務主體。

<a id="provide-certificate-through-code-in-an-application-cli" />
### 透過應用程式中的程式碼提供憑證

若要從 .NET 應用程式認證，請包含下列程式碼。擷取用戶端之後，您可以存取訂用帳戶中的資源。

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
       
如需有關使用憑證和 Azure CLI 的詳細資訊，請參閱 [Certificate-based auth with Azure Service Principals from Linux command line (從 Linux 命令列以憑證方式驗證 Azure 服務主體)](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)

## 後續步驟
  
- 若要了解如何使用入口網站與服務主體，請參閱[使用 Azure 入口網站建立新的 Azure 服務主體](./resource-group-create-service-principal-portal.md)  
- 如需實作 Azure Resource Manager 的安全性指導，請參閱 [Azure Resource Manager 的安全性考量](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0302_2016-->