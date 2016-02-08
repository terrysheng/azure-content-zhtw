<properties
	pageTitle="Azure AD 和應用程式：引導開發人員 | Microsoft Azure"
	description="針對 IT 專業人員所撰寫，本文提供整合 Azure 應用程式與 Active Directory 的指導方針。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="kgremban"/>

# Azure AD 和應用程式：引導開發人員

## 概觀

本指南提供開發用於 Azure Active Directory (AD) 的企業營運 (LoB) 應用程式的概觀，是專為 Active Directory/Office 365 全域系統管理員撰寫的。

建置整合 Azure AD 的應用程式，可讓您組織的使用者使用 Office 365 單一登入。在 Azure AD 中擁有應用程式可讓您控制為應用程式設定的驗證原則。若要深入了解條件式存取和如何使用 Multi-Factor Authentication (MFA) 保護應用程式，請參閱下列文件：[設定存取規則](active-directory-conditional-access-azuread-connected-apps.md)。

您的應用程式必須註冊，才能使用 Azure Active Directory。註冊應用程式可讓組織的開發人員使用 Azure AD 驗證組織的成員，以及要求存取他們的使用者資源，例如電子郵件、行事曆及文件等。

您目錄的任何成員 (不是來賓) 都可以註冊應用程式，亦稱為*建立應用程式物件*。

註冊應用程式可讓任一使用者執行下列動作：

- 取得 Azure AD 識別的應用程式的身分識別
- 取得應用程式可用來向 AD 驗證其身分的一個或多個密碼/金鑰
- 在 Azure 入口網站中指定應用程式的品牌名稱、標誌等
- 針對他們的應用程式使用 Azure AD 授權功能
  - 應用程式角色型存取控制 (RBAC)
  - 以 Azure Active Directory 做為 oAuth 授權伺服器 (保護應用程式公開的 API)

- 宣告讓應用程式如預期般運作所需的必要權限。這些包括：
	  - 應用程式權限 (僅限全域系統管理員)。例如：
	    - 另一個 Azure AD 應用程式中的角色成員資格，或相對於 Azure 資源、資源群組或訂用帳戶的角色成員資格
	  - 委派的權限 (任何使用者)。例如：
	    - (Azure AD) 登入和讀取設定檔
	    - (Exchange) 讀取郵件、傳送郵件
	    - (SharePoint) 讀取

> [AZURE.NOTE]根據預設，任何成員都可以註冊應用程式。若要了解如何限定只有特定成員擁有註冊應用程式的權限，請參閱[應用程式如何加入 Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance) 文件。

以下是身為全域系統管理員的您協助開發人員讓他們的應用程式進入生產階段所必須做的事：

- 設定存取規則 (存取原則/MFA)
- 設定應用程式需要指派使用者並指派使用者
- 隱藏預設的使用者同意體驗

## 設定存取規則

如前所述，請參閱下列文章以深入了解設定任何應用程式的存取規則。

[設定存取規則](active-directory-conditional-access-azuread-connected-apps.md)。

## 設定應用程式需要指派使用者並指派使用者

根據預設，要讓使用者存取應用程式，並不需要指派使用者。不過，如果應用程式公開角色或您希望應用程式出現在使用者的存取面板，您應該需要指派使用者，以及指派使用者和/或群組。

[需要指派使用者](active-directory-applications-guiding-developers-requiring-user-assignment.md)

如果您是 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 的訂閱者，我們強烈建議使用群組。將群組指派給應用程式，可讓您將持續進行的存取管理委派給群組擁有者。您可以建立群組，或使用群組管理功能要求您組織中負責的對象建立群組。

[將使用者指派至應用程式](active-directory-applications-guiding-developers-assigning-users.md) [將群組指派給應用程式](active-directory-applications-guiding-developers-assigning-groups.md)

## 隱藏使用者同意

根據預設，使用者必須同意使用權限才能登入。同意體驗 (被要求授予應用程式使用權限) 對於不熟悉必須做這類決定的使用者會令其不安。

對於您信任的應用程式，您可以代表您組織中的所有使用者同意應用程式。

如需 Azure 中的使用者同意和同意體驗的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](active-directory-integrating-applications.md)。

<!---HONumber=AcomDC_0128_2016-->