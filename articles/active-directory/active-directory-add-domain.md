<properties
	pageTitle="使用自訂網域名稱，以簡化您的使用者的登入經驗 |Microsoft Azure"
	description="說明如何將您自己的網域名稱新增至 Azure Active Directory (Azure AD)，以及其他相關資訊。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# 使用自訂網域名稱，以簡化您的使用者的登入體驗

您可以使用您自己的自訂網域，改善及簡化 Azure Active Directory 中的登入和其他使用者體驗。例如，如果您的組織擁有網域名稱 'contoso.com'，則您的使用者可利用熟悉的使用者名稱 (例如 'joe@contoso.com') 登入。

Azure Active Directory 中的每個目錄隨附內建網域名稱，格式為 'contoso.onmicrosoft.com'，可讓您開始使用 Azure 或其他 Microsoft 服務。[深入了解內建網域](#built-in-domain-names-for-azure-active-directory)。

## 在 Azure AD 中使用自訂網域名稱

如果貴組織擁有您的使用者熟悉的自訂網域名稱，則最佳作法是在 Azure Active Directory 中使用該自訂網域名稱。若要在 Azure Active Directory 中使用自訂網域名稱，您：

-   [新增並驗證您的自訂網域名稱](active-directory-add-domain-add-verify-general.md)

-   [將使用者指派至自訂網域](active-directory-add-domain-add-users.md)

## 在 Office 365 和其他服務中使用自訂網域名稱

如同您的 Azure AD 中的其他資源，您已新增並驗證的自訂網域名稱可以在 Office 365、Intune 及使用 Azure AD 的其他應用程式中使用。例如，在 Exchange Online 中使用自訂網域名稱可讓使用者以熟悉的電子郵件地址 (例如 joe@contoso.com) 傳送和接收電子郵件。若要讓這些其他應用程式使用自訂網域，您必須在 DNS 註冊機構新增其他 DNS 項目，如應用程式所記載。

-   [在 Office 365 中使用自訂網域](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=zh-TW&rs=zh-TW&ad=US)

-   [在 Intune 中使用自訂網域](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## 在 Azure AD 中管理網域名稱

在 Azure Active Directory 中，網域名稱逐漸不需要持續管理或系統管理。

-   [請參閱 Azure Active Directory 中的網域名稱的清單](active-directory-add-domain-add-users.md)

-   [如果您變更您的自訂網域名稱的 DNS 註冊機構，該怎麼辦](active-directory-add-domain-change-registrar.md)

## 刪除自訂網域名稱

如果貴組織不再使用該網域名稱，或如果您需要在另一個 Azure AD 中使用該網域名稱，您可以從您的 Azure AD 刪除自訂網域名稱。

-   [刪除自訂網域名稱](#_Deleting_a_custom)

## Azure Active Directory 的內建網域名稱

分辨 Azure Active Directory (Azure AD) 中的內建網域和您新增的自訂網域之間的差異。

-   內建：隨附於 Azure AD 目錄的網域，格式為 contoso.onmicrosoft.com

-   自訂：貴組織已擁有的網域名稱，例如 contoso.com

## 使用 PowerShell 或圖形 API 來管理網域名稱

Azure Active Directory 中網域的大部分管理工作也可以使用 Microsoft PowerShell，或使用圖形 API 以程式設計方式來完成。

-   [使用 PowerShell 管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [使用圖形 API 管理 Azure AD 中的網域名稱 (預覽)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## 後續步驟

如果您需要額外的資源來了解 Azure Active Directory 中的網域名稱使用，請嘗試：

- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [新增及驗證 Azure Active Directory 中的自訂網域名稱](active-directory-add-domain-add-verify-general.md)
- [將使用者指派至自訂網域](active-directory-add-domain-add-users.md)
- [變更您的自訂網域名稱的 DNS 註冊機構](active-directory-add-domain-change-registrar.md)
- [刪除 Azure Active Directory 中的自訂網域](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->