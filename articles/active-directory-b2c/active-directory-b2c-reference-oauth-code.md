<properties
	pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
	description="使用 Azure AD 的 OpenID Connect 驗證通訊協定實作來建置 Web 應用程式。"
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="dastrock"/>

# Azure AD B2C 預覽：OAuth 2.0 授權碼流程

OAuth 2.0 授權碼授與可用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。您可以使用 Azure AD B2C 的 OAuth 2.0 實作，將註冊、登入和其他身分識別管理工作加入至行動和桌面應用程式。本指南與語言無關，描述在不使用我們的任何開放原始碼程式庫的情況下，如何傳送和接收 HTTP 訊息。

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

關於 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格 4.1 節](http://tools.ietf.org/html/rfc6749)。它可在大部分的應用程式類型中用於執行驗證與授權，包括 [Web 應用程式](active-directory-b2c-apps.md#web-apps)和[原始安裝的應用程式](active-directory-b2c-apps.md#mobile-and-native-apps)。它可讓應用程式安全地取得 **access\_token**，而這些權杖可用於存取[授權伺服器](active-directory-b2c-reference-protocols.md#the-basics)所保護的資源。本指南將焦點集中在一種特別的 OAuth 2.0 授權碼流程 - **公開用戶端**。公開用戶端是指不可信任會安全地維護密碼完整性的任何用戶端應用程式。這包括行動應用程式、桌面應用程式，以及幾乎任何在裝置上執行且需要取得 access\_token 的應用程式。如果您想要使用 Azure AD B2C 將身分識別管理加入至 Web 應用程式，您應該使用 [OpenID Connect](active-directory-b2c-reference-oidc.md)，而不是 OAuth 2.0。

Azure AD B2C 擴充標準的 OAuth 2.0 流程，功能更強大，而不僅止於簡單的驗證和授權。它引進[**原則參數**](active-directory-b2c-reference-policies.md)，可讓您使用 OAuth 2.0 將使用者體驗加入至應用程式，例如註冊、登入和設定檔管理。以下我們將示範如何使用 OAuth 2.0 和原則，在原生應用程式中實作上述每一種體驗，並取得 access\_token 來存取 Web API。

下面的範例 HTTP 要求將使用我們的範例 B2C 目錄 **fabrikamb2c.onmicrosoft.com**，以及我們的範例應用程式和原則。您可以隨意使用這些值來自行嘗試要求，也可以換成您自己的值。了解如何[取得您自己的 B2C 目錄、應用程式和原則](#use-your-own-b2c-directory)。

## 1\.取得授權碼
授權碼流程始於用戶端將使用者導向 `/authorize` 端點。這是流程的互動部分，使用者將會實際地採取動作。在這項要求中，用戶端會在 `scope` 參數中指出它需要向使用者要求的權限，並在 `p` 參數中指出要執行的原則。以下提供三個範例 (插入換行以提高可讀性)，各使用不同的原則。

#### 使用登入原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### 使用註冊原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### 使用編輯設定檔原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 必要 | [Azure 入口網站](https://portal.azure.com)指派給應用程式的應用程式識別碼。 |
| response\_type | 必要 | 授權碼流程必須包含 `code`。 |
| redirect\_uri | 必要 | 您的應用程式可在應用程式的 redirect\_uri 傳送及接收驗證回應。其必須完全符合您在入口網站中註冊的其中一個 redirect\_uris，不然就必須得是編碼的 url。 |
| scope | 必要 | 範圍的空格分隔清單。單一範圍值向 Azure AD 表示同時要求的兩個權限。`openid` 範圍表示讓使用者登入和以 **id\_token** 形式取得使用者相關資料的權限 (這方面還有更多說明)。`offline_access` 範圍表示您的應用程式將需要 **refresh\_token**，才能長久存取資源。 |
| response\_mode | 建議使用 | 指定將產生的 authorization\_code 傳回到應用程式所應該使用的方法。可以是 'query'、'form\_post' 或 'fragment' 其中一種。
| state | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。其可以是您想要之任何內容的字串。隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。驗證要求出現前，也會先使用此狀態為使用者在應用程式中的狀態資訊編碼，例如他們先前所在的網頁或正在執行的原則。 |
| p | 必要 | 指出要執行的原則。它是 B2C 目錄中建立的原則名稱，值應該以 "b2c\_1\_" 開頭。在[這裡](active-directory-b2c-reference-policies.md)深入了解原則。 |
| prompt | 選用 | 表示需要的使用者互動類型。此時唯一有效的值是 'login'，強制使用者針對該要求輸入其認證。單一登入不會生效。 |

此時會要求使用者完成原則的工作流程。這可能需要使用者輸入其使用者名稱和密碼、以社交身分識別登入、註冊目錄，或任意數量的步驟 (視原則如何定義而定)。一旦使用者完成原則，Azure AD 就會使用 `response_mode` 參數中指定的方法，將回應傳回至指定的 `redirect_uri` 給您的應用程式。在上述各種情況下，回應完全相同，與已執行的原則無關。

使用 `response_mode=query` 的成功回應如下所示：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| code | 應用程式要求的 authorization\_code。應用程式可以使用授權碼要求目標資源的存取權杖。Authorization\_code 的有效期很短，通常約 10 分鐘後即到期。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |

錯誤回應可能也會傳送至 `redirect_uri`，讓應用程式可以適當地處理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |


## 2\.取得權杖
既然您已經取得 authorization\_code，您就可以傳送 `POST` 要求給 `/token` 端點，將 `code` 兌換成所需資源的權杖。在 Azure AD B2C 在預覽中，應用程式本身的後端 Web API 是您唯一可要求權杖的資源。在慣例上為您自己要求權杖的作法是使用範圍 `openid`：

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 必要 | 用來取得授權碼的原則。您不可在此要求中使用不同的原則。**請注意，此參數會加入至查詢字串**，而不是在 POST 本文中。 |
| client\_id | 必要 | [Azure 入口網站](https://portal.azure.com)指派給應用程式的應用程式識別碼。 |
| grant\_type | 必要 | 必須是授權碼流程的 `authorization_code`。 |
| scope | 必要 | 範圍的空格分隔清單。單一範圍值向 Azure AD 表示同時要求的兩個權限。`openid` 範圍表示讓使用者登入和以 **id\_token** 形式取得使用者相關資料的權限。它可以用來為應用程式本身的後端 Web API 取得權杖，由與用戶端相同的應用程式識別碼代表。`offline_access` 範圍表示您的應用程式將需要 **refresh\_token**，才能長久存取資源。 |
| code | 必要 | 您在流程的第一個階段中取得的 authorization\_code。 |
| redirect\_uri | 必要 | 應用程式的 redirect\_uri，指出您在此處收到 authorization\_code。 |

成功的權杖回應如下：

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| not\_before | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token\_type | 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。 |
| id\_token | 您所要求已簽署的 JWT 權杖。 |
| scope | 權杖有效的範圍，可用於快取權杖供以後使用。 |
| id\_token\_expires\_in | id\_token 的有效期 (以秒為單位)。 |
| profile\_info | Base 64 編碼的 JSON 字串，可能包含有關使用者的有用資訊，可顯示在原生應用程式中。確切內容取決於您在原則中設定的應用程式宣告 |
| refresh\_token | OAuth 2.0 重新整理權杖。在目前的權杖過期之後，應用程式可以使用這個權杖來取得其他權杖。Refresh\_token 的有效期很長，而且可以用來延長保留資源存取權的時間。如需詳細資訊，請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |
| refresh\_token\_expires\_in | 重新整理權杖的最長有效時間 (以秒為單位)。不過，重新整理權杖隨時都可能失效。 |

> [AZURE.NOTE]
	如果您現在問：「access\_token 在哪裡？」，請考慮下列問題。當您要求 `openid` 範圍時，Azure AD 會在回應中發出 JWT `id_token`。嚴格來說，雖然這個 `id_token` 不是 OAuth 2.0 access\_token，但在與應用程式本身的後端服務通訊時可以這樣用，由與用戶端相同的 client\_id 代表。`id_token` 仍然是已簽署的 JWT 持有人權杖，可在 HTTP 授權標頭中傳送給資源，並用來驗證要求。差別在於 `id_token` 沒有機制可縮小特定用戶端應用程式可能擁有的存取範圍。不過，當用戶端應用程式是唯一能夠與後端服務進行通訊的用戶端時 (如同目前的 Azure AD B2C 預覽一樣)，則不需要這種範圍設定機制。當 Azure AD B2C 預覽增加功能讓用戶端與第一方和第三方資源通訊時，將會引進 access\_token。不過，即便如此，使用 `id_tokens` 來與應用程式本身的後端服務通訊，仍然只是建議的模式。如需有關可使用 Azure AD B2C 預覽來建置的應用程式類型的詳細資訊，請參閱[這篇文章](active-directory-b2c-apps.md)。

錯誤回應格式如下：

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## 3\.使用權杖
既然您已經成功取得 `id_token`，在向後端 Web API 發出的要求中，您可以將權杖加入 `Authorization` 標頭中來使用權杖：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\.重新整理權杖
Id\_token 有效期很短，到期後必須重新整理，才能繼續存取資源。作法是向 `/token` 端點送出另一個 `POST` 要求，但這次要提供 `refresh_token`，而不是 `code`：

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| 參數 | | 說明 |
| ----------------------- | ------------------------------- | -------- |
| p | 必要 | 用來取得原始重新整理權杖的原則。您不可在此要求中使用不同的原則。**請注意，此參數會加入至查詢字串**，而不是在 POST 本文中。 |
| client\_id | 必要 | [Azure 入口網站](https://portal.azure.com)指派給應用程式的應用程式識別碼。 |
| grant\_type | 必要 | 必須是授權碼流程此階段的 `refresh_token`。 |
| scope | 必要 | 範圍的空格分隔清單。單一範圍值向 Azure AD 表示同時要求的兩個權限。`openid` 範圍表示讓使用者登入和以 **id\_token** 形式取得使用者相關資料的權限。它可以用來為應用程式本身的後端 Web API 取得權杖，由與用戶端相同的應用程式識別碼代表。`offline_access` 範圍表示您的應用程式將需要 **refresh\_token**，才能長久存取資源。 |
| redirect\_uri | 必要 | 應用程式的 redirect\_uri，指出您在此處收到 authorization\_code。 |
| refresh\_token | 必要 | 您在流程的第二個階段中取得的原始 refresh\_token。 |

成功的權杖回應如下：

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| not\_before | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token\_type | 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。 |
| id\_token | 您所要求已簽署的 JWT 權杖。 |
| scope | 權杖有效的範圍，可用於快取權杖供以後使用。 |
| id\_token\_expires\_in | id\_token 的有效期 (以秒為單位)。 |
| profile\_info | Base 64 編碼的 JSON 字串，可能包含有關使用者的有用資訊，可顯示在原生應用程式中。確切內容取決於您在原則中設定的應用程式宣告 |
| refresh\_token | OAuth 2.0 重新整理權杖。在目前的權杖過期之後，應用程式可以使用這個權杖來取得其他權杖。Refresh\_token 的有效期很長，而且可以用來延長保留資源存取權的時間。如需詳細資訊，請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |
| refresh\_token\_expires\_in | 重新整理權杖的最長有效時間 (以秒為單位)。不過，重新整理權杖隨時都可能失效。 |

錯誤回應格式如下：

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |


<!-- 

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png) 

-->

## 使用您自己 B2C 目錄

如果您想要親自嘗試這些要求，您必須先執行這三個步驟，然後以您自己值取代上面的範例值：

- [建立 B2C 目錄](active-directory-b2c-get-started.md)，並在要求中使用您的目錄名稱。
- [建立應用程式](active-directory-b2c-app-registration.md)來取得應用程式識別碼和 redirect\_uri。您可以在應用程式中加入**原生用戶端**。
- [建立您的原則](active-directory-b2c-reference-policies.md)來取得原則名稱。

<!---HONumber=AcomDC_0204_2016-->