<properties
	pageTitle="Azure Active Directory 版本 | Microsoft Azure"
	description="說明 Azure Active Directory 免費和付費版本選擇的主題。"
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
	ms.date="10/17/2015"
	ms.author="markvi"/>

# Azure Active Directory 版本

所有 Microsoft Online 商務服務都依賴 Azure Active Directory 進行登入和其他身分識別需求。如果您訂用帳戶任何 Microsoft Online 商務服務 (例如 Office 365、Microsoft Azure 等)，您會取得 Azure Active Directory (Azure AD) 並可存取所有免費的功能，如下所述。

Azure Active Directory 是一項可在雲端中為您的員工、合作夥伴和客戶提供完整身分識別和存取管理功能的服務。它結合了開發人員適用的目錄服務、進階身分識別控管、豐富標準架構平台，以及您自己的和數以千計的任何預先整合的應用程式的應用程式存取管理。利用 Azure Active Directory 免費版，您可以管理使用者和群組、與內部部署目錄同步處理、取得 Azure、Office 365 和數千個熱門 SaaS 應用程式 (像是 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等) 之間的單一登入。若要深入了解 Azure Active Directory，請閱讀[什麼是 Azure AD](active-directory-whatis.md)。


若要增強您的 Azure Active Directory，您可以使用 Azure Active Directory Basic 和 Premium 版本加入付費功能。付費版本的 Azure Active Directory 是建立在您現有的免費目錄上，提供的企業級功能跨越自助、增強的監視、安全性報告、Multi-Factor Authentication (MFA) 及您的行動工作力的安全存取。

Office 365 訂用帳戶包含的其他 Azure Active Directory 功能在以下比較表中說明。


> [AZURE.NOTE]這兩種版本的詳細價格請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。<br>目前在中國不支援 Azure Active Directory Premium 和 Azure Active Directory Basic。請透過 Azure Active Directory 論壇與我們連絡以取得詳細資訊


- **Azure Active Directory Basic** - 針對具有雲端優先需求的任務背景工作角色設計，此版本提供以雲端為中心的應用程式存取和自助身分識別管理解決方案。有了 Azure Active Directory 的 Basic 版本，您可獲得生產力增強和成本節約功能，例如：群組式存取管理、雲端應用程式的自助式密碼重設、Azure Active Directory 應用程式 Proxy (以使用 Azure Active Directory 發佈內部部署 Web 應用程式)，全都由可獲得 99.9% 運作時間的企業級 SLA 支援。
 
- **Azure Active Directory Premium** - 設計目的是為了幫助更要求身分識別和存取管理需求的組織，Azure Active Directory Premium 版本加入了功能豐富的企業級身分識別管理功能，並使得混合式使用者能夠順暢地存取內部部署和雲端功能。此版本包含資訊背景工作角色和混合環境中身分識別管理員對於應用程式存取、自助身分識別和存取管理 (IAM)、雲端中的身分識別保護和安全性所需的一切。它支援進階管理和委派資源，例如：動態群組和自助群組管理。它包含 Microsoft Identity Manager (一項內部部署及身分識別和存取管理套件)，並提供可為您的內部部署使用者啟用自助密碼重設之類解決方案的雲端回寫功能。

若要註冊並立即開始使用 Active Directory Premium，請參閱[開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)。


> [AZURE.NOTE]許多 Azure Active Directory 功能是透過「隨用隨付」版本提供：
>
>- Active Directory B2C 是面向消費者應用程式適用的身分識別和存取管理解決方案。如需詳細資料，請參閱：[Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	Azure Multi-Factor Authentication 可透過每一使用者或每一驗證提供者方式使用。如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](multi-factor-authentication.md)


<br>




