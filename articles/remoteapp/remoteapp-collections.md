<properties 
    pageTitle="Azure RemoteApp 需要何種集合？| Microsoft Azure" 
    description="深入了解可搭配 Azure RemoteApp 使用的集合類型。" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/07/2016" 
    ms.author="elizapo" />



# Azure RemoteApp 需要何種集合？

Azure RemoteApp 可讓您在任何裝置上與使用者共用應用程式和資源。做法是建立來保存應用程式和資源的集合，然後與使用者共用那些集合。有兩個不同的集合選項，各有不同的網路和驗證選項 - 哪一種最適合您？

讓我們逐步說明不同的考量和您必須做的選擇，以充分利用 Azure RemoteApp 集合。


## 快速了解不同集合類型間的差異

| | 雲端 | 混合式 |
|-----------|-------|--------|
|使用現有的 VNET| 是| 是|
|需要連線到 AD 的帳戶 (DirSync)| 否| 是|
|需要加入網域| 否| 是|
|需讓網域控制站可以存取 VNET| 否| 是|

## 雲端集合
- 可快速建立 - 集合的佈建速度很快，這表示使用者能更快取得您的應用程式。
- 提供您自己的應用程式或共用我們的應用程式。您可以使用自訂映像 (從 Azure VM 建立) 或您訂用帳戶隨附的其中一個映像。
- 您不需要設定集合與內部部署的網域之間的連線。
- 但您可以選擇使用自己的 Azure VNET 為您的內部部署環境提供存取方式以共用資料，或對資源 (如 SQL Server) 使用非 Windows 驗證 (使用資料庫驗證)。


那麼，要怎麼建立？

- 僅限雲端使用嗎？ 使用入口網站中的 [快速建立] 選項建立。
- 雲端 + VNET？ 使用 [使用 VPN 建立] 選項建立，但「不要」選擇加入網域。

## 混合式集合
- 提供內部部署網路 + Azure VNET 的完整存取權限。
- 包含應用程式和資料的網域加入存取權限。遠端應用程式可以向您內部部署的 Active Directory 驗證 - 接著便能存取您網域中的資源。
- 以現有的 System Center 解決方案和 Windows 群組原則啟用進階監視和管理功能 (透過在 Windows Server 2012 R2 上建置的自訂映像)
- 支援[快速路由](http://azure.microsoft.com/services/expressroute/)，可將您的 Azure VNET 連線到本機 VNET。

使用 [使用 VNET 建立] 選項建立，並「選擇」加入網域。

## 驗證選項
Azure RemoteApp 支援 Microsoft 帳戶和 Azure Active Directory 帳戶，但並非所有集合都支援所有方法。

| 帳戶類型 | | 雲端 | 雲端 + VNET | 混合式 |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|-------|
| Microsoft 帳戶 | | 是 | 是 | 否 |
| Azure Active Directory (Azure AD) | | | | |
| | 僅限 Azure AD 使用 | 是 | 是 | 否 |
| | 具有密碼同步的 AD Connect | 是 | 是 | 是 |
| | 不具密碼同步的 AD Connect | 是 | 是 | 否 |
| | 具 AD FS 的 AD Connect | 是 | 是 | 是 |
| | Azure 支援的第三方識別提供者 (例如 Ping) | 是 | 是 | 是 |
| Multi-Factor Authentication | | 是 | 是 | 是 |



### 雲端和雲端 + VNET 
使用雲端集合時，您可以使用 Microsoft 帳戶、Azure AD 帳戶或混合使用兩者。使用您的使用者最適合的帳戶。

使用 Microsoft 帳戶沒有任何明確要求。

如果您想要使用 Azure AD 帳戶，您必須確定您的 Azure AD 租用戶符合與您的訂用帳戶關聯的租用戶。當您建立 Azure RemoteApp 訂用帳戶時，您使用的 Azure AD 租用戶已自動與該訂用帳戶產生關聯。您授與權限的任一 Azure AD 使用者都必須是該租用戶。如有需要，您可以[變更 Azure AD 租用戶](remoteapp-changetenant.md) (與您的訂用帳戶關聯的 Azure AD 租用戶)。
 
### 混合式 (或雲端 + Azure AD + AD)

使用 Azure AD + 內部部署 Active Directory 是混合式集合的必要條件。您需要使用 AD Connect 來整合兩個目錄。但是您可以自行選擇如何設定 AD Connect。

AD Connect 有兩種案例 - 使用密碼同步化或使用 AD 同盟。請參閱 [AD Connect 資訊](active-directory-aadconnect.md)，了解哪一種案例最適合您。

您也可以使用 Azure AD + AD 搭配雲端集合。請確定您有遵循相同的設定步驟。

如需設定 Azure AD 和 Active Directory 所需的步驟，請參閱 [Azure RemoteApp 的 Azure AD + Active Directory 需求](remoteapp-ad.md)。

## 立即建立您的集合！
嗯，我想我們現在已經說明白了，那麼只剩下一件事要做 - 建立您的第一個 Azure RemoteApp 集合。

[建立雲端集合](remoteapp-create-cloud-deployment.md)或[建立混合式集合](remoteapp-create-hybrid-deployment.md) -立即建立。

<!---HONumber=AcomDC_0114_2016-->