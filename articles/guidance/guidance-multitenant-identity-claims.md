<properties
   pageTitle="在多組織用戶共享應用程式中使用宣告式身分識別 | Microsoft Azure"
   description="如何使用宣告進行簽發者驗證及授權"
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

# 在多組織用戶共享應用程式中使用宣告式身分識別

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

## 在 Azure AD 中的宣告

當使用者登入時，Azure AD 會傳送識別碼權杖，其中包含一組關於使用者的宣告。宣告只是一項資訊，以索引鍵/值組表示。例如，`email`=`bob@contoso.com`。宣告會有簽發者 &mdash; 在此案例中為 Azure AD &mdash; 其為驗證使用者並建立宣告的實體。因為您信任簽發者，所以您信任宣告。(相反地，如果您不信任簽發者，則請勿信任宣告！)

概括而言：

1.	使用者驗證。
2.	IDP 傳送一組宣告。
3.	應用程式標準化或加強宣告 (選擇性)。
4.	應用程式使用宣告來做出授權決策。

在 OpenID Connect 中，您取得的一組宣告是由驗證要求的[範圍參數]所控制。不過，Azure AD 會透過 OpenID Connect 發出一組有限的宣告；請參閱[支援的權杖和宣告類型]。如果您想要使用者的詳細資訊，您必須使用 Azure AD Graph API。

以下是應用程式通常可能會關注的一些 AAD 宣告：

識別碼權杖中的宣告類型 |	說明
-----------------------|--------------
aud | 權杖發行的對象。這會是應用程式的用戶端識別碼。一般而言，您應該不需要擔心這個宣告，因為中介軟體會自動驗證。範例：`"91464657-d17a-4327-91f3-2ed99386406f"`
groups | AAD 群組清單，使用者為其中的成員。範例：`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss | OIDC 權杖的[簽發者]。範例：`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
名稱 | 使用者的顯示名稱。範例：`"Alice A."`
oid | AAD 中使用者的物件識別碼。這個值是使用者的識別碼，不可變且無法重複使用。使用這個值，而非電子郵件，來做為唯一的使用者識別碼；電子郵件地址可以變更。如果您在應用程式中使用 Azure AD Graph API，物件識別碼是用於查詢設定檔資訊的值。範例：`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
角色 | 使用者的應用程式角色清單。範例：`["SurveyCreator"]`
tid | 租用戶識別碼。這個值是 Azure AD 中租用戶的唯一識別碼。範例：`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique\_name | 人類可讀的使用者顯示名稱。範例：`"alice@contoso.com"`
upn | 使用者主體名稱。範例：`"alice@contoso.com"`

下表列出識別碼權杖中出現的宣告類型。在 ASP.NET Core 1.0 中，OpenID Connect 中介軟體會在填入使用者主體的宣告集合時，轉換部分宣告類型：

