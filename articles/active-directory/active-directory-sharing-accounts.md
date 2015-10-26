<properties
	pageTitle="使用 Azure AD 共用帳戶 |Microsoft Azure"
	description="描述 Azure Active Directory 如何讓組織安全地共用內部部署應用程式和取用者雲端服務的帳戶。"
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
 	ms.date="10/13/2015"  
 	ms.author="stevenpo"/>

# 使用 Azure AD 共用帳戶

通常，組織中的情況是許多人必須使用單一使用者名稱和密碼。這通常發生在兩個情況下：

- 每個使用者必須使用唯一的登入和密碼存取應用程式時 (無論是內部部署的應用程式或取用者雲端服務，例如公司的社交媒體帳戶)。
- 建立多個使用者環境時。您可能有具備較高權限的單一本機帳戶可用來執行核心安裝、管理及復原活動 (例如 Office 365 的本機「全域系統管理員」帳戶或 Salesforce 中的根帳戶)。

傳統上，這些帳戶的共用方式是透過將認證 (使用者名稱/密碼) 散發給適當的人員，或是將認證儲存在多個受信任的代理人可以存取的共用位置。

## 傳統共用模型有什麼問題？

傳統共用模型有幾個缺點：

- 您必須將認證散發給需要存取新應用程式的所有人，他們才能進行存取。
- 每個共用的應用程式可能都需要唯一的一組共用認證，使用者必須記住許多組認證。當使用者必須記住許多認證時，他們會依靠有風險的做法 (例如寫下密碼)，風險因此會增加。
- 您不知道誰有權存取應用程式。
- 您不知道誰存取了應用程式。
- 當您需要移除某個應用程式的存取權時，您必須更新認證，並將認證重新散發給需要存取該應用程式的所有人。

## Azure Active Directory 有何助益？

Azure AD 提供使用共用帳戶的新方法，可以消除這些缺點。Azure AD 系統管理員可使用存取面板來設定使用者可存取哪些應用程式。系統管理員可針對每個應用程式選擇最適合該應用程式的單一登入類型。其中的密碼單一登入類型在該應用程式的登入程序期間，可讓 Azure AD 做為一種代理程式。系統管理員可以輕易地授與或撤銷應用程式的存取權，也隨時可以知道誰有權存取帳戶以及誰曾經存取帳戶。

使用者只需使用其組織帳戶 (即他們經常用來存取桌面或電子郵件的帳戶) 登入一次。他們只能探索和存取指派給他們的那些應用程式。使用共用帳戶，這份應用程式清單可以包含任何數目的共用認證。使用者不需記住或寫下多個可能使用的帳戶。

共用帳戶不只增加監督的方便性和改善可用性，也可增強安全性。具有認證使用權限的使用者看不到共用密碼，而是會在協調的驗證流程當中取得密碼的使用權限。此外，使用某些密碼 SSO 應用程式時，您可選擇讓 Azure AD 定期使用字元數多的複雜密碼來變換 (更新) 密碼，以提升帳戶安全性。

Azure AD 支援的共用帳戶適用於任何Enterprise Mobility Suite (EMS)、進階或基本型的授權使用者，含括所有類型的密碼單一登入應用程式。您可以共用應用程式庫中數千個預先整合的應用程式的帳戶，並可加入含有[自訂 SSO 應用程式](active-directory-single-sign-on-newly-acquired-saas-apps.md)的密碼驗證應用程式。

若要使用 Azure AD 來共用帳戶，您必須：

- 新增應用程式[應用程式庫](https://azure.microsoft.com/zh-TW/marketplace/active-directory/)或[自訂應用程式](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- 設定應用程式使用密碼單一登入 (SSO)
- 使用[以群組為基礎的指派](active-directory-accessmanagement-group-saasapps.md)，並選取輸入共用認證的選項
- 選擇性：在某些應用程式 (例如 Facebook、Twitter 或 LinkedIn) 中，您可以啟用 [Azure AD 自動變換密碼](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)的選項。

使用 Azure AD，可以透過 Multi-Factor Authentication (MFA) 讓您的共用帳戶更安全 (深入了解[使用 Azure AD 保護應用程式](multi-factor-authentication-get-started.md))，並可使用 [Azure AD 自助式](active-directory-accessmanagement-self-service-group-management.md)群組管理來委派管理誰有權存取應用程式。

## 如何開始使用？

首先，如果您還未使用 Azure AD，而且您是 IT 系統管理員：

- [試試看！](https://azure.microsoft.com/trial/get-started-active-directory/) - 您可以使用此連結立即註冊 30 天的免費試用版，不到 5 分鐘即可部署您的第一個雲端解決方案

啟用帳戶共用的 Azure AD 功能包括：

- [密碼單一登入](active-directory-passwords-getting-started.md)
- 密碼單一登入代理程式
- [群阻指派](active-directory-accessmanagement-self-service-group-management.md)
- 自訂密碼應用程式
- [應用程式使用量儀表板/報告](active-directory-passwords-get-insights.md)
- 使用者存取入口網站
- [應用程式 Proxy](active-directory-application-proxy-get-started.md)
- [Active Directory 市集](http://azure.microsoft.com/marketplace/active-directory/all/)

相關內容：

- [使用條件式存取保護應用程式](active-directory-conditional-access.md)
- [自助式群組管理/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=Oct15_HO3-->