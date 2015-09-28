<properties
	pageTitle="Azure Active Directory B2C 預覽：自訂屬性 | Microsoft Azure"
	description="如何在 Azure Active Directory B2C 中使用自訂屬性收集取用者相關資訊"
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

#  Azure Active Directory B2C 預覽：使用「自訂屬性」來收集取用者相關資訊

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

您的 Azure Active Directory (AD) B2C 目錄隨附一組內建屬性；例如名字、姓氏、城市、郵遞區號等等。不過，每個取用者導向應用程式針對想從取用者收集的資訊 (屬性)，皆具有獨特需求。Azure AD B2C 可讓您擴充目錄 (具體而言，您可擴充儲存在每個取用者帳戶上的屬性集合)。您可在 [Azure Preview 入口網站](https://portal.azure.com/)上建立自訂屬性，並將其使用於註冊原則，如下所示。您亦可使用「Azure AD 圖形 API」來讀取和寫入這些屬性，如[這裡](active-directory-b2c-devquickstarts-graph-dotnet.md)所示。

> [AZURE.NOTE]自訂屬性基本上會使用「[Azure AD 圖形 API 目錄結構描述延伸模組](https://msdn.microsoft.com/library/azure/dn720459.aspx)」。

## 如何建立自訂屬性

1. [瀏覽至 Azure Preview 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)...
2. 按一下 [使用者屬性]。
3. 按一下刀鋒視窗頂端的 [新增]。
4. 提供自訂屬性的**名稱** (例如「ShoeSize」) 和**說明** (選擇性)。按一下 [建立]。大功告成！

    > [AZURE.NOTE]目前僅可使用 "String" **資料類型**。我們日後將會新增更多資料類型 (例如 DateTime、Integer 等等)。

自訂屬性現已在**使用者屬性**清單中提供，並可在您的註冊原則中使用。

## 如何在您的註冊原則中使用自訂屬性

1. 瀏覽至 [Azure Preview 入口網站](htts://portal.azure.com/)上的 B2C 功能刀鋒視窗。有關如何執行此操作的說明，請參閱[這裡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [註冊原則]。
3. 按一下以開啟註冊原則 (例如「B2C\_1\_SiUp」)。按一下刀鋒視窗頂端的 [編輯]。
4. 按一下 [註冊屬性]，然後選取自訂屬性 (例如「ShoeSize」)。按一下 [確定]。
5. 按一下 [應用程式宣告]，然後選取自訂屬性。按一下 [確定]。 
6. 按一下刀鋒視窗頂端的 [儲存]。大功告成！

您可以使用原則上的「立即執行」功能來驗證取用者體驗。現在您應會在取用者註冊期間收集之屬性的清單中看見「ShoeSize」，其亦會在傳回至您應用程式的權杖中出現。

<!---HONumber=Sept15_HO3-->