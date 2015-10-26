<properties 
    pageTitle="我可以依賴 Azure AD 啟用單一登入 (SSO) 至我的所有應用程式 | Microsoft Azure" 
    description="了解 Azure Active Directory 如何協助您延伸身分識別的範圍和其管理。" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# 我可以依賴 Azure AD 啟用單一登入 (SSO) 至我的所有應用程式
本文的目標對象是想要深入了解 Azure Active Directory 為貴組織使用的應用程式所提供商務價值的 IT 決策者。

## 在以雲端式環境中執行應用程式  

現在，在雲端式世界中，您可以找到數千個應用程式來完成任何工作。IT 部門通常不太知道在他們的組織中使用的所有 SaaS 應用程式。在許多情況下，知道使用應用程式但是要取得任何層級的控管和安全性卻是昂貴又耗時。許多應用程式不支援與企業身分識別系統整合，在其他情況下，則由於成本與複雜度而避免一次性整合。

如此一來，

- 許多組織對未經授權存取公司資料、可能的資料外洩以及未受管理的應用程式所引入的應用程式中固有的安全性風險都有所顧慮。因為他們甚至不知道使用了多少應用程式或使用了哪些應用程式，即使是開始建置應付這些風險的方案，似乎都會令人怯步。
- 系統管理員需要個別管理由您的環境中受管理應用程式推出的所有 IAM 服務提供者。這包括建立和刪除使用者和群組以及授與或撤銷對這些應用程式的存取等工作。
- 您的使用者需要記住各種認證，才能存取他們需要處理的應用程式。忘記的密碼代表許多組織營運成本的巨大衝擊。這所有問題的共通點是他們對使用者的產能和您的營運成本有負面影響。  
 
## Azure Active Directory 有何助益？
Azure Active Directory 可協助您解決這些問題，方法是讓您輕鬆將現有身分識別基礎結構延伸至雲端，並且藉此：

- 將您的行動使用者的範圍延伸到任何應用程式 
- 將存取管理的範圍延伸到任何雲端應用程式 
- 偵測使用者使用/存取的應用程式


### 將您的身分識別的範圍延伸到任何應用程式 

根據設計，Azure Active Directory 會為您提供 SSO 連接至裝載於 Azure 和其他 Microsoft 線上服務 (例如 Office 365、CRM Online 和 Intune) 的雲端應用程式。

此外，它可以是您的身分識別訊息代理程式，用於單一登入至 Google、Amazon、IBM 等等的其他任何公用雲端中的應用程式。<br> 如果應用程式已廣為人知或受歡迎，我們會將其預先整合至 Azure Active Directory 的應用程式資源庫。現今，您可以在應用程式資源庫中找到約 2500 個預先整合的應用程式，而數量正持續成長。<br> 在應用程式資源庫中為任何預先整合的應用程式設定 SSO 只需按幾下滑鼠。如果應用程式資源庫中尚未列出我的應用程式？ 您可以使用精靈將任何公用應用程式整合到您的 Azure Active Directory，並為其啟用 SSO。如需詳細資訊，請參閱[使用 Azure Active Directory 為新取得的 SaaS 應用程式部署單一登入](active-directory-single-sign-on-newly-acquired-saas-apps.md)和[整合 Azure Active Directory SSO 與現有的應用程式](active-directory-sso-integrate-existing-apps.md)<br>組織所開發的應用程式呢？ 我們提供相關文件，可讓您的開發人員輕鬆地將組織所開發的應用程式整合到 Azure Active Directory。如果您想要深入了解，請參閱 [Azure AD 和應用程式：引導開發人員](active-directory-applications-guiding-developers-for-lob-applications.md)。

提供您預先整合應用程式、不在應用程式資源庫中的公用應用程式和您組織所開發的應用程式的支援，Azure Active Directory 可讓您對所有雲端應用程式提供 SSO。

Azure Active Directory 的值超出「僅」雲端應用程式。有了 Azure Active Directory，您也可以藉由提供透過 SSO 安全遠端存取內部部署應用程式，來延伸 Azure 身分識別的範圍。這表示，不需要 VPN 之類技術或其他傳統遠端存取基礎結構，即可利用遠端存取來存取內部部署 Web 應用程式。Azure Active Directory 透過應用程式 Proxy 功能提供這項功能。

傳統 SSO 模型是基於兩個身分識別儲存機制中的兩個個別帳戶的對應。有了 Azure Active Directory，您甚至可以將個別帳戶與共用的公司帳戶對應，例如公司的 Twitter 帳戶。藉由為公司共用帳戶實作 SSO，則不需要實際與您的使用者共用帳戶認證，這會大幅增加這些帳戶的保護。如果您想要深入了解，請參閱 [共用帳戶與 Azure AD](active-directory-sharing-accounts.md)

透過延伸您的身分識別的範圍，一個密碼即可為您提供數千個應用程式的存取。



### 將存取管理的範圍延伸到任何雲端應用程式

管理對雲端應用程式存取的成本會非常高昂 - 如果您必須以每個應用程式為基礎手動管理。利用 Azure Active Directory，您可以根據來自中心點 - 即 Azure 入口網站的群組來管理對雲端應用程式的存取權。您可以指派存取權給個別使用者或甚至群組。如需詳細資訊，請參閱[管理應用程式的存取](active-directory-managing-access-to-apps.md)。

某些應用程式，例如 Salesforce、Box、Google Apps 和 Concur 提供自動化介面用於建立和移除 (或停用) 帳戶。如果有提供者提供這樣的介面，Azure Active Directory 會加以利用。換句話說，對於提供相關自動化介面的應用程式，Azure Active Directory 提供自動化使用者佈建的支援。

使用自動使用者佈建：

- 當使用者已被授與相關應用程式的存取權時，Azure Active Directory 會自動佈建需要的 SSO 帳戶至應用程式。
- Azure Active Directory 中使用者的帳戶被刪除時，應用程式中的帳戶會被停用或刪除。設定自動化使用者佈建提供者並提供您下列優點：
- 	它可減少您的營運成本，因為它會將必須由您的 IT 人員執行的管理工作自動化。
- 它可以改善您環境的安全性，因為它會減少不必要存取已存在應用程式的機會。

如需自動化使用者佈建的詳細資訊，請參閱[自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)。


### 偵測使用者使用/存取的應用程式

使用 SSO 和帳戶佈建，Azure Active Directory 為您提供功能強大的機制來改善您的使用者和系統管理員可以使用您環境中已知應用程式的方式。不過，在現代企業中，IT 部門並通常不知道使用的所有雲端應用程式。利用雲端應用程式探索，Azure Active Directory 也為您提供偵測這些應用程式的解決方案。使用 Cloud App Discovery，您可以：

- 探索使用中的應用程式，並依使用者數目、流量或應用程式的 Web 要求數目，測量使用量。
- 識別正在使用應用程式的使用者。
- 匯出資料以進行其他離線分析。
- 設定讓應用程式受到 IT 控制的優先順序，並輕鬆地整合應用程式，以啟用單一登入和使用者管理功能。

如需雲端應用程式探索的詳細資訊，請參閱[如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)。


## 如何開始使用？

首先，如果您還未使用 Azure AD，而且您是 IT 系統管理員：

- [試試看！](https://azure.microsoft.com/trial/get-started-active-directory)您可以立即註冊免費 30 天的試用版，使用此連結不到 5 分鐘即可部署第一個雲端解決方案

<!---HONumber=Oct15_HO3-->