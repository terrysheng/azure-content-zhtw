<properties
   pageTitle="比較使用 Azure Active Directory 管理外部身分識別的功能 | Microsoft Azure"
   description="比較 Azure Active Directory B2B 共同作業、B2C 及多租用戶應用程式對於外部身分識別驗證和授權的支援"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# 比較使用 Azure Active Directory 管理外部身分識別的功能

除了對 SaaS 應用程式的管理行動工作力存取，Azure Active Directory (Azure AD) 可協助您的組織與商務夥伴共用資源，並提供應用程式給企業和取用者使用。

## 為企業開發應用程式

您是否會提供服務或應用程式 (例如薪資服務) 給企業？ Azure AD 提供的身分識別平台可讓您建置應用程式，而這些應用程式會與在部署 Office 365 或其他商務服務時已設定 Azure AD 的數百萬個組織完美整合。

**範例：**藥品經銷者會提供醫療用品和資訊系統給醫療保健產業。他們必須導入醫療實務的分析應用程式，並希望客戶管理自己的身分識別。此公司選擇 Azure AD 作為其實務管理應用程式的身分識別平台，並且視需要在客戶註冊時提供 Azure AD 身分識別。如需詳細資訊，請參閱《[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)》。

## 讓商務夥伴能夠存取您的企業資源

您是否有商務夥伴或公司外部的其他人員需要存取您的企業資源，例如 SharePoint 網站或 ERP 系統？ Azure AD 可讓系統管理員授與外部使用者 (不一定存在於 Azure AD 中) 對企業應用程式的單一登入存取。這可改善安全性，因為使用者會在離開合作夥伴組織時喪失存取權，然而您可控制組織內的存取原則。這也可簡化管理，因為您不需要管理外部夥伴目錄或每個夥伴的同盟關係，。

**範例：**影像處理公司會為零售商提供相片影像處理服務，和營運最大的照片自助列印機台零售網路。他們選擇 Azure AD，讓其零售業務合作夥伴的數千名使用者能夠使用自己的認證下載最新的合作夥伴行銷資料，並且從公司的供應商外部網路重新訂購相片處理用品。如需詳細資訊，請參閱 [Azure AD B2B 共同作業](active-directory-b2b-what-is-azure-ad-b2b.md)。

## 為取用者開發應用程式

您是否需要以安全且符合成本效益的方式，對數百萬名取用者發佈線上應用程式 (例如零售店面)？ Azure AD 提供的平台能夠讓您應用程式的取用者進行社交以及輸入使用者名稱/密碼登入、加上商標的自助式註冊及自助式密碼重設。這可提升取用者的方便性，同時降低您的開發人員的負荷。

**範例：**世界排名第 1 的運動用品加盟店需要直接與其 4 億 5 千萬名粉絲交流互動。若要這樣做，他們會使用 Azure AD 建置行動應用程式，以便進行使用者驗證和設定檔儲存。粉絲們可以透過 Facebook 等社交帳戶進行簡化的註冊和登入，或使用傳統的使用者名稱/密碼登入方式，即可在 iOS、Android 和 Windows 手機之間同時享有順暢體驗。以建立的 Azure AD 平台為建置基礎，可大幅減少自訂程式碼，同時提供經銷商自訂的品牌並緩和有關安全性、資料外洩和延展性的疑慮。如需詳細資訊，請參閱 [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)。

## Azure AD 功能的比較

Azure AD 功能可處理本文已討論的每個案例。下表應該有助於釐清哪些功能與您最相關：

| **請考慮此產品...** | [Azure AD 多租用戶 SaaS 應用程式](active-directory-developers-guide.md) | [Azure AD B2B 共同作業](active-directory-b2b-what-is-azure-ad-b2b.md) | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) |
|-----------------------|-------------------------|----------------------------|------------------------|
| **我是否需要提供...** | 對企業的服務 | 對我的應用程式的夥伴存取權 | 對顧客的服務 |
| **而我類似於...** | 藥品經銷商 | 影像處理公司 | 運動用品經銷商 |
| **針對下列各項部署應用程式...** | 練習管理 | 供應商外部網路 | 足球迷 |
| **目標...** | 醫師辦公室 | 已核准的商務夥伴 | 任何使用電子郵件的人員 |
| **何時可存取...** | 客戶系統管理員同意 | 我的系統管理員邀請 | 取用者註冊 |

<!---HONumber=AcomDC_0224_2016-->