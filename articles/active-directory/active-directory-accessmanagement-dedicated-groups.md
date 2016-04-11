<properties
	pageTitle="Azure Active Directory 中的專用群組 | Microsoft Azure"
	description="在 Azure Active Directory 中如何建立專用群組與其運作方式的概觀。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="curtand"/>

# Azure Active Directory 中的專用群組

在 Azure Active Directory (Azure AD) 中，專用群組功能會針對 Azure AD 預先定義的群組自動建立並填入成員資格。使用 Azure 傳統入口網站、Windows PowerShell Cmdlet 或以程式設計方式，均無法在專用群組中新增或移除成員。

>[AZURE.NOTE] 專用群組需要 Azure AD Premium 授權已指派給
>- 負責管理群組規則的系統管理員
>- 已由規則選取要成為群組成員的所有使用者

**啟用專用群組**

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [Active Directory]，然後開啟您組織的目錄。

2. 選取 [群組] 索引標籤，然後開啟您想要編輯的群組。

3. 選取 [設定] 索引標籤，然後將 [啟用專用群組] 設定為 [是]。

一旦將 [啟用專用群組] 參數設為 [**是**] 之後，您就可以將 [**啟用 [All Users] 群組**] 參數設為 [**是**]，進一步讓目錄自動建立 [All Users] 專用群組。接著，您也可以在 **[All Users] 群組欄位的 [顯示名稱]** 中輸入此專用群組的名稱進行編輯。

[All Users] 群組可用來將相同的權限指派給您目錄中的所有使用者。例如，您可以將 [All Users] 專用群組的存取權指派給此應用程式，以便為您目錄中的所有使用者授與 SaaS 應用程式的存取權。

專用的 [All Users] 群組包含目錄中的所有使用者，包括來賓與外部使用者。如果需要從群組中排除外部使用者，您可以使用如下的屬性型動態規則來建立群組：

				(user.userPrincipalName -notContains "#EXT#@")

如需從群組中排除所有來賓，請使用如下的規則：

				(user.userType -ne "Guest")

若要了解如何為動態群組成員資格建立進階規則 (可包含多個比較的規則)，請參閱[使用屬性來建立進階規則](active-directory-accessmanagement-groups-with-advanced-rules.md)。


這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0330_2016-->