<properties
	pageTitle="設定期間使用 Azure AD 設定新裝置 | Microsoft Azure"
	description="此主題說明使用者如何在初次執行體驗期間設定 Azure AD Join。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="femila"/>

# 設定期間使用 Azure AD 設定新裝置

在 Windows 10 中，使用者可以在初次執行體驗 (FRX) 時，把自己的裝置新增到 Azure Active Directory (Azure AD) 中。這讓組織可以把包含拆封授權的裝置發給自己的員工或學生，或是讓他們選擇自己的裝置 (CYOD)。如果裝置安裝了 Windows 10 專業版或 Windows 10 企業版，體驗則預設為公司所擁有裝置的安裝程序。

## 將裝置加入至 Azure AD


1. 當您開啟新裝置，並啟動安裝程序時，您應該會看到 [正在準備] 的訊息。請依照提示來設定您的裝置。
2. 首先，自訂您的地區及語言，然後接受 Microsoft 軟體授權條款。![自訂您的地區](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. 選取您要用來連線到網際網路的網路。
4. 選取您是使用自己的裝置，還是公司擁有的裝置。如果是公司擁有的，請按一下 [此裝置屬於我的組織]。如此會啟動 Azure AD Join 體驗。如果您使用的是 Windows 10 專業版，就會看到以下畫面。
<center>
![[誰是這部電腦的擁有者] 畫面](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.	輸入組織提供給您的認證。
<center>
![登入畫面](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.	當您輸入使用者名稱之後，系統就會在 Azure AD 中找到相符的租用戶。如果您位於同盟網域，系統會將您重新導向至內部部署安全性權杖服務 (STS) 伺服器，例如 Active Directory Federation Services (AD FS)。
7. 如果您是非同盟網域的使用者，請直接在裝載 Azure AD 的頁面上輸入您的認證。如果您的組織之前已設定公司商標，此時您也會看到組織的標誌及支援文字。
8.	系統會提示您進行 Multi-Factor Authentication 查問。IT 系統管理員可以設定這項查問。
9.	Azure AD 會查看該使用者/裝置是否需要在行動裝置管理 (MDM) 中註冊。
10.	接著 Windows 會在 Azure AD 的組織目錄中註冊裝置，並註冊行動裝置管理 (如果適用)。
11.	如果您是受管理的使用者，Windows 會透過自動登入程序帶您前往桌面。
12.	如果您是同盟使用者，系統會將您導向至 Windows 登入畫面來輸入認證。

> [AZURE.NOTE] 我們在 Windows 全新體驗中，不支援加入內部部署 Windows Server Active Directory 網域的功能。因此，如果您打算要讓電腦加入網域，應該要改為選取 [使用本機帳戶設定 Windows] 連結。然後，您就可以從自己電腦上的設定來加入網域，就跟您之前所做的一樣。

## 其他資訊
* [適合企業使用的 Windows 10：使用裝置工作的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [透過 Azure Active Directory Join 擴充 Windows 10 裝置的雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [透過 Microsoft Passport 不需要密碼就能驗證身分識別](active-directory-azureadjoin-passport.md)
* [了解適用於 Azure AD Join 的使用案例](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [將已加入網域裝置連接到 Azure AD 以體驗 Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->