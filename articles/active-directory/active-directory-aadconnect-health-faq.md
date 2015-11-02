<properties
	pageTitle="Azure AD Connect Health 常見問題集"
	description="此常見問題集會回答 Azure AD Connect Health 的相關問題。這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。"
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
	ms.date="10/15/2015"
	ms.author="billmath"/>


# Azure AD Connect Health 常見問題集 (FAQ)

此常見問題集會回答 Azure AD Connect Health 的相關問題。這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。

## 一般問題



**問：我管理多個 Azure AD 目錄。我如何切換到包含 Azure Active Directory Premium 的租用戶？**

您可以切換不同的 Azure AD 目錄，方法為在右上角選取目前登入的使用者名稱，然後選擇適當的帳戶。如果此處未列出帳戶，請選取 [登出]，然後使用已啟用 Azure Active Directory Premium 之目錄的全域系統管理員認證登入。

## 安裝問題



**問：在個別的伺服器上安裝 Azure AD Connect Health 代理程式有什麼影響？**

ADFS 伺服器上安裝 Microsoft Identity Health 代理程式對於 CPU、記憶體耗用量、網路頻寬和儲存體的影響非常少。

以下的數字是近似值。

- CPU 耗用量：增加約 1%
- 記憶體耗用量：最多 10% 的系統總記憶體
- 網路頻寬使用量：每 1000 個 ADFS 要求約 1 MB

>[AZURE.NOTE]如果代理程式無法與 Azure 通訊，則代理程式將在本機儲存資料，最多可達定義的上限。一旦代理程式達到限制，如果代理程式無法將資料上傳至服務，則新的 ADFS 交易將會根據「最近最少服務」覆寫任何「已快取」的交易。

- AD Health 代理程式的本機緩衝區儲存體：約 20 MB
- 稽核通道所需的資料存放區


建議您為 AD FS 稽核通道佈建 1024 MB (1 GB) 的磁碟空間，AD Health 代理程式才能處理所有資料。

**問：在安裝 Azure AD Connect Health 代理程式期間，我是否需要重新啟動我的伺服器？**

否，安裝代理程式不需要您重新啟動伺服器。不過，安裝某些先決條件的步驟可能需要重新啟動伺服器。

例如，在 Windows Server 2008 R2 上安裝 .Net 4.5 Framework 需要重新啟動伺服器。


**問：Azure AD Connect Health 服務是否透過通過 http proxy 運作？**

是。若是進行中的作業，您可以設定 Health 代理程式，以使用 HTTP Proxy 來轉送輸出 http 要求。如需詳細資訊，請參閱[設定 Azure AD Connect Health 代理程式使用 HTTP Proxy](active-directory-aadconnect-health-agent-install-adfs.md#configure-azure-ad-connect-health-agent-to-use-http-proxy)。

如果需要在代理程式註冊期間設定 Proxy，則您需要修改 Internet Explorer Proxy 設定。<br> 開啟 Internet Explorer -> [設定] -> [網際網路選項] -> [連線]-> [LAN 設定]。<br> 選取 [為您的 LAN 使用 Proxy 伺服器]。<br> 如果您有不同的 Proxy 連接埠供 HTTP 和 HTTPS/安全使用，請選取 [進階]。<br>


**問：連線到 Http Proxy 時，Azure AD Connect Health 服務是否支援基本驗證？**

否，目前不支援為基本驗證指定任意使用者名稱/密碼的機制。



## 操作問題



**問：我是否需要在 AD FS 應用程式 Proxy 伺服器或 Web 應用程式 Proxy 伺服器上啟用稽核？**

否，在 AD FS 應用程式 Proxy 伺服器或 Web 應用程式 Proxy 伺服器上都不需要啟用稽核。只需要在 AD FS 同盟伺服器上啟用即可。



**問：Azure AD Connect Health 警示如何獲得解決？**

Azure AD Connect Health 警示會在成功情況下獲得解決。Azure AD Connect Health 代理程式會定期偵測成功情況，並向服務回報。對於少數幾個警示，隱藏是以時間為基礎。也就是說，如果在警示產生的 48 小時內未觀察到相同的錯誤狀況，就會自動解決警示。




**問：我需要開放哪些防火牆連接埠，Azure AD Connect Health 代理程式才能運作？**

您必須開放 TCP/UDP 連接埠 80、443 和 5671，Azure AD Connect Health 代理程式才能夠與 Azure AD Health 服務端點進行通訊。

## 相關連結

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [適用於 AD FS 的 Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install-adfs.md)
* [使用 Azure AD Connect Health 來搭配 AD FS](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)

<!---HONumber=Oct15_HO4-->