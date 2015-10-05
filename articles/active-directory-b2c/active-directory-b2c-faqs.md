<properties
	pageTitle="Azure Active Directory B2C 預覽：常見問題集 | Microsoft Azure"
	description="關於 Azure Active Directory B2C 的常見問題集"
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

# Azure Active Directory B2C 預覽：常見問題集

此頁面回答有關 Azure Active Directory (AD) B2C 預覽的常見問題。請隨時回來查看最新消息。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### 在我現有以員工為主的 Azure AD 目錄中可以使用 Azure AD B2C 嗎？

目前您無法在您現有的 Azure AD 目錄中開啟 Azure AD B2C 功能。建議您建立另一個目錄來使用 Azure AD B2C 功能，亦即管理您的取用者。

### 我可以使用 Azure AD B2C 提供 Office 365 的社交登入 (Facebook 及 Google+) 嗎？

Azure AD B2C 無法與 Office 365 一起使用。一般而言，它不能用來提供 SaaS 應用程式 (Salesforce、Workday 等) 的驗證。它為消費者導向的網路和行動應用程式提供身分識別與存取管理，並不適用於員工或合作夥伴的案例。

### Azure AD B2C 中的「本機帳戶」是什麼？ 與 Azure AD 中的「公司帳戶或學校帳戶」有何不同？

在 Azure AD 目錄中，目錄中的每個使用者 (不包括目前有 Microsoft 帳戶的使用者) 以 `<xyz>@<directory domain>` 格式的電子郵件地址登入，其中 `<directory domain>` 是目錄中已驗證的其中一個網域，或初始 `<...>.onmicrosoft.com` 網域。這種類型的帳戶是「公司帳戶或學校帳戶」，也稱為「組織帳戶」。

在 Azure AD B2C 目錄中，大部分應用程式希望使用者以任何任意的電子郵件地址登入 (例如 joe@comcast.net、bob@gmail.com、sarah@contoso.com 或 jim@live.com))。這種類型的帳戶就是「本機帳戶」。現在，我們也支援任意使用者名稱 (僅單純字串) 作為本機帳戶 (例如、joe、bob、sarah 或 jim)。您在 Azure AD B2C 服務中有這兩個本機帳戶「類型」可選擇。

### 你們現在支援哪些社交共享身分識別提供者？ 你們打算在未來支援哪些？

我們目前支援 Facebook、Google+、LinkedIn 和 Amazon。根據客戶需求，我們將會增加支援 Microsoft 帳戶和其他熱門的社交身分識別提供者。

### 我可以設定「範圍」從各種社交身分識別提供者收集取用者的詳細資訊嗎？

否，但這項功能已在我們的規劃中。我們支援的一組社交身分識別提供者所使用的預設範圍如下：

- Facebook: email
- Google+: email
- Amazon: profile
- LinkedIn: r\_emailaddress r\_basicprofile

### 我的應用程式必須在 Azure 上執行才能使用 Azure AD B2C 嗎？

否，您可以將應用程式裝載於任何地方 (雲端或內部部署)。只要能夠在公開存取的端點上傳送和接收 HTTP 要求，就可以與 Azure AD B2C 互動。

### 我有多個 Azure AD B2C 目錄。如何在 Azure Preview 入口網站上管理它們？

在 Azure Preview 入口網站上，每一個 Azure AD B2C 目錄都有自己的 B2C 功能刀鋒視窗。請參閱[此處](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)，以了解您如何在 Azure Preview 入口網站瀏覽到特定目錄的 B2C 功能刀鋒視窗。在大部分瀏覽器中，當您在 Azure Preview 入口網站的 Azure AD B2C 目錄之間切換時，B2C 功能刀鋒視窗不會維持開啟。

### 如何自訂 Azure AD B2C 傳送的驗證電子郵件 (內容和寄件者欄位，亦即「寄件者:」欄位)？

如需詳細資訊，請參閱[這篇關於 UI 自訂的文章](active-directory-b2c-reference-ui-customization.md)。

### 如何將我現有的使用者名稱、密碼和設定檔從資料庫移轉至 Azure AD B2C？

您可以使用 Azure AD 圖形 API (請參閱[這裡](active-directory-b2c-devquickstarts-graph-dotnet.md)的範例) 來撰寫您的移轉工具。未來我們將提供各種現成的移轉選項和工具。

### 您可以使用 Azure AD Connect 將儲存於內部部署 Active Directory 的取用者身分識別移轉至 Azure AD B2C 嗎？

否，Azure AD Connect 不是設計來搭配 Azure AD B2C 一起使用。未來我們將提供各種現成的移轉選項和工具。

### Azure AD B2C 可以搭配 Microsoft Dynamics 之類的 CRM 系統一起使用嗎？

目前不支援。整合這些系統已在我們的規劃中。

### Azure AD B2C 可以搭配 SharePoint On-Premises 2016 或更舊的版本一起使用嗎？

目前不支援。Azure AD B2C 不支援 SAML 1.1 權杖。

### Azure AD B2C 提供哪些報告和稽核功能？ 與 Azure AD Premium 的功能相同嗎？

否，Azure AD B2C 不支援與 Azure AD Premium 相同的一組報告。Azure AD B2C 很快就會釋出基本的報告和稽核 API。

### 我可以將 Azure AD B2C 所提供的頁面 UI 當地語系化嗎？ 支援哪些語言？

目前，Azure AD B2C 僅針對英文做過最佳化。我們計劃儘速推出當地語系化功能。

### 我可以在 Azure AD B2C 提供的註冊與登入頁面上使用我自己的 URL 嗎? 比方說，將 URL 從 login.microsoftonline.com 變更為 login.contoso.com？

目前不支援。但這項功能已在我們的規劃中。

### 我可以從 Enterprise Mobility Suite (EMS) 中取得 Azure AD B2C 嗎？

否，Azure AD B2C 是隨用隨付的 Azure 服務，並不是 EMS 的一部分。

### 如何報告有關 Azure AD B2C 的問題？

請參閱關於 Azure AD B2C 的[這個支援主題](active-directory-b2c-support.md)。

### Azure AD B2C 將於何時正式運作？

我們目前無法提供正式運作日期的任何資訊。

## 相關資訊

您也可能想要檢閱目前的[預覽限制和條件約束](active-directory-b2c-limitations.md)。

<!---HONumber=Sept15_HO4-->