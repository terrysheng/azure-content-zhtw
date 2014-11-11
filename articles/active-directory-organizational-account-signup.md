<properties umbracoNaviHide="0" pageTitle="How To Configure Cloud Services" metaKeywords="Azure cloud services, cloud service, configure cloud service" description="Learn how to configure Azure cloud services." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Sign up for Azure as an organization" authors="terrylan" manager="terrylan" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# 以組織方式註冊 Azure

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

直至近日之前，您只能使用 Microsoft 帳戶 (Windows Live ID) 註冊新的 Azure 訂閱。Azure 現在支援使用下列任一種帳戶方式進行註冊：

-   **Microsoft 帳戶** (您為了個人用途而建立) - 可供存取所有消費者導向的 Microsoft 產品和雲端服務，例如 Outlook (Hotmail)、Messenger、SkyDrive、MSN、Xbox LIVE 或 Office Live。註冊 Outlook 即可以 @Outlook.com 地址自動建立 Microsoft 帳戶。建立之後，Microsoft 帳戶即可用於存取消費者相關的 Microsoft 雲端服務和/或 Azure。[詳細資訊][詳細資訊]

-   **組織帳戶** (系統管理員為了商務/學術用途而建立) - 可供存取所有小型、中型和企業商務層級的 Microsoft 雲端服務，例如 Azure、Windows Intune 或 Office 365。當您以組織方式註冊任一項服務時，會在 Azure Active Directory 中自動佈建雲端架構租用戶來代表您的組織。[詳細資訊][1]

    建立此租用戶後，系統管理員即可將組織帳戶核發給每一位員工/學員，並根據其所需存取的雲端服務訂閱 (例如 Azure)，將授權指派給這些帳戶。

    *想要以組織方式註冊 Azure 嗎？* **立即註冊**

## 什麼是 Azure Active Directory？

就如同 Active Directory 是透過 Windows Server 作業系統提供給客戶來管理內部部署目錄的服務，Azure Active Directory (Azure AD) 是透過 Azure 提供的服務，讓您能夠管理組織的雲端目錄。[詳細資訊][2]

因為這是貴組織的雲端目錄，所以您可以決定誰是您的使用者、哪些資訊保留在雲端中、誰可以使用或管理這些資訊，以及哪些應用程式或服務可以存取這些資訊。當您使用 Azure AD 時，Microsoft 會負責讓雲端中執行的 Active Directory 保有高延展性、高可用性和整合式災害復原能力，同時完全尊重貴組織對資訊的隱私與安全性需求。

### 與 Active Directory 內部部署整合

如果您的組織已使用 Active Directory 內部部署，則您可以利用 Azure AD 的目錄整合功能，例如目錄同步和單一登入，以進一步將每個現有的內部部署身分識別延伸至雲端，讓管理者和使用者的操作更順暢。[詳細資訊][3]

  [disclaimer]: ../includes/disclaimer.md
  [詳細資訊]: http://windows.microsoft.com/zh-tw/windows-live/sign-in-what-is-microsoft-account
  [1]: http://technet.microsoft.com/zh-TW/library/jj573650
  [2]: http://technet.microsoft.com/zh-TW/library/hh967619
  [3]: http://technet.microsoft.com/zh-TW/library/jj573653
