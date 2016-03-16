<properties
   pageTitle="如何整合商務用 OneDrive 和 Azure RemoteApp | Microsoft Azure"
   description="了解如何使用商務用 OneDrive 和 Azure RemoteApp。"
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/13/2016"
   ms.author="elizapo"/>

# 如何整合商務用 OneDrive 和 Azure RemoteApp

您可以使用商務用 OneDrive 做為 Azure RemoteApp 的檔案存放庫。您可以將跨裝置及工作區同步的檔案存放在商務用 OneDrive 中。使用者的[使用者設定檔磁碟](remoteapp-upd.md) (UPD) 可讓使用者儲存 Azure RemoteApp 應用程式的檔案，但這些檔案只能透過 Azure RemoteApp 存取。換句話說，商務用 OneDrive 可讓使用者隨時隨地存取檔案，無需透過 Azure RemoteApp。本文涵蓋支援的商務用 OneDrive 版本，以及管理員可以設定適用於 Azure RemoteApp 的商務用 OneDrive 的各種方法。

## 支援所有 OneDrive 版本嗎？

OneDrive 共有兩種版本：OneDive 和商務用 OneDrive。Azure RemoteApp 僅支援商務用 OneDrive。個人版的 OneDrive 可以運作，但沒有正式支援。此外，Azure RemoteApp (以及 RDSH/Citrix/終端伺服器) 僅支援最新版的商務用 OneDrive (也稱為 Next Gen Sync 用戶端)。

>[AZURE.NOTE]Azure RemoteApp 不支援 OneDrive (客戶/個人版)。由於未經認證能在 Windows Server 上運作，因此並不支援所有商務用 OneDrive 版本。雖然新用戶端 (Next Gen Sync 用戶端) 和舊版的 Groove 看起來可在 Azure RemoteApp 中正常運作，如 [https://support.microsoft.com/en-us/kb/2965687](https://support.microsoft.com/kb/2965687) 中的說明，但舊版的同步引擎在 Citrix/終端伺服器 (Windows Server) 上的功能並不完整。在 Azure RemoteApp (以及其他 Windows Server 部署) 使用新的同步用戶端。

## 商務用 OneDrive 的設定選項有何不同之處？

- **商務用 OneDrive 同步引擎的傳統設定：**商務用 OneDrive 同步用戶端可安裝在伺服器 SKU (遠端桌面和 RemoteApp 工作階段/終端伺服器工作階段) 及 RemoteApp 工作階段中要進行同步的資料夾中，就如同您在 Windows 用戶端 SKU 進行的作業相同。商務用 OneDrive 同步檔案的預設位置與在 Azure RemoteApp 中儲存使用者資料和設定的使用者設定檔磁碟相同，位於 C:\\users<username> 下。無論使用者登入任何 VM，此磁碟及 ODB 都會跟隨。商務用 OneDrive 應用程式必須由管理員發佈給所有使用者，而使用者必須在每個新的工作階段中加以啟動 (否則登入指令碼會自動加以啟動)，以確保同步引擎已啟動。商務用 OneDrive 會在執行工作階段的 VM 上下載完整檔案。同步使用者內容時，對於並未針對有大量使用者短暫登入每台機器的終端機器進行優化的項目而言，同步所有項目 (CPU/已傳輸的資料/已使用的儲存體) 的工作負載十分驚人。透過選擇性的同步功能可降低工作負載，但問題仍然可能發生。
- **「虛擬化」商務用 OneDrive/從複雜型用戶端重新導向到工作階段：**如果您將 OneDrive 同步到用戶端裝置的資料夾中，可以在磁碟機下選擇將該磁碟機[重新導向](remoteapp-redirection.md)到 Azure RemoteApp，該磁碟機應該與所有使用者用戶端的磁碟機相同，且都應該將 OneDrive 同步到該磁碟機下。如果使用者從任何其他用戶端存取 RemoteApp，可能會無法取得這些檔案 (解決方法是可以使用 OneDrive 線上版本存取檔案)。 
- **在不快取/同步檔案的情況下，將商務用 OneDrive 視為 Azure RemoteApp 環境中的磁碟機：** (將商務用 OneDrive 對應到 VM 上的磁碟機) 支援將商務用 OneDrive 對應到RDSH 環境中的網路磁碟機。適用於以下案例： 
	- 當使用精簡型用戶端 (非本機儲存體) 存取 Azure RemoteApp 時，應用程式需要儲存在商務用 OneDrive 中的檔案，但必須讓這些檔案「看起來像」本機檔案，而且系統管理員也無需將檔案同步到 VM。
	- 當商務用 OneDrive 中包含許多已選取要進行同步的大型檔案時。對於同步工作負載，當使用者要使用檔案時，可能不會同步所有檔案。再者，如果檔案總大小超過 50GB，就無法儲存在 UPD 中。

在上述案例中，管理員可以選擇將 VM 上的磁碟機對應到使用者的商務用 OneDrive http URL。以下是啟用此作業的一些選項：

- 影像中包含 Office 二進位檔，且不啟動商務用 OneDrive 同步引擎。
- 影像中不包含 Office 二進位檔，此情況的必要條件為擁有 Desktop Experience Pack 套件。特別是，需要安裝 WebClient 服務 (也稱為 WebDAV) 做為 Desktop Experience Pack 套件的一部分。 

### 在 Windows Server 2012 R2 上安裝 Desktop Experience Pack 套件
要安裝 Desktop Experience Pack 套件：

1. 在 [伺服器管理員] 中，按一下 [管理] > [新增角色及功能]。
2. 按一下 [功能] 及 [使用者介面與基礎結構] -> [桌面體驗]。

### 將磁碟機對應至商務用 OneDrive URL

依照支援文章中的指示進行：
[https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
設定中的重要步驟是確保您選取了 [讓我保持登入]。

以下是高層級的指示：

1.	尋找磁碟機 URL。用來對應磁碟機的 URL 是您瀏覽線上商務用 OneDrive 的主目錄時取得的磁碟機。例如：
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	在 RemoteApp 工作階段中使用瀏覽器開啟 URL，在登入您帳戶的 URL 前先選取 [讓我保持登入]。
3.	開啟 [Windows 檔案總管]，並將磁碟機對應至上述 URL。如果 該 URL 並未解析，可以使用簡短的形式：
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com。

	如此會立即建立對應的磁碟機，如下所示：
 
	![以 商務用 OneDrive 做為對應的網路磁碟機](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0121_2016-->