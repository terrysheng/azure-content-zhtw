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
	ms.date="02/09/2016"
	ms.author="curtand"/>

# Azure Active Directory 中的專用群組

在 Azure Active Directory 中，會自動建立專用群組，也會自動建立專用群組的群組成員資格。您無法透過 Azure 入口網站、Windows PowerShell Cmdlet，或以程式設計方式，在專用群組中新增或移除成員。若要啟用專用群組，在 Azure 入口網站的 [設定] 索引標籤上，將 **[啟用專用群組] 參數設定為 [是]**。

一旦將 [啟用專用群組] 參數設為 [**是**] 之後，您就可以將 [**啟用 [All Users] 群組**] 參數設為 [**是**]，進一步讓目錄自動建立 [All Users] 專用群組。接著，您也可以在 **[All Users] 群組欄位的 [顯示名稱]** 中輸入此專用群組的名稱進行編輯。

如果您想要將相同的權限指派給您目錄中的所有使用者，[All Users] 專用群組可能會很實用。例如，您可以將 [All Users] 專用群組的存取權指派給此應用程式，以便為您目錄中的所有使用者授與 SaaS 應用程式的存取權。

請注意，專用的「所有使用者」群組包含目錄中的所有使用者，當中有來賓與外部使用者。如果您需要排除外部使用者的群組，您可以使用類似下面的動態規則建立群組

(user.userPrincipalName -notContains "#EXT#@")

如需排除所有來賓的群組，請使用類似下面的規則

(user.userType -ne "Guest")

本文將詳細說明如何建立規則，以管理 Azure Active Directory 中群組的成員：

* [建立簡單的規則，設定群組的動態成員資格](active-directory-accessmanagement-simplerulegroup.md)


這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0224_2016-->