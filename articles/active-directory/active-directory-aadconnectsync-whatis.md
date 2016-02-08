<properties
	pageTitle="Azure AD Connect 同步處理：了解及自訂同步處理 | Microsoft Azure"
	description="說明 Azure AD Connect 同步處理如何運作及如何自訂。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：了解及自訂同步處理
Azure Active Directory Connect 同步處理服務 (Azure AD Connect 同步處理) 是 Azure AD Connect 的主要元件，負責與在您內部部署環境和雲端中 Azure AD 間同步處理身分識別資料相關的所有操作。Azure AD Connect 同步處理是 DirSync、Azure AD 同步，以及已設定 Azure Active Directory 連接器之 Forefront Identity Manager 的後續版本。

本主題是 **Azure AD Connect 同步處理** (也稱為**同步處理引擎**) 的基地，並列出與其相關的所有其他主題連結。如需 Azure AD Connect 的連結，請參閱[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

## Azure AD Connect 同步處理主題

| 主題 | 涵蓋內容和讀取時機 |
| ----- | ----- |
| **Azure AD Connect 同步處理基本概念** ||
| [了解架構](active-directory-aadconnectsync-understanding-architecture.md) | 適合於不熟悉同步處理引擎並想要深入了解所用架構和詞彙的人員。 |
| [技術概念](active-directory-aadconnectsync-technical-concepts.md) | 精簡版的架構主題，簡要說明所用的詞彙。 |
| [Azure AD Connect 的拓撲](active-directory-aadconnect-topologies.md) | 說明同步處理引擎支援的各種拓撲和案例。 |
| **自訂組態** ||
| [了解預設組態](active-directory-aadconnectsync-understanding-default-configuration.md)| 說明現成可用的規則和預設組態還說明規則如何一起運作，以供現成可用的案例使用。 |
| [了解使用者和連絡人](active-directory-aadconnectsync-understanding-users-and-contacts.md) | 延續前一個主題，並說明使用者和連絡人的組態如何一起運作 (特別是在多樹系的環境中)。 |
| [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | 深入了解組態模式的運作方式和運算式語言的語法。 |
| [變更預設組態的最佳作法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | 當您知道上述主題的詳細資料並需要變更現成可用的組態，以搭配您的案例或您的需求使用時。 |
| [設定篩選](active-directory-aadconnectsync-configure-filtering.md) | 說明如何限制哪些物件正同步處理至 Azure AD 的各種選項，以及逐步說明如何設定這些選項。 |
| **功能** ||
| [實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md) | 說明密碼同步處理的運作方式、實作方式，以及其操作方式及疑難排解方式。 |
| [防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | 說明 [防止意外刪除] 功能以及如何加以設定。 |
| **作業** ||
| [作業工作和考量](active-directory-aadconnectsync-operations.md) | 說明作業考量，例如災害復原。 |
| **詳細資訊和參考** ||
| [連接埠](active-directory-aadconnect-ports.md) | 列出您需要在同步處理引擎以及內部部署目錄與 Azure AD 之間開啟的連接埠。 |
| [將屬性同步處理至 Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | 列出在內部部署 AD 與 Azure AD 之間進行同步處理的所有屬性。 |
| [函式參考](active-directory-aadconnectsync-functions-reference.md) | 列出宣告式佈建中可用的所有函式。 |

## 其他資源

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->