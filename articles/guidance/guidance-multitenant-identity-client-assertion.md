<properties
   pageTitle="透過用戶端判斷提示從 Azure AD 取得存取權杖 | Microsoft Azure"
   description="使用用戶端判斷提示從 Azure AD 取得存取權杖的方式。"
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

# 透過用戶端判斷提示從 Azure AD 取得存取權杖

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

## 背景資訊

在 OpenID Connect 中使用授權碼流程或混合式流程時，用戶端會交換授權碼以取得存取權杖。在此步驟中，用戶端必須向伺服器驗證自身。

![用戶端密碼](media/guidance-multitenant-identity/client-secret.png)

驗證用戶端的一種方法是使用用戶端密碼。這是 [Tailspin Surveys][Surveys] 應用程式預設設定的方式。

以下是從用戶端向 IDP 提出的要求範例，要求存取權杖。請注意 `client_secret` 參數。

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

密碼只是一個字串，因此您必須確保不會洩漏值。最佳做法是防止用戶端密碼進入原始檔控制中。當您部署至 Azure 時，將密碼儲存在[應用程式設定][configure-web-app]中。

不過，任何可存取 Azure 訂用帳戶的使用者，都可以檢視應用程式設定。此外，一定會有嘗試將密碼簽入原始檔控制 (例如簽入部署指令碼中)，透過電子郵件共用它們等等。

為了增加安全性，您可以使用「用戶端判斷提示」來取代用戶端密碼。透過用戶端判斷提示，用戶端使用 X.509 憑證提供來自用戶端的權杖要求。用戶端憑證會安裝在 Web 伺服器上。一般而言，限制憑證的存取會比確保不讓任何人不慎揭露用戶端密碼更為容易。

以下是使用用戶端判斷提示的權杖要求：

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

請注意，不會再使用 `client_secret` 參數。相反地，`client_assertion` 參數包含使用用戶端憑證簽署的 JWT 權杖。`client_assertion_type` 參數會指定判斷提示的類型 &mdash; 在此案例中為 JWT 權杖。伺服器會驗證 JWT 權杖。若 JWT 權杖無效，權杖要求會傳回錯誤。

> [AZURE.NOTE] X.509 憑證並非唯一的用戶端判斷提示格式；我們在此將焦點放在其上，是因為它受 Azure AD 支援。

## 在 Surveys 應用程式中使用用戶端判斷提示

本節說明如何設定 Tailspin Surveys 應用程式以使用用戶端判斷提示。在這些步驟中，您將產生適用於開發的自我簽署憑證，但不適用於生產環境使用。

1. 執行 PowerShell 指令碼 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]，如下所示：

    ```
    .\Setup-KeyVault.ps -Subject [subject]
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

3. 執行下列命令取得憑證的指紋。

    ```
    certutil -store -user my [subject]
    ```

    其中 `[subject]` 是您在 PowerShell 指令碼中為 Subject 所指定的值。指紋會列在「Cert Hash(sha1)」之下。移除十六進位數字之間的空格。

4. 更新應用程式密碼。在 [方案總管] 中，以右鍵按一下 Tailspin.Surveys.Web 專案，然後選取 [管理使用者密碼]。在「AzureAd」底下加入「Asymmetric」項目，如下所示：

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    您必須將 `ValidationRequired` 設為 false，因為憑證不是由根 CA 授權單位簽署。在生產環境中，使用由 CA 授權單位簽署的憑證，並將 `ValidationRequired` 設為 true。

    另外請刪除 `ClientSecret` 項目，因為用戶端判斷提示不需要使用它。

5. 在 Startup.cs 中，找出註冊 `ICredentialService` 的程式碼。取消註解使用 `CertificateCredentialService` 的程式行，並註解化使用 `ClientCredentialService` 的程式行：

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

在執行時，Web 應用程式會從憑證存放區讀取憑證。憑證必須與 Web 應用程式安裝在相同的電腦上。

## 其他資源

- [在 Azure 網站應用程式中使用憑證][using-certs-in-websites]
- [RFC 7521][RFC7521]。定義傳送用戶端判斷提示的一般機制。
- [RFC 7523][RFC7523]。定義針對用戶端判斷提示使用 JWT 權杖的方式。


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[系列文章的其中一篇]: guidance-multitenant-identity.md
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->