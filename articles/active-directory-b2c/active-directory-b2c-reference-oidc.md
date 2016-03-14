<properties
	pageTitle="Azure Active Directory B2C 預覽版 | Microsoft Azure"
	description="使用 Azure Active Directory 的 OpenID Connect 驗證通訊協定實作來建置 Web 應用程式。"
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
	ms.date="02/18/2016"
	ms.author="dastrock"/>

# Azure Active Directory B2C 預覽版：利用 OpenID Connect 的 Web 登入

OpenID Connect 是驗證通訊協定，可用來讓使用者安全地登入 Web 應用程式。它的建置基礎為 OAuth 2.0。只要使用 Azure Active Directory (Azure AD) B2C 的 OpenID Connect 實作，您就可以把 Web 應用程式中的註冊、登入及其他身分識別管理工作外包給 Azure AD。本指南將以與語言無關的方式，來示範這該如何進行。它會說明如何傳送和接收 HTTP 訊息，但不使用我們的任何開放原始碼程式庫。

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 擴充 OAuth 2.0 的「授權」通訊協定來做為「驗證」通訊協定，讓您能利用 OAuth 來執行單一登入。它引進 `id_token` 的概念，這是一種安全性權杖，可讓用戶端驗證使用者的身分識別，並取得有關使用者的基本設定檔資訊。

