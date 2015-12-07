<properties 
	pageTitle="Azure AD 密碼重設 |Microsoft Azure"
	description="Azure AD 中的密碼管理功能說明，包括密碼重設、變更、密碼管理報告，以及本機內部部署 Active Directory 回寫。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>
	

# 使用者和系統管理員的 Azure AD 密碼重設

  >[AZURE.IMPORTANT]您來到此處是因為想要重設 Azure 或 O365 密碼嗎？ 若是如此，請[跳到本節](#users-how-to-manage-your-own-password)。
  
利用自助管理方式來降低成本和節省人力，一直以來都是世界各地 IT 部門追求的主要目標。因此，市場中充斥著各式產品，讓您能夠從雲端或內部部署管理內部部署群組、密碼或使用者設定檔。而獨樹一格的 Azure AD，可提供您一些現今市場上最容易使用且最強大的自助管理功能。

**Azure AD 密碼管理**提供一組讓使用者隨時隨地從任何裝置管理密碼的功能，同時還能遵循您定義的安全性原則。

##使用者：如何管理您自己的密碼
如果您是組織中使用 Office 365 或 Microsoft 帳戶存取工作資源的使用者 (不是系統管理員)，請按一下以下連結，以了解如何修正密碼的常見問題。

| 主題 | |
| --------- | --------- |
| 我想要註冊密碼重設 | [如何註冊密碼重設](active-directory-passwords-update-your-own-password.md#how-to-register-for-password-reset) |
| 我想要從 O365 變更我的密碼 | [如何從 O365 變更您的密碼](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-o365) |
| 我想要從 myapps.microsoft.com 變更我的密碼 | [如何從存取面板變更您的密碼](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-the-access-panel) |
| 我忘記密碼，想要加以重設 | [如何重設密碼](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password) |
| 我無法登入，想要解除鎖定我的帳戶 | [如何解除鎖定您的內部部署帳戶](active-directory-passwords-update-your-own-password.md#how-to-unlock-your-account) |
| 我想對失敗的密碼重設進行疑難排解 | [常見問題及其解決方案](active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) |

##系統管理員：了解如何開始使用 Azure AD 密碼重設
如果您是想要啟用 Azure AD 密碼重設，或只是想深入加以了解的系統管理員，請從下列連結開始取得您感興趣的內容。

| 主題 | |
| --------- | --------- |
| 支援的案例 | [Azure AD 密碼重設有何用途？](#what-is-possible-with-azure-ad-password-reset) |
| 為何要使用它？ | [為何要使用 Azure AD 密碼重設？](#why-use-azure-ad-password-reset) |
| 價格與可用性 | [價格與可用性](#pricing-and-availability) |
| 啟用密碼重設 | [為您的使用者啟用密碼重設](#enable-password-reset-for-your-users) |
| 自訂其運作方式 | [自訂密碼重設行為](#customize-password-reset-behavior) |
| 為我的使用者導入它 | [將您的使用者設定成使用密碼重設](#configure-your-users-to-use-password-reset) |
| 檢視報告 | [透過整合式報告檢視密碼重設活動](#view-password-reset-activity-with-integrated-reports) |
| 重設使用者的密碼 | [管理您的使用者密碼](#manage-your-users-passwords) |
| 設定組織的密碼原則 | [設定密碼原則](#set-password-policies) |
| 疑難排解問題 | [疑難排解問題](#troubleshoot-a-problem) |
| 常見問題集 | [閱讀常見問題集](#read-a-faq) |
| 技術詳細資訊 | [了解技術詳細資料](#understand-the-technical-details) |
| 新發行的功能 | [近期服務更新](#recent-service-updates) |
| 其他文件的連結 | [密碼重設文件的連結](#links-to-password-reset-documentation) |

### Azure AD 密碼重設有何用途？
以下是一些可使用 Azure AD 密碼管理功能執行的事項。

- **自助式密碼變更**可讓使用者或系統管理員變更過期或未過期密碼，無須向系統管理員或服務台請求支援。
- **自助式密碼重設**可讓使用者或系統管理員自動重設密碼，無須向系統管理員或服務台請求支援。自助式密碼重設需要 Azure AD Premium 或 Basic。如需詳細資訊，請參閱 Azure Active Directory 版本。
- **由系統管理員起始的密碼重設**可讓系統管理員在 [Azure 管理入口網站](https://manage.windowsazure.com)中重設使用者或其他系統管理員的密碼。
- **密碼管理活動報告**讓系統管理員得以深入了解其組織內發生的密碼重設和註冊活動。 
- **密碼回寫**允許從雲端管理內部部署密碼，讓同盟或已同步處理密碼的使用者能夠執行或代表上述所有案例。如果要進行密碼回寫，就必須使用 Azure AD Premium。如需詳細資訊，請參閱開始使用 Azure AD Premium。

### 為何要使用 Azure AD 密碼重設？
以下原因說明為何您應該使用 Azure AD 密碼管理功能

- **降低成本** - 支援輔助密碼重設通常佔組織 IT 部門 20% 的費用
- **改善使用者經驗** - 使用者不希望每次忘記密碼時，都必須致電服務台並花費大量時間在通話上。
- **減少服務台人力** - 密碼管理是大部分組織設立服務台的最大單一因素
- **發揮行動力** - 使用者可隨時隨地重設密碼

### 價格與可用性
Azure AD 密碼重設有 3 個可用層級，視您擁有的訂用帳戶而定：

- **Azure AD Free** - 僅限雲端的系統管理員可以重設自己的密碼
- **Azure AD Basic 或任何付費 O365 訂用帳戶** - 僅限雲端的使用者及僅限雲端的系統管理員可以重設自己的密碼
- **Azure AD Premium** - 任何使用者或系統管理員，包括僅限雲端、同盟或密碼同步處理的使用者可以重設自己的密碼 (需要[啟用密碼回寫](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords))

如需關於 Azure AD Premium 或 Basic 價格價的詳細資訊，請瀏覽 [Active Directory 價格詳細資料](https://azure.microsoft.com/pricing/details/active-directory/)頁面。

##為您的使用者啟用密碼重設
| 主題 | |
| --------- | --------- |
| 如何為雲端使用者啟用密碼重設？ | [讓使用者重設其雲端 Azure Active Directory 密碼](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| 如何為內部部署使用者啟用密碼重設和變更？ | [讓使用者重設或變更其內部部署 Active Directory 密碼](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| 如何將密碼重設的範圍限定為一組特定的使用者？ | [將密碼重設限定於特定使用者](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| 如何測試雲端密碼重設？ | [以使用者的身分重設您的 Azure AD 密碼](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| 如何測試內部部署密碼重設？ | [以使用者的身分重設您的內部部署 AD 密碼](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| 如何在後續停用密碼重設？ | [設定：使用者已啟用密碼重設](active-directory-passwords-customize.md#users-enabled-for-password-reset) |


##自訂密碼重設行為
| 主題 | |
| --------- | --------- |
| 如何變更支援的驗證方法？ | [設定：使用者可用的驗證方法](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| 如何變更必要驗證方法數目？ | [設定：必要驗證方法數目](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| 如何設定自訂安全性問題？ | [設定：自訂安全性問題](active-directory-passwords-customize.md#custom-security-questions) |
| 如何設定預先定義的當地語系化安全性問題？ | [設定：以知識為基礎的安全性問題](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| 如何變更必要的安全性問題數目？ | [設定：註冊或重設的安全性問題數目](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| 如何自訂使用者聯繫系統管理員的方式？ | [設定：自訂「連絡您的系統管理員」連結](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| 如何讓使用者直接解除鎖定 AD 帳戶而不必重設密碼？ | [設定：讓使用者直接解除鎖定 AD 帳戶而不必重設密碼](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| 如何為使用者啟用密碼重設通知？ | [設定：在使用者的密碼重設時通知使用者](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| 如何為系統管理員啟用密碼重設通知？ | [設定：在系統管理員重設其密碼時通知其他系統管理員](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| 如何自訂密碼重設的外觀及操作？ | [設定：公司名稱、商標和標誌](active-directory-passwords-customize.md#password-managment-look-and-feel) |


##將您的使用者設定成使用密碼重設
| 主題 | |
| --------- | --------- |
| 如何得知某個帳戶是否已設定密碼重設？ | [如何針對密碼重設設定帳戶？](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| 如何為使用者設定密碼重設？ | [為您的使用者填入密碼重設驗證資料的方式](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| 如何為我的使用者手動上傳資料？ | [自行上傳密碼重設資料](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| 如何使用 PowerShell 為我的使用者讀取或設定資料？ | [如何存取密碼為使用者重設資料](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| 如何從內部部署同步處理密碼重設資料？ | [密碼重設使用哪些資料](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| 如何利用電子郵件活動促使我的使用者註冊及使用密碼重設？ | [以電子郵件推動密碼重設](active-directory-passwords-best-practices.md#email-based-rollout) |
| 如何促使我的使用者在登入時註冊？ | [以強制註冊的方式推動密碼重設](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| 如何促使我的使用者定期重新確認他們的註冊？ | [設定：使用者必須在幾天後重新確認其驗證資料](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| 什麼是將密碼重設傳達給使用者的最佳作法？ | [建立您自己的密碼入口網站供使用者使用](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |


##透過整合式報告檢視密碼重設活動
| 主題 | |
| --------- | --------- |
| 我該至何處查看密碼重設報告？ | [密碼管理報告概觀](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| 哪裡可以查看使用者在我的組織中使用密碼重設的情形？ | [檢視密碼重設活動](active-directory-passwords-get-insights.md#view-password-reset-activity) |
| 哪裡可以查看有多少使用者註冊及其註冊的項目？ | [檢視密碼重設註冊活動](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) |
| 如何從 API 取得密碼重設報告？ | [建立 Azure AD 應用程式以存取報告 API](active-directory-reporting-api-getting-started.md#creating-an-azure-ad-application-to-access-the-api) |
| 透過 API 可取得何種密碼重設報告資訊？ | [報告 API 中提供的密碼重設和註冊事件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |


##管理您的使用者密碼
| 主題 | |
| --------- | --------- |
| 如何從 O365 管理入口網站重設使用者的密碼？ | [在 Office 365 中重設使用者的密碼](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| 如何使用 PowerShell 重設使用者的密碼？ | [使用 Set-MsolUserPassword 重設使用者的密碼](https://msdn.microsoft.com/library/azure/dn194140.aspx) |


##設定密碼原則
| 主題 | |
| --------- | --------- |
| 如何從 Office 365 設定組織的密碼到期原則？ | [設定密碼到期原則](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| 如何使用 PowerShell 將特定使用者的密碼設定為永不過期？ | [使用 PowerShell 將個別使用者的密碼設定為永不過期](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| 如何使用 PowerShell 確認使用者的密碼是否設定為永不過期 | [使用 PowerShell 確認個別使用者的密碼到期狀態](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |


##疑難排解問題
| 主題 | |
| --------- | --------- |
| 我需要協助時應提供何種資訊給支援人員？ | [您需要協助時應包含的資訊](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| 如何修正密碼重設的問題 | [疑難排解密碼重設入口網站](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| 如何修正密碼回寫的問題 | [疑難排解密碼回寫](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| 如何修正密碼回寫連線的問題 | [疑難排解密碼回寫連線](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| 如何修正密碼重設組態的問題 | [在 Azure 管理入口網站中疑難排解密碼重設組態](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| 如何修正密碼重設報告的問題 | [在 Azure 管理入口網站中疑難排解密碼管理報告](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| 如何修正密碼重設註冊的問題 | [疑難排解密碼重設註冊入口網站](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| 密碼回寫事件記錄檔錯誤碼 | [密碼回寫事件記錄檔錯誤碼](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |


##閱讀常見問題集
| 主題 | |
| --------- | --------- |
| 我想要閱讀關於密碼重設註冊的常見問題集 | [密碼重設註冊常見問題集](active-directory-passwords-faq.md#password-reset-registration) |
| 我想要閱讀關於密碼重設的常見問題集 | [密碼重設常見問題集](active-directory-passwords-faq.md#password-reset) |
| 我想要閱讀關於密碼重設報告的常見問題集 | [密碼管理報告常見問題集](active-directory-passwords-faq.md#password-management-reports) |
| 我想要閱讀關於密碼回寫的常見問題集 | [密碼回寫常見問題集](active-directory-passwords-faq.md#password-writeback) |


##了解技術詳細資料

| 主題 | |
| --------- | --------- |
| 我想要了解什麼是密碼回寫 | [密碼回寫概觀](active-directory-passwords-learn-more.md#password-writeback-overview) |
| 我想要了解密碼回寫的運作方式 | [密碼回寫如何運作？](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| 我想要了解密碼回寫支援的案例 | [密碼回寫的支援案例](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| 我想要了解密碼回寫如何受到保護 | [密碼回寫的安全性模型](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| 我想要了解密碼重設入口網站的運作方式 | [密碼重設入口網站的運作方式](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| 我想要了解密碼重設所使用的資料 | [密碼重設使用哪些資料？](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## 近期服務更新

####在登入 Office 365 應用程式時強制密碼重設註冊 - 2015 年 11 月

- 現在，在啟用[強制註冊](active-directory-passwords-customize.md#require-users-to-register-when-signing-in)功能後，您的使用者將必須使用工作或學校帳戶從他們登入的任何位置進行註冊。這將可大幅增加許多組織可上線以重設密碼的速度。藉由這項新功能，我們發現大型組織在短短 2 週內即可上線。

####支援解除鎖定 Active Directory 帳戶而不必重設密碼 - 2015 年 11 月

- 僅解除鎖定而不重設，是近來客服中心龐大的業務之一。事實上，許多組織有高達 70% 的密碼重設預算都花在解除鎖定帳戶上。 為了因應此需求，現在您可以利用 Azure AD 密碼重設啟用特定功能，讓您的使用者直接解除鎖定 AD 帳戶，而不需重設密碼。請在此處了解如何開啟此功能：[設定：讓使用者直接解除鎖定 AD 帳戶而不必重設密碼](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password)。

####對註冊頁面的可用性更新 - 2015 年 10 月

- 現在，當使用者註冊資料之後，他或她只需按一下 [看起來不錯] 即可更新資料而不需要重新傳送電子郵件或打電話。

####改進密碼回寫的可靠性 - 2015 年 9 月

- 在 Azure AD Connect 9 月發行起，密碼回寫代理程式現在將更積極地重試連接，還有其他更強固的容錯移轉功能。

####用於擷取密碼重設報告資料的 API - 2015 年 8 月

- 現在，密碼重設報告背後的資料可以直接從 [Azure AD 的報告和事件 API](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 擷取。

####在雲端網域加入期間支援 Azure AD 密碼重設 - 2015 年 8 月

- 現在，任何雲端使用者可以在雲端網域加入上架體驗期間，直接從 Windows 10 登入畫面重設他或她的密碼。請注意，這尚未在 Windows 10 登入畫面上公開。

####在登入 Azure 和同盟應用程式時強制密碼重設註冊 - 2015 年 7 月

- 除了在登入 myapps.microsoft.com 時強制執行註冊，我們現在支援在登入 Azure 管理入口網站和您的任何同盟單一登入應用程式期間強制註冊

####安全性問題當地語系化支援 - 2015 年 5 月

- 現在，您可以在設定密碼重設的安全性問題時，選擇以完整 O365 語言集當地語系化的預先定義安全性問題。

####密碼重設期間帳戶解除鎖定支援 - 2015 年 6 月

- 如果您使用密碼回寫，並且您在帳戶已鎖定時重設密碼，我們將會自動解除鎖定您的 Active Directory 帳戶！

####加上標誌的 SSPR 註冊 - 2015 年 4 月

- 密碼重設註冊頁面現在已加上您的公司標誌！

####安全性問題 - 2015 年 3 月

- 我們已發行安全性問題至 GA！

####帳戶解除鎖定 - 2015 年 3 月

- 現在使用者可以在重設密碼後解除帳戶鎖定

## 敬請期待

以下是一些我們目前正在處理的酷炫功能！

**支援在登入期間提醒使用者更新其註冊資料** - 進行中

- 現在，我們支援在存取 myapps.microsoft.com 時提醒使用者更新其所註冊的資料，但我們正努力對所有登入實現此功能。

## 密碼重設文件的連結
以下是所有 Azure AD 密碼重設文件頁面的連結：

* [**重設自己的密碼**](active-directory-passwords-update-your-own-password.md) - 了解身為系統使用者如何重設或變更自己的密碼
* [**運作方式**](active-directory-passwords-how-it-works.md) - 了解六個不同的服務元件及其功能
* [**開始使用**](active-directory-passwords-getting-started.md) - 了解如何讓使用者重設及變更雲端或內部部署密碼
* [**自訂**](active-directory-passwords-customize.md) - 了解如何依照組織的需求自訂外觀和服務行為
* [**最佳作法**](active-directory-passwords-best-practices.md) - 了解如何快速部署且有效管理組織的密碼
* [**深入探索**](active-directory-passwords-get-insights.md) - 了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) - 取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何快速移難排解服務的問題
* [**深入了解**](active-directory-passwords-learn-more.md) - 深入探索服務運作方式的技術細節

<!---HONumber=AcomDC_1125_2015-->