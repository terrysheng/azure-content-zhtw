<properties
   pageTitle="在多租用戶應用程式中驗證 | Microsoft Azure"
   description="多租用戶應用程式如何才能從 Azure AD 驗證使用者"
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

# 在多租用戶應用程式中驗證

本文是[系列文章的其中一篇](guidance-multitenant-identity.md)。這一系列另外還附有完整的[範例應用程式]。

本文說明多租用戶應用程式如何使用 OpenID Connect (OIDC) 才能從 Azure Active Directory (Azure AD) 驗證使用者。

## 概觀

我們的[參考實作](guidance-multitenant-identity-tailspin.md)是 ASP.NET Core 1.0 應用程式。此應用程式會使用內建的 OpenID Connect 中介軟體來執行 OIDC 驗證流程。下圖概括顯示使用者登入時會發生什麼情況。

![驗證流程](media/guidance-multitenant-identity/auth-flow.png)

1.	使用者在應用程式中按一下 [登入] 按鈕。這個動作是由 MVC 控制器處理。
2.	MVC 控制器會傳回 **ChallengeResult** 動作。
3.	中介軟體會攔截 **ChallengeResult** 並建立 302 回應，進而將使用者重新導向至 Azure AD 登入頁面。
4.	使用者會向 Azure AD 進行驗證。
5.	Azure AD 會將 ID 權杖傳送至應用程式。
6.	中介軟體會驗證 ID 權杖。此時，使用者已在應用程式內驗證。
7.	中介軟體會將使用者重新導向回到應用程式。

## 向 Azure AD 註冊應用程式

若要啟用 OpenID Connect，SaaS 提供者會在自己的 Azure AD 租用戶內註冊應用程式。

若要註冊應用程式，請依照「在 Azure 管理入口網站中註冊應用程式」一節中[整合應用程式與 Azure Active Directory](../active-directory/active-directory-integrating-applications.md) 中的步驟執行。

在 [設定] 頁面中：

-	請記下用戶端識別碼。
-	在 [應用程式為多租用戶] 之下，選取 [是]。
-	將 [回覆 URL] 設定為 Azure AD 將傳送驗證回應的 URL。您可以使用您的應用程式的基底 URL。
  -	注意：URL 路徑可以是任何項目，只要主機名稱符合已部署的應用程式即可。
  -	您可以設定多個回覆 URL。在開發期間，您可以使用 `localhost` 位址在本機執行應用程式。
-	產生用戶端密碼：在 [金鑰] 底下，按一下 [選取持續時間] 下拉式清單並挑選 1 或 2 年。當您按一下 [儲存]，就會看見金鑰。請務必複製此值，因為在您重新載入組態頁面時不會再次顯示該值。

## 設定驗證中介軟體

本節說明如何在 ASP.NET Core 1.0 中設定驗證中介軟體，以便利用 OpenID Connect 進行多租用戶驗證。

在您的啟動類別中加入 OpenID Connect 中介軟體：

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] 請參閱 [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs)。

