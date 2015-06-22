<properties
	pageTitle="什麼是 Azure Active Directory？"
	description="使用 Azure Active Directory 將現有的內部部署身分識別延伸至雲端，或開發 Azure AD 整合式應用程式。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# 什麼是 Azure Active Directory？


[運作方式](active-directory-works.md)<br> [入門](active-directory-get-started.md)<br> [後續步驟](active-directory-next-steps.md)<br> [深入了解](active-directory-learn-map.md)

Azure Active Directory (Azure AD) 運用 Active Directory 一直都提供的彈性和安全性，為您的企業提供了擴充內部部署投資的簡易方法，以便利用雲端服務。這是一個雲端平台，可藉由擴充您已有的身分識別基礎結構來解決雲端時代的問題，而不需要移動任何東西。您可以繼續從您的現有投資與內部部署功能獲益，同時在雲端中利用 Azure AD 取得身分識別和存取管理。

使用 Azure AD，您可以集中控制對應用程式和資源的存取、輕鬆地加入現有的資源 (雲端服務或內部部署應用程式)，以及整合您正在開發的應用程式。- 您的使用者可以取得自助式功能與其所有應用程式的單一登入存取，所以不需要知道到哪裡尋找每個應用程式，或必須記住個別的密碼。- 您的企業可以與您可邀請加入 SharePoint 網站 (或參與其 SharePoint 網站) 的商業夥伴、供應商和企業客戶在雲端共同作業。

使用 Azure AD，您可以從單一位置管理一切，並且讓該控制項同時反映在雲端和內部部署。不需重複進行管理；Azure AD 會直接嵌入您已有的項目。[深入了解](active-directory-aadconnect.md)。




> [AZURE.NOTE]若要使用 Azure Active Directory，您需要 Azure 帳戶。如果您沒有帳戶，您可以[註冊免費的 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)。


## Azure Active Directory 有何作用？

因為 Azure AD 是完善的服務，所以會為組織內不同的人員提供不同的優點：

- 如果您是商務決策者，使用 Azure AD 可達成雲端應用程式的承諾以及確信可符合治理需求的行動工作力。
- 如果您式服務提供者，使用 Azure AD 可讓您輕鬆地處理身分識別與存取需求，將您的服務連接至客戶的現有身分識別解決方案，同時觸及 Microsoft Azure 和 Office 365 客戶。Azure AD 也可以解決所有的後端存取需求，所以不論是內部或外部，您可以確信適當的人員有適當的存取權。
- 如果您是 IT 專業人員，使用 Azure AD 可讓您以「雲端速度」增加對於作業的掌控力和可視性。 使用 Azure AD，您會知道人員正在使用的項目，並透過自助式公用項目增添羽翼。<br> <br>

![][1]

##您應了解 Azure AD 的九大事項

### 從任何地方管理 Active Directory 密碼

使用 Azure Active Directory，讓使用者能夠從任何裝置、在任何位置管理其 Active Directory 或 Azure Active Directory 密碼。管理員可以管理密碼和通知原則，還可以即時查看這些密碼重設作業的詳細稽核活動。[深入了解 Azure AD 密碼管理](http://aka.ms/ssproverview)

### 設定雲端資源的存取規則

您可以設定規則和原則，以控制誰能夠存取雲端應用程式和資源以及在哪些情況下存取。例如，您可以要求多因素驗證 (MFA)，且根據裝置或位置管理存取權。[深入了解 Azure MFA](../multi-factor-authentication.md)。

### 單一登入至任何應用程式

Azure Active Directory 提供雲端和內部部署應用程式的安全單一登入，包括 Microsoft Office 365 和無數的 SaaS 應用程式，例如 Salesforce、Workday、DocuSign、ServiceNow 和 Box。[深入了解 SaaS 應用程式](http://azure.microsoft.com/marketplace/active-directory/)。

### 適用於任何裝置

使用者可以從個人化的 Web 存取面板、行動應用程式、Office 365 或自訂公司入口網站，使用其現有的工作認證啟動應用程式，而且不論是在 iOS、Mac OS X、Android 或 Windows 裝置上作業，都有相同的經驗。

### 設定外部存取規則

外部使用者可以使用內建的應用程式 Proxy，安全地存取防火牆後的內部部署網路。使用應用程式 Proxy，可以對雲端應用程式的存取或對傳統內部部署應用程式的存取強制執行您設定的所有規則和原則 (包括多因素驗證)，而不需進行重寫或直接在網際網路上公開。[深入了解 Azure AD 應用程式 Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)。

### 監視使用者的存取

最後，Azure AD 讓您方便取得組織中當下發生情況的詳細資訊。透過進階報告和分析，就可以取得有關您的使用者存取的唯一資訊。例如，使用應用程式探索，您可以找出您的組織中正在使用哪些應用程式。[深入了解 Azure AD 雲端應用程式探索](https://appdiscovery.azure.com/)。

### 進階報告、稽核和分析
Azure AD 為系統管理員提供了許多每日使用量和存取報告 ([Azure AD 報告](active-directory-view-access-usage-reports.md))，而使用報告 API ([Azure AD 報告 API](active-directory-reporting-api-getting-started.md)) 可以取得自訂報告和資料深入探討。在付費版的 Azure AD 中可取得更多的包括，包括特殊權限動作的稽核 ([Azure AD 稽核](active-directory-view-access-usage-reports.md))。

### 對每個人而言簡單又安全的經驗

範例：- 使用者會取得簡單的使用經驗，讓自己的設定檔、應用程式，以及其管理資源的存取能力放在一個地方，而不需要專門的訓練。多因素驗證、SaaS 應用程式、混合式工具，以及自助功能都已準備就緒。

- 管理員具有 Azure AD 管理入口網站和 Windows PowerShell 的存取權，可進行完善的管理。

- 開發人員有一組一致的 RESTful API，並可方便存取以發行和取用應用程式介面。

### 挑選您喜愛的版本

Azure AD 共有三種版本：

- 免費版，這只是雲端目錄服務。
- Basic 版，這是可提供 SaaS 應用程式存取的雲端目錄服務。
- Premium 版，這是一套完整、規則驅動、自助式管理目錄服務解決方案。

若要查看此處所述的功能，請啟動您的 [Azure 免費試用版](http://azure.microsoft.com/trial/get-started-active-directory/)。

[深入了解 Azure AD 版本](active-directory-editions.md)


## 其他資源

* [以組織方式註冊 Azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58--> 