<properties
	pageTitle="Azure Active Directory B2C 預覽：概觀 | Microsoft Azure"
	description="使用 Azure Active Directory B2C 開發取用者導向應用程式"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：在您的應用程式中註冊與登入取用者

**Azure Active Directory B2C** 是適用於取用者導向 Web 與行動應用程式的全方位雲端身分識別管理解決方案。其為高可用性的全域服務，可針對數億萬的取用者身分識別進行級別調整。Azure Active Directory B2C 建置於企業級安全平台，確保讓您的應用程式、商務和取用者受到安全防護。

在過去，應用程式開發人員若想要註冊並讓取用者登入其應用程式，他們會編寫自己的程式碼。而且他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。Azure Active Directory B2C 為開發人員提供更理想的做法，透過安全且以標準為基礎的平台以及豐富的可延伸原則組合，協助將取用者身分識別管理整合至應用程式。使用 Azure Active Directory B2C 可讓取用者使用其現有的社交帳戶 (Facebook、Google、Amazon、LinkedIn) 註冊應用程式，或是建立新的認證 (電子郵件地址與密碼，或使用者名稱與密碼)；後者稱為「本機帳戶」。

Azure Active Directory B2C 處於預覽版狀態。我們冀望在此期間能聆聽您的寶貴意見反應與試用體驗。我們可能會根據這些意見反應進行重大變更，以改善服務。在這段期間，請勿使用預覽版發行生產應用程式。透過「[使用者心聲](http://feedback.azure.com/forums/169401-azure-active-directory)」(User Voice) 與我們分享您的想法。

## 開始使用

若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。參閱[本文](active-directory-b2c-get-started.md)中所述的步驟，取得您的專屬租用戶。

您可透過以下方式針對 Azure Active Directory B2C 服務撰寫應用程式：選擇直接傳送通訊協定訊息、使用 [OAuth 2.0](active-directory-b2c-protocols.md#oauth2-authorization-code-flow) 或 [Open ID Connect](active-directory-b2c-protocols.md#openid-connect-sign-in-flow)，或是使用我們的程式庫為您執行工作 (在下方選擇您喜愛的平台以開始使用)。

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## 新功能

請不時返回此處查看，瞭解關於 Azure Active Directory B2C 預覽未來變更的相關資訊。我們也會使用 @AzureAD 發佈任何更新的相關推文。

- 瞭解關於[可延伸原則架構](active-directory-b2c-reference-policies.md)，以及您在應用程式中可建立和使用之原則類型的資訊。
- 當前[預覽版的限制和條件約束](active-directory-b2c-limitations.md)。

## 做法

瞭解如何使用特定 Azure Active Directory B2C 預覽功能：

- 設定您要在取用者導向應用程式中使用的 ([Facebook](active-directory-b2c-setup-fb-app.md)、[Google +](active-directory-b2c-setup-goog-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md)) 帳戶。
- [使用自訂屬性來收集取用者相關資訊](active-directory-b2c-reference-custom-attr.md)。
- [在取用者導向應用程式中啟用 Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)。
- [設定取用者的自助式密碼重設](active-directory-b2c-reference-sspr.md)。
- 針對 Azure Active Directory B2C [自訂提供之註冊、登入和其他取用者導向頁面的外觀](active-directory-b2c-reference-ui-customization.md)。
- 在 Azure Active Directory B2C 租用戶中，[使用 Azure Active Directory 圖形 API 來以程式設計方式建立、讀取、更新和刪除取用者](active-directory-b2c-devquickstarts-graph-dotnet.md)。

## 參考

以下連結有助於深入探索服務：

- 請參閱 [Active Directory B2C 定價資訊](https://azure.microsoft.com/pricing/details/active-directory-b2c)
- 取得使用 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 標記之「堆疊溢位」的相關說明。
- 透過 [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) 告訴我們您對預覽版的想法 - 我們冀望瞭解您的看法！ 請在您的文章標題中使用 "AzureADB2C:" 字詞，以方便我們尋找。
- Azure Active Directory B2C 使用稱為「應用程式模型 v2.0」的應用程式註冊模型，可支援業界標準通訊協定、OpenID Connect 和 OAuth 2.0。
  - [應用程式模型 v2.0 通訊協定參考](active-directory-b2c-reference-protocols.md)
  - [應用程式模型 v2.0 權杖參考](active-directory-b2c-reference-tokens.md)
- [Azure Active Directory B2C 常見問題集](active-directory-b2c-faqs.md)
- [針對 Azure Active Directory B2C 的檔案支援要求](active-directory-b2c-support.md)

<!---HONumber=Nov15_HO2-->