由於它會擴充 OAuth 2.0，因此也能讓應用程式安全地取得 **access\_tokens**。您可以使用 access\_tokens，來存取受到[授權伺服器](active-directory-b2c-reference-protocols.md#the-basics)保護的資源。如果您要建置的 Web 應用程式是裝載在伺服器上，且必須透過瀏覽器來存取，我們建議您使用 OpenID Connect。如果您想要利用 Azure AD B2C 在行動或桌面應用程式中新增身分識別管理功能，您應該要使用 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md)，而不是 OpenID Connect。

Azure AD B2C 擴充標準的 OpenID Connect 通訊協定，功能更強大，而不僅止於簡單的驗證和授權。它採用[**原則參數**](active-directory-b2c-reference-policies.md)，讓您能利用 OpenID Connect 來為應用程式新增更多使用者體驗，例如註冊、登入和設定檔管理。以下我們將示範如何利用 OpenID Connect 和原則，在您的 Web 應用程式中實作上述每一種體驗。我們也會示範如何取得用來存取 Web API 的 access\_token。

下面的範例 HTTP 要求將使用我們的範例 B2C 目錄 **fabrikamb2c.onmicrosoft.com**，以及我們的範例應用程式 ****https://aadb2cplayground.azurewebsites.net** 和原則。您可以隨意使用這些值來自行試驗要求，也可以把它們換成您自己的值。了解如何[取得您自己的 B2C 租用戶、應用程式和原則](#use-your-own-b2c-directory)。

## 傳送驗證要求
當 Web 應用程式需要驗證使用者和執行原則時，它可以將使用者導向至 `/authorize` 端點。這是流程中互動式的部分，能讓使用者根據原則來實際採取行動。

在這項要求中，用戶端會在 `scope` 參數中指出它需要向使用者要求的權限，以及在 `p` 參數中指出它要執行的原則。以下提供三個範例 (插入換行以提高可讀性)，各使用不同的原則。為了瞭解每個要求的運作方式，請試著將要求貼到瀏覽器來執行。

#### 使用登入原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### 使用註冊原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### 使用編輯設定檔原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 參數 | 必要？ | 說明 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 必要 | [Azure 入口網站](https://portal.azure.com/)指派給您應用程式的應用程式識別碼。 |
| response\_type | 必要 | 回應類型，必須針對 OpenID Connect 來加入 `id_token`。如果您的 Web 應用程式也需要權杖來呼叫 Web API，您可以使用 `code+id_token`，如同這裡的作法一樣。 |
| redirect\_uri | 必要 | 應用程式的 redirect\_uri，您的應用程式可在此傳送及接收驗證回應。它必須與您在入口網站中註冊的某個 redirect\_uris 完全符合，不過它必須是 URL 編碼的。 |
| scope | 必要 | 範圍的空格分隔清單。單一範圍值，向 Azure AD 指出受到要求的兩個權限。`openid` 範圍指出要以 **id\_token** 的形式 (本文章稍後將進一步說明) 來登入使用者，以及取得使用者相關資料的權限。對於 Web 應用程式，`offline_access` 範圍是選擇性。它表示您的應用程式將需要 **refresh\_token**，才能長久存取資源。 |
| response\_mode | 建議 | 必須用來將所得的 authorization\_code 傳回至應用程式的方法，可以是 'query'、'form\_post' 或 'fragment'。 |
| state | 建議 | 同樣會隨權杖回應傳回之要求中所包含的值。它可以是您所想要內容中的字串。隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。驗證要求出現前，也會先使用此狀態為使用者在應用程式中的狀態資訊編碼，例如他們先前所在的網頁。 |
| nonce | 必要 | 包含在要求中的值 (由應用程式所產生)，將會包含在所得的 id\_token 中來做為宣告。應用程式接著便可確認此值，以減少權杖重新執行攻擊。此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| p | 必要 | 系統將會執行的原則。這是在您的 B2C 租用戶中所建立的原則名稱。原則名稱值的開頭必須是「b2c\_1\_」。如需深入了解原則，請參閱[可延伸的原則架構](active-directory-b2c-reference-policies.md)。 |
| prompt | 選用 | 需要的使用者互動類型。此時唯一有效的值是 'login'，強制使用者針對該要求輸入其認證。單一登入將沒有作用。 |

此時會要求使用者完成原則的工作流程。視原則的定義方式，這可能會牽涉到讓使用者輸入自己的使用者名稱及密碼、以社交身分識別登入、註冊目錄，或是其他任何數目的步驟。

當使用者完成原則之後，Azure AD 就會使用您在 `response_mode` 參數中指定的方法，將回應傳回至指定的 `redirect_uri` 來給您的應用程式。在上述各種情況下，回應完全相同，與已執行的原則無關。

使用 `response_mode=query` 的成功回應如下所示：

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| id\_token | 應用程式要求的 id\_token。您可以使用 id\_token 確認使用者的身分識別，並以使用者開始工作階段。如需 id\_token 及其內容的詳細資料，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |
| code | 應用程式要求的 authorization\_code (如果您使用 `response_type=code+id_token`)。應用程式可以使用授權碼來要求目標資源的 access\_token。Authorization\_codes 的存在時間很短，通常會在大約 10 分鐘後過期。 |
| state | 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |

錯誤回應也能夠傳送到 `redirect_uri`，讓應用程式能適當地處理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 說明 |
| ----------------------- | ------------------------------- |
| 錯誤 | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error\_description | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| state | 如需完整說明，請參閱前一個表格。如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。 |


## 驗證 id\_token
只接收 id\_token 並不足以驗證使用者，您必須驗證 id\_token 的簽章，並依照應用程式的需求來確認權杖中的宣告。Azure AD B2C 使用 [JSON Web Tokens (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密編譯來簽署權杖及驗證其是否有效。

視您偏好的語言而定，有許多針對驗證 JWT 的開放原始碼程式庫可用。我們建議您探索這些程式庫，而不是實作您自己的驗證邏輯。當您在思考如何適當地運用那些程式庫時，這裡的資訊會很有用。

Azure AD B2C 具有 OpenID Connect 中繼資料端點，可讓應用程式在執行階段提取 Azure AD B2C 的相關資訊。這項資訊包括端點、權杖內容和權杖簽署金鑰。您的 B2C 租用戶中的每個原則都有一份 JSON 中繼資料文件。例如，`fabrikamb2c.onmicrosoft.com` 中適用於 `b2c_1_sign_in` 原則的中繼資料文件位於：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此設定文件的屬性之一是 `jwks_uri`，就上述原則而言，它的值是：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`。

若要判斷哪個原則用來簽署 id\_token (以及何處可擷取中繼資料)，您有兩個選項。首先，原則名稱包含在 id\_token 的 `acr` 宣告中。如需有關如何剖析 id\_token 中的宣告的相關資訊，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。另一個選項是當您發出要求時，在 `state` 參數的值中將原則編碼，然後將它解碼來判斷已使用的原則。任一種方法都絕對有效。

當您從 OpenID Connect 中繼資料端點取得中繼資料文件之後，就可以使用 RSA 256 (位於此端點) 公用金鑰來驗證 id\_token 的簽章。此端點可能隨時會列出多個金鑰，每個金鑰由 `kid` 識別。Id\_token 標頭也包含 `kid` 宣告，其指出簽署 id\_token 所使用的金鑰。如需詳細資訊，請參閱[Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)，包括其中的＜[驗證權杖](active-directory-b2c-reference-tokens.md#validating-tokens)＞及[簽署金鑰變換的相關重要資訊](active-directory-b2c-reference-tokens.md#validating-tokens)的小節。
<!--TODO: Improve the information on this-->

當您驗證 id\_token 的簽章之後，就必須驗證幾個宣告：

- 您應該驗證 `nonce` 宣告以防止權杖重新執行攻擊。其值應該是您在登入要求中所指定的內容。
- 您應該驗證 `aud` 宣告，以確保應用程式的 id\_token 已核發。這個值就是您應用程式的應用程式識別碼。
- 您應該驗證 `iat` 及 `exp` 宣告，以確保 id\_token 沒有過期。

視您的案例而定，您可能也會想要驗證其他宣告。一些常見的驗證包括：

- 確保使用者/組織已為應用程式註冊。
- 確保使用者有適當的授權/權限。
- 確保驗證方式有一定的強度，例如 Azure Multi-Factor Authentication。

如需 id\_token 中的宣告的詳細資訊，請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。

當您徹底驗證 id\_token 之後，即可利用使用者開始工作階段，並使用 id\_token 中的宣告來取得應用程式中使用者的相關資訊。這項資訊可以用於顯示、記錄、授權等等。

## 取得權杖
如果您的 Web 應用程式只需要執行原則，您可以略過接下來的幾節。適用於這幾節的 Web 應用程式需要對 Web API 進行已驗證的呼叫，而且還受到 Azure AD B2C 的保護。

您可以藉由傳送 `POST` 要求給 `/token` 端點，把您取得的權杖 authorization\_code (透過使用 `response_type=code+id_token`) 兌換成所需的資源。在 Azure AD B2C 在預覽版中，您唯一可以要求權杖的資源，就是應用程式本身的後端 Web API。通常用來為您自己要求權杖的方法，就是使用範圍 `openid`：

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| 參數 | 必要？ | 說明 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 必要 | 用來取得授權碼的原則。您無法在此要求中使用不同的原則。請注意，您要把這個參數新增到**查詢字串**，而不是 POST 主體中。 |
| client\_id | 必要 | [Azure 入口網站](https://portal.azure.com/)指派給您應用程式的應用程式識別碼。 |
| grant\_type | 必要 | 授與的類型，針對授權碼流程來說，必須是 `authorization_code`。 |
| scope | 必要 | 範圍的空格分隔清單。單一範圍值，向 Azure AD 指出受到要求的兩個權限。`openid` 範圍指出要以 **id\_token** 的形式來登入使用者，以及取得使用者相關資料的權限。它可以用來把權杖傳送到您應用程式本身的後端 Web API，而該 API 是由與用戶端相同的應用程式識別碼所代表的。`offline_access` 範圍表示您的應用程式將需要 **refresh\_token**，才能長久存取資源。 |
| code | 必要 | 您在流程的第一個階段中取得的 authorization\_code。 |
| redirect\_uri | 必要 | 應用程式的 redirect\_uri，指出您在此處收到 authorization\_code。 |
| client\_secret | 必要 | 您在 [Azure 入口網站](https://portal.azure.com/)中產生的應用程式密碼。這個應用程式密碼是重要的安全性構件，您應該要用安全的方法把它儲存在您的伺服器上。您也應該注意定期輪換此用戶端密碼。 |

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
| token\_type | 權杖類型值。Azure AD 唯一支援的類型是 Bearer。 |
| id\_token | 您所要求已簽署的 JWT 權杖。 |
| scope | 權杖有效的範圍，可用於快取權杖供以後使用。 |
| id\_token\_expires\_in | Id\_token 的有效時間長度 (以秒為單位)。 |
| profile\_info | Base 64 編碼的 JSON 字串，可能包含使用者相關的有用資訊，以便顯示在您的原生應用程式中。而它的確切內容，取決於您在原則中所設定的應用程式宣告。 |
| refresh\_token | OAuth 2.0 refresh\_token。應用程式在目前的權杖過期之後，可以使用這個權杖來取得其他權杖。Refresh\_token 的存在時間很長，且可以用來長期保留資源存取權。如需詳細資訊，請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)。請注意，您必須同時在授權和權杖要求中使用範圍 `offline_access`，才能接收 refresh\_token。 |
| refresh\_token\_expires\_in | refresh\_token 可能有效的時間長度上限 (以秒為單位)。不過，refresh\_token 隨時都可能會失效。 |

> [AZURE.NOTE]
	如果您現在問：「access\_token 在哪裡？」，請考慮下列問題。當您要求 `openid` 範圍時，Azure AD 會在回應中發出 JWT `id_token`。嚴格來說，雖然這個 `id_token` 不是 OAuth 2.0 access\_token，但它在與您應用程式本身的後端服務 (由與用戶端相同的 client\_id 來代表) 通訊時，可以當做 OAuth 2.0 access\_token 來使用。`id_token` 仍然是已簽署的 JWT 持有人權杖，可在 HTTP 授權標頭中傳送給資源，並用來驗證要求。<br><br>差別在於 `id_token` 沒有機制可縮小特定用戶端應用程式可能擁有的存取範圍。不過，當用戶端應用程式是唯一能夠與後端服務進行通訊的用戶端時 (如同目前的 Azure AD B2C 預覽一樣)，則不需要這種範圍設定機制。<br><br>當 Azure AD B2C 預覽版加入可讓用戶端與其他第一方和第三方資源通訊的功能時，就將會採用 access\_token。不過，即使到了那時候，我們仍然會建議您使用 `id_tokens` 來與您應用程式本身的後端服務通訊。如需詳細資訊，請參閱您可以利用 Azure AD B2C 預覽版來建置的[應用程式類型](active-directory-b2c-apps.md)。

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

## 使用權杖
您已經成功取得 `id_token`，因此您可以在對後端 Web API 發出的要求中使用該權杖，方法是在 `Authorization` 標頭中加入該權杖：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 重新整理權杖
Id\_tokens 只會短暫存在。因此在該權杖過期之後，您必須重新整理權杖，才能繼續存取資源。方法是向 `/token` 端點送出另一個 `POST` 要求，但這次要提供 `refresh_token`，而不是 `code`：

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| 參數 | 必要 | 說明 |
| ----------------------- | ------------------------------- | -------- |
| p | 必要 | 用來取得原始 refresh\_token 的原則。您無法在此要求中使用不同的原則。請注意，您要把這個參數新增到**查詢字串**，而不是 POST 主體中。 |
| client\_id | 必要 | [Azure 入口網站](https://portal.azure.com/)指派給您應用程式的應用程式識別碼。 |
| grant\_type | 必要 | 授與的類型，針對授權碼流程的這部分來說，必須是 `refresh_token`。 |
| scope | 必要 | 範圍的空格分隔清單。單一範圍值，向 Azure AD 指出受到要求的兩個權限。`openid` 範圍指出要以 **id\_token** 的形式來登入使用者，以及取得使用者相關資料的權限。它可以用來把權杖傳送到您應用程式本身的後端 Web API，而該 API 是由與用戶端相同的應用程式識別碼所代表的。`offline_access` 範圍表示您的應用程式將需要 **refresh\_token**，才能長久存取資源。 |
| redirect\_uri | 必要 | 應用程式的 redirect\_uri，指出您在此處收到 authorization\_code。 |
| refresh\_token | 必要 | 您在流程的第二個階段中取得的原始 refresh\_token。請注意，您必須同時在授權和權杖要求中使用範圍 `offline_access`，才能接收 refresh\_token。 |
| client\_secret | 必要 | 您在 [Azure 入口網站](https://portal.azure.com/)中產生的應用程式密碼。這個應用程式密碼是重要的安全性構件，您應該要用安全的方法把它儲存在您的伺服器上。您也應該注意定期輪換此用戶端密碼。 |

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
| token\_type | 權杖類型值。Azure AD 唯一支援的類型是 Bearer。 |
| id\_token | 您所要求已簽署的 JWT 權杖。 |
| scope | 權杖有效的範圍，可用於快取權杖供以後使用。 |
| id\_token\_expires\_in | Id\_token 的有效時間長度 (以秒為單位)。 |
| profile\_info | Base 64 編碼的 JSON 字串，可能包含使用者相關的有用資訊，以便顯示在您的原生應用程式中。而它的確切內容，取決於您在原則中所設定的應用程式宣告。 |
| refresh\_token | OAuth 2.0 refresh\_token。應用程式在目前的權杖過期之後，可以使用這個權杖來取得其他權杖。Refresh\_token 的存在時間很長，且可以用來長期保留資源存取權。如需詳細資訊，請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)。 |
| refresh\_token\_expires\_in | refresh\_token 可能有效的時間長度上限 (以秒為單位)。不過，refresh\_token 隨時都可能會失效。 |

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

Here is the entire flow for a native app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## 傳送登出要求

當您想要讓使用者登出應用程式時，只是清除應用程式的 Cookie，或是結束使用者的工作階段是不夠的。您也必須把使用者重新導向至 Azure AD 來登出。如果您沒有這麼做，使用者可能不必重新輸入認證，就能夠向您的應用程式重新驗證自己的身分。這是因為使用者將擁有有效的 Azure AD 單一登入工作階段。

您只要把使用者重新導向至 `end_session_endpoint` (列於之前＜驗證 id\_token＞一節中所述的 OpenID Connect 中繼資料文件中) 即可：

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 參數 | 必要？ | 說明 |
| ----------------------- | ------------------------------- | ------------ |
| p | 必要 | 使用者最近一次用來登入應用程式的原則。 |
| post\_logout\_redirect\_uri | 建議 | 使用者在成功登出之後，應該要前往的 URL。如果沒有包含這項資料，Azure AD B2C 會向使用者顯示一般的訊息。 |

> [AZURE.NOTE]
	雖然將使用者導向至 `end_session_endpoint` 能清除使用者的部分 Azure AD B2C 單一登入狀態，但無法讓使用者登出自己的社交識別提供者 (IDP) 工作階段。如果使用者之後在登入時選取相同的 IDP，該使用者不必輸入自己的認證，就能讓系統重新驗證自己的身分。雖然使用者想要登出您的 B2C 應用程式，但這並不一定代表他們想要完全登出自己的 Facebook 帳戶。不過，如果是使用本機帳戶，使用者的工作階段便會正確地結束。

## 使用您自己的 B2C 租用戶

如果您想要親自嘗試這些要求，您必須先執行下列三個步驟，然後用您自己值來取代上面的範例值：

- [建立 B2C 租用戶](active-directory-b2c-get-started.md)，並在要求中使用您租用戶的名稱。
- [建立應用程式](active-directory-b2c-app-registration.md)來取得應用程式識別碼和 redirect\_uri。您可以在應用程式中加入 **Web 應用程式/Web API**，並選擇性地建立**應用程式密碼**。
- [建立您的原則](active-directory-b2c-reference-policies.md)來取得原則名稱。

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web app](active-directory-v2-flows.md#web-apps). The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=AcomDC_0302_2016-------->