如需啟動類別的詳細資訊，請參閱 ASP.NET Core 1.0 文件中的[應用程式啟動](https://docs.asp.net/en/latest/fundamentals/startup.html)。

設定下列中介軟體選項：

- **ClientId**。當您在 Azure AD 中註冊應用程式時所得到之應用程式的用戶端識別碼。
- **Authority**。對於多租用戶應用程式，將此選項設定為 `https://login.microsoftonline.com/common/`。這是 Azure AD 一般端點的 URL，可讓任何 Azure AD 租用戶的使用者進行登入。如需一般端點的詳細資訊，請參閱[此部落格文章](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/)。
- 在 **TokenValidationParameters**，將 **ValidateIssuer** 設為 false。這表示應用程式會負責驗證 ID 權杖中的簽發者值(中介軟體仍會驗證權杖本身)。 如需驗證簽發者的詳細資訊，請參閱[簽發者驗證](guidance-multitenant-identity-claims.md#issuer-validation)。
- **CallbackPath**。將此選項設定為您在 Azure AD 中註冊的 [回覆 URL] 中的路徑。例如，如果回覆 URL 是 `http://contoso.com/aadsignin`，**CallbackPath** 應該是 `aadsignin`。如果您未設定此選項，預設值為 `signin-oidc`。
- **PostLogoutRedirectUri**。指定要在登出後重新導向使用者的 URL。這應該是允許匿名要求的頁面 &mdash; 通常是首頁。
- **SignInScheme**。將此選項設定為 `CookieAuthenticationDefaults.AuthenticationScheme`。此設定表示在驗證使用者之後，使用者宣告就會儲存在本機在 Cookie 中。此 Cookie 是使用者在瀏覽器工作階段期間保持已登入狀態的方法。
- **事件。** 事件回呼；請參閱[驗證事件](#authentication-events)。

此外，將 Cookie 驗證中介軟體新增至管線。此中介軟體負責將使用者宣告寫入至 Cookie，然後在後續頁面載入期間讀取 Cookie。

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## 起始驗證流程

若要在 ASP.NET MVC 中開始驗證流程，請從控制器傳回 **ChallengeResult**：

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

這會導致中介軟體傳回可重新導向至驗證端點的 302 (找到) 回應。

## 使用者登入工作階段

如上所述，當使用者第一次登入時，Cookie 驗證中介軟體會將使用者宣告寫入至 Cookie。之後，系統會藉由讀取 Cookie 來驗證 HTTP 要求。

依預設，Cookie 中介軟體會寫入 [工作階段 Cookie][session-cookie]，該 Cookie 會在使用者關閉瀏覽器時立即遭到刪除。使用者下次瀏覽網站時，則必須再次登入。不過，如果您在 **ChallengeResult** 中將 **IsPersistent** 設定為 true，則中介軟體會寫入持續性 Cookie，以便使用者在關閉瀏覽器後保持已登入狀態。您可以設定 Cookie 到期日；請參閱[控制 Cookie 選項][cookie-options]。持續性 Cookie 對使用者而言比較方便，但可能不適用於您希望使用者每次登入的一些應用程式 (例如，銀行應用程式)。

## 關於 OpenID Connect 中介軟體

ASP.NET 中的 OpenID Connect 中介軟體會隱藏大部分的通訊協定詳細資料。本節包含一些有關實作的注意事項，這對於了解通訊協定流程很有幫助。

首先，讓我們從 ASP.NET 的角度 (忽略應用程式與 Azure AD 之間的 OIDC 通訊協定流程細節) 檢查驗證流程。下圖顯示此程序。

![登入流程](media/guidance-multitenant-identity/sign-in-flow.png)

在此圖中，有兩個 MVC 控制器。帳戶控制器會處理登入要求，而首頁控制器會為首頁提供服務。

以下是驗證程序：

1. 使用者按一下「登入」按鈕，而瀏覽器會傳送 GET 要求。例如：`GET /Account/SignIn/`。
2. 帳戶控制器會傳回 `ChallengeResult`。
3. OIDC 中介軟體會傳回 HTTP 302 回應，並重新導向至 Azure AD。
4. 瀏覽器會將驗證要求傳送至 Azure AD
5. 使用者登入 Azure AD，而 Azure AD 會送回驗證回應。
6. OIDC 中介軟體會建立宣告主體，並將它傳遞給 Cookie 驗證中介軟體。
7. Cookie 中介軟體會將宣告主體序列化並設定 Cookie。
8. OIDC 中介軟體會重新導向至應用程式的回呼 URL。
10. 瀏覽器會遵循重新導向，並在要求中傳送 Cookie。
11. Cookie 中介軟體會將此 Cookie 還原序列化為宣告主體，並設定 `HttpContext.User` 等於宣告主體。要求會路由傳送至 MVC 控制器。

### 驗證票證

如果驗證成功，OIDC 中介軟體會建立驗證票證，其中包含保存使用者宣告的宣告主體。您可以在 **AuthenticationValidated** 或 **TicketReceived** 事件內存取此票證。

> [AZURE.NOTE] 直到整個驗證流程完成時，`HttpContext.User` 仍會保存匿名主體，而不是已驗證的使用者。匿名主體具有空的宣告集合。在驗證完成且應用程式重新導向之後，Cookie 中介軟體將驗證 Cookie 還原序列化並將 `HttpContext.User` 設定為代表已驗證使用者的宣告主體。

### 驗證事件

在驗證過程中，OpenID Connect 中介軟體會引發一連串的事件：

- **RedirectToAuthenticationEndpoint**。在中介軟體重新導向至驗證端點之前進行呼叫。您可以使用此事件來修改重新導向 URL。例如，加入要求參數。請參閱[新增系統管理員同意提示](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt)中的範例。

- **AuthorizationResponseReceived**。在中介軟體收到來自識別提供者 (IDP) 的驗證回應之後，但在中介軟體驗證此回應之前呼叫。

- **AuthorizationCodeReceived**。使用授權碼來呼叫。

- **TokenResponseReceived**。在中介軟體從 IDP 取得存取權杖之後呼叫。僅適用於授權碼流程。

- **AuthenticationValidated**。在中介軟體驗證 ID 權杖之後呼叫。此時，應用程式有一組關於使用者的已驗證宣告。您可以使用此事件對宣告執行額外的驗證，或轉換宣告。請參閱[使用宣告](guidance-multitenant-identity-claims.md)。

- **UserInformationReceived**。如果中介軟體從使用者資訊端點取得使用者設定檔，則會呼叫。僅適用於授權碼流程，而且只有在中介軟體選項中的 `GetClaimsFromUserInfoEndpoint = true` 時使用。

- **TicketReceived**。在驗證完成時呼叫。這是最後一個事件，並假設驗證成功。在此事件處理之後，使用者就會登入應用程式。

- **AuthenticationFailed**。如果驗證失敗時，則會呼叫。使用此事件來處理驗證失敗 &mdash; 例如，藉由重新導向至錯誤頁面。

若要提供這些事件的回呼，請在中介軟體上設定 [事件] 選項。有兩種方式可宣告事件處理常式：使用 Lambda 內嵌，或在衍生自 **OpenIdConnectEvents** 的類別中。

使用 Lambda 內嵌：

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

衍生自 **OpenIdConnectEvents**：

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

如果事件回呼有任何實質邏輯，則建議使用第二種方法，如此才不會擾亂您的啟動類別。我們的參考實作會使用這種方法；請參閱 [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs)。

### OpenID Connect 端點

Azure AD 支援 [OpenID Connect 探索](https://openid.net/specs/openid-connect-discovery-1_0.html)，其中識別提供者 (IDP) 會從 [知名的端點](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)傳回 JSON 中繼資料文件。中繼資料文件包含的資訊如下：

-	授權端點的 URL。這是應用程式重新導向來驗證使用者的位置。
-	「結束工作階段」端點的 URL，也就是應用程式即將登出使用者的位置。
-	用以取得簽署金鑰的 URL，而用戶端會使用這些金鑰來驗證它從 IDP 取得的 OIDC 權杖。

根據預設，OIDC 中介軟體會知道如何擷取此中繼資料。在中介軟體中設定 [Authority] 選項，而中介軟體會建構中繼資料的 URL。(您可藉由設定 **MetadataAddress** 選項來覆寫中繼資料 URL)。

### OpenID Connect 流程

根據預設，OIDC 中介軟體會使用混合式流程搭配表單 POST 回應模式。

-	「混合式流程」表示用戶端可以在同一回往返存取授權伺服器時取得 ID 權杖和授權碼。
-	「表單 POST 回應模式」表示授權伺服器會使用 HTTP POST 要求將 ID 權杖和授權碼傳送至應用程式。這些值會使用表單 URL 方式進行編碼 (content type = "application/x-www-form-urlencoded")。

當 OIDC 中介軟體重新導向至授權端點時，重新導向 URL 會包含 OIDC 所需的所有查詢字串參數。混合式流程：

-	client\_id。這個值設定於 **ClientId** 選項
-	scope = "openid profile"，這表示它是 OIDC 要求而且我們想要使用者的設定檔。
-	response\_type = "code id\_token"。這會指定混合式流程。
-	response\_mode = "form\_post"。這會指定表單 POST 回應。

若要指定不同的流程，請設定選項的 **ResponseType** 屬性。例如：

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-------->