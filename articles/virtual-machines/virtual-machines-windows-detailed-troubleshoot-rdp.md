<properties
	pageTitle="遠端桌面的詳細疑難排解 |Microsoft Azure"
	description="RDP 連線至執行 Windows 之 Azure 虛擬機器的詳細疑難排解。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="dkshir"/>

# 以 Windows 為基礎的 Azure 虛擬機器之遠端桌面連線的詳細疑難排解

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

本文章提供診斷和修正複雜的以 Windows 為基礎的 Azure 虛擬機器的遠端桌面錯誤的詳細疑難排解步驟。

> [AZURE.IMPORTANT] 若要解決較常見的遠端桌面錯誤，請務必閱讀[遠端桌面的基本疑難排解文章](virtual-machines-windows-troubleshoot-rdp-connection.md)再繼續進行。

如果您收到遠端桌面錯誤訊息，不像[基本遠端桌面疑難排解指南](virtual-machines-windows-troubleshoot-rdp-connection.md)中所涵蓋的任何特定錯誤訊息，您可以依照下列步驟，嘗試找出遠端桌面 (或 [RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)) 用戶端無法連線至 Azure VM 的 RDP 服務的原因。

如果在本文章中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下**取得支援**。如需關於使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。


## 遠端桌面連線的元件

以下是 RDP 連線相關的元件：

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

在繼續之前，在心裡檢閱最後一個成功的遠端桌面連線至 VM 以來的變更可能有幫助。例如：

