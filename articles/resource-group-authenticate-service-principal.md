<properties
   pageTitle="使用 Azure 資源管理員驗證服務主體"
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
   ms.date="08/14/2015"
   ms.author="tomfitz"/>

# 使用 Azure 資源管理員驗證服務主體

本主題顯示如何允許服務主體 (例如自動化程序、應用程式或服務) 存取您訂用帳戶中的其他資源。使用 Azure 資源管理員，您可以使用角色存取控制將允許的動作授與服務主體，並驗證該服務主體。本主題顯示如何使用 PowerShell 和 Azure CLI 將角色指派給服務主體，並驗證服務主體。

其示範如何使用使用者名稱與密碼或憑證進行驗證。

您可以使用 Mac、Linux 和 Windows 適用的 Azure PowerShell 或 Azure CLI。如果您未安裝 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](./powershell-install-configure.md)。如果您未安裝 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli-install.md)。

## 概念
1. Azure Active Directory (AAD) - 雲端的身分識別與存取管理服務。如需詳細資訊，請參閱[什麼是 Azure Active Directory](active-directory/active-directory-whatis.md)。
2. 服務主體 - 必須存取其他資源之目錄中應用程式的執行個體。
3. AD 應用程式 - 向 AAD 識別應用程式的目錄記錄。如需詳細資訊，請參閱 [Azure AD 中的驗證基本概念](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)。

## 使用密碼驗證服務主體 - PowerShell

在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供應用程式識別碼和密碼驗證為服務主體。

1. 執行 **New-AzureADApplication** 命令，以建立新的 AAD 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        PS C:\> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     檢查新的應用程式物件。需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得 JWT 權杖。

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}


2. 建立應用程式的服務主體。

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

3. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色存取控制的詳細資訊，請參閱[管理和稽核資源存取權](azure-portal/resource-group-rbac.md)。

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. 擷取用來建立角色指派的訂用帳戶。稍後會使用此訂用帳戶，來取得服務主體角色指派所在租用戶的 **TenantId**。

        PS C:\> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     如果您已在非目前所選取訂用帳戶的訂用帳戶中建立角色指派，則可以指定 **SubscriptoinId** 或 **SubscriptionName** 參數來擷取不同的訂用帳戶。

5. 執行 **Get-Credential** 命令，以建立含有您認證的新 **PSCredential** 物件。

        PS C:\> $creds = Get-Credential

     系統會提示您輸入認證。

     ![][1]

     針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。針對密碼，使用您在建立帳戶時所指定的密碼。

6. 使用您輸入的認證做為 **Add-AzureAccount** Cmdlet 的輸入，以將服務主體登入：

        PS C:\> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     您現在應該驗證為您所建立 AAD 應用程式的服務主體。


## 使用憑證驗證服務主體 - PowerShell

在這一節中，您將執行相關步驟來建立 Azure Active Directory 應用程式的服務主體、指派角色給服務主體，並藉由提供憑證驗證為服務主體。本主題假設您已簽發憑證。

其顯示兩種方法來使用憑證 - 金鑰認證和金鑰值。您可以使用其中一種方法。

首先，您必須在 PowerShell 中設定一些值，以便稍後建立應用程式時使用。

1. 針對這兩種方法，從您的憑證建立 X509Certificate 物件並擷取金鑰值。使用憑證的路徑以及該憑證的密碼。

        $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. 如果您使用金鑰認證，請建立金鑰認證物件並將其值設為上一個步驟中的 `$keyValue`。

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

        $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    或者，使用第二個範例來指派金鑰認證。

         $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyCredentials $keyCredential

    檢查新的應用程式物件。需要有 **ApplicationId** 屬性，才能建立服務主體、角色指派以及取得 JWT 權杖。

        PS C:\> $azureAdApplication

        Type                    : Application
        ApplicationId           : 76fa8d97-f07e-4b9a-b871-a57a7acd777a
        ApplicationObjectId     : c36b7b57-a949-4401-b381-18a5210aff10
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access <Your Application Display Name> on behalf of the signed-in
                          user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <Your Application Display Name>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId": "9f13c6c6-35ba-43d6-b8b3-6a87aa641388",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application to access <Your Application Display Name> on your behalf.",
                            "userConsentDisplayName": "Access <Your Application Display Name>",
                            "lang": null
                          }}

4. 建立應用程式的服務主體。

        PS C:\> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

5. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色存取控制的詳細資訊，請參閱[管理和稽核資源存取權](azure-portal/resource-group-rbac.md)。

        PS C:\> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

6. 若要從應用程式進行驗證，請包含下列程式碼。擷取用戶端之後，您可以存取訂用帳戶中的資源。

        string clientId = "<Client ID for your AAD app>"; 
        var subscriptionId = "<Your Azure SubscriptionId>"; 
        string tenant = "<AAD tenant name>.onmicrosoft.com"; 

        var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

        X509Certificate2 cert = null; 
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
        string certName = "examplecert"; 
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
        

## 使用密碼驗證服務主體 - Azure CLI

您將從建立服務主體開始。若要這麼做，我們必須在目錄中建立應用程式。本節將逐步引導如何在目錄中建立新的應用程式。

1. 執行 **azure ad app create** 命令，以建立新的 AAD 應用程式。提供應用程式的顯示名稱、描述應用程式之頁面的 URI (未確認連結)、識別應用程式的 URI，以及應用程式身分識別的密碼。

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    傳回 Azure AD 應用程式。需要有 ApplicationId 屬性，才能建立服務主體、角色指派以及取得 JWT 權杖。

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

2. 建立應用程式的服務主體。提供在上一個步驟中傳回的應用程式識別碼。

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    傳回新的服務主體。授與權限時，需要物件識別碼。
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    您現在已在目錄中建立服務主體，但未將任何權限或範圍指派給服務。您必須明確地授與服務主體權限，才能在某個範圍執行作業。

3. 授與服務主體對您訂用帳戶的權限。在此範例中，您會將讀取訂用帳戶中所有資源的權限授與服務主體。針對 **ServicePrincipalName** 參數，提供您在建立應用程式時所使用的 **ApplicationId** 或 **IdentifierUris**。如需角色存取控制的詳細資訊，請參閱[管理和稽核資源存取權](azure-portal/resource-group-rbac.md)。

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. 列出帳戶，並尋找輸出中的 **TenantId** 屬性，以判斷服務主體角色指派所在租用戶的 **TenantId**。

        azure account list

5. 使用服務主體做為您的身分識別來進行登入。針對使用者名稱，使用您在建立應用程式時所使用的 **ApplicationId**。針對密碼，使用您在建立帳戶時所指定的密碼。

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    您現在應該驗證為您所建立 AAD 應用程式的服務主體。

## 後續步驟
  
- 如需角色存取控制的概觀，請參閱[管理和稽核資源存取權](azure-portal/resource-group-rbac.md)  
- 若要了解如何使用入口網站與服務主體，請參閱[使用 Azure 入口網站建立新的 Azure 服務主體](./resource-group-create-service-principal-portal.md)  
- 如需實作 Azure 資源管理員安全性的指導，請參閱 [Azure 資源管理員的安全性考量](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=August15_HO8-->