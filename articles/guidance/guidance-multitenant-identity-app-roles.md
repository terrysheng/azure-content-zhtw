<properties
   pageTitle="應用程式角色 | Microsoft Azure"
   description="如何使用應用程式角色執行授權"
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

#  在多組織用戶共享應用程式中的應用程式角色

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

應用程式角色是用來指派權限給使用者。例如，[Tailspin Surveys][Tailspin] 應用程式會定義下列角色：

- 系統管理員。可在所有屬於該租用戶的問卷上執行所有 CRUD 作業。
- 建立者。可以建立新的問卷。
- 讀取者。可以讀取任何屬於該租用戶的問卷。

您可以看出角色最終會在[授權]期間轉譯成權限。但是第一個問題是如何指派及管理角色。我們識別出三個主要選項：

-	[Azure AD 應用程式角色](#roles-using-azure-ad-app-roles)
-	[Azure AD 安全性群組](#roles-using-azure-ad-security-groups)
-	[應用程式角色管理員](#roles-using-an-application-role-manager)。

## 使用 Azure AD 應用程式角色的角色

這是我們在 Tailspin Surveys 應用程式中使用的方法。

在這個方法中，SaaS 提供者透過將應用程式角色加入應用程式資訊清單來定義它們。客戶註冊之後，客戶所屬 AD 目錄的系統管理員會將使用者指派至角色。當使用者登入時，指派給使用者的角色會以宣告方式傳送。

> [AZURE.NOTE] 如果客戶有 Azure AD Premium，系統管理員可以將安全性群組指派給角色，而且群組的成員將會繼承應用程式角色。這是很方便的角色管理方式，因為群組擁有者不需要是 AD 系統管理員。

此方法的優點：

-	簡單的程式設計模型。
-	角色是應用程式特定的。針對某一應用程式的角色宣告不會傳送至其他應用程式。
-	如果客戶將應用程式從他們的 AD 租用戶移除，角色就會消失。
-	除了讀取使用者的設定檔之外，應用程式不需要任何額外的 Active Directory 權限。

缺點：

- 沒有 Azure AD Premium 的客戶無法指派安全性群組至角色。對於這些客戶，所有的使用者指派都必須由 AD 系統管理員進行。
- 如果您有與 Web 應用程式分離的後端 Web API，針對 Web 應用程式的角色指派不會套用至 Web API。如需這方面的詳細討論，請參閱[保護後端 Web API]。

### 實作

**定義角色。** SaaS 提供者在應用程式資訊清單中宣告應用程式角色。例如，以下是 Surveys 應用程式的資訊清單項目：

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

`value` 屬性出現在角色宣告中。`id` 屬性是已定義角色的唯一識別碼。一律會產生新的 GUID 值做為 `id`。

**指派使用者**。當新的客戶註冊時，該應用程式會在客戶的 AD 租用戶中註冊。此時，該租用戶的 AD 系統管理員可以指派使用者至角色。

> [AZURE.NOTE] 如稍早前所述，有 Azure AD Premium 的客戶也可以指派安全性群組至角色。

下列 Azure 入口網站的螢幕擷取畫面顯示三位使用者。Alice 已直接指派至角色。Bob 是稱為「Surveys Admin」的安全性群組 (已指派至角色) 的成員，因此繼承了角色。Charles 未指派至任何角色。

![已指派的使用者](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] 或者，應用程式可使用 Azure AD Graph API 來以程式設計的方式指派角色。不過，這需要應用程式取得客戶的 AD 目錄的寫入權限。擁有這類權限的應用程式能進行多種造成危害的行為，客戶必須信任該應用程式不會擾亂他們的目錄。許多客戶可能不願意授予此層級的存取權。

**取得角色宣告**。當使用者登入時，應用程式會在宣告中收到類型為 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 的使用者指派角色。

使用者可以擁有多個角色，或沒有角色。在授權程式碼中，請不要假設使用者僅有一個角色宣告。請改為撰寫會檢查是否有特定宣告值的程式碼：

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## 使用 Azure AD 安全性群組的角色

在此方法中，角色會以 AD 安全性群組呈現。應用程式會根據使用者的安全性群組成員資格來將權限指派至使用者。

優點：

-	對於沒有 Azure AD Premium 的客戶，此方法可讓客戶使用安全性群組來管理角色指派。

缺點：

- 複雜度。由於每個租用戶會傳送不同的群組宣告，因此應用程式必須針對每個租用戶持續追蹤安全性群組所對應的應用程式角色。
- 如果客戶將應用程式從其 AD 租用戶中移除，則安全性群組會留在其 AD 目錄中。

### 實作

在應用程式資訊清單中，將 `groupMembershipClaims` 屬性設為 "SecurityGroup"。這樣才能從 AAD 取得群組成員資格宣告。

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

當新的客戶註冊時，應用程式會指示客戶為應用程式所需的角色建立安全性群組。客戶接著需要將群組物件識別碼輸入應用程式。應用程式會依據租用戶將這些識別碼儲存在將群組識別碼對應至應用程式角色的資料表中。

> [AZURE.NOTE] 或者，應用程式可使用 Azure AD Graph API 來以程式設計的方式建立群組。這樣比較不會出錯。不過，這需要應用程式取得客戶的 AD 目錄的「讀取及寫入所有群組」權限。許多客戶可能不願意授予此層級的存取權。

當使用者登入時：

1.	應用程式會以宣告方式收到使用者的群組。每個宣告的值為群組的物件識別碼。
2.	Azure AD 會限制權杖中傳送之群組的數目。如果群組數目超過此限制，Azure AD 會傳送特殊的「超額」宣告。如果出現該宣告，應用程式就必須查詢 Azure AD Graph API 來取得該使用者所屬的所有群組。如需詳細資訊，請參閱 [Authorization in Cloud Applications using AD Groups (在雲端應用程式中使用 AD 群組的授權)] 中的 "Groups claim overage" (群組宣告超額) 一節。
3.	應用程式會在自己的資料庫中查閱物件識別碼，以尋找所對應要指派至使用者的應用程式角色
4.	應用程式會將自訂的宣告值加入至表示應用程式角色的使用者主體。例如：`survey_role` = "SurveyAdmin"。

授權原則應使用自訂的角色宣告，而不是群組宣告。

## 使用應用程式角色管理員的角色

使用此方法時，應用程式角色不會儲存在 Azure AD 中。相反地，應用程式會將每個使用者的角色指派儲存在自己的資料庫 &mdash; 例如，使用 ASP.NET 身分識別中的 **RoleManager** 類別。

優點：

-	應用程式完全控制角色和使用者的指派。

缺點：

- 更複雜、較難維護。
- 無法使用 AD 安全性群組管理角色指派。
- 將使用者資訊儲存在應用程式資料庫，當新增或移除使用者時，資料庫可能不會與租用戶的 AD 目錄同步。   

這種方法有許多現有的範例。例如，請參閱[使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service]。


## 其他資源

-	[Authorization in a web app using Azure AD application roles & role claims (Web 應用程式中使用 Azure AD 應用程式角色與角色宣告的授權)]
-	[Authorization in Cloud Applications using AD Groups (雲端應用程式中使用 AD 群組的授權)]
-	[Roles based access control in cloud applications using Azure AD (雲端應用城市中使用 Azure AD 的角色型存取控制)]
-	[支援的權杖和宣告類型]。說明在 Azure AD 中的角色和群組宣告。
-	[了解 Azure Active Directory 應用程式資訊清單]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[系列文章的其中一篇]: guidance-multitenant-identity.md
[授權]: guidance-multitenant-identity-authorize.md
[保護後端 Web API]: guidance-multitenant-identity-web-api.md
[Authorization in Cloud Applications using AD Groups (在雲端應用程式中使用 AD 群組的授權)]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[Authorization in Cloud Applications using AD Groups (雲端應用程式中使用 AD 群組的授權)]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[了解 Azure Active Directory 應用程式資訊清單]: ../active-directory/active-directory-application-manifest.md
[支援的權杖和宣告類型]: ../active-directory/active-directory-token-and-claims.md
[Roles based access control in cloud applications using Azure AD (雲端應用城市中使用 Azure AD 的角色型存取控制)]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[Authorization in a web app using Azure AD application roles & role claims (Web 應用程式中使用 Azure AD 應用程式角色與角色宣告的授權)]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-------->