-	oid > `http://schemas.microsoft.com/identity/claims/objectidentifier`
-	tid > `http://schemas.microsoft.com/identity/claims/tenantid`
-	unique\_name > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-	upn > `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## 宣告轉換

驗證流程期間，您可能會想要修改從 IDP 取得的宣告。在 ASP.NET Core 1.0 中，您可以從 OpenID Connect 中介軟體，在 **AuthenticationValidated** 事件內部中執行宣告轉換。(請參閱[驗證事件]。)

您在 **AuthenticationValidated** 之中新增的任何宣告，會儲存在工作階段驗證 Cookie 中。它們不會推送回 Azure AD。

以下是一些宣告轉換的範例：

-	**宣告標準化**，或讓宣告在使用者之間一致。如果您正從多個 IDP 取得宣告，這特別有關，其中可能會針對類似的資訊使用不同的宣告類型。例如，Azure AD 會傳送「upn」宣告，其中包含使用者的電子郵件。其他 IDP 可能會傳送「email」宣告。下列程式碼會將「upn」宣告轉換為「email」宣告：

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- 針對不存在的宣告新增**預設宣告值** &mdash; 例如，將使用者指派到預設角色。在某些情況下，這可以簡化授權邏輯。
- 使用關於使用者的應用程式特定資訊，新增**自訂宣告類型**。例如，您可能會在資料庫中儲存關於使用者的某些資訊。您可以搭配這項資訊，將自訂宣告新增至驗證票證。宣告會儲存在 Cookie 中，所以您只需在每個登入工作階段，從資料庫中取得一次。另一方面，您也會想要避免建立過大的 Cookie，所以您必須考量 Cookie 大小與資料庫查閱之間的取捨。   

驗證流程完成之後，宣告即可在 `HttpContext.User` 中提供。此時，您應該將它們視為唯讀的集合 &mdash; 例如，使用它們來做出授權決策。

## 簽發者驗證
在 OpenID Connect 中，簽發者宣告 ("iss") 會識別發行識別碼權杖的 IDP。OIDC 驗證流程的其中一部份，是驗證簽發者宣告是否符合實際的簽發者。OIDC 中介軟體會為您處理這部分。

在 Azure AD 中，每個 AD 租用戶的簽發者值是唯一的 (`https://sts.windows.net/<tenantID>`)。因此，應用程式應該執行額外的檢查，確保簽發者代表的租用戶允許登入應用程式。

針對單一租用戶應用程式，您可以只檢查簽發者是否為您擁有的租用戶。實際上，OIDC 中介軟體預設會自動執行此動作。在多租用戶應用程式中，您需要允許多個簽發者，對應到不同的租用戶。以下是常用的方法：

-	在 OIDC 中介軟體選項中，將 **ValidateIssuer** 設為 false。這會關閉自動檢查。
-	當租用戶註冊時，會將租用戶及簽發者儲存到您的使用者 DB 中。
-	每當使用者登入時，便會查閱資料庫中的簽發者。如果找不到簽發者，則表示該租用戶尚未註冊。您可以將他們重新導向至註冊頁面。
-  您也可以將特定租用戶設為黑名單；例如，沒有為他們的訂用帳戶付費的客戶。

如需詳細的討論，請參閱[Sign-up and tenant onboarding (註冊和租用戶上架)]。

## 使用宣告進行授權

使用宣告，使用者的身分識別就不再是單體式實體。例如，使用者可能會有電子郵件地址、電話號碼、生日、性別等等。也可能使用者的 IDP 儲存了所有的這類資訊。但當您驗證使用者時，您通常只會取得這些資訊的子集做為宣告。在此模型中，使用者的身分識別只是宣告的組合。當您進行有關使用者的授權決策時，您將會尋找特定的宣告集。換句話說，「使用者 X 是否可執行動作 Y」的問題最後會變成「使用者 X 是否有宣告 Z」。

以下是一些檢查宣告的基本模式。

-  若要檢查使用者是否有含有特定值的特定宣告：

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    此程式碼會檢查使用者是否有含有「Admin」的角色宣告。它會正確地處理使用者沒有角色宣告，或有多個角色宣告的情況。

    **ClaimTypes** 類別定義常用宣告類型的常數。不過，您可以使用任何字串值做為宣告類型。

-	當您預期最多只有一個值時，若要取得宣告類型的單一值：
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-	取得宣告類型的所有值：

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

如需在授權中使用宣告的詳細資訊，請參閱[授權]。

## 其他資源

- [宣告型授權]


<!-- Links -->
[系列文章的其中一篇]: guidance-multitenant-identity.md
[範圍參數]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[支援的權杖和宣告類型]: ../active-directory/active-directory-token-and-claims.md
[簽發者]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[驗證事件]: guidance-multitenant-identity-authenticate.md#authentication-events
[Sign-up and tenant onboarding (註冊和租用戶上架)]: guidance-multitenant-identity-signup.md
[授權]: guidance-multitenant-identity-authorize.md
[宣告型授權]: https://docs.asp.net/en/latest/security/authorization/claims.html
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-------->