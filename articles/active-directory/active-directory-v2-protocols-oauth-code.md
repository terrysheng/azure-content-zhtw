
<properties
	pageTitle="Azure AD v2.0 OAuth 授權碼流程 | Microsoft Azure"
	description="使用 Azure AD 實作 OAuth 2.0 驗證通訊協定建置 Web 應用程式。"
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
	ms.date="01/11/2015"
	ms.author="dastrock"/>

# v2.0 通訊協定 - OAuth 2.0 授權碼流程

OAuth 2.0 授權碼授與可用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。使用應用程式模型 v2.0 實作 OAuth 2.0，您可以新增登入及 API 存取到您的行動應用程式和傳統型應用程式。本指南與語言無關，描述在不使用我們的任何開放原始碼程式庫的情況下，如何傳送和接收 HTTP 訊息。

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽。如需如何與正式運作之 Azure AD 服務整合的相關資訊，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

關於 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格 4.1 節](http://tools.ietf.org/html/rfc6749)。在大部分的應用程式類型中，其用於執行驗證與授權，包括 [Web Apps](active-directory-v2-flows.md#web-apps) 和[原始安裝的應用程式](active-directory-v2-flows.md#mobile-and-native-apps)。其可讓應用程式安全地取得 access\_token，這些權杖可用於存取以 v2.0 應用程式模型保護的資源。



## 要求授權碼
授權碼流程始於用戶端將使用者導向 `/authorize` 端點。在這項要求中，用戶端會指出必須向使用者索取的權限：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

> [AZURE.TIP] 請嘗試將此要求貼至瀏覽器！

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | 必要 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| response\_type | 必要 | 授權碼流程必須包含 `code`。 |
| redirect\_uri | 建議使用 | 應用程式的 redirect\_uri，您的應用程式可在此傳送及接收驗證回應。其必須完全符合您在入口網站中註冊的其中一個 redirect\_uris，不然就必須得是編碼的 url。對於原生和行動應用程式，您應該使用 `urn:ietf:wg:oauth:2.0:oob` 的預設值。 |
| scope | 必要 | 您要使用者同意的[範圍](active-directory-v2-scopes.md)清單 (以空格分隔)。 |
| response\_mode | 建議使用 | 指定將產生的權杖送回到應用程式所應該使用的方法。可以是 `query` 或 `form_post`。 |
| state | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。其可以是您想要之任何內容的字串。隨機產生的唯一值通常用於[防止跨站台要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)。此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| prompt | 選用 | 表示需要的使用者互動類型。此時的有效值為「登入」、「無」和「同意」。`prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。`prompt=none` 則相反 - 它會確保不會對使用者顯示任何互動式提示。如果要求無法透過單一登入以無訊息方式完成，v2.0 端點會傳回錯誤。`prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| login\_hint | 選用 | 如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。通常應用程式會在重新驗證期間使用此參數，已使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。 |
| domain\_hint | 選用 | 可以是 `consumers` 或 `organizations` 其中一個。如果包含，它會略過使用者在 v2.0 登入頁面上經歷的以電子郵件為基礎的探索程序，導致稍微更佳流暢的使用者經驗。通常應用程式會在重新驗證期間使用此參數，方法是擷取上一次登入的 `tid`。如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用 `domain_hint=consumers`。否則，使用 `domain_hint=organizations`。 |

此時，會要求使用者輸入其認證並完成驗證。v2.0 端點也會確保使用者已經同意 `scope` 查詢參數所示的權限。如果使用者未曾同意這些權限的任何一項，就會要求使用者同意要求的權限。[這裡提供權限、同意與多租用戶應用程式](active-directory-v2-scopes.md)的詳細資料。

一旦使用者驗證並同意，v2.0 端點就會使用 `response_mode` 參數中指定的方法，將回應傳回至位於指定所在 `redirect_uri` 的應用程式。

#### 成功回應
使用 `response_mode=query` 的成功回應如下所示：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| code | 應用程式要求的 authorization\_code。應用程式可以使用授權碼要求目標資源的存取權杖。Authorization\_code 的有效期很短，通常約 10 分鐘後即到期。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |

#### 錯誤回應
錯誤回應可能也會傳送至 `redirect_uri`，讓應用程式可以適當地處理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## 要求存取權杖
既然您已經取得 authorization\_code 並獲得使用者授權，您就可以將 `POST` 要求傳送至 `/token` 端點，贖回 `code` 以取得所需資源的 `access_token`：

```
// Line breaks for legibility only

POST /common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] 將以下 curl 命令重試匯入 postman！(您必須以自己的程式碼取代 `code`，才能匯入成功)

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```


| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | 必要 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| grant\_type | 必要 | 必須是授權碼流程的 `authorization_code`。 |
| scope | 必要 | 範圍的空格分隔清單。在此階段中要求的範圍必須相當於或為第一個階段中所要求的範圍子集。如果這個要求中指定的範圍遍及多個資源伺服器，v2.0 端點就會傳回第一個範圍內所指定資源的權杖。如需範圍的詳盡說明，請參閱[權限、同意和範圍](active-directory-v2-scopes.md)。 |
| code | 必要 | 您在流程的第一個階段中取得的 authorization\_code。 |
| redirect\_uri | 必要 | 用來取得 authorization\_code 的相同 redirect\_uri 值。 |
| client\_secret | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。其不應用於原生應用程式，因為裝置無法穩當地儲存 client\_secret。Web Apps 和 Web API 都需要應用程式密碼，其能夠將 client\_secret 安全地儲存在伺服器端。 |

#### 成功回應
成功的權杖回應如下：

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| access\_token | 所要求的存取權杖。應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。 |
| token\_type | 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。 |
| expires\_in | 存取權杖的有效期 (以秒為單位)。 |
| scope | access\_token 有效的範圍。 |
| refresh\_token | OAuth 2.0 重新整理權杖。應用程式可以使用這個權杖，在目前的存取權杖過期之後，取得其他的存取權杖。Refresh\_token 的有效期很長，而且可以用來延長保留資源存取權的時間。如需詳細資訊，請參閱 [v2.0 權杖參考](active-directory-v2-tokens.md)。 |
| id\_token | 不帶正負號的 JSON Web Token (JWT)。應用程式可以 base64Url 解碼這個權杖的區段，要求已登入使用者的相關資訊。應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。如需 id\_token 的詳細資訊，請參閱 [v2.0 應用程式模型權杖參考](active-directory-v2-tokens.md)。 |

#### 錯誤回應
錯誤回應格式如下：

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| error\_codes | 有助於診斷的 STS 特定錯誤碼清單。 |
| timestamp | 發生錯誤的時間。 |
| trace\_id | 有助於診斷的要求唯一識別碼。 |
| correlation\_id | 有助於跨元件診斷的要求唯一識別碼。 |

## 使用存取權杖
既然您已經成功取得 `access_token`，您就可以透過在 `Authorization` 標頭中包含權杖，在 Web API 的要求中使用權杖：

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] 請嘗試以下命令！(但以您自己的權杖加以取代)

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## 重新整理存取權杖
Access\_token 有效期很短，且您必須在其到期後重新整理，才能繼續存取資源。方法是：向 `/token` 端點送出另一個 `POST` 要求，這次提供 `refresh_token`，而不提供 `code`：

