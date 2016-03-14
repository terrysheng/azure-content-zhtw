<properties
	pageTitle="Azure Active Directory 版本 | Microsoft Azure"
	description="說明 Azure Active Directory 免費和付費版本選擇的主題。Azure Active Directory Basic 是免費版本，而 Azure Active Directory Premium 是付費版本。"
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markvi"/>

# Azure Active Directory 版本

所有 Microsoft Online 商務服務都依賴 Azure Active Directory 進行登入和其他身分識別需求。如果您訂用帳戶任何 Microsoft Online 商務服務 (例如 Office 365、Microsoft Azure 等)，您會取得 Azure Active Directory (Azure AD) 並可存取所有免費的功能，如下所述。


Azure Active Directory 是一項可在雲端中為您的員工、合作夥伴和客戶提供完整身分識別和存取管理功能的服務。它結合了開發人員適用的目錄服務、進階身分識別控管、豐富標準架構平台，以及您自己的和數以千計的任何預先整合的應用程式的應用程式存取管理。利用 Azure Active Directory 免費版，您可以管理使用者和群組、與內部部署目錄同步處理、取得 Azure、Office 365 和數千個熱門 SaaS 應用程式 (像是 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等) 之間的單一登入。若要深入了解 Azure Active Directory，請閱讀[什麼是 Azure AD](active-directory-whatis.md)。



若要增強您的 Azure Active Directory，您可以使用 Azure Active Directory Basic 和 Premium 版本加入付費功能。付費版本的 Azure Active Directory 是建立在您現有的免費目錄上，提供的企業級功能跨越自助、增強的監視、安全性報告、Multi-Factor Authentication (MFA) 及您的行動工作力的安全存取。

Office 365 訂用帳戶包含的其他 Azure Active Directory 功能在以下比較表中說明。


> [AZURE.NOTE] 這兩種版本的詳細價格請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。<br>目前在中國不支援 Azure Active Directory Premium 和 Azure Active Directory Basic。請透過 Azure Active Directory 論壇與我們連絡以取得詳細資訊


- **Azure Active Directory Basic** - 針對具有雲端優先需求的任務背景工作角色設計，此版本提供以雲端為中心的應用程式存取和自助身分識別管理解決方案。有了 Azure Active Directory 的 Basic 版本，您可獲得生產力增強和成本節約功能，例如：群組式存取管理、雲端應用程式的自助式密碼重設、Azure Active Directory 應用程式 Proxy (以使用 Azure Active Directory 發佈內部部署 Web 應用程式)，全都由可獲得 99.9% 運作時間的企業級 SLA 支援。
 
- **Azure Active Directory Premium** - 設計目的是為了幫助更要求身分識別和存取管理需求的組織，Azure Active Directory Premium 版本加入了功能豐富的企業級身分識別管理功能，並使得混合式使用者能夠順暢地存取內部部署和雲端功能。此版本包含資訊背景工作角色和混合環境中身分識別管理員對於應用程式存取、自助身分識別和存取管理 (IAM)、雲端中的身分識別保護和安全性所需的一切。它支援進階管理和委派資源，例如：動態群組和自助群組管理。它包含 Microsoft Identity Manager (一項內部部署及身分識別和存取管理套件)，並提供可為您的內部部署使用者啟用自助密碼重設之類解決方案的雲端回寫功能。

若要註冊並立即開始使用 Active Directory Premium，請參閱[開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)。


> [AZURE.NOTE] 
許多 Azure Active Directory 功能是透過「隨用隨付」版本提供：
>
>- Active Directory B2C 是面向消費者應用程式適用的身分識別和存取管理解決方案。如需詳細資料，請參閱：[Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	Azure Multi-Factor Authentication 可透過每一使用者或每一驗證提供者方式使用。如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)


##比較正式推出的功能

