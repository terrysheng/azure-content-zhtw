<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


#在 Azure Active Directory 中管理安全性群組

Azure Active Directory (Azure AD) 的其中一項主要功能是管理資源的存取權。這些資源可以是目錄的一部分，例如透過目錄中的角色或目錄外部的資源 (例如 SaaS 應用程式、Azure 服務以及 SharePoint 網站或內部部署資源) 管理物件的權限。資源擁有者可以指派群組存取資源，透過這種方式，授與該群組成員存取資源。群組的擁有者就可以管理群組的成員資格。實際上，資源擁有者是委派權限給群組的擁有者，以指派使用者存取其資源。


##如何建立及管理安全性群組嗎

**在 Azure 入口網站中建立群組**

1. 在 Azure 入口網站中，按一下 [Active Directory]，然後按一下貴組織的目錄名稱。
2. 按一下 [群組] 索引標籤。
3. 在 [群組] 頁面中，按一下 [新增群組]。
4. 在 [加入群組] 視窗中，指定群組的名稱與描述。
5. 您可以使用 Office 365 帳戶入口網站、Windows Intune 帳戶入口網站或 Azure 入口網站完成這項工作 (視貴組織所訂閱的服務而定)。如需使用入口網站管理 Azure Active Directory 的詳細資訊，請參閱[管理 Azure AD 目錄](active-directory-administer)。

## 如何指派或移除安全性群組中的使用者

**將成員加入 Azure 入口網站的群組**

1. 在 Azure 入口網站中，按一下 [Active Directory]，然後按一下貴組織的目錄名稱。
2. 按一下 [群組] 索引標籤。
3. 在 [群組] 頁面上，按一下您想要加入成員的群組名稱。根據預設，這會顯示所選群組的 [成員] 索引標籤。
4. 在該群組的頁面上，按一下 [加入成員]。
5. 在 [加入成員] 頁面上，按一下您想要加入此群組成員之使用者或群組的名稱，並確定這個名稱加入了 [已選取] 窗格中。


**移除 Azure 入口網站的群組成員**

1. 在 Azure 入口網站中，按一下 [Active Directory]，然後按一下貴組織的目錄名稱。
2. 按一下 [群組] 索引標籤。
3. 在 [群組] 頁面上，按一下您想要從其中移除成員的群組的名稱。
4. 在該群組的頁面上，按一下 [成員] 索引標籤。
5. 在該群組的頁面上，按一下您想要從這個群組移除之成員的名稱，然後按一下 [移除]。
6. 按一下 [是] 確定問題的回答，以確認您想要移除群組的這名成員。


## 如何使用規則動態管理安全性群組的成員

**若要啟用特定群組的動態成員資格，請執行下列步驟：**

1. 在 Azure 入口網站的 [群組] 索引標籤底下，選取您想要編輯的群組，然後在此群組的 [設定] 索引標籤中，將 [啟用動態成員資格] 參數設定為 [是]。
2. 您現在可以為將控制此群組的動態成員資格如何運作的群組，設定一個簡單的單一規則。請確定已選取 [加入使用者] 選項按鈕，然後從下拉式功能表中選取一個使用者屬性 (例如，department、jobTitle 等)。
3. 接著，選取一個條件 (不等於、等於、開頭不是、開頭為、不包含、包含、不符合、符合)，最後指定所選使用者屬性的值。

例如，如果某個群組指派給 SaaS 應用程式 (如需詳細資訊，請參閱「在 Azure AD 中將群組存取權指派給 SaaS 應用程式」)，而且您透過設定一個規則，藉以新增設定為 Equals(-eq)Sales Rep 的 jobTitle 的使用者，以啟用此群組的動態成員資格，則在您的 Azure AD 目錄中，其職稱設定為 Sales Rep 的所有使用者將可以存取此 SaaS 應用程式。

## 其他資訊

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->