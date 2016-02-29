<properties
	pageTitle="Azure Active Directory 中應用程式管理的文章索引 | Microsoft Azure"
	description="了解如何自訂同盟憑證的到期日期，以及如何更新即將到期的憑證。"
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markvi;liviodlc"/>

#Azure Active Directory 中應用程式管理的文章索引

本頁面提供一份完整清單，列出撰寫 Azure Active Directory (Azure AD) 中各種應用程式相關功能的所有文章。

每個主要功能區都有簡介，同時也會根據要尋找的資訊指引您需要閱讀的文章。

##概觀文章

對於只需要 Azure AD 應用程式管理功能之簡要說明的使用者，以下文章是很好的起點。

| 文章指南 | |
| :---: | --- |
| 簡介 Azure AD 解決的應用程式管理問題 | [使用 Azure Active Directory (AD) 管理應用程式](active-directory-enable-sso-scenario.md) |
| Azure AD 中與啟用單一登入、定義有權存取應用程式的人員，以及使用者如何啟動應用程式相關的各種功能概觀 | [Azure Active Directory 中的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md) |
| 探討將應用程式整合至 Azure AD 時所涉及的不同步驟 | [整合 Azure Active Directory 與應用程式](active-directory-integrating-applications-getting-started.md)<br /><br />[啟用 SaaS 應用程式的單一登入](active-directory-sso-integrate-saas-apps.md)<br /><br />[管理應用程式的存取](active-directory-managing-access-to-apps.md) |
| 如何在 Azure AD 中表示應用程式的技術說明 | [將應用程式加入 Azure AD 的方式和原因](active-directory-how-applications-are-added.md) |

##疑難排解文章

本節可讓您快速存取相關的疑難排解指南。在此頁面的其餘部分可以找到每個功能範圍的詳細資訊。

