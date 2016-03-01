<properties
	pageTitle="Azure AD Connect：開始使用快速設定 | Microsoft Azure"
	description="了解如何下載、安裝和執行 Azure AD Connect 的安裝精靈。"
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
	ms.topic="get-started-article"
	ms.date="02/18/2016"
	ms.author="billmath;andkjell"/>

# 使用快速設定開始使用 Azure AD Connect
下列文件將協助您開始使用 Azure Active Directory Connect。本文件說明使用 Azure AD Connect 的快速安裝。

## 相關文件
如果您尚未閱讀[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md) 上的文件，下表提供相關主題的連結。在開始安裝之前，您需要閱讀以粗體顯示的前兩個主題。

| 主題 | |
| --------- | --------- |
| **下載 Azure AD Connect** | [下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **硬體和必要條件** | [Azure AD Connect：硬體和必要條件](active-directory-aadconnect-prerequisites.md) |
| 使用自訂設定進行安裝 | [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md) |
| 從 DirSync 升級 | [從 Azure AD Sync 工具 (DirSync) 升級](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| 安裝之後 | [驗證安裝和指派授權](active-directory-aadconnect-whats-next.md) |
| 用於安裝的帳戶 | [Azure AD Connect 帳戶與權限的詳細資訊](active-directory-aadconnect-accounts-permissions.md) |


## 快速安裝 Azure AD Connect
選取 [快速設定] 是預設選項，而且是其中一個最常見的案例。這樣做時，Azure AD Connect 會使用密碼同步選項部署同步作業。這僅適用於單一樹系，而且可讓您的使用者使用其內部部署密碼來登入雲端。使用 [快速安裝] 會在完成安裝之後自動開始同步處理 (不過您可以選擇不開啟)。使用此選項，只要簡短地按幾下即可將內部部署目錄擴充至雲端。

![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started-express/welcome.png)

### 使用快速設定安裝 Azure AD Connect

1. 以本機系統管理員身分登入您想要安裝 Azure AD Connect 的伺服器。這應該是您想要做為同步處理伺服器的伺服器。
2. 瀏覽並按兩下 AzureADConnect.msi
3. 在 [歡迎] 畫面上，選取同意授權條款的方塊，然後按一下 [繼續]。
4. 在 [快速設定] 畫面上，按一下 [**使用快速設定**]。![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. 在 [連接到 Azure AD] 畫面上，輸入您的 Azure AD 的 Azure 全域系統管理員使用者名稱和密碼。按 [下一步]。![連接至 AAD](./media/active-directory-aadconnect-get-started-express/connectaad.png)如果您收到錯誤訊息，而且有連線問題，請參閱[對連線問題進行疑難排解](active-directory-aadconnect-troubleshoot-connectivity.md)。
6. 在 [連接到 AD DS] 畫面上輸入企業系統管理員帳戶的使用者名稱和密碼。按 [下一步]。![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. 在 [準備好設定] 畫面中，按一下 [安裝]。
	- 在 [準備好設定] 頁面上，您可以選擇取消核取 [設定一完成，即開始同步處理程序] 核取方塊。如果這麼做，精靈會設定同步處理但會停用該工作而不執行，直到您在 [工作排程器] 將它重新啟用。一旦啟用工作，每隔 30 分鐘就會執行同步處理。
	- 此外，您也可以選擇核取對應 [Exchange 混合式部署] 的核取方塊，以設定同步處理服務。如果您不打算在雲端和內部部署均設置 Exchange 信箱，則不需要此設定。![歡迎使用 Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
8. 當安裝完成時，按一下 [結束]。
9. 安裝完成之後，請先登出再重新登入，才能使用 Synchronization Service Manager 或同步處理規則編輯器。

如需使用快速安裝的影片，請參閱下列內容：

[AZURE.VIDEO azure-active-directory-connect-express-settings]

## 後續步驟
安裝了 Azure AD Connect 之後，您可以[驗證安裝和指派授權](active-directory-aadconnect-whats-next.md)。

深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0224_2016-->