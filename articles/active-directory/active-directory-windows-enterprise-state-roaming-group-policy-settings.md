<properties
	pageTitle="群組原則和 MDM 設定 | Microsoft Azure"
	description="提供應該在公司所擁有的裝置上使用的群組原則和行動裝置管理 (MDM) 設定的相關資訊。這些原則會套用至使用者的整個裝置。"
	services="active-directory"
    keywords="什麼是企業狀態漫遊的群組原則和 MDM 設定, 企業狀態漫遊, windows 雲端"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# 群組原則和 MDM 設定

只在公司所擁有的裝置上使用這些群組原則和行動裝置管理 (MDM) 設定，因為這些原則會套用到使用者的整個裝置。套用 MDM 原則來停用個人、使用者所擁有的裝置的設定同步處理，會對使用該裝置造成負面影響。此外，在裝置上的其他使用者帳戶也會被原則影響。

要管理個人 (未受管理) 裝置的漫遊的企業可以使用 Azure 入口網站啟用或停用漫遊，而不是使用群組原則或 MDM下表描述可用的原則設定。

## MDM 設定
MDM 原則設定會套用至 Windows 10 及 Windows 10 行動裝置版。

| 名稱 | 說明 |
|------------------------------------|----------------------------------------------------------------------|
| 允許 Microsoft 帳戶連接 | 允許使用者在裝置上使用 Microsoft 帳戶進行驗證 |
| 允許同步處理我的設定 | 允許使用者漫遊 Windows 設定和應用程式資料 |
 
## 群組原則設定
群組原則設定會套用至加入 Active Directory 網域的 Windows 10 裝置上。資料表包含管理同步處理設定的舊版設定，但不適用於 Windows 10 的企業狀態漫遊。

| 名稱 | 說明 |
|-------------------------------------|-------------|
| 帳戶：封鎖 Microsoft 帳戶 |此原則設定會防止使用者在這部電腦上新增新的 Microsoft 帳戶|
| 不要同步處理 |允許使用者漫遊 Windows 設定和應用程式資料|
| 不要同步處理個人化 |停用主題群組的同步處理|
| 不要同步處理瀏覽器設定 |停用 Internet Explorer 群組的同步處理|
| 不要同步處理密碼 |停用密碼群組的同步處理|
| 不要同步處理其他 Windows 設定 |停用其他 Windows 設定群組的同步處理|
| 不要同步處理桌面個人化 |不要使用；沒有作用|
| 不要同步處理已計量連接 |停用已計量連接的漫遊，例如行動電話 3G|
| 不要同步處理應用程式 |不要使用；沒有作用|
|不要同步處理應用程式設定 |停用應用程式資料的漫遊|
|不要同步處理啟動設定 |不要使用；沒有作用|


## 相關主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [在 Azure Active Directory 中啟用企業狀態漫遊](active-directory-windows-enterprise-state-roaming-enable.md)
- [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Windows 10 漫遊設定參考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->