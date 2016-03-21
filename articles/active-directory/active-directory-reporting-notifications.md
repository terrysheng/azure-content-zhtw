<properties
	pageTitle="Azure Active Directory 報告通知"
	description="如何使用 Azure Active Directory 報告通知找出可疑登入。"
	services="active-directory"
	documentationCenter=""
	authors="dhanyahk"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="dhanyahk"/>

# Azure Active Directory 報告通知

## 哪些報告會產生電子郵件通知

在此階段中，只有異常的登入活動報告會觸發電子郵件通知。

## 什麼是「異常的登入」？

異常登入是指機器學習服務演算法已經根據結合異常登入位置和裝置的「不可能的行進」條件識別的登入活動。這可能表示駭客已嘗試使用此帳戶進行登入。

## 誰會收到電子郵件通知？

電子郵件會傳送給所有已獲指派 Active Directory Premium 授權的全域管理員。為了確保能夠送達，我們也會將電子郵件傳送到管理員的備用電子郵件地址。管理員應將 aad-alerts-noreply@mail.windowsazure.com 納入其安全寄件者清單中，以免遺漏電子郵件。

## 這些電子郵件的傳送頻率為何？

如果過去的 30 天內或自從最後一次傳送電子郵件後 (視何者日數較少) 發生 10 起新的異常登入活動，就會傳送本電子郵件。

## 如何存取電子郵件中提到的報告？

當您按一下連結時，系統會將您重新導向至 Azure 傳統入口網站中的報告頁面。若要存取報告，您必須同時是：

- 您的 Azure 訂用帳戶的管理員或共同管理員

- 目錄中的全域管理員，並獲得指派的 Active Directory Premium 授權。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

## 我可以關閉這些電子郵件嗎？

是，若要關閉 Azure 傳統入口網站中異常登入的相關通知，請按一下 [設定]，然後選取 [通知] 區段之下的 [已停用]。

## 接下來
- 想知道有哪些安全性、稽核及活動報告可用嗎？ 請查看 [Azure AD 安全性、稽核及活動報告](active-directory-view-access-usage-reports.md)
- [開始使用 Azure Active Directory Premium](active-directory-get-started-premium.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)

<!---HONumber=AcomDC_0309_2016-->