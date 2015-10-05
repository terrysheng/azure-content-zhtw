<properties
	pageTitle="Azure Active Directory B2C 預覽：限制 | Microsoft Azure"
	description="Azure Active Directory B2C 的限制清單"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：限制

Azure Active Directory (AD) B2C 有幾項功能在預覽期間還不支援。在 Azure AD B2C 正式運作之前將移除其中諸多限制，但如果您在預覽期間使用 Azure AD B2C 建置消費者導向的應用程式，則應該注意這些限制。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Azure AD B2C 目錄期間的問題

您在[建立 Azure AD B2C 目錄期間](active-directory-b2c-get-started)可能會遇到一些已知問題。請參閱這篇[文章](active-directory-b2c-support-create-directory.md)，取得指導方針。

## 驗證電子郵件和自助式密碼重設頁面上的商標問題

預設驗證電子郵件和自助式密碼重設頁面包含「Microsoft」和「Azure」商標元素。我們日後將移除這些商標元素。您可以使用[公司商標功能](./active-directory/active-directory-add-company-branding.md)來變更頁面上的商標，屆時這些商標元素將不會再出現。

## 支援實際執行應用程式

與 Azure AD B2C 整合的應用程式，不應該當成實際執行層級的應用程式公開發行。Azure AD B2C 目前處於預覽階段，隨時可能發布重大變更，此項服務也不提供任何 SLA 保證。並非所有發生的事件都會受到支援。如果您願意接受依賴開發中服務所帶來的風險，請務必發佈推文到 @AzureAD 連絡我們，討論您的應用程式或服務的範圍。

## 應用程式的限制

Azure AD B2C 預覽目前不支援下列類型的應用程式。如需受支援的應用程式類型的描述，請參閱這篇[文章](active-directory-b2c-apps)。

### 單一頁面應用程式 (Javascript)

許多現代化應用程式都有單一頁面應用程式 (SPA) 前端，主要是以 Javascript 撰寫，而且通常採用 AngularJS、Ember.js、Durandal 等 SPA 架構。Azure AD B2C 預覽中還未提供此流程。

### 精靈 / 伺服器端應用程式

如果應用程式含有長時執行的處理序或不需要使用者操作，也仍然需要方法來存取受保護的資源，例如 Web API。這些應用程式可以透過 [OAuth 2.0 用戶端認證流程](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow)，利用應用程式身分識別 (而非取用者委派身分識別) 驗證及取得權杖。Azure AD B2C 預覽中還未提供此流程 - 也就是說，只有在進行互動式取用者登入流程之後，應用程式才能取得權杖。

### 獨立的 Web API

在 Azure AD B2C 預覽中，您能夠[建置使用 OAuth 2.0 權杖保護的 Web API](active-directory-b2c-apps.md#web-apis)。不過，該 Web API 只能從共用相同應用程式識別碼的用戶端接收權杖。不支援建置從數個不同用戶端存取的 Web API。

## 程式庫與 SDK 的限制

並非所有語言和平台都具有支援 Azure AD B2C 預覽的程式庫。驗證程式庫集合目前僅適用於 .NET、iOS、Android 和 NodeJS。[使用者入門](active-directory-b2c-overview.md#getting-started)一節提供各平台對應的快速入門教學課程。

如果您想要使用其他語言或平台來整合應用程式與 Azure AD B2C 預覽，請參閱 [OAuth 2.0 和 OpenID Connect 通訊協定參考](active-directory-b2c-protocols.md)，其中指示如何建構與 Azure AD B2C 服務進行通訊所需的 HTTP 訊息。

## 通訊協定的限制

Azure AD B2C 預覽支援 OpenID Connect 和 OAuth 2.0。不過，並非每個通訊協定的所有特性與功能都已實作。若要進一步了解 Azure AD B2C 預覽所支援的通訊協定功能的範圍，請參閱 [OpenID Connect 和 OAuth 2.0 通訊協定參考](active-directory-b2c-protocols.md)。

## 權杖的限制

許多由 Azure AD B2C 預覽所簽發的權杖都實作為 JSON Web Token (簡稱 JWT)。不過，並非 JWT 中包含的所有資訊 (又稱為「宣告」) 都相當完備，不然就是有所疏漏。例如 "sub" 和 "preferred\_username" 宣告。在預覽期間，此處的資訊會有很大的變化。若要進一步了解目前 Azure AD B2C 服務所發出的權杖，請參閱[權杖參考](active-directory-b2c-tokens.md)。

## Azure 入口網站上的使用者管理問題

在 Azure Preview 入口網站上可存取 B2C 功能。不過，您可以使用 Azure 入口網站來存取其他的目錄功能，包括使用者管理。目前，Azure 預覽入口網站上的使用者管理 ([使用者] 索引標籤) 有幾個已知問題。

- 以本機帳戶使用者而言 (亦即，以電子郵件地址和密碼或使用者名稱和密碼來註冊的取用者)，[使用者名稱] 欄位未對應至註冊期間使用的登入識別項 (電子郵件地址或使用者名稱)。這是因為 Azure 入口網站上顯示的欄位，實際上是使用者主體名稱 (UPN)，而這在 B2C 案例中沒有用到。若要檢視本機帳戶的登入識別項，請在 [Graph Explorer](https://graphexplorer.cloudapp.net/) 中尋找使用者物件。您將會遇到與社交帳戶使用者 (亦即，以 Facebook、Google+ 等註冊的取用者) 同樣的問題，但在此情況下，沒所謂的登入識別項。

    ![本機帳戶 - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- 以本機帳戶使用者而言，您將無法在 [設定檔] 索引標籤中編輯任何欄位和儲存變更。我們將儘快修正此問題。

## 刪除 Azure AD B2C 目錄時的限制

您無法在 Azure 入口網站中刪除 Azure AD B2C 目錄。

<!---HONumber=Sept15_HO4-->