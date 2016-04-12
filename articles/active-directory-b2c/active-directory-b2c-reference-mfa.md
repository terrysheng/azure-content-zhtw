<properties
	pageTitle="Azure Active Directory B2C 預覽：Multi-Factor Authentication | Microsoft Azure"
	description="如何在受 Azure Active Directory B2C 保護的取用者導向應用程式中啟用 Multi-Factor Authentication"
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
	ms.date="03/28/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：在取用者導向應用程式中啟用 Multi-Factor Authentication

Azure Active Directory (Azure AD) B2C 直接整合 [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)，讓您能夠針對取用者導向應用程式的註冊與登入使用體驗，增添第二層安全性。您連一行程式碼都不用寫，即可達成此目標。目前我們支援撥打電話與簡訊驗證。如果您已經建立註冊和登入原則，您仍然可以啟用 Multi-Factor Authentication。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
Multi-Factor Authentication 也可以在建立註冊和登入原則時啟用，而非單靠編輯現有原則來啟用。

此功能可協助應用程式處理諸如下列的各種案例：

- 您在存取某一個應用程式時不需要 Multi-Factor Authentication，但在存取另一應用程式時需要它。例如，取用者可以使用社交或本機帳戶登入汽車保險應用程式，但必須先驗證電話號碼，才能存取同個目錄中註冊的家庭保險應用程式。
- 一般而言，您在存取應用程式時不需要 Multi-Factor Authentication，但在存取其中的敏感部分資訊時則需要它。例如，取用者可使用社交或本機帳戶登入銀行應用程式來檢查帳戶餘額，但必須先完成電話號碼驗證，才能嘗試進行轉帳匯款作業。

## 修改註冊原則以啟用 Multi-Factor Authentication

1. [遵循下列步驟以瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [註冊原則]。
3. 按一下以開啟註冊原則 (例如 "B2C\_1\_SiUp")。
4. 按一下 [Multi-Factor Authentication]，並將 [狀態] 設為 [開啟]。按一下 [確定]。
5. 按一下刀鋒視窗頂端的 [儲存]。

您可以使用原則上的「立即執行」功能來驗證取用者體驗。確認下列項目：

在進行 Multi-Factor Authentication 步驟之前，會在目錄中建立取用者帳戶。在步驟執行過程中，系統會要求取用者提供電話號碼進行驗證。若驗證成功，會將電話號碼附加至取用者帳戶以供之後使用。即使取用者取消或卸除，下次登入時系統仍會要求其再度驗證電話號碼 (已啟用 Multi-Factor Authentication)。

## 修改登入原則以啟用 Multi-Factor Authentication

1. [遵循下列步驟以瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [登入原則]。
3. 按一下以開啟登入原則 (例如 "B2C\_1\_SiIn")。按一下刀鋒視窗頂端的 [編輯]。
4. 按一下 [Multi-Factor Authentication]，並將 [狀態] 設為 [開啟]。按一下 [確定]。
5. 按一下刀鋒視窗頂端的 [儲存]。

您可以使用原則上的「立即執行」功能來驗證取用者體驗。確認下列項目：

取用者登入 (使用社交或本機帳戶) 時，若已將通過驗證的電話號碼附加至取用者帳戶，系統會要求其進行驗證。如果未附加電話號碼，系統會要求取用者提供電話號碼以進行驗證。驗證成功之後，即會將電話號碼附加至取用者帳戶以供之後使用。

## 其他原則上的 Multi-Factor Authentication

如同對上面的註冊和登入原則所述，也可以對註冊或登入原則和密碼重設原則啟用 Multi-Factor Authentication。很快即可用於設定檔編輯原則。

<!---HONumber=AcomDC_0330_2016-->