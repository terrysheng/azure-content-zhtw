<properties
   pageTitle="使用金鑰保存庫來保護應用程式的機密資訊 | Microsoft Azure"
   description="如何使用金鑰保存庫服務來儲存應用程式的機密資訊"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 使用 Azure 金鑰保存庫來保護應用程式的機密資訊

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

## 概觀

應用程式設定經常含有機密內容因此必須受到保護，例如：

- 資料庫連接字串
- 密碼
- 密碼編譯金鑰

最佳安全性做法是，絕對不要在原始檔控制中儲存這些機密資訊。這些資訊很容易外洩，即使原始程式碼儲存機制是私用的也是一樣。而且不只是讓一般使用者接觸不到機密資訊這麼簡單。在較大型的專案中，您可能會想要限制哪些開發人員和操作員可以存取生產機密資訊 (測試或開發環境的設定並不相同)。

比較安全的選擇是將這些機密資訊儲存在 [Azure 金鑰保存庫][KeyVault]。金鑰保存庫是用於管理密碼編譯金鑰和其他機密資訊的雲端託管服務。本文說明如何使用金鑰保存庫來儲存應用程式的組態設定。

在 [Tailspin Surveys][Surveys] 應用程式中，下列設定屬於機密資訊：

- 資料庫連接字串。
- Redis 連接字串。
- Web 應用程式的用戶端密碼。

為了將組態機密資訊儲存在金鑰保存庫中，Surveys 應用程式實作連結到 ASP.NET Core 1.0 [組態系統][configuration]的自訂組態提供者。自訂提供者會在啟動時讀取金鑰保存庫中的組態設定。

Surveys 應用程式會從下列位置載入組態設定：

- appsettings.json 檔案
- [使用者密碼存放區][user-secrets] (僅限開發環境，用於測試)
- 裝載環境 (Azure Web Apps 中的應用程式設定)
- 金鑰保存庫

上述每一個位置都會覆寫前一個位置，以便儲存在金鑰保存庫中的設定會優先採用。

> [AZURE.NOTE] 依預設會停用金鑰保存庫組態提供者。在本機執行應用程式時並不需要此項目。在生產部署中則會予以啟用。

> [AZURE.NOTE] 金鑰保存庫提供者目前不受 .NET Core 支援，因為它需要 [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault] 封裝。

應用程式啟動時會從每個已註冊的組態提供者讀取組態，並將其填入強型別的選項物件 (如需詳細資訊，請參閱[使用選項和組態物件][options])。

## 實作

[KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] 類別是插入 ASP.NET Core 1.0 [組態系統][configuration]的組態提供者。

若要使用 `KeyVaultConfigurationProvider`，請在啟動類別呼叫 `AddKeyVaultSecrets` 擴充方法：

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

請注意，`KeyVaultConfigurationProvider` 需要進行一些組態設定，並且必須儲存在另一個組態來源。

當應用程式啟動時，`KeyVaultConfigurationProvider` 會列舉金鑰保存庫中的所有機密資訊。它會針對每個機密資訊尋找名為 'ConfigKey' 的標記。標記的值是組態設定的名稱。

> [AZURE.NOTE] [Tags][金鑰標記]是與金鑰一起儲存的選擇性中繼資料。金鑰名稱不能包含冒號 (:) 字元，所以這邊使用標記。

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] 請參閱 [KeyVaultConfigurationProvider.cs]。

## 在 Surveys 應用程式中設定金鑰保存庫

必要條件：

- 安裝 [Azure Resource Manager Cmdlet][azure-rm-cmdlets]。
- 如[執行 Surveys 應用程式][readme]中所述地設定 Surveys 應用程式。

高階步驟：

1. 設定租用戶中的系統管理員使用者。
2. 設定用戶端憑證。
3. 建立金鑰保存庫。
4. 將組態設定新增至金鑰保存庫。
5. 取消註解可啟用金鑰保存庫的程式碼。
6. 更新應用程式的使用者機密資訊。

### 設定系統管理員使用者

> [AZURE.NOTE] 若要建立金鑰保存庫，您必須使用可管理 Azure 訂用帳戶的帳戶。此外，任何您授權讀取金鑰保存庫的應用程式必須在和該帳戶相同的租用戶中註冊。

