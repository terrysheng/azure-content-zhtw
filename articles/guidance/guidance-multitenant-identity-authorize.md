<properties
   pageTitle="多組織用戶共享應用程式中的授權 | Microsoft Azure"
   description="如何在多組織用戶共享應用程式中執行授權"
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

# 多組織用戶共享應用程式中的授權

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

本文中我們將說明兩種常見的授權方法。

-	**以角色為基礎的授權**根據指派給使用者的角色授權動作。例如，某些動作需要系統管理員角色。
-	**以資源為基礎的授權**。根據特定的資源授權動作。例如，每個資源都有一個擁有者。擁有者可以刪除該資源；其他使用者則不行。

一般的應用程式會採用兩種的混合。例如，若要刪除資源，使用者必須是資源擁有者「或」系統管理員。

## 以角色為基礎的授權

[Tailspin Surveys][Tailspin] 應用程式會定義下列角色：

- 系統管理員。可在所有屬於該租用戶的問卷上執行所有 CRUD 作業。
- 建立者。可以建立新的問卷
- 讀取者。可以讀取任何屬於該租用戶的問卷

套用至應用程式「使用者」的規則。在 Surveys 應用程式中，使用者可能是系統管理員、建立者或讀取者其中之一。

如需如何定義及管理角色的討論，請參閱[應用程式角色]。

不論您如何管理角色，您的授權程式碼看起來都會很相似。ASP.NET Core 1.0 導入稱為「授權原則」的抽象概念。使用此功能，您會使用程式碼定義授權原則，並將那些原則套用至控制器動作。該原則已從控制器分離出來。

### 建立原則

若要定義原則，請先建立實作 `IAuthorizationRequirement` 的類別。從 `AuthorizationHandler` 衍生是最簡單的方式。在 `Handle` 方法中，檢查相關的宣告。

以下是來自 Tailspin Surveys 應用程式的範例：

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] 請參閱 [SurveyCreatorRequirement.cs]

這個類別會定義使用者建立新問卷的需求。使用者必須是 SurveyAdmin 或 SurveyCreator 角色。

在您的啟動類別中，定義包含一或多個需求的具名原則。如果有多個需求，使用者必須符合「每個」需求才能獲得授權。下列程式碼定義兩個原則：

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] 請參閱 [Startup.cs]

此程式碼也會設定驗證配置，它會告訴 ASP.NET 如果授權失敗時應執行哪一個驗證中介軟體。在此情況下，我們指定 Cookie 驗證中介軟體，因為 cookie 驗證中介軟體可將使用者重新導向至「禁止」頁面。[禁止] 頁面的位置會設定在 Cookie 中介軟體的 AccessDeniedPath 選項中，請參閱 [Configuring the authentication middleware (設定驗證中介軟體)]。

### 授權控制器動作

最後，若要在 MVC 控制器中授權動作，請在 `Authorize` 屬性中設定原則：

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

在舊版的 ASP.NET 中，您會在該屬性上設定 **Roles** 屬性：

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

ASP.NET Core 1.0 中仍支援這樣設定，但相較於授權原則它有一些缺點：

-	它會假設訂定的宣告類型。原則可以檢查任何宣告類型。角色只是宣告的一種類型。
-	角色名稱是以硬式編碼寫在屬性中。若使用原則，授權原則會全部在同一個位置，讓更新或甚至從組態設定載入變得更容易。
-	原則可支援簡單的角色成員資格無法表示的更複雜授權決策 (例如，年齡 >= 21)。

## 以資源為基礎的授權。

每當授權需依賴將受作業影響的特定資源時，就會發生_以資源為基礎的授權_。在 Tailspin Surveys 應用程式中，每個問卷都有一個擁有者，以及零至多個參與者。

-	擁有者可以讀取、更新、刪除、發佈及取消發佈問卷。
-	擁有者可將參與者指派至問卷。
-	參與者可以讀取及更新問卷。

請留意，「擁有者」和「參與者」不是應用程式角色；它們按每個問卷儲存在應用程式資料庫中。例如，若要查看使用者是否可以刪除問卷，應用程式會檢查該使用者是否為問卷的擁有者。

在 ASP.NET Core 1.0 中，可透過從 **AuthorizationHandler** 衍生和覆寫 **Handle** 方法來實作以資源為基礎的授權。

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

請注意，此類別是 Survey 物件的強型別。在啟動時註冊 DI 的類別：

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

若要執行授權檢查，請使用 **IAuthorizationService** 介面，您可以將它插入您的控制器。下列程式碼會檢查使用者是否可以讀取問卷：

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

因為我們傳入 `Survey` 物件，所以此呼叫將會叫用 `SurveyAuthorizationHandler`。

在您的授權程式碼中，理想的方法是彙總所有使用者以角色為基礎和以資源為基礎的權限，然後針對要執行的作業查看彙總集合。以下是來自 Surveys 應用程式的範例。該應用程式定義數個權限類型：

- Admin
- 參與者
- [建立者]
- 擁有者
- 讀取者

該應用程式也定義一組可能對問卷進行的作業：

- 建立
- 讀取
- 更新
- 刪除
- Publish
- Unpublsh

下列程式碼會針對特定使用者和問卷建立權限清單。請注意，此程式碼會同時察看使用者的應用程式角色，以及問卷中的 owner/contributor 欄位。

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] 請參閱 [SurveyAuthorizationHandler.cs]。

在多租用戶應用程式中，您必須確定權限不會「洩漏」至其他租用戶的資料。在 Surveys 應用程式中，Contributor 權限能夠跨租用戶 &mdash; 您可以將其他租用戶中的某個人指派為為參與者。其他權限受限於該使用者所屬的租用戶，因此程式碼會在授予那些權限類型之前檢查租用戶識別碼。(即問卷建立時指派的 `TenantId` 欄位。)

下一步是針對作業 (讀取、更新、刪除等) 檢查權限。Surveys 應用程式使用函式的查閱資料表來實作此步驟：

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## 其他資源

- [以資源為基礎的授權](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [以自訂原則為基礎的授權](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[系列文章的其中一篇]: guidance-multitenant-identity.md
[應用程式角色]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configuring the authentication middleware (設定驗證中介軟體)]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-------->