
<properties
	pageTitle="Azure AD v2.0 OpenID Connect 通訊協定 | Microsoft Azure"
	description="使用 Azure AD v2.0 的 OpenID Connect 驗證通訊協定實作來建置 Web 應用程式。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# v2.0 通訊協定 - OpenID Connect
OpenID Connect 是建置在 OAuth 2.0 的驗證通訊協定之上，可用來將使用者安全地登入 Web 應用程式。使用 v2.0 端點實作 OpenID Connect，您可以將登入及 API 存取新增至您 Web 架構的應用程式中 。本指南以不考慮語言的方式來示範如何這樣做，描述在不使用我們的任何開放原始碼程式庫的情況下，如何傳送和接收 HTTP 訊息。

> [AZURE.NOTE]
	v2.0 端點並未支援所有的 Azure Active Directory 案例和功能。若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。
    
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 擴充 OAuth 2.0「授權」**通訊協定，可作為「驗證」通訊協定，讓您使用 OAuth 執行單一登入。它引進 `id_token` 的概念，這是一種安全性權杖，可讓用戶端驗證使用者的身分識別，並取得有關使用者的基本設定檔資訊。因為它擴充 OAuth 2.0，所以也可讓應用程式安全地取得 **access\_tokens**，而這些權杖可用於存取[授權伺服器](active-directory-v2-protocols.md#the-basics)所保護的資源。如果您要建置的 [Web 應用程式](active-directory-v2-flows.md#web-apps)是裝載於伺服器且透過瀏覽器存取，建議使用 OpenID Connect。

## 通訊協定圖表 - 登入
最基本的登入流程包含下列步驟 - 以下將詳細說明每一個步驟。

![OpenId Connect 區隔線](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## 傳送登入要求
當您的 Web 應用程式需要驗證使用者時，其可以將使用者導向至 `/authorize` 端點。這個要求類似 [OAuth 2.0 授權碼流程](active-directory-v2-protocols-oauth-code.md)的第一個階段，有幾個重要的區別：

- 要求必須在 `scope` 參數中包含範圍 `openid`。
- `response_type` 參數必須包含 `id_token`
- 要求必須包含 `nonce` 參數


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [AZURE.TIP] 請嘗試將下列項目貼至網頁瀏覽器！

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=form_post&state=12345&nonce=678910
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 必要 | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。如需更多詳細資訊，請參閱[通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| client\_id | 必要 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| response\_type | 必要 | 必須包含 OpenID Connect 登入的 `id_token`。它也可能包含其他 response\_types，例如 `code`。 |
| redirect\_uri | 建議使用 | 應用程式的 redirect\_uri，您的應用程式可在此傳送及接收驗證回應。其必須完全符合您在入口網站中註冊的其中一個 redirect\_uris，不然就必須得是編碼的 url。 |
| scope | 必要 | 範圍的空格分隔清單。針對 OpenID Connect，即必須包含範圍 `openid`，其會在同意 UI 中轉譯成「讓您登入」權限。您也可以在此要求中包含其他範圍以要求同意。 |
| nonce | 必要 | 由應用程式產生且包含在要求中的值，會以宣告方式包含在產生的 id\_token 中。應用程式接著便可確認此值，以減少權杖重新執行攻擊。此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| response\_mode | 建議使用 | 指定將產生的 authorization\_code 傳回到應用程式所應該使用的方法。可以是 'query'、'form\_post' 或 'fragment' 其中一種。針對 Web 應用程式，建議使用 `response_mode=form_post`，確保會以最安全的方式將權杖傳輸至您的應用程式。  
| state | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。其可以是您想要之任何內容的字串。隨機產生的唯一值通常用於[防止跨站台要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)。此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| prompt | 選用 | 表示需要的使用者互動類型。此時的有效值為「登入」、「無」和「同意」。`prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。`prompt=none` 則相反 - 它會確保不會對使用者顯示任何互動式提示。如果要求無法透過單一登入以無訊息方式完成，v2.0 端點會傳回錯誤。`prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| login\_hint | 選用 | 如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。通常 app 會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。 |
| domain\_hint | 選用 | 可以是 `consumers` 或 `organizations` 其中一個。如果包含，它會略過使用者在 v2.0 登入頁面上經歷的以電子郵件為基礎的探索程序，導致稍微更佳流暢的使用者經驗。通常 app 會在重新驗證期間使用此參數，方法是從 id\_token 擷取 `tid` 宣告。如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用 `domain_hint=consumers`。否則，使用 `domain_hint=organizations`。 |
此時，會要求使用者輸入其認證並完成驗證。v2.0 端點也會確保使用者已經同意 `scope` 查詢參數所示的權限。如果使用者未曾同意這些權限的任何一項，就會要求使用者同意要求的權限。[這裡提供權限、同意與多租用戶應用程式](active-directory-v2-scopes.md)的詳細資料。

一旦使用者驗證並同意，v2.0 端點就會使用 `response_mode` 參數中指定的方法，將回應傳回至位於指定所在 `redirect_uri` 的應用程式。

#### 成功回應
使用 `response_mode=form_post` 的成功回應如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| id\_token | 應用程式要求的 id\_token。您可以使用 id\_token 確認使用者的身分識別，並以使用者開始工作階段。如需 Id\_token 及其內容的詳細資訊，請參閱 [v2.0 端點權杖參考](active-directory-v2-tokens.md)。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |

#### 錯誤回應
錯誤回應可能也會傳送至 `redirect_uri`，讓應用程式可以適當地處理：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## 驗證 id\_token
僅接收 id\_token 不足以驗證使用者，您必須驗證 id\_token 簽章，並依照應用程式的需求確認權杖中的宣告。v2.0 端點使用 [JSON Web Tokens (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密簽署權杖及驗證其是否有效。

您可以選擇驗證用戶端程式碼中的 `id_token`，但是常見的作法是將 `id_token` 傳送至後端伺服器，並且在那裡執行驗證。一旦驗證了 id\_token 的簽章，就會有數項宣告需要驗證。如需詳細資訊，請參閱 [v2.0 權杖參考](active-directory-v2-tokens.md)，包括[驗證權杖](active-directory-v2-tokens.md#validating-tokens)和[有關簽署金鑰變換的重要資訊](active-directory-v2-tokens.md#validating-tokens)。我們建議利用程式庫來剖析和驗證權杖 - 對於大部分語言和平台至少有一個可用。<!--TODO: Improve the information on this-->

您可能也希望根據自己的案例驗證其他宣告。一些常見的驗證包括：

- 確保使用者/組織已註冊應用程式。
- 確保使用者擁有正確的授權/權限
- 確保驗證具有特定強度，例如多重要素驗證。

如需 id\_token 中宣告的詳細資訊，請參閱 [v2.0 端點權杖參考](active-directory-v2-tokens.md)。

一旦驗證完畢 id\_token，即可利用使用者開始工作階段，並使用 id\_token 中的宣告來取得應用程式中的使用者相關資訊。這項資訊可以用於顯示、記錄、授權等等。

## 傳送登出要求

v2.0 端點目前不支援 OpenIdConnect `end_session_endpoint`。這表示應用程式無法向 v2.0 端點傳送要求，而無法結束使用者工作階段及清除 v2.0 端點設定的 Cookie。若要將使用者登出，應用程式只需結束自身的使用者工作階段，並完整地將使用者工作階段留給 v2.0 端點即可。下次使用者嘗試登入時，就會看到列出其主動登入帳戶的 [選擇帳戶] 頁面。在該頁面上，使用者可以選擇登出任一帳戶，結束 v2.0 端點的工作階段。

<!--

When you wish to sign the user out of the app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## 通訊協定圖表 - 權杖取得
許多 Web Apps 不僅需要將使用者登入，同時需要使用 OAuth 代表使用者來存取 Web 服務。這個案例針對使用者驗證合併 OpenID Connect，同時使用 OAuth 授權碼流程取得可用來取得 access\_token 的 authorization\_code。

完整的 OpenID Connect 登入和權杖取得流程如下所示 - 以下將詳細說明每一個步驟。

![OpenId Connect 區隔線](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## 取得存取權杖
若要取得存取權杖，您需要稍微修改上述的登入要求：


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

> [AZURE.TIP] 請嘗試將下列要求貼至瀏覽器！

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=form_post&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910
```

在要求中包含權限範圍，並且使用 `response_type=code+id_token`，v2.0 端點可確保使用者已經同意 `scope` 查詢參數中表示的權限，並且將授權碼傳回至您的應用程式以交換存取權杖。

#### 成功回應
使用 `response_mode=form_post` 的成功回應如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| id\_token | 應用程式要求的 id\_token。您可以使用 id\_token 確認使用者的身分識別，並以使用者開始工作階段。如需 Id\_token 及其內容的詳細資訊，請參閱 [v2.0 端點權杖參考](active-directory-v2-tokens.md)。 |
| code | 應用程式要求的 authorization\_code。應用程式可以使用授權碼要求目標資源的存取權杖。Authorization\_code 的有效期很短，通常約 10 分鐘後即到期。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |

#### 錯誤回應
錯誤回應可能也會傳送至 `redirect_uri`，讓應用程式可以適當地處理：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

一旦取得授權 `code` 和 `id_token`，您可以將使用者登入，並且代表他們取得存取權杖。若要將使用者登入，您必須完整地如[上方](#validating-the-id-token)所述驗證 `id_token`。若要取得存取權杖，您可以遵循我們的 [OAuth 通訊協定文件](active-directory-v2-protocols-oauth-code.md#request-an-access-token)中所述的步驟。

<!---HONumber=AcomDC_0224_2016-->