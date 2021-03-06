<properties
	pageTitle="Azure Active Directory B2C 預覽：限制 | Microsoft Azure"
	description="Azure Active Directory B2C 的限制清單"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：限制

Azure Active Directory (Azure AD) B2C 有幾項特性與功能在預覽期間還不支援。在 Azure AD B2C 正式運作之前將移除其中諸多限制，但如果您在預覽期間使用 Azure AD B2C 建置消費者導向的應用程式，則應該注意這些限制。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Azure AD B2C 租用戶期間的問題

如果您在[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started)期間遇到問題，請參閱[建立 Azure AD 租用戶或 Azure AD B2C 租用戶 - 問題與解決方法](active-directory-b2c-support-create-directory.md)以取得指導方針。

## 驗證電子郵件上的商標問題

預設的驗證電子郵件包含 Microsoft 商標。未來我們會將它移除。您目前可以使用[公司商標功能](../active-directory/active-directory-add-company-branding.md)來移除它。

## 支援實際執行應用程式

與 Azure AD B2C 整合的應用程式，不應該當成實際執行層級的應用程式公開發行。Azure AD B2C 目前處於預覽階段，隨時可能發佈重大變更，此項服務也不提供任何服務等級協定保證。並非所有發生的事件都會受到支援。如果您願意接受依賴仍在開發中的服務所帶來的風險，請務必發佈推文到 @AzureAD 連絡我們，以討論您的應用程式或服務的範圍。

## 應用程式的限制

Azure AD B2C 預覽目前不支援下列類型的應用程式。如需受支援應用程式類型的描述，請參閱 [Azure AD B2C 預覽：應用程式類型](active-directory-b2c-apps.md)。

### 單一頁面應用程式 (JavaScript)

許多現代化應用程式都有單一頁面應用程式 (SPA) 前端，主要是以 JavaScript 撰寫，而且通常採用 AngularJS、Ember.js、Durandal 等 SPA 架構。Azure AD B2C 預覽中還未提供此流程。

### 精靈 / 伺服器端應用程式

如果應用程式含有長時間執行的處理序或不需要使用者操作，也仍然需要方法來存取受保護的資源，例如 Web API。這些應用程式可以透過 [OAuth 2.0 用戶端認證流程](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow)，利用應用程式的身分識別 (而非取用者的委派身分識別) 來驗證及取得權杖。Azure AD B2C 預覽中還未提供此流程，因此，目前只有在進行互動式取用者登入流程之後，應用程式才能取得權杖。

### 獨立的 Web API

在 Azure AD B2C 預覽中，您能夠[建置使用 OAuth 2.0 權杖保護的 Web API](active-directory-b2c-apps.md#web-apis)。不過，該 Web API 只能從共用相同應用程式識別碼的用戶端接收權杖。不支援建置從數個不同用戶端存取的 Web API。

### Web API 鏈結 (代理者)

許多架構中都有一個 Web API 需要呼叫另一個下游 Web API，而兩者都受 Azure AD B2C 保護。此情況常見於有 Web API 後端的原生用戶端，而後端會再呼叫 Azure AD 圖形 API 等 Microsoft 線上服務。

使用 OAuth 2.0 Jwt 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。不過，Azure AD B2C 預覽目前未實作代理者流程。

## 程式庫與 SDK 的限制

並非所有語言和平台都具有支援 Azure AD B2C 預覽的程式庫。這組驗證程式庫目前僅適用於 .NET、iOS、Android 和 NodeJS。[開始使用](active-directory-b2c-overview.md#getting-started)一節提供各平台對應的快速啟動教學課程。

如果您想要使用其他語言或平台來整合應用程式與 Azure AD B2C 預覽，請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定參考](active-directory-b2c-reference-protocols.md)，其中說明如何建構與 Azure AD B2C 服務進行通訊所需的 HTTP 訊息。

## 通訊協定的限制

Azure AD B2C 預覽支援 OpenID Connect 和 OAuth 2.0。不過，並非每個通訊協定的所有特性與功能都已實作。若要進一步了解 Azure AD B2C 預覽所支援的通訊協定功能的範圍，請參閱 [OpenID Connect 和 OAuth 2.0 通訊協定參考](active-directory-b2c-reference-protocols.md)。不提供 SAML 和 WS-Fed 通訊協定支援。

## 權杖的限制

許多由 Azure AD B2C 預覽所簽發的權杖都實作為 JSON Web Token (簡稱 JWT)。不過，並非 JWT 中包含的所有資訊 (又稱為「宣告」) 都相當完備，不然就是有所疏漏。例如 "sub" 和 "preferred\_username" 宣告。在預覽期間，此處的資訊會有很大的變化。若要進一步了解目前 Azure AD B2C 服務所發出的權杖，請參閱[權杖參考](active-directory-b2c-reference-tokens.md)。

## Azure 傳統入口網站上的使用者管理問題

在 Azure 入口網站上可存取 B2C 功能。不過，您可以使用 Azure 傳統入口網站來存取其他的租用戶功能，包括使用者管理。目前，Azure 傳統入口網站上的使用者管理 ([使用者] 索引標籤) 有幾個已知問題：

- 以本機帳戶使用者而言 (亦即，以電子郵件地址和密碼或使用者名稱和密碼來註冊的取用者)，[使用者名稱] 欄位未對應至註冊期間使用的登入識別碼 (電子郵件地址或使用者名稱)。這是因為 Azure 傳統入口網站上顯示的欄位，實際上是使用者主體名稱 (UPN)，而這在 B2C 案例中沒有用到。若要檢視本機帳戶的登入識別項，請在 [Graph Explorer](https://graphexplorer.cloudapp.net/) 中尋找使用者物件。您將會遇到與社交帳戶使用者 (亦即，以 Facebook、Google+ 等註冊的取用者) 同樣的問題，但在此情況下，沒所謂的登入識別項。

    ![本機帳戶 - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- 以本機帳戶使用者而言，您將無法在 [設定檔] 索引標籤中編輯任何欄位和儲存變更。我們將儘快修正此問題。

## 在 Azure 傳統入口網站上的系統管理員起始密碼重設問題

如果您針對本機帳戶取用者，在 Azure 傳統入口網站上重設其密碼 ([使用者] 索引標籤上的 [重設密碼] 命令)，該取用者下次登入時將無法變更密碼，且會被鎖定不得使用您的應用程式。我們正在努力修正這個問題。解決方法是使用 [Azure AD 圖形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 重設取用者的密碼。

## 刪除 Azure AD B2C 租用戶的限制

您無法在 Azure 傳統入口網站中刪除 Azure AD B2C 租用戶。

## 在 Azure 傳統入口網站上驗證網域的問題

您目前無法在 [Azure 傳統入口網站](https://manage.windowsazure.com/)上順利驗證網域。我們正在努力修復此問題。

<!---HONumber=AcomDC_0224_2016-->