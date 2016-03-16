<properties
    pageTitle="使用 Azure Active Directory (AD) 管理應用程式 | Microsoft Azure"
    description="本文章說明整合 Azure Active Directory 與您的內部部署、雲端和 SaaS 應用程式的優點。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/16/2016"
      ms.author="markvi"/>

# 使用 Azure Active Directory 來管理應用程式

在實際工作流程或內容以外，企業對所有應用程式有兩個基本需求：

1. 要增加生產力，應用程式應該易於探索及存取

2. 要啟用安全性和控管，組織需要控制和監督可以存取與實際存取每個應用程式的對象

就雲端應用程式而言，使用身分識別來控制「*誰有權執行什麼*」最能達成此目標。

在運算術語中：

- 「誰」被稱為「身分識別」 - 用來管理使用者和群組

- 「什麼」被稱為「存取管理」 - 用來管理對受保護資源的存取

這兩個元件結合在一起就稱為「身分識別與存取管理 (IAM)」，[Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) 團隊將它定義為「*可讓適當的個人以正當理由在適當時機存取適當資源的安全性規定*」。

好，那麼問題是什麼？ 如果 IAM *未*在一個地方使用整合解決方案加以管理：

- 身分識別系統管理員需要個別建立和個別更新所有應用程式中的使用者帳戶，這是既重複又耗時的活動。

- 使用者必須記住多個認證，才能存取他們需要使用的應用程式。如此一來，使用者傾向於將密碼寫下，或使用其他密碼管理解決方案，而這會帶來其他資料安全性的風險。

- 重複、耗時的活動減少了使用者和系統管理員處理可增加您的企業營收之商務活動的時間。

因此，通常何者會防止組織採用整合式 IAM 解決方案？

- 大多數技術解決方案是基於需由組織為自己的應用程式部署及調整的軟體平台。

- 採用雲端應用程式通常會比 IT 組織可以與現有的 IAM 解決方案整合需要更高的費率。

- 安全性與監控工具需要其他的自訂與整合，以達到完善 E2E 案例。

## 與應用程式整合的 Azure Active Directory

Azure Active Directory 是 Microsoft 的全方位「身分識別即服務」(IDaaS) 解決方案，它能夠：

- 讓 IAM 做為雲端服務 

- 提供集中式存取管理、單一登入 (SSO) 及報告功能

