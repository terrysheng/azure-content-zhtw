<properties
  pageTitle="使用 Azure AD 管理應用程式的存取 | Microsoft Azure"
  description="描述 Azure Active Directory 如何讓組織可以指定每個使用者擁有存取權的應用程式。"
  services="active-directory"
  documentationCenter=""
  authors="msStevenPo"
  manager="stevenpo"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/12/2015"
  ms.author="stevenpo"/>


# 管理應用程式的存取

在應用程式整合到您的組織的身分識別系統之後，進行中的存取管理、使用方式評估和報告持續成為一項挑戰。在許多情況下，您的 IT 系統管理員或技術服務人員需要對管理您的應用程式的存取權持續採取主動角色。有時候，指派是由一般或分區 IT 小組執行。在許多情況下，指派決策要由商務決策人員刪除，在 IT 進行指派之前需要其核准。其他組織投資於與現有的自動化身分識別與存取管理系統的整合，像是角色型存取控制 (RBAC) 或屬性型存取控制 (ABAC)。整合與規則開發傾向於特製化且昂貴。監視或報告任一管理方式是自己單獨、昂貴且複雜的投資。

## Azure Active Directory 有何助益？

 Azure AD 對於已設定的應用程式支援廣泛的存取管理，讓組織得以透過委派並納入系統管理員管理，輕易地達成正確的存取原則，範圍包括自動、屬性式指派 (ABAC 或 RBAC 案例)。有了 Azure AD，您可以輕鬆地達成複雜的原則，結合單一應用程式的多個管理模型並且甚至可以在具有相同對象的應用程式之間重覆使用管理規則。

 - [加入新的應用程式](active-directory-sso-newly-acquired-saas-apps.md)
 - [加入現有的應用程式](active-directory-sso-integrate-existing-apps)

 Azure AD 的應用程式指派著重於兩種主要的作業模式：- 個別指派：具有全域、使用者或 AU 權限的 IT 系統管理員可以選取個別的使用者帳戶和授與這些使用者存取應用程式。- 群組型指派 (僅限付費 Azure AD)：具有全域、使用者或 AU 權限的 IT 系統管理員可以將群組指派給應用程式。特定使用者的存取權決於在使用者嘗試存取應用程式時，使用者是否為群組的成員。在此模式中，系統管理員可以有效地建立指派規則，說明「獲指派群組的任何現有成員可存取應用程式」。使用這個指派選項，系統管理員可以受益於 Azure AD 群組管理選項，包括屬性式動態群組、外部系統群組 (例如：內部部署的 AD 或工作日)、系統管理員或自助管理的群組。單一群組可以輕鬆地指派給多個應用程式，確保與指派同質性的應用程式可以共用指派規則，降低整體管理複雜度。

系統管理員可以使用這些兩種指派模式，達成任何令人需要的指派管理方法。

利用 Azure AD，使用方式和指派報告可完全整合，讓系統管理員輕鬆地報告指派狀態、指派錯誤和平均使用量。

## Azure AD 的複雜應用程式指派

考慮 Salesforce 之類的應用程式。在許多組織中，Salesforce 主要是由行銷和銷售組織使用。通常，行銷小組的成員對 Salesforce 具有高特殊權限存取權，而銷售小組的成員具有限制存取權。在許多情況下，資訊工作者的廣大人口具有對應用程式的限制存取權。這些規則的例外狀況會使情況更加複雜。它通常是行銷或銷售領導小組的特權，用來授與使用者存取或在這些一般規則外個別變更其角色。

利用 Azure AD，可以預先設定 Salesforce 之類的應用程式使用單一登入 (SSO) 和自動化佈建。一旦設定了應用程式，系統管理員可以採取一次性動作來建立及指派適當的群組。在此範例中，系統管理員可以執行下列指派：

- 可以定義屬性式群組，以自動代表行銷和銷售小組的所有成員使用部門或角色之類的屬性：
    - 行銷群組的所有成員會都指派給 salesforce 中的「行銷」角色
    - 銷售小組群組的所有成員會都指派給 salesforce 中的「銷售」角色。進一步細分可以使用代表指派給不同 salesforce 角色地區銷售小組的多個群組。
- 若要啟用例外狀況機制，可以為每個角色建立自助群組。例如，可以將「salesforce 行銷例外狀況」群組建立為自助群組。群組可以指派給 salesforce 行銷角色，而行銷領導小組可成為擁有者。行銷領導小組的成員可以加入或移除使用者、設定聯結原則或甚至核准或拒絕個別使用者加入的要求。這是透過不需要經過擁有者或成員特殊訓練的資訊工作者適當的體驗來支援。

在此情況下，所有指派的使用者會自動佈建至 salesforce，因為當他們加入至不同群組時，他們的角色指派會在 salesforce 中更新。使用者就能夠透過 Microsoft 應用程式存取面板、Office Web 用戶端或甚至是藉由瀏覽至其組織的 Salesforce 登入頁面來探索及存取 Salesforce。系統管理員能夠使用 Azure AD 報告輕鬆檢視使用方式和指派狀態。

 系統管理員可以運用 [Azure AD 的條件式存取](active-directory-conditional-access.md)為特定角色設定存取原則。這些原則可以包括是否允許公司環境外部的存取，甚至是 Multi-Factor Authentication 或裝置需求，以在各種情況下達成存取。


## 如何開始使用？

首先，如果您還未使用 Azure AD，而且您是 IT 系統管理員：

 - [試試看！](https://azure.microsoft.com/trial/get-started-active-directory/) - 您可以立即註冊免費 30 天的試用版，使用此連結不到 5 分鐘即可部署第一個雲端解決方案

啟用帳戶共用的 Azure AD 功能包括：

- [群組指派](active-directory-accessmanagement-self-service-group-management.md)
- 加入應用程式至 Azure AD
- 開始使用指派
- 應用程式指派常見問題集
- [應用程式使用方式儀表板/報告](active-directory-passwords-get-insights.md)

## 哪裡可以深入了解？

- [使用條件式存取來保護應用程式](active-directory-conditional-access.md)
- [自助群組管理/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=Oct15_HO3-->