```
// Line breaks for legibility only

POST /common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] 將以下 curl 命令重試匯入 postman！(您必須以自己的權杖取代 refresh\_token，才能匯入成功)

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | -------- |
| client\_id | 必要 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 指派給應用程式的應用程式識別碼。 |
| grant\_type | 必要 | 必須是授權碼流程此階段的 `refresh_token`。 |
| scope | 必要 | 範圍的空格分隔清單。在此階段中要求的範圍必須相當於或為原始 authorization\_code 要求階段中所要求的範圍子集。如果這個要求中指定的範圍遍及多個資源伺服器，v2.0 端點就會傳回第一個範圍內所指定資源的權杖。如需範圍的詳盡說明，請參閱[權限、同意和範圍](active-directory-v2-scopes.md)。 |
| refresh\_token | 必要 | 您在流程的第二個階段中取得的 refresh\_token。 |
| redirect\_uri | 必要 | 用來取得 authorization\_code 的相同 redirect\_uri 值。 |
| client\_secret | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。其不應用於原生應用程式，因為裝置無法穩當地儲存 client\_secret。Web Apps 和 Web API 都需要應用程式密碼，其能夠將 client\_secret 安全地儲存在伺服器端。 |

#### 成功回應
成功的權杖回應如下：

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| access\_token | 所要求的存取權杖。應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。 |
| token\_type | 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。 |
| expires\_in | 存取權杖的有效期 (以秒為單位)。 |
| scope | access\_token 有效的範圍。 |
| refresh\_token | 新的 OAuth 2.0 重新整理權杖。您應該用新取得的重新整理權杖取代舊的重新整理權杖，以確定盡可能保持重新整理權杖有效的時間。 |
| id\_token | 不帶正負號的 JSON Web Token (JWT)。應用程式可以 base64Url 解碼這個權杖的區段，要求已登入使用者的相關資訊。應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。如需 id\_token 的詳細資訊，請參閱 [v2.0 應用程式模型權杖參考](active-directory-v2-tokens.md)。 |

#### 錯誤回應
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| error\_codes | 有助於診斷的 STS 特定錯誤碼清單。 |
| timestamp | 發生錯誤的時間。 |
| trace\_id | 有助於診斷的要求唯一識別碼。 |
| correlation\_id | 有助於跨元件診斷的要求唯一識別碼。 |

## 摘要
概括而言，原生/行動應用程式的整個驗證流程看起來像是這樣：

![OAuth 授權碼流程](../media/active-directory-v2-flows/convergence_scenarios_native.png)

<!---HONumber=AcomDC_0128_2016-->