| 功能範圍 | |
| :---: | --- |
| 同盟單一登入 | [對 SAML 型單一登入進行疑難排解](active-directory-saml-debugging.md) |
| 密碼單一登入 | [疑難排解 Internet explorer 的存取面板擴充功能](active-directory-saas-ie-troubleshooting.md) |
| 應用程式 Proxy | [應用程式 Proxy 疑難排解指南](active-directory-application-proxy-troubleshoot.md) |
| 內部部署 AD 與 Azure AD 之間的單一登入 | [對密碼同步化進行疑難排解](active-directory-aadconnectsync-implement-password-synchronization.md#managing-password-synchronization)<br /><br />[對密碼回寫進行疑難排解](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) | 
| 動態群組成員資格 | [對動態群組成員資格進行疑難排解](active-directory-accessmanagement-troubleshooting.md) |

##單一登入 (SSO)

###同盟單一登入：使用一個身分識別登入多個應用程式

單一登入可讓使用者只使用一組認證，存取各種不同的應用程式和服務。您可以透過同盟這種方法啟用單一登入。當使用者嘗試登入同盟應用程式時，會被重新導向至其組織的官方登入頁面 (由 Azure Active Directory 轉譯)，然後一旦驗證成功，即會重新導向回應用程式。

| 文章指南 | |
| :---: | --- |
| 簡介同盟和其他類型的登入 | [使用 Azure AD 進行單一登入](active-directory-appssoaccess-whatis.md) |
| 透過已簡化摸單一登入設定步驟，與 Azure AD 預先整合的數千個 SaaS 應用程式 | [開始使用 Azure AD 應用程式庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[支援同盟的預先整合應用程式完整清單](http://aka.ms/aadfederatedapps)<br /><br />[如何將您的應用程式新增至 Azure AD 應用程式庫](active-directory-app-gallery-listing.md) |
| 超過 150 個應用程式教學課程，教導您如何為 [Salesforce](active-directory-saas-salesforce-tutorial.md)、[ServiceNow](active-directory-saas-servicenow-tutorial.md)、[Google Apps](active-directory-saas-google-apps-tutorial.md)、[Workday](active-directory-saas-workday-tutorial.md) 等應用程式設定單一登入 | [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md) |
| 如何手動設定及自訂您的單一登入設定 | [設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入](active-directory-saas-custom-apps.md)<br /><br />[如何為預先整合的應用程式自訂在 SAML 權杖中發出的宣告](active-directory-saml-claims-customization.md) |
| 使用 SAML 通訊協定的同盟應用程式疑難排解指南 | [對 SAML 型單一登入進行疑難排解](active-directory-saml-debugging.md) |
| 如何設定應用程式的憑證到期日，以及如何更新憑證 | [在 Azure Active Directory 中管理同盟單一登入的憑證](active-directory-sso-certs.md) |

同盟單一登入適用於所有版本的 Azure AD，每個使用者最多十個應用程式。[Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 支援無限多個應用程式。如果您的組織具有 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 或 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)，您就可以[使用群組來指派對同盟應用程式的存取權](#how-to-manage-who-has-access-to-which-apps)。

###密碼型單一登入：非同盟應用程式的帳戶共用和 SSO

為了對不支援同盟的應用程式啟用單一登入，Azure AD 會提供數個密碼管理功能，而這些功能可安全地將密碼儲存到 SaaS 應用程式，並自動將使用者登入那些應用程式。您可以輕鬆地散發新建立帳戶的認證，並與多人共用小組帳戶。使用者不需要知道可存取的帳戶認證。

| 文章指南 | |
| :---: | --- |
| 密碼型 SSO 運作方式的簡介以及簡要的技術概觀 | [使用 Azure AD 進行密碼型單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| 與共用帳戶相關的案例以及 Azure AD 如何解決這些問題的摘要 | [使用 Azure AD 共用帳戶](active-directory-sharing-accounts.md) |
| 自動定期變更特定應用程式的密碼 | [自動密碼變換 (預覽)](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx0) |
| Internet Explorer 版本的 Azure AD 密碼管理擴充功能的部署和疑難排解指南 | [如何使用群組原則部署 Internet Explorer 的存取面板延伸模組](active-directory-saas-ie-group-policy.md)<br /><br />[疑難排解 Internet Explorer 的存取面板延伸模組](active-directory-saas-ie-troubleshooting.md) |

密碼型單一登入適用於所有版本的 Azure AD，每個使用者最多十個應用程式。[Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 支援無限多個應用程式。如果您的組織具有 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 或 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)，您就可以[使用群組來指派對應用程式的存取權](#how-to-manage-who-has-access-to-which-apps)。自動密碼變換是一項 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 功能。

###應用程式 Proxy：單一登入和遠端存取內部部署應用程式

如果您的私人網路中具有需要由網路外部的使用者和裝置存取的應用程式，您可以使用 Azure AD 應用程式 Proxy，對這些應用程式啟用安全遠端存取。

| 文章指南 | |
| :---: | --- |
| Azure AD 應用程式 Proxy 及其運作方式的概觀 | [提供安全的內部部署應用程式遠端存取](active-directory-application-proxy-get-started.md)<br /><br />[Azure AD 應用程式 Proxy 如何運作？](active-directory-appssoaccess-enable-hybrid-access.md) |
| 如何設定應用程式 Proxy 以及如何發佈第一個應用程式的教學課程 | [如何設定 Azure AD 應用程式 Proxy](active-directory-application-proxy-enable.md)<br /><br />[如何以無訊息方式安裝應用程式 Proxy 連接器](active-directory-application-proxy-silent-installation.md)<br /><br />[如何使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)<br /><br />[如何使用您自己的網域名稱](active-directory-application-proxy-custom-domains.md) |
| 如何對使用應用程式 Proxy 發佈的應用程式啟用單一登入和條件式存取 | [使用應用程式 Proxy 進行單一登入](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[條件式存取和應用程式 Proxy](active-directory-application-proxy-conditional-access.md) |
| 如何對下列案例使用應用程式 Proxy 的指引 | [如何支援原生用戶端應用程式](active-directory-application-proxy-native-client.md)<br /><br />[如何支援宣告感知 (Claims-Aware) 應用程式](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[如何支援在個別網路和位置上發行的應用程式](active-directory-application-proxy-connectors.md) |
| 應用程式 Proxy 的疑難排解指南 | [應用程式 Proxy 疑難排解指南](active-directory-application-proxy-troubleshoot.md) |

應用程式 Proxy 適用於所有版本的 Azure AD，每個使用者最多十個應用程式。[Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 支援無限多個應用程式。如果您的組織具有 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 或 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)，您就可以[使用群組來指派對應用程式的存取權](#how-to-manage-who-has-access-to-which-apps)。

您也可能會對 [Azure AD 網域服務](../active-directory-domain-services/active-directory-ds-overview.md)產生興趣，這些服務可讓您將內部部署應用程式移轉到 Azure，同時又滿足這些應用程式的身分識別需求。

###在 Azure AD 與內部部署 AD 之間啟用單一登入

如果您的組織在內部部署上維護 Windows Server Active Directory，並在雲端中維護 Azure Active Directory，您可能會想在這兩個系統之間啟用單一登入。Azure AD Connect (將這兩個系統整合在一起的工具) 提供多個設定單一登入的選項：建立與 ADFS 的同盟或其他同盟提供者，或啟用密碼同步處理。

| 文章指南 | |
| :---: | --- |
| Azure AD Connect 中提供之單一登入選項的概觀，以及管理混合式環境的相關資訊 | [Azure AD Connect 中的使用者登入選項](active-directory-aadconnect-user-signin.md) |
| 同時使用內部部署 Active Directory 和 Azure Active Directory 管理環境的一般指引 | [Azure AD 混合式身分識別設計考量](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md) |
| 使用密碼同步處理來啟用 SSO 的相關指導方針 | [使用 Azure AD Connect 實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)<br /><br />[對密碼同步處理進行疑難排解](https://support.microsoft.com/zh-TW/kb/2855271) |
| 使用密碼回寫來啟用 SSO 的相關指導方針 | [開始使用 Azure AD 中的密碼管理](active-directory-passwords-getting-started.md)<br /><br />[疑難排解密碼回寫](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| 使用協力廠商身分識別提供者來啟用 SSO 的指導方針 | [可用來啟用單一登入的相容協力廠商身分識別提供者清單](https://aka.ms/ssoproviders) | 
| Windows 10 使用者如何透過 Azure AD Join 享受單一登入的優點 | [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-overview.md) |

Azure AD Connect 適用於[所有版本的 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)。「Azure AD 自助式密碼重設」適用於 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)。對內部部署 AD 進行「密碼回寫」是一項 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 功能。

###條件式存取：強制高風險應用程式符合額外的安全性需求

一旦您對應用程式和資源設定單一登入，就可以在每次登入該應用程式時，強制符合特定的安全性需求，進一步保護機密應用程式的安全。比方說，您可以使用 Azure AD，要求只要存取特定應用程式就需要執行 Multi-Factor Authentication，不論該應用程式是否本來就支援該功能。條件式存取的另一個常見範例是要求使用者連線到組織信任的網路，以便能夠存取特別敏感的應用程式。

| 文章指南 | |
| :---: | --- |
| 簡介跨 Azure AD、Office365 和 Intune 提供的條件式存取功能 | [使用條件式存取管理風險](active-directory-conditional-access.md) |
| 如何為下列類型的資源啟用條件式存取 | [SaaS 應用程式的條件式存取](active-directory-conditional-access-azuread-connected-apps.md)<br /><br />[Office 365 服務的條件式存取](active-directory-conditional-access-device-policies.md)<br /><br />[內部部署應用程式的條件式存取](active-directory-conditional-access-on-premises-setup.md)<br /><br />[透過 Azure AD 應用程式 Proxy 發佈之內部部署應用程式的條件式存取](active-directory-application-proxy-conditional-access.md) |
| 如何向 Azure Active Directory 註冊裝置，以便啟用裝置型條件式存取原則 | [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)<br /><br />[如何為加入網域的 Windows 裝置啟用自動裝置註冊](active-directory-conditional-access-automatic-device-registration.md)<br />— [適用於 Windows 8.1 裝置的步驟](active-directory-conditional-access-automatic-device-registration-windows8_1.md)<br />— [適用於 Windows 7 裝置的步驟](active-directory-conditional-access-automatic-device-registration-windows7.md) |
| 如何對包括 Multi-Factor Authentication 的原則使用 Android 版本的 Azure Authenticator 應用程式 | [適用於 Android 的 Azure Authenticator](active-directory-conditional-access-azure-authenticator-app.md) |

「條件式存取」是一項 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 功能。


##應用程式和 Azure AD

###Cloud App Discovery：尋找您的組織使用哪些 SaaS 應用程式

Cloud App Discovery 有助於 IT 部門了解整個組織正在使用哪些 SaaS 應用程式 。它可以測量應用程式使用情況，讓 IT 可以判斷哪些購買的應用程式可在 IT 控制下獲得最大益處，且可與 Azure ad 整合。

| 文章指南 | |
| :---: | --- |
| 其運作方式的一般概觀 | [使用 Cloud App Discovery 尋找未經約束的雲端應用程式](active-directory-cloudappdiscovery-whatis.md) |
| 深入探討其運作方式，並回答隱私權問題 | [安全性和隱私權考量](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| 常見問題集 | [Cloud App Discovery 的常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| 部署 Cloud App Discovery 的教學課程 | [群組原則部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[System Center 部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[在具有自訂連接埠的 Proxy 伺服器上安裝](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Cloud App Discovery 代理程式之更新的變更記錄檔 | [變更記錄檔](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery 是一項 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 功能。

###自動佈建和取消佈建 SaaS 應用程式中的使用者帳戶

在 SaaS 應用程式 (例如 Dropbox、Salesforce、ServiceNow 等等) 中自動建立、維護和移除使用者身分識別。在 Azure AD 與您的 SaaS 應用程式之間比對和同步處理現有的身分識別，並在使用者離開組織時自動停用帳戶來控制存取。

| 文章指南 | |
| :---: | --- |
| 了解其運作方式，並尋找常見問題的解答 | [將 SaaS 應用程式的使用者佈建/取消佈建自動化](active-directory-saas-app-provisioning.md) |
| 設定如何在 Azure AD 與您的 SaaS 應用程式之間對應資訊 | [自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)<br><br>[撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| 如何對任何支援 SCIM 通訊協定的應用程式啟用自動化佈建 | [對任何啟用 SCIM 的應用程式設定自動化使用者佈建](active-directory-scim-provisioning.md) |
| 取得佈建失敗的通知 | [佈建通知](active-directory-saas-account-provisioning-notifications.md) |
| 根據應用程式的屬性值限制可以佈建應用程式的人員 | [範圍篩選器](active-directory-saas-scoping-filters.md) |

自動化使用者佈建適用於所有版本的 Azure AD，每個使用者最多十個應用程式。[Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 支援無限多個應用程式。如果您的組織具有 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 或 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)，您就可以[使用群組來管理要佈建哪些使用者](#how-to-manage-who-has-access-to-which-apps)。

###建置與 Azure AD 整合的應用程式

如果您的組織從事開發或維護特定業務 (LoB) 應用程式，或如果您是使用 Azure Active Directory 客戶的應用程式開發人員，下列教學課程將協助您整合應用程式與 Azure AD。

| 文章指南 | |
| :---: | --- |
| 有關 IT 專業人員和應用程式開發人員整合應用程式與 Azure AD 的指南 | [適用於開發 Azure AD 應用程式的 IT 專業人員指南](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Azure Active Directory 的開發人員指南](active-directory-developers-guide.md) |
| 應用程式廠商如何將其應用程式新增至 Azure AD 應用程式資源庫 | [在 Azure Active Directory 應用程式資源庫中列出您的應用程式](active-directory-app-gallery-listing) |
| 如何使用 Azure Active Directory 管理所開發之應用程式的存取 | [如何為所開發的應用程式啟用使用者指派](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[將使用者指派給您的應用程式](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[將群組指派給您的應用程式](active-directory-applications-guiding-developers-assigning-groups.md) |

如果您開發的是消費者對應應用程式，您可能會對使用 [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 感興趣，這可讓您不必開發自己的身分識別系統，即可管理您的使用者。[深入了解](../active-directory-b2c/active-directory-b2c-overview.md)。


##管理應用程式的存取

###使用群組和自助方式，管理誰可以存取哪些應用程式

為了協助您管理誰應該存取哪些資源，Azure Active Directory 可讓您使用群組設定指派和權限級別。IT 可以選擇啟用自助式功能，以便在需要時，使用者可以直接要求權限。

| 文章指南 | |
| :---: | --- |
| Azure AD 存取管理功能的概觀 | [管理應用程式存取簡介](active-directory-managing-access-to-apps.md)<br /><br />[存取管理在 Azure AD 的運作方式](active-directory-manage-groups.md)<br /><br />[如何使用群組管理 SaaS 應用程式的存取權](active-directory-accessmanagement-group-saasapps.md) |
| 啟用應用程式和群組的自助式管理 | [自助式應用程式管理](active-directory-self-service-application-access.md)<br /><br />[自助式群組管理](active-directory-accessmanagement-self-service-group-management.md) |
| 在 Azure AD 設定群組的指示 | [如何建立安全性群組](active-directory-accessmanagement-manage-groups.md)<br /><br />[如何指定群組的擁有者](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[如何使用「所有使用者」群組](active-directory-accessmanagement-dedicated-groups.md) |
| 使用動態群組，利用屬性型成員資格規則自動填入群組成員資格 | [動態群組成員資格：簡單規則](active-directory-accessmanagement-simplerulegroup.md)<br /><br />[動態群組成員資格：進階規則](active-directory-accessmanagement-groups-with-advanced-rules.md)<br /><br />[疑難排解群組的動態成員資格](active-directory-accessmanagement-troubleshooting.md) |

群組型應用程式存取管理適用於 [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/)。自助式群組管理、自助式應用程式管理以及動態群組是 [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) 功能。

###B2B 共同作業：啟用應用程式的合作夥伴存取

如果您的公司已與其他公司合作，則您可能需要管理公司應用程式的合作夥伴存取。Azure Active Directory B2B 共同作業提供簡單且安全的方式，與合作夥伴共用您的應用程式。這項功能目前只能預覽。

| 文章指南 | |
| :---: | --- |
| 不同 Azure AD 功能的概觀，協助您管理外部使用者，例如合作夥伴、 客戶等。 | [比較 Azure AD 中管理外部身分識別的功能](active-directory-b2b-compare-external-identities.md) |
| 簡介 B2B 共同作業預覽，以及如何開始使用 | [簡單安全的雲端合作夥伴與 Azure AD 整合](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory B2B 共同作業](active-directory-b2b-collaboration-overview.md) |
| 深入探討 Azure AD B2B 共同作業及其使用方式 | [B2B 共同作業：運作方式](active-directory-b2b-how-it-works.md)<br /><br />[Azure AD B2B 共同作業預覽的目前限制](active-directory-b2b-current-preview-limitations.md)<br /><br />[使用 Azure AD B2B 共同作業預覽的詳細逐步解說](active-directory-b2b-detailed-walkthrough.md) |
| 具有 Azure AD B2B 共同作業如何運作的技術詳細資料參考文章 | [適用於新增合作夥伴使用者的 CSV 檔案格式](active-directory-b2b-references-csv-file-format.md)<br /><br />[受 Azure AD B2B 共同作業影響的使用者屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[合作夥伴使用者的使用者權杖格式](active-directory-b2b-references-external-user-token-format.md) |

「B2B 共同作業」預覽目前適用於[所有版本的 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)。

###存取面板：存取應用程式和自助式功能的入口網站

使用者可以在 Azure AD 存取面板上啟動應用程式和存取自助式功能，而自助式功能可讓使用者管理自己的應用程式和群組成員資格。除了存取面板外，下列清單還包括其他存取啟用 SSO 之應用程式的選項。

| 文章指南 | |
| :---: | --- |
| 將單一登入應用程式部署到使用者可用的各種選項比較 | [對使用者部署 Azure AD 整合應用程式](active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users) |
| 存取面板和其行動對等 MyApps 的概觀 | [存取面板和 MyApps 簡介](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| 如何從 Office 365 網站存取 Azure AD 應用程式 | [使用 Office 365 應用程式啟動程式](https://support.office.com/zh-TW/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| 如何從 Intune Managed Browser 行動應用程式存取 Azure AD 應用程式 | [Intune Managed Browser](https://technet.microsoft.com/zh-TW/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| 如何使用深層連結來存取 Azure AD 應用程式以起始單一登入 | [取得應用程式的直接登入連結](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

「存取面板」適用於[所有版本的 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)。

###報告：輕鬆稽核應用程式存取變更及監視應用程式的登入

Azure Active Directory 提供數個報告和警示，以協助您監視組織對應用程式的存取。您可在應用程式發生異常登入時收到警示，也可以追蹤使用者存取應用程式的時間及變更原因。

| 文章指南 | |
| :---: | --- |
| Azure Active Directory 中報告功能的概觀 | [開始使用 Azure AD 報告](active-directory-reporting-getting-started.md) |
| 如何監視使用者的登入和應用程式使用情況 | [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)<br /><br />[Azure Active Directory 所有使用者登入活動報告](active-directory-reporting-all-user-sign-in-activity-report.md) |
| 追蹤對可以存取特定應用程式所做變更的人員 | [Azure Active Directory 稽核報告事件](active-directory-reporting-audit-events.md) |
| 使用報告 API，將這些報告的資料匯出至您偏好的工具 | [開始使用 Azure AD 報告 API](active-directory-reporting-api-getting-started.md) |

若要查看不同版本的 Azure Active Directory 隨附哪些報告，請[按一下這裡](active-directory-view-access-usage-reports.md#report-editions)。

##另請參閱

[什麼是 Azure Active Directory？](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory 網域服務](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)

<!---HONumber=AcomDC_0218_2016-->