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
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="curtand"/>


# 在 Azure Active Directory 中管理群組

Azure Active Directory (Azure AD) 的其中一項主要功能是管理資源的存取權。這些資源可以是目錄中的物件，或是目錄外部的資源 (例如 SaaS 應用程式、Azure 服務、SharePoint 網站或內部部署資源)。此外，資源擁有者可以指派 Azure AD 群組資源的存取權。這會將資源的存取權授與該群組的成員。然後，群組擁有者負責管理群組中的成員資格。實際上，資源擁有者是將指派使用者至其資源的權限委派給群組擁有者。

## 如何建立安全性群組？

**建立群組**

您可以使用 Office 365 帳戶入口網站、Windows Intune 帳戶入口網站或 Azure 入口網站完成這項工作 (視貴組織所訂閱的服務而定)。如需使用入口網站管理 Azure Active Directory 的詳細資訊，請參閱[管理 Azure AD 目錄](active-directory-administer.md)。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中選取 [Active Directory]，然後選取您組織的目錄名稱。

2. 選取 [群組] 索引標籤。

3. 選取 [新增群組]。

4. 在 [新增群組] 視窗中，指定群組的名稱與描述。


## 如何新增或移除安全性群組中的個別使用者？

**新增個別使用者到群組**

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中選取 [Active Directory]，然後選取您組織的目錄名稱。

2. 選取 [群組] 索引標籤。

3. 開啟要在其中新增成員的群組。根據預設，這會顯示所選群組的 [成員] 索引標籤。

4. 選取 [新增成員]。

5. 在 [新增成員] 頁面上，選取您想要加入此群組成員之使用者或群組的名稱，並確定這個名稱加入了 [已選取] 窗格中。


**從群組中移除個別使用者**

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中選取 [Active Directory]，然後選取您組織的目錄名稱。

2. 選取 [群組] 索引標籤。

3. 開啟要從中移除成員的群組。

4. 選取 [成員] 索引標籤，選取您想要從這個群組移除之成員的名稱，然後按一下 [移除]。

6. 在提示中確認您想要從群組中移除這個成員。


## 如何動態管理群組的成員資格？

在 Azure AD 中，您可以非常輕鬆地設定一個簡單的規則 (只進行單一比較的規則) 來判斷哪些使用者要成為群組的成員。例如，如果某個群組已指派給 SaaS 應用程式，而且您設定了規則來新增職稱為「銷售代表」的使用者，則 Azure AD 目錄中所有具有該職稱的使用者都能存取此 SaaS 應用程式。

> [AZURE.NOTE] 您可以為安全性群組或 Office 365 群組的動態成員資格設定規則。目前對應用程式的群組式指派並不支援巢狀群組成員資格。群組的動態成員資格需要 Azure AD Premium 授權已指派給
>- (負責管理群組規則的系統管理員)
>- 已由規則選取要成為群組成員的所有使用者。

**啟用群組的動態成員資格**

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中選取 [Active Directory]，然後選取您組織的目錄名稱。

2. 選取 [群組] 索引標籤，然後開啟您想要編輯的群組。

3. 選取 [設定] 索引標籤，然後將 [啟用動態成員資格] 設定為 [是]。

4. 為將要控制此群組的動態成員資格如何運作的群組，設定一個簡單的規則。請確定已選取 [新增使用者位置] 選項，然後從清單 (例如，department、jobTitle 等) 中選取一個使用者屬性。

5. 接著，選取一個條件 (不等於、等於、開頭不是、開頭為、不包含、包含、不符合、符合)，最後指定所選使用者屬性的值。

若要了解如何為動態群組成員資格建立進階規則 (可包含多個比較的規則)，請參閱[使用屬性來建立進階規則](active-directory-accessmanagement-groups-with-advanced-rules.md)。

## 其他資訊

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0330_2016-->