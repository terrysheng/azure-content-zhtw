<properties
	pageTitle="Azure Active Directory B2C 預覽：支援 | Microsoft Azure"
	description="如何提出 Azure Active Directory B2C 的支援要求"
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
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽：如何提出 Azure Active Directory B2C 的支援要求

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

您可使用下列步驟，在 Azure 入口網站上提出 Azure Active Directory (AD) B2C 的支援要求：

1. [遵循下列步驟以瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 從 B2C 租用戶切換至另一個擁有相關聯 Azure 訂用帳戶的租用戶。後者通常為員工租用戶，或是您註冊 Azure 訂用帳戶時所建立的預設租用戶。若要深入瞭解 Azure 訂用帳戶與 Azure AD 目錄之間的關係，請詳閱[本文](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad)。

    > [AZURE.IMPORTANT]此為必要步驟。若您未執行此步驟，處理程序將會失敗。

    ![支援 - 切換目錄](./media/active-directory-b2c-support/support-switch-dir.png)

3. 切換目錄後，按一下 [說明 + 支援]。

    ![支援 - 支援](./media/active-directory-b2c-support/support-support.png)

4. 遵循[本文](http://blogs.msdn.com/b/mast/archive/2013/10/24/windows-azure-technical-support-for-msdn-technet-or-mpn-users-and-partners.aspx)中所述的步驟，針對 Azure AD B2C 提出支援要求。使用以下詳細資料完成步驟：

    - **要求類型**為**技術**。
	- **資源**為 **Active Directory**。
	- 在 [問題類別] 刀鋒視窗中，選取 [B2C 預覽] 做為 [問題類型] 與適當 [類別]。
	- 在 [說明] 刀鋒視窗中，描述問題的詳細資料。在 [資源] 文字方塊中，提供 B2C 租用戶的名稱；例如 contosob2c.onmicrosoft.com。

5. 提交支援要求後，您可在「開始面板」上按一下 [說明 + 支援]，然後按一下 [管理支援要求]。

## 已知問題：在 B2C 租用戶的內容中提出支援要求

若您遺漏上述的步驟 2，且嘗試在 B2C 租用戶的內容中建立支援要求，您將會看到以下錯誤。

> [AZURE.IMPORTANT]請不要嘗試在 B2C 租用戶中註冊新的 Azure 訂用帳戶。

![支援 - 無訂用帳戶](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0107_2016-->