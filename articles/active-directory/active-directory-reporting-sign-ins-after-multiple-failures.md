<properties
	pageTitle="在多次失敗後登入"
	description="指出在多次連續登入嘗試失敗後成功登入的使用者的報告。"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# 在多次失敗後登入
此報告指出在多次連續登入嘗試失敗後成功登入的使用者。可能的原因包括：

- 使用者忘記密碼</li><li>使用者是成功密碼猜測暴力密碼破解攻擊的受害者

這份報告的結果將顯示您在成功登入之前所做的連續失敗登入嘗試次數，以及與第一次成功登入相關聯的時間戳記。

**報告設定**︰您可以設定最小的連續失敗登入嘗試次數，必須達到此數目才會顯示於報告中。當您變更此設定時，請務必注意這些變更將不會套用到目前顯示於現有報表中的任何現有失敗登入。不過，會將它們套用到未來所有的登入。只有經過授權的管理員才可以變更此報告。


![在多次失敗後登入](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=AcomDC_0309_2016-->