> [AZURE.NOTE] 如需這項資料的不同檢視，請參閱 [Azure Active Directory 功能](https://www.microsoft.com/server-cloud/products/azure-active-directory/Features.aspx)。

| | Azure AD Free | Azure AD Basic | Azure AD Premium |
| ---                      | :-:           | :-:            | :-:              |
| 常用功能 | ![勾選][12] | ![勾選][12] | ![勾選][12] |
| 基本功能 | | ![勾選][12] | ![勾選][12] |
| 進階功能 | | | ![勾選][12] |



<br>

**常用功能**

- [目錄物件](#directory-objects) 

- [使用者與群組管理 (新增/更新/刪除)/以使用者為基礎的佈建、裝置註冊](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)

- [單一登入 (SSO)](#single-sign-on-sso)

- [雲端使用者的自助式密碼變更](#self-service-password-change-for-cloud-users)

- [Connect (可將內部部署目錄延伸至 Azure Active Directory 的同步處理引擎)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)

- [安全性/使用量報告](#securityusage-reports)



**基本功能**

- [以群組為基礎的存取管理/佈建](#group-based-access-managementprovisioning)

- [雲端使用者的自助式密碼重設](#self-service-password-reset-for-cloud-users)

- [創建公司品牌 (登入頁面/存取面板自訂)](#company-branding-logon-pagesaccess-panel-customization)

- [應用程式 Proxy](#application-proxy)

- [SLA 99.9%](#sla-999)


**進階功能**

- [自助式群組和應用程式管理/自助式應用程式新增/動態群組](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)

- [使用內部部署回寫來進行的自助式密碼重設/變更/解除鎖定](#self-service-password-resetchangeunlock-with-on-premises-write-back)

- [Multi-Factor Authentication (雲端與內部部署 (MFA Server))](#multi-factor-authentication-cloud-and-on-premises-mfa-server)

- [MIM CAL + MIM 伺服器](#mim-cal-mim-server)

- [雲端應用程式探索](#cloud-app-discovery)

- [Connect Health](#connect-health)

- [群組帳戶的自動密碼變換](#automatic-password-rollover-for-group-accounts)


**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能**

- [將裝置加入 Azure AD、Desktop SSO、適用於 Azure AD 的 Microsoft Passport、系統管理員 Bitlocker 復原](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)

- [MDM 自動註冊、自助式 Bitlocker 復原、Windows 10 裝置透過 Azure AD Join 取得的其他本機系統管理員](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)




<br>
<hr>
## 常用功能
#### 目錄物件 

**類型：**常用功能

預設使用量配額為 150,000 個物件。物件是指目錄服務中由其各自的唯一辨別名稱所表示項目。供驗證之用的使用者項目即為物件的一個例子。如果您的需求將超出此預設配額，請連絡支援服務。50 萬個物件的數目限制不適用於 Office 365、Microsoft Intune 或任何其他依賴 Azure Active Directory 提供目錄服務的付費 Microsoft 線上服務。


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| 最多 500,000 個物件| 沒有物件數目限制| 沒有物件數目限制| Office 365 使用者帳戶沒有物件數目限制|


<br>

#### 使用者與群組管理 (新增/更新/刪除)/以使用者為基礎的佈建、裝置註冊

**類型：**常用功能

**可用性：**


| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|

**其他詳細資訊：**

- [管理 Azure AD 目錄](active-directory-administer.md)
- [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)


<br>
<hr>
#### 單一登入 (SSO)

**類型：**常用功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| 每個使用者 10 個應用程式 [1] <br>(預先整合的 SaaS 和開發人員整合的應用程式)| 每個使用者 10 個應用程式 [1] <br> (免費層 + 應用程式 Proxy 應用程式) | 沒有限制 [2] <br> (免費層、基本層 + 自助式應用程式整合範本)| 每個使用者 10 個應用程式 [1] <br> (預先整合的 SaaS 和開發人員整合的應用程式)|

[1] 使用 Azure AD Free 和 Azure AD Basic 時，使用者如果已獲指派 SaaS 應用程式的存取權，便可在其 [存取面板] 最多看見 10 個應用程式，並取得這些應用程式的 SSO 存取權。使用 Free 和 Basic 版本時，系統管理員可以設定 SSO 並指派任意數量的 SaaS 應用程式存取權給使用者，不過使用者在其 [存取面板] 中一次只能看到 10 個應用程式。

[2] 任何支援 SAML、SCIM 或表單型驗證之應用程式的自助式整合 (藉由使用應用程式庫功能表中提供的範本)。如需詳細資訊，請參閱[設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)。

**其他詳細資訊：**

- [使用 Azure Active Directory (AD) 管理應用程式](active-directory-enable-sso-scenario.md)

<br>
<hr>
#### 雲端使用者的自助式密碼變更

**類型：**常用功能

**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|

**其他詳細資訊：**

- [如何更新自己的密碼](active-directory-passwords-update-your-own-password.md)


<br>
<hr>
#### Connect (可將內部部署目錄延伸至 Azure Active Directory 的同步處理引擎) 

**類型：**常用功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|

**其他詳細資訊：**

- [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<br>
<hr>
#### 安全性/使用量報告

**類型：**常用功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| 3 個基本報告| 3 個基本報告| 進階報告| 3 個基本報告|

**其他詳細資訊：**

- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

<br>
<hr>

## 進階和基本功能
#### 以群組為基礎的存取管理/佈建

**類型：**基本功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | ![勾選][12]| ![勾選][12]| |

**其他詳細資訊：**

- [使用群組來管理 SaaS 應用程式的存取權](active-directory-accessmanagement-group-saasapps.md)

<br>
<hr>
#### 雲端使用者的自助式密碼重設

**類型：**基本功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | ![勾選][12]| ![勾選][12]| ![勾選][12]|

**其他詳細資訊：**

- [使用者和系統管理員的 Azure AD 密碼重設](active-directory-passwords.md)

<br>
<hr>
#### 創建公司品牌 (登入頁面/存取面板自訂)

**類型：**基本功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | ![勾選][12]| ![勾選][12]| ![勾選][12]|

**其他詳細資訊：**

- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)

<br>
<hr>
#### 應用程式 Proxy

**類型：**基本功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | ![勾選][12]| ![勾選][12]| |

**其他詳細資訊：**

- [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)

<br>
<hr>
#### SLA 99.9%

**類型：**基本功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | ![勾選][12]| ![勾選][12]| ![勾選][12]|

**其他詳細資訊：**

- [服務等級協定](https://azure.microsoft.com/support/legal/sla/)

<br>
<hr>

## 進階功能
#### 自助式群組和應用程式管理/自助式應用程式新增/動態群組

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |



<br>
         
#### 使用內部部署回寫來進行的自助式密碼重設/變更/解除鎖定

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |



<br>

#### Multi-Factor Authentication (雲端與內部部署 (MFA Server))

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| 針對 Office 365 應用程式限定於雲端|

**其他詳細資訊：**

- [什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)

<br>
<hr>
#### MIM CAL + MIM 伺服器 

Microsoft Identity Manager 伺服器軟體的權限會隨 Windows Server 授權 (任何版本) 一起授與。由於 Microsoft Identity Manager 在 Windows Server OS 上執行，只要該伺服器正在執行有效且已授權的 Windows Server 複本，Microsoft Identity Manager 便可以在該伺服器上安裝並使用。Microsoft Identity Manager Server 不需要其他個別授權。

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |



<br>

#### Cloud App Discovery 

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |

**其他詳細資訊：**

- [使用 Cloud App Discovery 尋找未受管理的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)

<br>
<hr>
#### Connect Health

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |

**其他詳細資訊：**

- [在雲端中監視內部部署身分識別基礎結構和同步處理服務。](active-directory-aadconnect-health.md)

<br>
<hr>
#### 群組帳戶的自動密碼變換

**類型：**進階功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |


<br>
<hr>
## Azure Active Directory Join – 僅適用於 Windows 10 的相關功能
#### 將裝置加入 Azure AD、Desktop SSO、適用於 Azure AD 的 Microsoft Passport、系統管理員 Bitlocker 復原

**類型：**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|


<br>

#### MDM 自動註冊、自助式 Bitlocker 復原、Windows 10 裝置透過 Azure AD Join 取得的其他本機系統管理員

**類型：**Azure Active Directory Join – 僅適用於 Windows 10 的相關功能


**可用性：**

| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| :-: | :-: | :-: | :-: |
| | | ![勾選][12]| |

<hr>


## Azure AD 預覽功能
除了免費、基本和 Premium Edition 的正式推出功能，Azure AD 也提供您預覽功能的集合。您可以使用預覽功能獲得不久的將來可用的功能的印象，並判斷這些功能是否可協助改善您的環境。

**可用的預覽功能：**

- [B2B 共同作業](active-directory-b2b-collaboration-overview.md)
- [管理單位](active-directory-administrative-units-management.md)
- Privileged Identity Management
- [HR 應用程式整合](active-directory-saas-workday-inbound-tutorial.md)
- [企業狀態漫遊](active-directory-windows-enterprise-state-roaming-overview.md)
- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)





## 接下來

- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0302_2016-->