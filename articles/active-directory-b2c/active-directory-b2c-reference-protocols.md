<properties
	pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
	description="如何直接使用 Azure AD B2C 預覽支援的通訊協定，建置應用程式。"
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C 預覽：驗證通訊協定

Azure AD B2C 支援 OpenID Connect 與 OAuth 2.0 兩種業界標準通訊協定，為應用程式提供身分識別即服務。雖然這是符合標準的服務，但這些通訊協定在任兩個實作之間仍會有些微差異。若您選擇藉由直接傳送和處理 HTTP 要求來撰寫程式碼，而非使用我們的其中一個開放原始碼程式庫，則可參考這裡提供的實用資訊。我們建議您在深入探索每個特定通訊協定的詳細資訊前，先行閱讀此頁面上的簡要資訊，但若您已熟悉 Azure AD B2C，則可直接前往《[通訊協定參考指南](#protocols)》。

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
## 基本概念
使用 Azure AD B2C 的每個應用程式都必須在 [Azure 入口網站](https://portal.azure.com)中您的 B2C 目錄中註冊。應用程式註冊處理序會收集與指派一些值給您的應用程式：

- 可唯一識別應用程式的**應用程式 ID**
- 可將回應導回應用程式的**重新導向 URI** 或**封裝識別碼**
- 其他幾個狀況特定的值。如需詳細資訊，請瞭解關於如何[註冊應用程式](active-directory-b2c-app-registration.md)的資訊。

註冊完成後，應用程式即會向 v2.0 端點傳送要求以與 Azure AD 通訊：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

幾乎在所有的 OAuth 和 OpenID Connect 流程中，都有四個參與交換的合作對象：

![OAuth 2.0 角色](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- **授權伺服器**為 Azure AD v2.0 端點。其負責確保使用者的身分識別、授與及撤銷資源存取權，以及核發權杖。其也稱作為識別提供者：安全地處理與使用者資訊、使用者存取權，以及流程中合作對象彼此間信任關係有關的任何項目。
- **資源擁有者**通常是使用者。其是擁有資料的一方，而且有權允許第三方存取該資料或資源。
- **OAuth 用戶端**是您的應用程式，系統會透過其「應用程式識別碼」加以識別。其通常是與使用者互動的對象，而且會向授權伺服器要求權杖。用戶端必須獲得資源擁有者授權才能存取資源。
- **資源伺服器**是資源或資料所在位置。其信任授權伺服器會安全地驗證及授權 OAuth 用戶端，並使用 Bearer access\_token 來確保可以授與資源的存取權。

## 原則

Azure AD B2C **原則**可視為服務的最重要服務。Azure AD B2C 可引進原則，允許 Azure AD B2C 來執行簡易驗證與授權以外的更多操作，藉以延伸標準 OAuth 2.0 和 OpenID Connect 通訊協定。原則可完整描述取用者身分識別經驗，例如註冊、登入或設定檔編輯。您可在管理 UI 中定義原則，以及在 HTTP 驗證要求中使用特殊查詢參數來執行原則。原則並非 OAuth 2.0 和 OpenID Connect 的標準功能，因此您應該花點時間瞭解它們。如需詳細資訊，請參閱《[Azure AD B2C 原則參考指南](active-directory-b2c-reference-policies)》。


## 權杖
Azure AD B2C 的 OAuth 2.0 和 OpenID Connect 實作廣泛運用持有人權杖，包括以 JWT 表示的持有人權杖。持有人權杖是輕巧型的安全性權杖，授權「持有者」存取受保護的資源。從這個意義上說，「持有者」是可出示權杖的任何一方。雖然某一方必須先向 Azure AD 驗證以收到持有人權杖，但如果傳輸和儲存時未採取必要的步驟來保護權杖，它可能會被非預期的一方攔截和使用。雖然某些安全性權杖都有內建的機制，可防止未經授權的人士使用權杖，但持有者權杖沒有這項機制，而必須以安全通道來傳輸，例如傳輸層安全性 (HTTPS)。如果持有人權杖以純文字傳輸，惡意人士可能使用攔截式攻擊來取得權杖，然後未經授權存取受保護的資源。儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。務必確定您的應用程式以安全的方式傳輸和儲存持有人權杖。關於持有者權杖的其他安全性考量，請參閱 [RFC 6750 第 5 節](http://tools.ietf.org/html/rfc6750)。

如需 Azure AD B2C 中所用各種不同權杖類型的詳細資訊，請參閱 [Azure AD 權杖參考](active-directory-b2c-reference-tokens.md)。

## 通訊協定

若您準備好查看部分範例要求，請開始使用以下的其中一個教學課程。每個教學課程皆對應至特定的驗證案例。若您在判斷適用權限流程時需要協助，請查閱[您可以使用 Azure AD B2C 建置的應用程式類型](active-directory-b2c-apps)。

- [使用 OAuth 2.0 建置行動與原生應用程式](active-directory-b2c-reference-oauth-code.md)
- [使用 OpenID Connect 建置 Web 應用程式](active-directory-b2c-reference-oidc.md)
- 使用 OAuth 2.0 隱含流程建置單一頁面應用程式 (敬請期待)
- 使用 OAuth 2.0 用戶端認證流程建置精靈或伺服器端處理程序 (敬請期待)
- 透過 OAuth 2.0 資源擁有者密碼認證流程，取得採用使用者名稱和密碼的權杖 (敬請期待)
- 透過 OAuth 2.0 代理者流程在 Web API 中取得權杖 (敬請期待)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=Sept15_HO4-->