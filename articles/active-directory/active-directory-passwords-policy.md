<properties
	pageTitle="密碼原則和 Azure Active Directory 中的限制 | Microsoft Azure"
	description="描述適用於 Azure Active Directory 中的密碼的原則，包括允許的字元、長度和有效期限"
  services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="curtand"/>


# 密碼原則和 Azure Active Directory 中的限制

本文說明和儲存在 Azure AD 目錄中的使用者帳戶相關聯的密碼原則和複雜性需求。

## 適用於所有使用者帳戶的 UserPrincipalName 原則

每個需要登入 Azure AD 驗證系統的使用者帳戶，都必須具有與該帳戶相關聯的唯一使用者主體名稱 (UPN) 屬性值。下表列出適用於內部部署的 Active Directory 來源使用者帳戶 (已同步至雲端) 和僅適用於雲端使用者帳戶的原則。

| 屬性 | UserPrincipalName 需求 |
|   ----------------------- |   ----------------------- |
| 允許的字元 | <ul> <li>A – Z</li> <li>a -z </li><li>0 – 9</li> <li> . - \_ ! # ^ ~</li></ul> |
| 不允許的字元 | <ul> <li>@</li> <li>'@' 符號前面不可直接包含句點字元 '.'</li></ul> |
| 長度限制 | <ul> <li>總長度不得超過 113 個字元</li><li>'@' 符號前是 64 個字元</li><li>' @' 符號後是 48 個字元</li></ul>

## 僅適用於雲端使用者帳戶的密碼原則

下表描述可套用至在 Azure AD 中建立及管理的使用者帳戶的可用密碼原則設定。

| 屬性 | 需求 |
|   ----------------------- |   ----------------------- |
| 允許的字元 | <ul><li>A – Z</li><li>a -z </li><li>0 – 9</li> <li>@ # $ % ^ & * - \_ ! + = [ ] { } | \\ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 不允許的字元 | <ul><li>Unicode 字元</li><li>空格</li><li>空格</li><li> **僅限強式密碼**：'@' 符號前面不能直接包含點字元 '.'</li></ul> |
| 密碼限制 | <ul><li>8 個字元的最小和最大 16 個字元</li><li>* * 不易破解的密碼 * *： 需要 3 從以下的 4:<ul><li>小寫字元</li><li>大寫字元</li><li>數字 (0-9)</li><li>符號 (請參閱上面的密碼限制)</li></ul></li></ul> |
| 密碼到期時間 | <ul><li>預設值：**90** 天 </li><li>值可透過適用於 Windows PowerShell 的 Azure Active Directory 模組使用 Set-MsolPasswordPolicy Cmdlet 設定。</li></ul> |
| 密碼到期通知 | <ul><li>預設值：**14** 天 (密碼到期前)</li><li>值可使用 Set-msolpasswordpolicy Cmdlet 設定。</li></ul> |
| 密碼到期 | <ul><li>預設值：**false** 天 (表示已啟用該密碼到期) </li><li>可以針對個別使用者帳戶使用 Set-msoluser Cmdlet 設定值。 </li></ul> |
| 密碼歷程記錄 | 無法再次使用上次密碼。 |
| 密碼歷程記錄期間 | 不限次數 |
| 帳戶鎖定 | 10 次嘗試登入失敗 (錯誤密碼) 之後，使用者會被封鎖一分鐘。後續嘗試登入的錯誤會增加使用者被封鎖的時間。 |


## 後續步驟

* [從任何地方管理您的密碼](active-directory-passwords.md)
* [密碼管理如何運作](active-directory-passwords-how-it-works.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入了解](active-directory-passwords-learn-more.md)

<!---HONumber=AcomDC_0114_2016-->