- 如果 VM 的 IP 位址或包含 VM 的雲端服務 (也稱為虛擬 IP 位址 [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) 已變更，則 RDP 失敗可能是因為 DNS 用戶端快取仍然具有為 DNS 名稱註冊的*舊 IP 位址*。請清除 DNS 用戶端快取並嘗試再次連接 VM。或嘗試直接與新的 VIP 連接。
- 如果您使用協力廠商應用程式來管理遠端桌面連線，而不是使用 Azure 入口網站，請確認應用程式組態包含正確的遠端桌面流量的 TCP 連接埠。您可以在 [Azure 入口網站](https://portal.azure.com)查看某個傳統虛擬機器的這個連接埠，方法是按一下該 VM 的 [設定] > [端點]。


## 預備步驟

繼續詳細疑難排解之前，

- 檢查 Azure 傳統入口網站或 Azure 入口網站中虛擬機器的狀態是否有任何明顯的問題
- 請遵循[基本疑難排解指南中常見的 RDP 錯誤的快速檢修步驟](virtual-machines-windows-troubleshoot-rdp-connection.md#quickfixrdp)


嘗試在完成這些步驟之後透過遠端桌面重新連接至 VM。


## 詳細疑難排解

由於下列來源的問題，可能會造成遠端桌面用戶端無法連線到 Azure VM 上的遠端桌面服務：

- 遠端桌面用戶端電腦
- 組織內部網路的邊緣裝置
- 雲端服務端點和存取控制清單 (ACL)
- 網路安全性群組
- 以 Windows 為基礎的 Azure 虛擬機器

### 來源 1：遠端桌面用戶端電腦

請確認您的電腦可以建立遠端桌面連線到另一部內部部署且以 Windows 為基礎的電腦。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

如果不能，請在您的電腦上檢查下列項目：

- 正封鎖遠端桌面流量的本機防火牆設定。
- 正阻止遠端桌面連線的本機安裝用戶端 Proxy 軟體。
- 正阻止遠端桌面連線的本機安裝網路監視軟體。
- 正阻止遠端桌面連線的其他類型安全性軟體，這會監視流量或允許/不允許特定類型的流量。

在所有這些情況下，請暫時停用此軟體，然後嘗試透過遠端連線連接到內部部署電腦。如果這樣即可找出造成這個問題的實際原因，請和網路管理員合作，修正軟體設定以允許遠端桌面連線。

### 來源 2：組織內部網路的邊緣裝置

請確認直接連接到網際網路的電腦能遠端連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

如果您沒有直接連線到網際網路的電腦，則在資源群組或雲端服務中建立和測試新的 Azure 虛擬機器。如需詳細資訊，請參閱[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-hero-tutorial.md)。測試完成之後，您可以刪除虛擬機器和資源群組或雲端服務。

如果您可以對直接連線到網際網路的電腦建立遠端桌面連線，請檢查組織內部網路的邊緣裝置之下列項目：

- 正封鎖網際網路 HTTPS 連接的內部防火牆
- 正阻止遠端桌面連線的 Proxy 伺服器
- 正阻止遠端桌面連線且在邊緣網路裝置上執行的入侵偵測或網路監視軟體。

請和網路管理員合作，修正您組織內部網路的邊緣裝置設定，允許以 HTTPS 為基礎的網際網路遠端桌面連線。

### 來源 3：雲端服務端點和 ACL

對於使用傳統部署模型建立的虛擬機器，請確認另一部位於相同雲端服務或虛擬網路的 Azure 虛擬機器能使用遠端桌面連線到您的 Azure 虛擬機器。

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] 如果是在資源管理員中建立的虛擬機器，請跳到[來源 4：網路安全性群組](#nsgs)。

如果在同一個雲端服務或虛擬網路中沒有另一部虛擬機器，可以建立一部新的，方法是使用[在 Azure 中建立執行 Windows 的虛擬機器](virtual-machines-windows-hero-tutorial.md)中的步驟。測試完成之後，請刪除額外的虛擬機器。

如果您可以透過遠端桌面連線到相同雲端服務或虛擬網路中的虛擬機器，則請檢查下列項目：

- 目標 VM 上的遠端桌面流量端點組態：端點的私用 TCP 連接埠必須符合 VM 的遠端桌面服務所接聽的 TCP 連接埠 (預設值為 3389)。
- 目標 VM 上的遠端桌面流量端點的 ACL：ACL 讓您可指定要根據來源 IP 位址允許或拒絕來自網際網路的連入流量。設定錯誤的 ACL 會阻止送至端點的連入遠端桌面流量。檢查您的 ACL，以確保允許來自您的 Proxy 或其他邊緣伺服器的公用 IP 位址之連入流量。如需詳細資訊，請參閱[什麼是網路存取控制清單 (ACL)？](../virtual-network/virtual-networks-acl.md)。

若要檢查端點是否為問題來源，請移除目前的端點，再選擇外部連接埠號碼介於 49152 到 65535 的隨機連接埠來建立新的端點。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-windows-classic-setup-endpoints.md)。

### <a id="nsgs"></a>來源 4：網路安全性群組

網路安全性群組能夠更精確地控制受允許的輸入和輸出流量。您可以在 Azure 虛擬網路中建立跨越子網路和雲端服務的規則。請檢查您的網路安全性群組規則，以確保允許來自網際網路的遠端桌面流量。

如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

### 來源 5：以 Windows 為基礎的 Azure 虛擬機器

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

使用 [Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)以查看失敗是否是因為 Azure 虛擬機器本身。如果這個診斷套件無法解決「RDP 連線至 Azure VM (需重新開機)」問題，請遵循[本文](virtual-machines-windows-reset-rdp.md)中的指示來重設虛擬機器上的「遠端桌面服務」。這將會：

- 啟用「遠端桌面」 Windows 防火牆預設規則 (TCP 連接埠 3389)。
- 藉由將 HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections 登錄值設為 0 ，啟用遠端桌面連線。

再次嘗試來自您電腦的連線。如果您還是無法透過遠端桌面連線，請檢查下列可能的問題：

- 遠端桌面服務未在目標 VM 上執行。
- 遠端桌面服務未在 TCP 連接埠 3389 上接聽。
- Windows 防火牆或另一個本機防火牆有阻止遠端桌面流量的輸出規則。
- 在 Azure 虛擬機器上執行的入侵偵測或網路監視軟體正在阻止遠端桌面連線。

對於使用傳統部署模型所建立的 VM，您可以使用關於 Azure 虛擬機器的遠端 Azure PowerShell 工作階段。首先，您會需要安裝虛擬機器之代管雲端服務的憑證。移至[設定安全遠端 PowerShell 對 Azure 虛擬機器的存取權](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)，以及下載 **InstallWinRMCertAzureVM.ps1** 指令碼檔案到您的本機電腦。

接下來，如果尚未安裝 Azure PowerShell，則請先安裝。請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

接下來，開啟 Azure PowerShell 命令提示字元，並將目前資料夾變更為 **InstallWinRMCertAzureVM.ps1** 指令碼檔案的位置。若要執行 Azure PowerShell 指令碼，您必須設定正確的執行原則。請執行 **Get-ExecutionPolicy** 命令來判斷您目前的原則層級。如需設定適當層級的相關資訊，請參閱 [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)。

接下來，請填入您的 Azure 訂用帳戶名稱、雲端服務名稱以及虛擬機器名稱 (移除 < and > 字元)，然後再執行這些命令。

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

您可以從 **Get-AzureSubscription** 命令顯示畫面中的 _SubscriptionName_ 屬性，取得正確的訂用帳戶名稱。您可以從 **Get-AzureVM** 命令顯示畫面中的 _ServiceName_ 欄位，取得虛擬機器的雲端服務名稱。

請檢查您是否有新的憑證，開啟目前使用者的憑證嵌入式管理單元，然後查看 **Trusted Root Certification Authorities\\Certificates** 資料夾。您應該在 Issued To 資料行中查看具有您的雲端服務之 DNS 名稱的憑證 (範例：cloudservice4testing.cloudapp.net)。

接下來，使用這些命令起始遠端 Azure PowerShell 工作階段。

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

輸入有效的系統管理員認證後，您應該會看到像是 Azure PowerShell 提示字元的結果：

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

此提示的第一個部分是您的雲端服務名稱，其中包含目標 VM，這可以與 "cloudservice4testing.cloudapp.net" 不同。您現在可以發出可用於此雲端服務的 Azure PowerShell 命令，來調查前面所提到的其他問題並修正組態。

### 若要手動更正接聽 TCP 連接埠的遠端桌面服務

如果透過遠端 Azure PowerShell 工作階段提示字元，無法針對「RDP 連線至 Azure VM (需重新開機)」問題執行 [Azure IaaS (Windows) 診斷套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)，請執行這個命令。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber 屬性會顯示目前的連接埠號碼。如有需要，請使用此命令，將遠端桌面連接埠號碼變更回預設值 (3389)。

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

確認已使用此命令將連接埠變更為 3389。

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

使用此命令結束遠端 Azure PowerShell 工作階段。

	Exit-PSSession

確認 Azure VM 的遠端桌面端點也正在使用 TCP 連接埠 3398 做為其內部連接埠。重新啟動 Azure VM，然後再試一次遠端桌面連線。


## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設 Windows 虛擬機器的密碼或遠端桌面服務](virtual-machines-windows-reset-rdp.md)

[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)

[疑難排解存取在 Azure 虛擬機器上執行的應用程式](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0323_2016-->