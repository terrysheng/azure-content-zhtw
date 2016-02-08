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
	ms.date="01/25/2016"
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
 
>-	Azure Multi-Factor Authentication 可透過每一使用者或每一驗證提供者方式使用。如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](multi-factor-authentication.md)


<br>

| 功能類型| 特性| 免費版| 基本版| Premium Edition| 僅 Office 365 應用程式 |
| --- | --- | --- | --- | --- | --- |
| **常用功能**| 目錄物件 [1]| 最多 500,000 個物件| 沒有物件數目限制| 沒有物件數目限制| Office 365 使用者帳戶沒有物件數目限制|
| | [使用者與群組管理 (新增 / 更新 / 刪除)，以使用者為基礎的佈建](active-directory-administer.md)、[裝置註冊](active-directory-conditional-access-device-registration-overview.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [SSO 至 SaaS 應用程式、自訂應用程式、應用程式 Proxy 應用程式](active-directory-enable-sso-scenario.md)| 每位使用者 10 個應用程式 [2]| 每位使用者 10 個應用程式 [2]| 沒有限制| 每位使用者 10 個應用程式 [2]|
| | [雲端使用者的自助式密碼變更](active-directory-passwords-update-your-own-password.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [Connect - 用於內部部署目錄與 Azure Active Directory 之間的同步處理](active-directory-aadconnect.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | **預覽**：[ B2B 共同作業](active-directory-b2b-collaboration-overview.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [安全性 / 使用量報告](active-directory-view-access-usage-reports.md)| 基本報告| 基本報告| 進階報告| 基本報告|
| **高階和基本功能**| [以群組為基礎的應用程式存取管理和佈建](active-directory-accessmanagement-group-saasapps.md)| | ![勾選][12]| ![勾選][12]| |
| | [雲端使用者的自助式密碼重設](active-directory-passwords.md)| | ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [創建公司品牌 (登入頁面與存取面板自訂)](active-directory-add-company-branding.md)| | ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [應用程式 Proxy](active-directory-application-proxy-get-started.md)| | ![勾選][12]| ![勾選][12]| |
| | [高可用性 SLA 運作時間 (99.9%)](https://azure.microsoft.com/support/legal/sla/)| | ![勾選][12]| ![勾選][12]| ![勾選][12]|
| **僅限高階功能**| 自助式群組管理 / 自助式新增應用程式 / 動態群組成員資格| | | ![勾選][12]| |
| | [自助式密碼重設、變更、使用內部部署寫回解鎖](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![勾選][12]| |
| | [多重要素驗證 (雲端與內部部署)](multi-factor-authentication.md)| | | ![勾選][12]| 針對 Office 365 應用程式限定於雲端|
| | [Microsoft Identity Manager (MIM) 使用者授權與 MIM 伺服器 [3]](http://www.microsoft.com/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![勾選][12]| |
| | [雲端應用程式探索](active-directory-cloudappdiscovery-whatis.md)| | | ![勾選][12]| |
| | [Azure Active Directory Connect Health](active-directory-aadconnect-health.md)| | | ![勾選][12]| |
| | 群組帳戶的自動密碼變換| | | ![勾選][12]| |
| | **預覽**：條件式存取| | | ![勾選][12]| |
| | **預覽**：Privileged Identity Management| | | ![勾選][12]| |
| **Windows 10 與 Azure AD Join 相關功能**| 將 Windows 10 裝置加入 Azure AD、Desktop SSO、適用於 Azure AD 的 Microsoft Passport、系統管理員 Bitlocker 復原| ![勾選][12]| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | MDM 自動註冊、自助式 Bitlocker 復原、Windows 10 裝置透過 Azure AD Join 取得的其他本機系統管理員| | | ![勾選][12]| |





[1] 預設使用配額為 150,000 個物件。物件是指目錄服務中由其各自的唯一辨別名稱所表示項目。供驗證之用的使用者項目即為物件的一個例子。如果您的需求將超出此預設配額，請連絡支援服務。500,000 個物件數目限制並不適用於依賴 Azure Active Directory 目錄服務的 Office 365、Microsoft Intune 或任何其他 Microsoft 付費線上服務。

[2] 若使用 Azure AD Free 及 Azure AD Basic，已獲指派可存取 SaaS 應用程式的使用者便可在其存取面板設定最多 10 個應用程式並取得這些應用程式的 SSO 存取。系統管理員可以設定 SSO 並使用免費和基本層次指派任意數量的使用者存取給 SaaS，不過使用者在存取面板中一次只能看到 10 個應用程式。

[3] Microsoft Identity Manager Server 伺服器軟體的權限會連同 Windows Server 授權 (任何版本) 一起授與。由於 Microsoft Identity Manager 在 Windows Server OS 上執行，只要該伺服器正在執行有效且已授權的 Windows Server 複本，Microsoft Identity Manager 便可以在該伺服器上安裝並使用。Microsoft Identity Manager Server 不需要其他個別授權。



## 接下來

- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0128_2016-->