<properties
	pageTitle="從多個地理區域登入"
	description="這份報告指出使用者有兩次登入似乎來自不同的地區，且使用者不可能在登入間的時間內在這兩個區域之間移動。"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# 從多個地理區域登入

這份報告包含使用者的成功登入，而其中有兩次登入似乎來自不同的區域，且使用者不可能在登入間的時間內在這兩個區域之間移動。可能的原因包括：

- 使用者與其他使用者共用其密碼

- 使用者正在使用遠端桌面啟動 Web 瀏覽器來登入

- 駭客已從不同的國家/地區登入使用者帳戶

- 使用者使用 VPN 或 Proxy

- 使用者同時從多個裝置登入 (例如桌上型電腦和行動電話)，而行動電話的 IP 位址異常。

這份報表的結果會顯示成功登入事件，以及登入間的時間、登入疑似源自哪些區域，以及這些區域間估計的移動時間。顯示的移動時間只是估計值，而且可能不同於位置之間的實際移動時間。


![從多個地理區域登入](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=AcomDC_0309_2016-->