在此步驟中，您將確保在登入為 Surveys 應用程式註冊所在之租用戶的使用者時，您可以建立金鑰保存庫。

首先請變更與 Azure 訂用帳戶相關聯的目錄。

1. 登入 [Azure 管理入口網站][azure-management-portal]。

2. 按一下 [設定]。

    ![Settings](media/guidance-multitenant-identity/settings.png)

3. 選取 Azure 訂用帳戶。

4. 按一下入口網站底部的 [編輯目錄]。

    ![Settings](media/guidance-multitenant-identity/edit-directory.png)

5. 在 [變更相關聯的目錄] 中，選取 Surveys 應用程式註冊所在的 Azure AD 租用戶。

    ![設定](media/guidance-multitenant-identity/edit-directory2.png)

6. 按一下箭號按鈕完成對話方塊。

在 Surveys 應用程式註冊所在的 Azure AD 租用戶中建立系統管理員使用者。

1. 登入 [Azure 管理入口網站][azure-management-portal]。

2. 選取應用程式註冊所在的 Azure AD 租用戶。

3. 按一下 [使用者] > [新增使用者]。

4. 在 [新增使用者] 對話方塊中，將使用者指派給全域管理員角色。

將系統管理員使用者新增為 Azure 訂用帳戶的共同管理員。

1. 登入 [Azure 管理入口網站][azure-management-portal]。

2. 按一下 [設定]，然後選取 Azure 訂用帳戶。

3. 按一下 [系統管理員]。

4. 按一下入口網站底部的 [新增]。

5. 輸入先前所建立之系統管理員使用者的電子郵件。

6. 核取訂用帳戶的核取方塊。

7. 按一下核取記號按鈕來完成對話方塊。

![新增共同系統管理員](media/guidance-multitenant-identity/co-admin.png)


### 設定用戶端憑證

1. 執行 PowerShell 指令碼 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]，如下所示：
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    針對 `Subject` 參數，輸入任何名稱，如「surveysapp」。指令碼會產生自我簽署憑證，並將它儲存在「目前使用者/個人」憑證存放區。

2. 指令碼的輸出是 JSON 片段。將此片段加入 Web 應用程式的資訊清單，如下所示：

    1. 登入 [Azure 管理入口網站][azure-management-portal]並瀏覽至您的 Azure AD 目錄。

    2. 按一下 [應用程式]。

    3. 選取 Surveys 應用程式。

    4.	按一下 [管理資訊清單] 並選取 [下載資訊清單]。

    5.	在文字編輯器中開啟資訊清單 JSON 檔案。將指令碼的輸出貼入至 `keyCredentials` 屬性。看起來應該會像下面這樣：
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	儲存 JSON 檔案的變更。

    7.	返回入口網站。按一下 [管理資訊清單] > [上傳資訊清單] 然後上傳 JSON 檔案。

3. 將相同的 JSON 片段新增至 Web API (Surveys.WebAPI) 的應用程式資訊清單。

4. 執行下列命令取得憑證的指紋。
    ```
    certutil -store -user my [subject]
    ```
    其中 `[subject]` 是您在 PowerShell 指令碼中為 Subject 所指定的值。指紋會列在「Cert Hash(sha1)」之下。移除十六進位數字之間的空格。

您稍後會使用指紋。

### 建立金鑰保存庫

1. 執行 PowerShell 指令碼 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]，如下所示：

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    當系統提示您輸入認證時，以您稍早建立的 Azure AD 使用者身分登入。指令碼會建立新的資源群組，而該資源群組內會有新的金鑰保存庫。

    注意：針對 -Location 參數，您可以使用下列 PowerShell 命令來取得有效區域清單：

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. 使用下列參數再次執行 SetupKeyVault.ps：

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    其中

    - 金鑰保存庫名稱 = 您在上一個步驟中提供給金鑰保存庫的名稱。
    - Web 應用程式用戶端識別碼 = Surveys Web 應用程式的用戶端識別碼。
    - Web API 用戶端識別碼 = Surveys.WebAPI 應用程式的用戶端識別碼。

    範例：
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] 您可以從 [Azure 管理入口網站][azure-management-portal]取得用戶端識別碼。選取 Azure AD 租用戶、選取應用程式，然後按一下 [設定]。

    此指令碼會授權 Web 應用程式和 Web API 擷取金鑰保存庫中的機密資訊。如需詳細資訊，請參閱[開始使用 Azure 金鑰保存庫][authorize-app]。

