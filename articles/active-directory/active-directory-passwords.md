<properties 
	pageTitle="Azure AD 密碼管理是什麼 | Microsoft Azure"
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
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# 從任何地方管理您的密碼
利用自助管理方式來降低成本和節省人力，一直以來都是世界各地 IT 部門追求的主要目標。因此，市場中充斥著各式產品，讓您能夠從雲端或內部部署管理內部部署群組、密碼或使用者設定檔。而獨樹一格的 Azure AD，可提供您一些現今市場上最容易使用且最強大的自助管理功能。

**Azure AD 密碼管理**提供一組讓使用者隨時隨地從任何裝置管理密碼的功能，同時還能遵循您定義的安全性原則。

## 概觀
您在五分鐘內即可開始試驗 Azure AD 密碼管理，而且在幾小時內就能將它部署到整個組織。以下是一些實用資源，可協助您使用該服務：

* [**運作方式**](active-directory-passwords-how-it-works.md) - 了解六個不同的服務元件及其功能
* [**開始使用**](active-directory-passwords-getting-started.md) - 了解如何讓使用者重設及變更雲端或內部部署密碼
* [**自訂**](active-directory-passwords-customize.md) - 了解如何依照組織的需求自訂外觀和服務行為
* [**最佳作法**](active-directory-passwords-best-practices.md) - 了解如何快速部署且有效管理組織的密碼
* [**深入探索**](active-directory-passwords-get-insights.md) - 了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) - 取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何快速移難排解服務的問題
* [**深入了解**](active-directory-passwords-learn-more.md) - 深入探索服務運作方式的技術細節


## Azure AD 密碼管理有何用途？
以下是一些可使用 Azure AD 密碼管理功能執行的事項。

- **自助式密碼變更**可讓使用者或系統管理員變更過期或未過期密碼，無須向系統管理員或服務台請求支援。
- **自助式密碼重設**可讓使用者或系統管理員自動重設密碼，無須向系統管理員或服務台請求支援。自助式密碼重設需要 Azure AD Premium 或 Basic。如需詳細資訊，請參閱 Azure Active Directory 版本。
- **由系統管理員起始的密碼重設**可讓系統管理員在 [Azure 管理入口網站](https://manage.windowsazure.com)中重設使用者或其他系統管理員的密碼。
- **密碼管理活動報告**讓系統管理員得以深入了解其組織內發生的密碼重設和註冊活動。 
- **密碼回寫**允許從雲端管理內部部署密碼，讓同盟或已同步處理密碼的使用者能夠執行或代表上述所有案例。如果要進行密碼回寫，就必須使用 Azure AD Premium。如需詳細資訊，請參閱開始使用 Azure AD Premium。

## 為何要使用 Azure AD 密碼管理？
以下原因說明為何您應該使用 Azure AD 密碼管理功能

- **降低成本** - 支援輔助密碼重設通常佔組織 IT 部門 20% 的費用
- **改善使用者經驗** - 使用者不希望每次忘記密碼時，都必須致電服務台並花費大量時間在通話上。
- **減少服務台人力** - 密碼管理是大部分組織設立服務台的最大單一因素
- **發揮行動力** - 使用者可隨時隨地重設密碼

## 近期服務更新

**對註冊頁面的可用性更新** - 2015 年 10 月

- 現在，當使用者註冊資料之後，他或她只需按一下 [看起來不錯] 即可更新資料而不需要重新傳送電子郵件或打電話。

**改進密碼回寫的可靠性** - 2015 年 9 月

- 在 Azure AD Connect 9 月發行起，密碼回寫代理程式現在將更積極地重試連接，還有其他更強固的容錯移轉功能。

**用於擷取密碼重設報告資料的 API** - 2015 年 8 月

- 現在，密碼重設報告背後的資料可以直接從 [Azure AD 的報告和事件 API](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 擷取。

**在雲端網域加入期間支援 Azure AD 密碼重設** - 2015 年 8 月

- 現在，任何雲端使用者可以在雲端網域加入上架體驗期間，直接從 Windows 10 登入畫面重設他或她的密碼。請注意，這尚未在 Windows 10 登入畫面上公開。

**在登入 Azure 和同盟應用程式時強制密碼重設註冊** - 2015 年 7 月

- 除了在登入 myapps.microsoft.com 時強制執行註冊，我們現在支援在登入 Azure 管理入口網站和您的任何同盟單一登入應用程式期間強制註冊

**安全性問題當地語系化支援** - 2015 年 5 月

- 現在，您可以在設定密碼重設的安全性問題時，選擇以完整 O365 語言集當地語系化的預先定義安全性問題。

**密碼重設期間帳戶解除鎖定支援** - 2015 年 6 月

- 如果您使用密碼回寫，並且您在帳戶已鎖定時重設密碼，我們將會自動解除鎖定您的 Active Directory 帳戶！

**加上標誌的 SSPR 註冊** - 2015 年 4 月

- 密碼重設註冊頁面現在已加上您的公司標誌！

**安全性問題** - 2015 年 3 月

- 我們已發行安全性問題至 GA！

**帳戶解除鎖定** - 2015 年 3 月

- 現在使用者可以在重設密碼後解除帳戶鎖定

## 敬請期待

以下是一些我們目前正在處理的酷炫功能！

**支援解除鎖定 Active Directory 帳戶而不必重設密碼** - 即將登場！

- 許多人要求解除鎖定 AD 帳戶，而不要重設密碼的功能。我們很高興地宣布，我們正對這項功能做最後的修飾，很快就會發行給使用密碼回寫的任何人！

**支援在登入期間提醒使用者更新其註冊資料** - 進行中

- 現在，我們支援在存取 myapps.microsoft.com 時提醒使用者更新其所註冊的資料，但我們正努力對所有登入實現此功能。

**在登入 Office 365 應用程式時強制密碼重設註冊** - 進行中

- 越來越多的 Office 應用程式即將於最新和最大的 Azure AD 登入經驗中推出。推出時，它們會自動支援 SSPR 強制的註冊！

<br/> <br/> <br/>

**其他資源**


* [密碼管理的運作方式](active-directory-passwords-how-it-works.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入了解](active-directory-passwords-learn-more.md)
* [MSDN 上的密碼管理](https://msdn.microsoft.com/library/azure/dn510386.aspx)

<!---HONumber=Oct15_HO4-->