- 支援應用程式庫中[數千個應用程式](https://azure.microsoft.com/marketplace/active-directory/) (包括 Salesforce、Google Apps、Box、Concur 等) 的整合式存取管理。


藉由 Azure Active Directory，您為合作夥伴和客戶 (企業或消費者) 發佈的所有應用程式都可享有相同的身分識別與存取管理功能。<br> 這可讓您大幅降低營運成本。

如果您需要實作尚未列在應用程式資源庫的應用程式，該怎麼辦？ 雖然這比起從應用程式資源庫的設定應用程式的 SSO 更為耗時一點，Azure AD 提供了精靈可協助您完成組態。

Azure AD 的價值不僅僅是雲端應用程式而已。您也可以透過提供安全的遠端存取，將它與內部部署應用程式搭配使用。有了安全的遠端存取，您就不再需要 VPN 或其他傳統的遠端存取管理實作。

Azure AD 透過為您的所有應用程式提供集中式存取管理和單一登入 (SSO)，提供了主要資料安全性和生產力問題的解決方案。

- 使用者透過登入一次即可存取多個應用程式，使得他們有更多時間可帶來收入或完成商務作業活動。

- 身分識別系統管理員可以在一個位置管理對應用程式的存取。

對使用者和貴公司的優點很明顯。讓我們仔細看看對身分識別系統管理員和組織的優點。

## 整合式應用程式優點

SSO 程序有兩個步驟：

- 驗證：驗證使用者身分識別的程序。

- 授權：使用存取原則決定啟用或封鎖對資源的存取。

使用 Azure AD 來管理應用程式及啟用 SSO 時：

- 驗證是在使用者的內部部署 (例如 AD) 或 Azure AD 帳戶中完成。

- 授權會基於 Azure AD 指派及保護原則執行，以確保一致的使用者體驗，並讓您在任何應用程式上加入指派、位置和 MFA 條件，而不論其內部功能為何。

請務必了解，授權在目標應用程式上執行的方式，會根據應用程式與 Azure AD 整合的方式而有所不同。

- **由服務提供者預先整合應用程式**：如同 Office 365 和 Azure，這些應用程式都是直接在 Azure AD 上建置並仰賴其完整的身分識別和存取管理功能。對這些應用程式的存取是透過目錄資訊與權杖發行來啟用。

- **由 Microsoft 預先整合的應用程式和自訂應用程式**：這些是依賴內部應用程式目錄並可獨立於 Azure AD 運作的獨立雲端應用程式。對這些應用程式的存取會透過發行應用程式特定認證給對應於應用程式帳戶來啟用。根據應用程式的功能，認證可以是同盟權杖或先前佈建在應用程式中帳戶的使用者名稱或密碼。

- **內部應用程式**：透過 Azure AD 應用程式 Proxy 發佈的應用程式，主要可啟用對內部部署應用程式的存取。這些應用程式依賴於 Windows Server Active Directory 之類的內部部署目錄中心。透過觸發 Proxy 來提供應用程式內容給一般使用者，同時接受內部部署上的登入需求，即可啟用對這些應用程式的存取。

例如，如果使用者加入您的組織，您需要在主要登入作業的 Azure AD 中為使用者建立帳戶。如果此使用者需要存取 Salesforce 之類的受管理應用程式，您也需要在 Salesforce 中為此使用者建立帳戶，並將它連結到 Azure 帳戶，SSO 才能運作。當使用者離開組織時，建議您將 Azure AD 帳戶和使用者具有存取權的應用程式 IAM 存放區中的所有對應帳戶刪除。

## 存取偵測

在現代企業中，IT 部門並通常不知道使用的所有雲端應用程式。結合 Cloud App Discovery，Azure AD 為您提供偵測這些應用程式的解決方案。

## 帳戶管理

傳統上，管理各種應用程式中的帳戶是由組織中的 IT 或支援人員執行的手動程序。Azure AD 將跨所有服務提供者整合的應用程式和由 Microsoft 預先整合的這些應用程式的帳戶管理完全自動化，可支援自動化使用者佈建或 SAML JIT。

## 自動的使用者佈建

某些應用程式提供自動化介面用於建立和移除 (或停用) 帳戶。如果有提供者提供這樣的介面，Azure AD 會加以利用。這樣可以減少您的營運成本，因為系統管理工作會自動發生，並改善您環境的安全性，因為它會減少未經授權存取的機會。

## 存取管理

使用 Azure AD，您可以使用個別或規則驅動指派來管理對應用程式的存取。您也可以將存取管理委派給組織中適當的人員，以獲得確保最佳的監督並減少技術服務人員的負擔。

## 內部部署應用程式

內建的應用程式 Proxy 可讓您將內部部署應用程式發佈給使用者，以獲得對現代化雲端應用程式一致的存取經驗以及 Azure AD 監視、報告和安全性功能的優點。

## 報告和監控

Azure AD 為您提供預先整合的報告與監控功能，可讓您知道可以存取與實際存取每個應用程式的對象。

## 相關功能

您可以使用 Azure AD 透過更細微的存取原則和預先整合的 MFA 來保護您的應用程式。若要深入了解 Azure MFA，請參閱 [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/)。

## 開始使用

若要開始使用 Azure AD 來整合應用程式，請查看[整合 Azure Active Directory 與應用程式入門指南](active-directory-integrating-applications-getting-started.md)。

## 另請參閱

[Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

<!---HONumber=AcomDC_0218_2016-->