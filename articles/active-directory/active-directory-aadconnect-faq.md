<properties
	pageTitle="Azure AD Connect 常見問題集 |Microsoft Azure"
	description="此頁面包含有關 Azure AD Connect 的常見問題集。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Azure Active Directory Connect 常見問題集

## 快速安裝

## 自訂安裝

## 網路
**問：我的防火牆、網路裝置或其他軟硬體會限制在網路上開啟連線的時間上限。使用 Azure AD Connect 時，用戶端逾時閥值時間應該多長？**

所有網路軟體、實體裝置或其他軟硬體限制連線開啟時間上限的閥值應該至少為 5 分鐘 (300 秒)，以便讓安裝 Azure AD Connect 用戶端的伺服器與 Azure Active Directory 連線。這也適用於所有先前發行的 Microsoft Identity 同步處理工具。


**問：如果我收到一封電子郵件，要求我更新我的 Office 365 憑證，該怎麼辦**

請參考[這篇](active-directory-aadconnect-o365-certs.md)文章中的指引來更新憑證解決問題。

## 疑難排解

**問：如何取得 Azure AD Connect 的說明？**

[搜尋 Microsoft 知識庫 (KB)](https://www.microsoft.com/zh-TW/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- 在 Microsoft 知識庫 (KB) 中搜尋有關 Azure AD Connect 支援的常見協助修正問題的技術解決方案。

[Microsoft Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/zh-TW/home?forum=WindowsAzureAD)

- 按一下[這裡](https://social.msdn.microsoft.com/Forums/azure/zh-TW/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，即可在社群中搜尋和瀏覽技術問題和答案，或提出自己的問題。


[Azure AD Connect 客戶支援](https://manage.windowsazure.com/?getsupport=true)

- 使用此連結透過 Azure 入口網站取得支援。

<!---HONumber=Oct15_HO3-->