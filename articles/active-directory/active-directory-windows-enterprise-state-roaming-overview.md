<properties
	pageTitle="企業狀態漫遊概觀 | Microsoft Azure"
	description="提供 Windows 裝置中企業狀態漫遊設定的相關資訊。企業狀態漫遊提供使用者跨 Windows 裝置的一致體驗，並且減少設定新的裝置所需的時間。"
	services="active-directory"
    keywords="什麼是企業狀態漫遊, 企業同步處理, windows 雲端"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# 企業狀態漫遊概觀

使用 Windows 10，[Azure Active Directory (Azure AD)](active-directory-whatis.md) 使用者能夠安全地將其使用者設定和應用程式設定資料同步處理至雲端。企業狀態漫遊提供使用者跨 Windows 裝置的一致體驗，並且減少設定新的裝置所需的時間。企業狀態漫遊的運作類似於首先在 Windows 8 中引進的標準[取用者設定同步處理](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs)。此外，企業狀態漫遊提供：

- **分隔公司和取用者資料** – 組織可以控制他們的資料，取用者雲端帳戶中不會混合公司資料，企業雲端帳戶中也不會混合取用者資料。 
- **增強式安全性** – 資料會在離開使用者的 Windows 10 裝置之前自動加密，方法是使用 Azure Rights Management (Azure RMS)，資料在雲端中待用時會保持加密。所有內容在雲端中待用時會保持加密，除了命名空間，例如設定名稱和 Windows 應用程式名稱。  
- **管理和監視服務** – 對於誰同步處理您組織中的設定以及在哪些裝置上的更多控制權和可見性。 
- **雲端中資料的地理位置** – 資料會根據 Azure AD 網域的國家/地區，儲存在 Azure 區域中。 



| 文章 | 說明 |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [在 Azure Active Directory 中啟用企業狀態漫遊](active-directory-windows-enterprise-state-roaming-enable.md) | 企業狀態漫遊適用於具有 Premium Azure Active Directory (Azure AD) 訂用帳戶的任何組織。如需有關如何取得 Azure AD 訂用帳戶的詳細資訊，請參閱 Azure AD 產品頁面。 |
| [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md) | 本主題將回答 IT 系統管理員可能會遇到的設定和應用程式資料同步處理的一些問題。 |
| [設定同步處理的群組原則和 MDM 設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10 提供群組原則和行動裝置管理 (MDM) 原則設定，以限制設定同步處理。 |
| [Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | 以下是在 Windows 10 中進行漫遊及/或備份的所有設定的完整清單。 |

<!---HONumber=AcomDC_0204_2016-->