| 功能類型| 特性| 免費版| 基本版| Premium Edition |
| --- | --- | --- | --- | --- |
| **常用功能**| 目錄即服務| ![勾選][12] 最多 50 萬個使用者 [1]| ![勾選][12] 沒有物件數目限制| ![勾選][12] 沒有物件數目限制|
| | [使用 UI 或 Windows PowerShell Cmdlet 的使用者和群組管理](active-directory-administer.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [裝置註冊](active-directory-conditional-access-device-registration-overview.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [存取面板入口網站，讓以 SSO 為基礎的使用者存取 SaaS 和自訂應用程式](active-directory-saas-access-panel-introduction.md)| ![勾選][12] 每位使用者最多 10 個應用程式 [2]| ![勾選][12] 每位使用者最多 1 個應用程式 [2]| ![勾選][12] 沒有應用程式限制|
| | [以使用者為基礎的應用程式存取管理和佈建](active-directory-saas-app-provisioning.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | 雲端使用者的自助式密碼變更| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [Azure AD Connect – 用於內部部署目錄與 Azure Active Directory 之間的同步處理](active-directory-aadconnect.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [標準安全性報告](active-directory-view-access-usage-reports.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| | [B2B 共同作業 (跨組織共同作業) (預覽功能)](active-directory-b2b-collaboration-overview.md)| ![勾選][12]| ![勾選][12]| ![勾選][12]|
| **高階和基本功能**| [在 [登入] 和 [存取面板] 頁面上自訂公司標誌和色彩](active-directory-add-company-branding.md)| 隨附於 Office 365 訂用帳戶 [4]| ![勾選][12]| ![勾選][12]|
| | [雲端使用者的自助式密碼重設](active-directory-passwords.md)| 隨附於 Office 365 訂用帳戶 [4]| ![勾選][12]| ![勾選][12]|
| | [應用程式 Proxy：內部部署 Web 應用程式的安全遠端存取和 SSO](active-directory-application-proxy-get-started.md)| | ![勾選][12]| ![勾選][12]|
| | [以群組為基礎的應用程式存取管理和佈建](active-directory-accessmanagement-group-saasapps.md)| | ![勾選][12]| ![勾選][12]|
| | [高可用性 SLA 運作時間 (99.9%)](https://azure.microsoft.com/support/legal/sla/)| Microsoft Online Services SLA [5]| ![勾選][12]| ![勾選][12]|
| **僅限高階功能**| [進階應用程式使用情況報告](active-directory-view-access-usage-reports.md)| | | ![勾選][12]|
| | [雲端使用者的自助群組管理] (Azure AD 中使用者的自助群組管理)| | | ![勾選][12]|
| | [包含內部部署寫回功能的自助式密碼重設](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![勾選][12]|
| | [Microsoft Identity Manager (MIM) 使用者授權 – 用於內部部署身分識別與存取管理](http://www.microsoft.com/zh-TW/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![勾選][12] [3]|
| | [進階異常安全性報告 (Machine Learning 型)](active-directory-view-access-usage-reports.md)| | | ![勾選][12]|
| | [雲端應用程式探索](active-directory-cloudappdiscovery-whatis.md)| | | ![勾選][12]|
| | [適用於雲端使用者的 Multi-Factor Authentication 服務](multi-factor-authentication.md)| 隨附於 Office 365 訂用帳戶 [4]| | ![勾選][12]|
| | [適用於內部部署使用者的 Multi-Factor Authentication 服務](multi-factor-authentication.md)| | | ![勾選][12]|
| | [Azure Active Directory Connect Health 以監控內部部署 Active Directory 基礎結構的健康情況，並取得使用情況分析](active-directory-aadconnect-health.md)| | | ![勾選][12]|




[1] 50 萬個物件數目限制並不適用於採用 Azure Active Directory 目錄服務的 Office 365、Office Microsoft Intune 或任何其他 Microsoft 線上服務。

[2] 在 Azure Active Directory Free 和 Azure Active Directory Basic 中，有權存取每個 SaaS 應用程式的使用者在他們的存取面板中最多可以看到 10 個應用程式，並取得他們的 SSO 存取權限 (假設系統管理員已先以 SSO 為他們進行設定)。在免費版本中，系統管理員可以設定 SSO，並將使用者存取權指派給任意數目的 SaaS 應用程式，不過，使用者在存取面板中一次只會看到 10 個應用程式。

[3] Microsoft Identity Manager Server 伺服器軟體的權限會連同 Windows Server 授權 (任何版本) 一起授與。因為 Microsoft Identity Manager 會在 Windows Server 作業系統上執行，因此只要伺服器執行有效且經過授權的 Windows Server 複本，就可以在該伺服器上安裝並使用 Microsoft Identity Manager。Microsoft Identity Manager Server 不需要其他個別授權。

[4] 隨附於 Office 365 的 Azure AD 付費的功能僅限於存取 Office 365 應用程式時使用。

[5] Microsoft 的各項線上商務服務、Office 365、Microsoft Intune 或仰賴 Azure Active Directory 提供目錄服務的任何其他 Microsoft 線上服務，會承載自己的服務 SLA，以此方式延伸來使用 Azure Active Directory。若要深入了解，請參閱 [Microsoft Online Services SLA](https://gallery.technet.microsoft.com/online-SLA-ea09109e)。




## 後續步驟

- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_1203_2015-->