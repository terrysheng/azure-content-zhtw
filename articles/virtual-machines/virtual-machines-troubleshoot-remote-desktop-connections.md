<properties
	pageTitle="無法透過 RDP 連線到 Azure VM | Microsoft Azure"
	description="疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面或 RDP 連線"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# 疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線

遠端桌面 (RDP) 連接到執行 Windows 之 Azure 虛擬機器失敗，可以有各種原因。本文將協助您找出原因並加以更正。

> [AZURE.NOTE]本文僅適用於執行 Windows 之 Azure 虛擬機器。如需疑難排解執行 Linux 之 Azure 虛擬機器的連線，請參閱[這篇文章](virtual-machines-troubleshoot-ssh-connections.md)。

## 連絡 Azure 客戶支援

如果在本文章中有任何需要協助的地方，您可以連絡在 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)上的 Azure 專員。

或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後按一下**取得支援**。如需使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 基本步驟

這些基本步驟可協助解決大部分的遠端桌面連線失敗：

- 從 [Azure 入口網站](https://portal.azure.com)重設遠端桌面服務。依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [重設遠端存取]。

![重設遠端存取](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- [重新啟動虛擬機器](https://msdn.microsoft.com/library/azure/dn763934.aspx)。

- [調整虛擬機器的大小](https://msdn.microsoft.com/library/dn168976.aspx)。


## 在 Windows 上執行 Azure IaaS 診斷封裝

如果您從執行 Windows 8、Windows 8.1、Windows Server 2012 或 Windows Server 2012 R2 的電腦上執行疑難排解，您可以嘗試執行 [Azure IaaS (Windows) 診斷封裝](http://support.microsoft.com/kb/2976864)。此封裝可以解決許多常見的遠端桌面問題。

1.	按一下[支援診斷頁面](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)上的 **Microsoft Azure IaaS (Windows) 診斷封裝**。按一下 [建立] 進行新的診斷工作階段。您可以和不同的目標電腦**共用**此工作階段，或在本機電腦上**下載**它。
2.	**執行**工作階段，**接受** Microsoft 授權合約和**啟動**診斷工具。
3.	在快顯視窗中驗證您的 Azure 訂用帳戶並依照提示進行。
4.	在**您的 Azure VM 遇到下列哪些問題？**頁面上，選取 **RDP 連線至 Azure VM (需要重新開機)** 問題。

如果 Azure IaaS 診斷封裝無法執行或沒有幫助，請繼續進行下一節，根據您從遠端桌面用戶端收到的錯誤來修正問題。


## 常見的 RDP 錯誤

以下是您嘗試連線 Azure 虛擬機器和遠端桌面時可能最常遇到的錯誤：

1. [遠端桌面連線錯誤：遠端工作階段已中斷連線，因為沒有可用的遠端桌面授權伺服器來提供授權](#rdplicense)。

2. [遠端桌面連線錯誤：遠端桌面無法找到電腦「名稱」](#rdpname)。

3. [遠端桌面連線錯誤：發生驗證錯誤。無法連絡本機安全性授權](#rdpauth)。

4. [Windows 安全性錯誤：您的認證無法運作](#wincred)。

5. [遠端桌面連線錯誤：這部電腦無法連線到遠端電腦](#rdpconnect)。

<a id="rdplicense"></a>
### 遠端桌面連線錯誤：遠端工作階段已中斷連線，因為沒有可用的遠端桌面授權伺服器來提供授權。

原因：遠端桌面角色的 120 天授權寬限期已過期，您需要安裝授權。

做為因應措施，請從 Azure 入口網站儲存 RDP 檔案的本機複本，並在 Windows PowerShell 命令提示字元執行該命令來連接。

		mstsc <File name>.RDP /admin

這只會停用該連接的授權。

如果您並非真的需要多於兩個對虛擬機器的同步遠端桌面連線，則您可以使用伺服器管理員來移除遠端桌面伺服器角色。

另請參閱 [Azure VM 失敗並出現「沒有可用的遠端桌面授權伺服器」](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx)部落格文章。

<a id="rdpname"></a>
### 遠端桌面連線錯誤：遠端桌面無法找到電腦「名稱」。

原因：您電腦上的遠端桌面用戶端無法解析此 RDP 檔案設定中的電腦名稱。

可能的解決方案：

- 如果您位於組織內部網路，請確保您的電腦能存取並傳送 HTTPS 流量給 Proxy 伺服器。
- 如果您使用本機儲存的 RDP 檔案，請嘗試使用 Azure 入口網站所產生的檔案。如此可確保您的虛擬機器或雲端服務和虛擬機器的端點連接埠有正確的 DNS 名稱。以下是由 Azure 入口網站產生的 RDP 檔案範例：

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

RDP 檔案中的位址部分有雲端服務的完整網域名稱，包含 VM (在本範例中為 tailspin-azdatatier.cloudapp.net) 以及遠端桌面流量端點的外部 TCP 連接埠 (55919)。

<a id="rdpauth"></a>
### 遠端桌面連線錯誤：發生驗證錯誤。無法連絡本機安全性授權。

原因：目標 VM 在認證的使用者名稱部分找不到安全性授權。

若您的使用者名稱格式為 *SecurityAuthority*\*UserName* (範例：CORP\\User1)，則 *SecurityAuthority* 部分便應輸入虛擬機器的電腦名稱 (作為本機的安全性授權)，或 Active Directory 網域名稱。

可能的解決方案：

- 如果您的使用者帳戶是本機的 VM，請檢查 VM 名稱是否正確拼字。
- 如果該帳戶位於 Active Directory 網域，請檢查網域名稱的拼字。
- 如果該帳戶是 Active Directory 網域帳戶且網域名稱的拼字正確，請確認該網域內可以使用網域控制站。網域控制站電腦並未啟動，在包含網域控制站的 Azure 虛擬網路中是常見的問題。您可以使用本機系統管理員帳戶做為因應措施，而非網域帳戶。

<a id="wincred"></a>
### Windows 安全性錯誤：您的認證無法運作。

原因：目標 VM 無法驗證您的帳戶名稱和密碼。

以 Windows 為基礎的電腦可以驗證本機帳戶或網域帳戶之認證。

- 如果是本機帳戶，請使用 *ComputerName*\*UserName* 語法 (範例：SQL1\\Admin4798)。
- 如果是網域帳戶，請使用 *DomainName*\*UserName* 語法 (範例：CONTOSO\\johndoe)。

如果您在新的 Active Directory 樹系將虛擬機器提升為網域控制器，您用來登入的本機系統管理員帳戶也會轉換為對等的帳戶，在新樹系和網域中使用相同的密碼。本機系統管理員帳戶隨即刪除。例如，如果您以本機系統管理員帳戶 DC1\\DCAdmin 登入，並提升此虛擬機器為 corp.contoso.com 網域中新樹系的網域控制器，則會刪除 DC1\\DCAdmin 本機帳戶，並且以相同密碼建立一個新的網域帳戶 (CORP\\DCAdmin)。

請確保帳戶名稱為虛擬機器可驗證為有效帳戶的名稱，且密碼正確。

如果您需要變更本機系統管理員帳戶的密碼，請參閱[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-password.md)。

<a id="rdpconnect"></a>
### 遠端桌面連線錯誤：這部電腦無法連線到遠端電腦。

原因：用來連接的帳戶並沒有遠端桌面的登入權限。

每部 Windows 電腦都有遠端桌面使用者本機群組，其中包含能夠遠端登入的帳戶和群組。本機系統管理員群組成員也有權限，即使這些帳戶未列在遠端桌面使用者本機群組中。對於加入網域的機器，本機系統管理員群組也包含此網域的網域系統管理員。

請確保您正用於連接的帳戶具有遠端桌面登入權限。請使用網域或本機系統管理員帳戶做為因應措施，來透過遠端桌面連線，並使用電腦管理嵌入式管理單元將想要的帳戶新增到遠端桌面使用者本機群組 (**系統工具 > 本機使用者和群組 > 群組 > 遠端桌面使用者**)。


## 詳細疑難排解

如果沒有發生這些錯誤，而您仍然無法透過遠端桌面連線到 VM，請參閱[這篇文章](virtual-machines-rdp-detailed-troubleshoot.md)找出其他原因。


## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-password.md)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO3-->