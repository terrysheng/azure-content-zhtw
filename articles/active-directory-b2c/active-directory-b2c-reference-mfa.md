<properties
	pageTitle="Azure Active Directory B2C 預覽：Multi-Factor Authentication | Microsoft Azure"
	description="如何在受 Azure Active Directory B2C 保護的取用者導向應用程式中啟用 Multi-Factor Authentication"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：在取用者導向應用程式中啟用 Multi-Factor Authentication

Azure Active Directory (AD) B2C 直接整合 [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)，協助您輕鬆針對取用者導向應用程式的註冊與登入使用體驗，增添第二層安全性。您連一行程式碼都不用寫，即可達成此目標。目前我們支援使用撥打電話與簡訊做為驗證選項。若您已建立註冊和登入原則 (如[本文章]()中所述)，則可按後續章節所示開啟 Multi-Factor Authentication。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]Multi-Factor Authentication 亦可在建立註冊和登入原則時開啟，而非單靠編輯現有原則將其開啟。

應用程式可運用此功能處理諸如下列的各種案例，其中您：

- 存取某一應用程式時需要 Multi-Factor Authentication，但在存取另一應用程式時需要它。例如，取用者可使用社交或本機帳戶登入汽車保險應用程式，但必須先完成電話號碼驗證，才能存取同個目錄中註冊的家庭保險應用程式。
- 一般而言，存取應用程式時無需 Multi-Factor Authentication，但在存取其中的敏感部分資訊時則需要它。例如，取用者可使用社交或本機帳戶登入銀行應用程式檢查帳戶餘額，但必須先完成電話號碼驗證，才能嘗試進行轉帳匯款作業。

## 修改註冊原則以啟用 Multi-Factor Authentication

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)...
2. 按一下 [註冊原則]。
3. 按一下以開啟註冊原則 (例如「B2C\_1\_SiUp」)。
4. 按一下 [Multi-Factor Authentication]，並將 [狀態] 設為 [開啟]。按一下 [確定]。
5. 按一下刀鋒視窗頂端的 [儲存]。大功告成！

您可以使用原則上的「立即執行」功能來驗證取用者體驗。您應會看到以下的正常運作流程：

出現 Multi-Factor Authentication 步驟前，即會在目錄中建立取用者帳戶。在步驟執行過程中，系統會要求取用者提供電話號碼進行驗證。若驗證成功，會將電話號碼附加至取用者帳戶以供之後使用。即使取用者取消或卸除，下次登入時系統仍會要求其再度驗證電話號碼 (已啟用 Multi-Factor Authentication；請參閱下一節)。

## 修改登入原則以啟用 Multi-Factor Authentication

1. 瀏覽至 [Azure Preview 入口網站](htts://portal.azure.com/)上的 B2C 功能刀鋒視窗。有關如何執行此操作的說明，請參閱[這裡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [登入原則]。
3. 按一下以開啟登入原則 (例如「B2C\_1\_SiIn」)。按一下刀鋒視窗頂端的 [編輯]。
4. 按一下 [Multi-Factor Authentication]，並將 [狀態] 設為 [開啟]。按一下 [確定]。
5. 按一下刀鋒視窗頂端的 [儲存]。大功告成！

您可以使用原則上的「立即執行」功能來驗證取用者體驗。您應會看到以下的正常運作流程：

取用者登入 (使用社交或本機帳戶) 時，若已將通過驗證的電話號碼附加至取用者帳戶，系統會要求其進行驗證。若未附加電話號碼，系統會要求取用者提供一個電話號碼並加以驗證；驗證成功後，即會將此電話號碼附加至取用者帳戶以供之後使用。

<!---HONumber=Sept15_HO3-->