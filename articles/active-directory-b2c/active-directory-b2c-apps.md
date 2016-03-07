<properties
	pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
	description="您在 Azure Active Directory B2C 預覽中可建置的應用程式類型。"
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
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Azure Active Directory B2C 預覽：應用程式類型

Azure Active Directory (Azure AD) B2C 支援各種現代應用程式架構的驗證。全部都以產業標準通訊協定 [OAuth 2.0](active-directory-b2c-reference-protocols.md) 或 [OpenID Connect](active-directory-b2c-reference-protocols.md) 為基礎。此文件簡要描述您可以建置的應用程式類型，不涉及您慣用的語言或平台。在您[開始建立應用程式](active-directory-b2c-overview.md#getting-started)之前，也可協助您先了解一些高階案例。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 基本概念
每個使用 Azure AD B2C 的應用程式都必須透過 [Azure 入口網站](https://portal.azure.com/)，註冊在 [B2C 目錄](active-directory-b2c-get-started.md)中。應用程式註冊程序會收集與指派一些值給您的應用程式：

- 可唯一識別應用程式的**應用程式識別碼**。
- 可用來將回應導回至應用程式的**重新導向 URI**。
- 案例特有的其他任何值。如需詳細資訊，請瞭解如何[註冊應用程式](active-directory-b2c-app-registration.md)。

註冊應用程式之後，應用程式即會向 Azure AD v2.0 端點傳送要求，以與 Azure AD 通訊：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

每個傳送至 Azure AD B2C 的要求都指定一個**原則**。原則控制 Azure AD 的行為。您也可以使用這些端點來建立一組可靈活自訂的使用者體驗。常見的原則包括註冊、登入和設定檔編輯原則。如果您不熟悉原則，在繼續之前，請參閱 Azure AD B2C 的[可延伸原則架構](active-directory-b2c-reference-policies.md)。

每個應用程式與 v2.0 端點的互動都遵循類似的高階模式：

1. 應用程式會將使用者導向至 v2.0 端點以執行[原則](active-directory-b2c-reference-policies.md)。
2. 使用者完成根據原則定義來完成原則。
4. 應用程式會從 v2.0 端點接收某種安全性權杖。
5. 應用程式使用此安全性權杖存取受保護的資訊或受保護的資源。
6. 資源伺服器會驗證安全性權杖，以確認可以授與存取權。
7. 應用程式會定期重新整理安全性權杖。

<!-- TODO: Need a page for libraries to link to -->
根據您要建置的應用程式類型而定，這些步驟可能稍有不同。開放原始碼程式庫可以為您處理細節。

## Web 應用程式
對於裝載於伺服器且透過瀏覽器存取的 Web 應用程式 (包括 .NET、PHP、Java、Ruby、Python、Node.js)，Azure AD B2C 支援以 [OpenID Connect](active-directory-b2c-reference-protocols.md) 完成一切使用者體驗。這包括登入、註冊和設定檔管理。在 Azure AD B2C 的 OpenID Connect 實作中，Web 應用程式會向 Azure AD 發出驗證要求，以起始這些使用者體驗。要求的結果是`id_token`。這個安全性權杖代表使用者的身分識別。它也以宣告形式提供使用者的相關資訊：

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

請參閱 [B2C 權杖參考](active-directory-b2c-reference-tokens.md)，以深入了解應用程式可用的權杖和宣告類型。

在 Web 應用程式中，每次執行[原則](active-directory-b2c-reference-policies.md)會採用下列高階步驟：

![Web 應用程式泳道映像](./media/active-directory-b2c-apps/webapp.png)

使用接收自 Azure AD 的公開簽署金鑰來驗證 `id_token`，就足以驗證使用者的身分識別。這也會設定工作階段 Cookie，在後續頁面要求上可用來識別使用者。

若要查看此案例的實際運作情形，請在[使用者入門](active-directory-b2c-overview.md#getting-started)一節的 Web 應用程式登入程式碼範例中擇一試用。

除了讓登入更簡單，Web 伺服器應用程式可能也需要存取後端 Web 服務。在此情況下，Web 應用程式可能執行稍有不同的 [OpenID Connect 流程](active-directory-b2c-reference-oidc.md)，並使用授權碼和重新整理權杖來取得權杖。以下 [Web API](#web-apis) 一節描述此案例。

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web API
您可以使用 Azure AD B2C 來保護 Web 服務，例如應用程式的 RESTful Web API。Web API 可以使用 OAuth 2.0 來保護它們的資料。它們也可以使用權杖來驗證內送 HTTP 要求。Web API 的呼叫端會在 HTTP 要求的授權標頭中附加權杖：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

然後，Web API 會使用這個權杖來驗證 API 呼叫端的身分識別，並從編碼在權杖中的宣告擷取呼叫端的相關資訊。請參閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)，以深入了解應用程式可用的權杖和宣告類型。

> [AZURE.NOTE]
	Azure AD B2C 預覽目前僅支援以各自已知的用戶端存取的 Web API。例如，完整的應用程式可能包括 iOS 應用程式、Android 應用程式和後端 Web API。完全支援這種架構。目前不支援協力廠商用戶端 (例如另一個 iOS 應用程式) 存取相同的 Web API。完整應用程式的所有元件必須共用單一應用程式識別碼。

Web API 接收的權杖可以來自許多類型的應用程式，包括 Web 應用程式、桌面和行動應用程式、單一頁面應用程式、伺服器端精靈，以及其他 Web API。以下是 Web 應用程式呼叫 Web API 的完整流程範例：

![Web 應用程式 Web API 泳道映像](./media/active-directory-b2c-apps/webapi.png)

若要深入了解授權碼、重新整理權杖和取得權杖的步驟，請參閱 [OAuth 2.0 通訊協定](active-directory-b2c-reference-oauth-code.md)。

若要了解如何使用 Azure AD B2C 保護 Web API，請查看[使用者入門](active-directory-b2c-overview.md#getting-started)一節的 Web API 教學課程。

## 行動和原生應用程式
安裝在裝置中的應用程式 (如行動和桌面應用程式) 通常需要代替使用者存取後端服務或 Web API。您可以將自訂的身分識別管理體驗加入至原生應用程式，並使用 Azure AD B2C 和 [OAuth 2.0 授權碼流程](active-directory-b2c-reference-oauth-code.md)安全地呼叫後端服務。

在此流程中，應用程式會執行[原則](active-directory-b2c-reference-policies.md)，並在使用者完成原則之後，從 Azure AD 接收 `authorization_code`。`authorization_code` 代表應用程式有權限代替目前登入的使用者呼叫後端服務。然後應用程式就可以在背景中以 `authorization_code` 來兌換 `id_token` 和 `refresh_token`。應用程式可以在 HTTP 要求中使用 `id_token` 向後端 Web API 驗證。它也可以使用 `refresh_token` 來取得新的 `id_token` (當舊的已過期時)。

> [AZURE.NOTE]
	Azure AD B2C 預覽目前僅支援用來存取應用程式本身的後端 Web 服務的 ID 權杖。例如，完整的應用程式可能包括 iOS 應用程式、Android 應用程式和後端 Web API。完全支援這種架構。目前不支援 iOS 應用程式使用 OAuth 2.0 存取權杖來存取協力廠商 Web API。完整應用程式的所有元件必須共用單一應用程式識別碼。

![原生應用程式泳道映像](./media/active-directory-b2c-apps/native.png)

## 目前的預覽版本限制
Azure AD B2C 預覽目前不支援下列類型的應用程式，但計劃於正式運作時提供支援。[限制和約束](active-directory-b2c-limitations.md)中說明 Azure AD B2C 預覽相關的其他限制和約束。

### 單一頁面應用程式 (JavaScript)
許多新式的應用程式都有單一頁面應用程式前端，主要是以 JavaScript 撰寫。它們通常使用 AngularJS、Ember.js、Durandal 等架構。正式運作的 Azure AD 服務使用 OAuth 2.0 隱含流程來支援這些應用程式。但 Azure AD B2C 預覽中還未提供此流程。應該很快就會提供。

### 精靈/伺服器端應用程式
如果應用程式含有長時間執行的處理序或不需要使用者操作，也仍然需要方法來存取受保護的資源，例如 Web API。這些應用程式可以使用應用程式身分識別 (而非使用者委派身分識別) 和使用 OAuth 2.0 用戶端認證流程，以驗證及取得權杖。

Azure AD B2C 目前不支援此流程。這些應用程式只有在互動式使用者流程發生之後，才取得權杖。將於近期內加入用戶端認證流程。

### Web API 鏈結 (代理者流程)
許多架構中都有一個 Web API 需要呼叫另一個下游 Web API，而兩者都受 Azure AD B2C 保護。此案例常見於有 Web API 後端的原生用戶端。這接著會呼叫 Microsoft 線上服務，例如 Azure AD Graph API。

使用 OAuth 2.0 JWT 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為代理者流程。不過，Azure AD B2C 預覽目前未實作代理者流程。

<!---HONumber=AcomDC_0224_2016-->