<properties
	pageTitle="Azure Active Directory B2C Preview：服務健全狀況 | Microsoft Azure"
	description="針對次要 Azure Active Directory B2C 問題、狀態和補救措施的通知"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C Preview：服務健全狀況

這個頁面會針對次要服務問題、狀態和補救措施發出通知。您可將這個頁面加入書籤供日後參考。也請持續監視 [Azure 狀態儀表板](https://azure.microsoft.com/status/)。

### 2016 年 3 月 22 日：B2C 租用戶上的 SSO Bug

SSO (單一登入) Bug 於太平洋標準時間 2016 年 3 月 17 日下午 1 點出現，於太平洋標準時間 2016 年 3 月 18 日上午 10 點趨緩。期間受到影響的消費者人數為 <100。我們會持續密切監視這個問題。發生這個 Bug 的消費者狀況為：

1. 以「本機帳戶」設定登入原則，作為設定的唯一身分識別提供者。
2. 消費者第一次成功登入 Azure AD B2C。
3. 消費者嘗試再次登入，但未取得 SSO，而是看到了錯誤訊息。

消費者唯一的因應措施 (步驟 3 之後)，是關閉並重新開啟瀏覽器，再重新驗證。

<!---HONumber=AcomDC_0323_2016-->