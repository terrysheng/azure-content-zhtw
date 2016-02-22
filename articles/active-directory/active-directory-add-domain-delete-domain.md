<properties
	pageTitle="刪除 Azure Active Directory 中的自訂網域 | Microsoft Azure"
	description="如何刪除 Azure Active Directory 中的自訂網域"
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
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# 刪除自訂網域名稱

您可以刪除您不再需要搭配 Azure AD 目錄使用的自訂網域名稱；例如，如果您有新的公司名稱、如果您使用不同的 Azure AD 目錄等等。您也可以刪除未驗證的網域，例如，如果您在新增之後發現名稱輸入錯誤，或您未正確設定有關網域是否為同盟的值。

## 開始之前

移除網域名稱之前，建議您閱讀下列資訊：

- 無法移除在註冊時為目錄所提供的原始 contoso.onmicrosoft.com 網域名稱。
- 除非移除子網域，否則無法移除任何具有與其相關聯的子網域的最上層網域。例如，如果您有 corp.adatum.com 或另一個使用最上層網域名稱的子網域，則無法移除 adatum.com。如需詳細資訊，請參閱支援文章[當您嘗試移除 Office 365 的網域時發生「網域有關聯的子網域」或「您無法移除有子網域的網域」錯誤](https://support.microsoft.com/kb/2787792/)。
- 是否已啟用目錄同步處理？ 如果是，則會將網域自動新增至您的帳戶，如下所示：contoso.mail.onmicrosoft.com。無法移除此網域名稱。
- 您必須先移除與網域相關聯的所有使用者或電子郵件帳戶中的網域名稱，才能移除網域名稱。您可以移除所有帳戶，也可以大量編輯使用者帳戶來變更其網域名稱資訊和電子郵件地址。如需詳細資訊，請參閱[在 Azure AD 中建立或編輯使用者](active-directory-create-users.md)。請務必移除：

	-   使用者名稱或電子郵件地址中有網域的任何使用者

	-   電子郵件地址中有網域的任何擁有郵件功能的群組

	-   網域屬於其回覆 URL 一部分的任何應用程式

- 如果您是將 SharePoint Online 網站裝載於正用於 SharePoint Online 網站集合的網域名稱，則必須先刪除網站集合，才能移除網域名稱。

## 刪除網域

1.  使用具有該目錄之全域管理員權限的帳戶登入 Azure 傳統入口網站。

2.  開啟您的目錄，然後選取 [網域]。

3.  選取網域，然後按一下 [刪除]。

## 後續步驟

- [使用自訂網域名稱，以簡化您的使用者的登入體驗](active-directory-add-domain.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [Add and verify a custom domain name in Azure Active Directory (新增及驗證 Azure Active Directory 中的自訂網域名稱)](active-directory-add-domain-add-verify-general.md)
- [將使用者指派至自訂網域](active-directory-add-domain-add-users.md)
- [變更您的自訂網域名稱的 DNS 註冊機構](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->