### 將組態設定新增至金鑰保存庫

1. 執行 SetupKeyVault.ps，如下所示：

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    其中

    - 金鑰保存庫名稱 = 您在上一個步驟中提供給金鑰保存庫的名稱。
    - Redis DNS 名稱 = Redis 快取執行個體的 DNS 名稱。
    - Redis 存取金鑰 = Redis 快取執行個體的存取金鑰。

    此命令會將密碼新增到金鑰保存庫。密碼是名稱/值組再加上標記：

    -	應用程式不會使用金鑰名稱，但金鑰名稱在金鑰保存庫內必須是唯一的。
    -	值為組態選項的值，在此案例中為 Redis 連接字串。
    -	「ConfigKey」標記則會保存組態金鑰的名稱。

2. 這個階段很適合測試是否已成功將密碼儲存到金鑰保存庫。執行下列 PowerShell 命令：

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    輸出中應該會顯示密碼值加上一些中繼資料：

    ![PowerShell 輸出](media/guidance-multitenant-identity/get-secret.png)

3. 再次執行 SetupKeyVault.ps 以新增資料庫連接字串：

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    其中 `<<DB connection string>>` 是資料庫連接字串的值。

    若要使用本機資料庫進行測試，請複製 Tailspin.Surveys.Web/appsettings.json 檔案中的連接字串。如果您這麼做，請務必將雙反斜線 ('\\\') 變更為單一反斜線。雙反斜線是 JSON 檔案中的逸出字元。

    範例：

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### 取消註解可啟用金鑰保存庫的程式碼

1. 開啟 Tailspin.Surveys 方案。

2. 在 [Tailspin.Surveys.Web/Startup.cs][web-startup] 中，找出下列程式碼區塊並取消註解。

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. 在 [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup] 中，找出下列程式碼區塊並取消註解。

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. 在 [Tailspin.Surveys.Web/Startup.cs][web-startup] 中，找出註冊 `ICredentialService` 的程式碼。取消註解使用 `CertificateCredentialService` 的程式行，並註解化使用 `ClientCredentialService` 的程式行：

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    這項變更可讓 Web 應用程式使用[用戶端判斷提示][client-assertion]來取得 OAuth 存取權杖。使用用戶端判斷提示就不需要 OAuth 用戶端密碼。或者，您可以將用戶端密碼儲存在金鑰保存庫中。不過，金鑰保存庫和用戶端判斷提示都使用用戶端憑證，因此如果您啟用金鑰保存庫，則最好也啟用用戶端判斷提示。

### 更新使用者密碼

在 [方案總管] 中，以右鍵按一下 Tailspin.Surveys.Web 專案，然後選取 [管理使用者密碼]。在 secrets.json 檔案中，刪除現有 JSON 並貼上下列內容：

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

請將 [方括號] 中的項目更換為正確值。

- `AzureAd:ClientId`：Surveys 應用程式的用戶端識別碼。
- `AzureAd:WebApiResourceId`：當您在 Azure AD 建立 Surveys.WebAPI 應用程式時所指定的應用程式識別碼 URI。
- `Asymmetric:CertificateThumbprint`：您先前在建立用戶端憑證時所取得的憑證指紋。
- `KeyVault:Name`：金鑰保存庫的名稱。

> [AZURE.NOTE] 您先前建立的憑證不是由根 CA 授權單位簽署，因此 `Asymmetric:ValidationRequired` 為 false。在生產環境中，使用由 CA 授權單位簽署的憑證，並將 `ValidationRequired` 設為 true。

儲存更新後的 secrets.json 檔案。

接下來，在 [方案總管] 中以滑鼠右鍵按一下 Tailspin.Surveys.WebApi 專案，然後選取 [管理使用者密碼]。刪除現有 JSON 並貼上下列內容：

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

更換 [方括號] 中的項目，並儲存 secrets.json 檔案。

> [AZURE.NOTE] 若為 Web API，請務必使用 Surveys.WebAPI 應用程式 (而非 Surveys 應用程式) 的用戶端識別碼。


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[金鑰標記]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[系列文章的其中一篇]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->