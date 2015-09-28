<properties
	pageTitle="遠端桌面的詳細疑難排解 |Microsoft Azure"
	description="RDP 連線至執行 Windows 之 Azure 虛擬機器的詳細疑難排解。"
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

# 以 Windows 為基礎的 Azure 虛擬機器之遠端桌面連線的詳細疑難排解

本文章提供複雜之遠端桌面錯誤的詳細疑難排解步驟。

> [AZURE.IMPORTANT]若要解決較常見的遠端桌面錯誤，請務必閱讀[遠端桌面的基本疑難排解](virtual-machines-troubleshoot-remote-desktop-connections.md)再繼續進行。

## 連絡 Azure 客戶支援

如果在本文章中有任何需要協助的地方，您可以連絡在 [MSDN Azure 和 Stack Overflow 論壇](http://azure.microsoft.com/support/forums/)上的 Azure 專員。

或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後按一下**取得支援**。如需使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 一般遠端桌面錯誤訊息

有時候您可能會在 [遠端桌面連線訊息] 視窗中看到此錯誤訊息：「遠端桌面因其中一項原因而無法連線到遠端電腦」

當遠端桌面用戶端無法在虛擬機器上與遠端桌面連線時，會發生此錯誤。有多種原因會造成此錯誤。

下列為一組相關的元件。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

在深入了解逐步的疑難排解程序之前，先在心中檢閱您在可以成功建立遠端桌面連線之後曾進行過的變更，再使用這些變更當成修正問題的基礎會相當有幫助。例如：

- 如果您可以建立遠端桌面連線，且變更了虛擬機器或包含虛擬機器之雲端服務的公用 IP 位址 (又稱為虛擬 IP 位址 [VIP])，則您的 DNS 用戶端快取可能會有 DNS 名稱和「舊 IP 位址」的項目。請清除 DNS 用戶端快取並重試一次。或者，請嘗試使用新的 VIP 建立連線。
- 如果您從使用 Azure 入口網站或 Azure Preview 入口網站變更為使用應用程式來管理遠端桌面連線，則請確保應用程式組態包含用於遠端桌面流量之隨機決定的 TCP 連接埠。

下節將逐步地隔離和判定此問題的各種根本原因，並提供解決方案和因應措施。


## 預備步驟

進行詳細疑難排解之前請先執行這些步驟。

- 檢查 Azure 入口網站或 Azure Preview 入口網站中虛擬機器的狀態。
- [重新啟動虛擬機器](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [調整虛擬機器的大小](https://msdn.microsoft.com/library/dn168976.aspx)

在這些步驟之後，請再試一次遠端桌面連線。


## 詳細疑難排解

下列來源的問題或錯誤設定，可能會造成遠端桌面用戶端無法連線到 Azure 虛擬機器上的遠端桌面服務：

- 遠端桌面用戶端電腦
- 組織內部網路的邊緣裝置
- 雲端服務端點和存取控制清單 (ACL)
- 網路安全性群組
- 以 Windows 為基礎的 Azure 虛擬機器

### 來源 1：遠端桌面用戶端電腦

若要排除您的電腦為問題或錯誤設定來源之可能性，請確認您的電腦可以建立遠端桌面連線到另一部內部部署且以 Windows 為基礎的電腦。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

如果不能，請在您的電腦上檢查下列項目：

- 正封鎖遠端桌面流量的本機防火牆設定。
- 正阻止遠端桌面連線的本機安裝用戶端 Proxy 軟體。
- 正阻止遠端桌面連線的本機安裝網路監視軟體。
- 正阻止遠端桌面連線的其他類型安全性軟體，這會監視流量或允許/不允許特定類型的流量。

在所有這些情況下，請嘗試暫時停用此軟體，然後再嘗試遠端桌面連線到內部部署的電腦，以找出根本原因。然後請和網路管理員合作，修正軟體設定以允許遠端桌面連線。

### 來源 2：組織內部網路的邊緣裝置

若要排除您組織內部網路的邊緣裝置為問題或錯誤設定來源之可能性，請確認直接連接到網際網路的電腦能遠端連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

如果您沒有直接連線到網際網路的電腦，則您只要將新的 Azure 虛擬機器建立於專屬的資源群組或雲端服務中並加以使用即可。如需詳細資訊，請參閱[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-tutorial.md)。當您完成測試後，請刪除此資源群組或虛擬機器及雲端服務。

如果您可以對直接連線到網際網路的電腦建立遠端桌面連線，請檢查組織內部網路的邊緣裝置之下列項目：

- 正封鎖網際網路 HTTPS 連接的內部防火牆。
- 正阻止遠端桌面連線的 Proxy 伺服器。
- 正阻止遠端桌面連線且在邊緣網路裝置上執行的入侵偵測或網路監視軟體。

請和網路管理員合作，修正您組織內部網路的邊緣裝置設定，允許以 HTTPS 為基礎的網際網路遠端桌面連線。

### 來源 3：雲端服務端點和 ACL

若要為使用服務管理 API 建立的虛擬機器排除雲端服務端點和 ACL 為問題或錯誤設定來源之可能性，請確認另一部位於相同雲端服務或虛擬網路的 Azure 虛擬機器能使用遠端桌面連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]如果是在資源管理員中建立的虛擬機器，請跳到[來源 4：網路安全性群組](#nsgs)。

如果在同一個雲端服務或虛擬網路中沒有另一部虛擬機器，可以建立一部新的。如需詳細資訊，請參閱[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-tutorial.md)。當您完成測試後，請刪除額外的虛擬機器。

如果您可以建立遠端桌面連線到相同雲端服務或虛擬網路中的虛擬機器，則請檢查下列項目：

- 目標虛擬機器上的遠端桌面流量端點組態。此端點的私用 TCP 連接埠必須符合虛擬機器上遠端桌面服務正在接聽的 TCP 連接埠，根據預設為 3389。
- 目標虛擬機器上的遠端桌面流量端點 ACL。ACL 讓您可指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入遠端桌面流量。檢查您的 ACL，以確保允許來自您的 Proxy 或其他邊緣伺服器的公用 IP 位址之連入流量。如需詳細資訊，請參閱[什麼是網路存取控制清單 (ACL)？](../virtual-network/virtual-networks-acl.md)。

若要排除端點為問題或錯誤設定來源之可能性，請移除目前的端點，再選擇外部連接埠號碼介於 49152 到 65535 的隨機連接埠來建立新的端點。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。

### <a id="nsgs"></a>來源 4：網路安全性群組

網路安全性群組能夠更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許來自網際網路的遠端桌面流量。

如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

### 來源 5：以 Windows 為基礎的 Azure 虛擬機器

最後一組可能的問題就是 Azure 虛擬機器本身。

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

[遠端桌面的基本疑難排解文章](virtual-machines-troubleshoot-remote-desktop-connections.md)描述如何使用 [Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)。如果這個診斷套件無法解決「RDP 連線至 Azure VM (需重新開機)」問題，請依照[此文章](virtual-machines-windows-reset-password.md)中的指示來重設虛擬機器上的「遠端桌面服務」服務。這將會：

- 啟用「遠端桌面」 Windows 防火牆預設規則 (TCP 連接埠 3389)。
- 藉由將 HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections 登錄值設為 0 ，啟用遠端桌面連線。

再次嘗試來自您電腦的連線。如果無法成功，下列為某些可能的原因：

- 遠端桌面服務未在目標虛擬機器上執行。
- 遠端桌面服務未在 TCP 連接埠 3389 上接聽。
- Windows 防火牆或另一個本機防火牆有阻止遠端桌面流量的輸出規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止遠端桌面連線。

若要為使用服務管理 API 建立的虛擬機器修正這些可能的問題，您可以使用遠端 Azure PowerShell 工作階段連線到 Azure 虛擬機器。首先，您會需要安裝虛擬機器之代管雲端服務的憑證。移至[設定安全遠端 PowerShell 對 Azure 虛擬機器的存取權](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)，以及下載 **InstallWinRMCertAzureVM.ps1** 指令碼檔案到您本機電腦的資料夾。

接下來，如果尚未安裝 Azure PowerShell，則請先安裝。請參閱[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

接下來，開啟 Azure PowerShell 命令提示字元，並將目前資料夾變更為 **InstallWinRMCertAzureVM.ps1** 指令碼檔案的位置。若要執行 Azure PowerShell 指令碼，您必須設定正確的執行原則。請執行 **Get-ExecutionPolicy** 命令來判斷您目前的原則層級。如需設定適當層級的相關資訊，請參閱 [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)。

接下來，請填入您的 Azure 訂用帳戶名稱、雲端服務名稱以及虛擬機器名稱 (移除 < and > 字元)，然後再執行這些命令。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

您可以從 **Get-AzureSubscription** 命令顯示的 _SubscriptionName_ 屬性，取得正確的訂用帳戶名稱。您可以從 **Get-AzureVM** 命令顯示畫面中的 _ServiceName_ 欄位，取得虛擬機器的雲端服務名稱。

如欲證明您有新的憑證，請開啟目前使用者的憑證嵌入式管理單元，然後查看 **Trusted Root Certification Authorities\\Certificates** 資料夾。您應該在 Issued To 資料行中查看具有您的雲端服務之 DNS 名稱的憑證 (範例：cloudservice4testing.cloudapp.net)。

接下來，使用這些命令起始遠端 Azure PowerShell 工作階段。

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

輸入有效的系統管理員認證後，您應該會看到像是 Azure PowerShell 提示字元的結果：

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

提示字元的第一部分表示您現在發出 Azure PowerShell 命令給包含目標虛擬機器的雲端服務。您的雲端服務名稱會與 "cloudservice4testing.cloudapp.net" 不同。

您現在可以發出 Azure PowerShell 命令，調查其他前面所提到的問題，並修正組態。

### 若要手動更正接聽 TCP 連接埠的遠端桌面服務

如果透過遠端 Azure PowerShell 工作階段提示字元，無法針對「RDP 連線至 Azure VM (需重新開機)」問題執行 [Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，請執行這個命令。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber 屬性會顯示目前的連接埠號碼。如有需要，請使用此命令，將遠端桌面連接埠號碼變更回預設值 (3389)。

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

確認已使用此命令將連接埠變更為 3389。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

使用此命令結束遠端 Azure PowerShell 工作階段。

	Exit-PSSession

確認 Azure 虛擬機器的遠端桌面端點也正在使用 TCP 連接埠 3398 做為其內部連接埠。然後，重新啟動 Azure 虛擬機器，再試一次遠端桌面連線。


## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-password.md)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO3-->