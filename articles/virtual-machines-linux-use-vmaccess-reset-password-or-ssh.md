<properties 
	pageTitle="如何使用 Linux 虛擬機器 VMAccess" 
	description="如何使用 Linux 的 VMAccess 延伸項目來重設密碼和 SSH 金鑰、重新傳送 SSH 組態，並刪除 Linux 使用者" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# 如何為 Linux 虛擬機器重設密碼或 SSH #

如果您由於忘記密碼、安全殼層 (SSH) 索引鍵不正確或 SSH 設定有問題而無法連線到 Linux 虛擬機器，請使用 VMAccessForLinux 延伸項目來重設密碼或 SSH 金鑰，或修復 SSH 組態。 

## 需求

- Microsoft Azure Linux Agent 2.0.5 版或更新版本。虛擬機器映像庫中的大多數 Linux 映像均包含 2.0.5 版。若要找出所安裝的版本，請執行 `waagent -version`。若要更新代理程式，請依照 [Azure Linux 代理程式使用者指南] 的指示進行。
- Azure PowerShell。您將使用 **Set-AzureVMExtension** Cmdlet 中的命令，自動載入和設定 **VMAccessForLinux** 延伸模組。如需設定 Azure PowerShell 的詳細資訊，請參閱 [如何安裝及設定 Azure PowerShell]。
- 新的密碼或一組 SSH 金鑰 (如果要重設其中一項)。如果您想要重設 SSH 組態，則不需要這些。 

## 不需安裝

不需要先安裝 VMAccess 延伸項目即可使用。只要在虛擬機器安裝 Linux 代理程式，延伸項目便會在您執行使用 **Set-AzureVMExtension** Cmdlet 的 Azure PowerShell 命令時自動載入。 

## 使用該延伸項目來重設密碼、SSH 金鑰或 SSH 組態，或刪除使用者

您將使用 **Set-AzureVMExtension** Cmdlet 進行 VMAccess 讓您進行的任何變更。在所有情況下，都可以開始使用雲端服務名稱和虛擬機器名稱來取得虛擬機器物件，並將它儲存在變數中。 

填入雲端服務和虛擬機器名稱，然後在系統管理員層級 Azure PowerShell 命令提示字元執行下列命令。取代括號中的所有內容，包括 < 和 > 字元。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

如果您不知道雲端服務和虛擬機器名稱，請執行 **Get-AzureVM** 以顯示目前訂閱中所有 VM 的該項資訊。


> [AZURE.NOTE] 以 $ 開頭的命令列將設定之後在 PowerShell 命令中使用的 PowerShell 變數。

如果您已經使用 Azure 管理入口網站建立虛擬機器，請執行下列額外的命令：

	$vm.GetInstance().ProvisionGuestAgent = $true

此命令將避免在下列各節執行 Set AzureVMExtension 命令時發生「式必須先在 VM 物件啟用佈建客體代理程，才能設定 IaaS VM 存取延伸項目」錯誤。 

然後，您可以執行下列工作：

+ [重設密碼](#password)
+ [重設 SSH 金鑰](#SSHkey)
+ [重設密碼和 SSH 金鑰](#both)
+ [重設 SSH 組態](#config)
+ [刪除使用者](#delete)

### <a name="password"></a>重設密碼

填入目前的 Linux 使用者名稱和新的密碼，然後執行這些命令。

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 如果您要重設現有使用者帳戶的密碼或 SSH 金鑰，務必輸入確切的使用者名稱。如果輸入不同的名稱，VMAccess 延伸項目會建立新的使用者帳戶，並將密碼指派給該帳戶。


### <a name="SSHKey"></a>重設 SSH 金鑰

填入目前 Linux 使用者名稱以及包含 SSH 金鑰的憑證路徑，然後執行這些命令。

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>重設密碼和 SSH 金鑰

填入目前 Linux 使用者名稱、新的密碼，以及包含 SSH 金鑰的憑證路徑，然後執行這些命令。

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>重設 SSH 組態

SSH 組態中的錯誤可導致您無法存取虛擬機器。您可以將 SSH 組態重設為其預設狀態來修正此問題。這將移除組態中所有新的存取參數，例如使用者名稱、密碼和 SSH 金鑰，但是這不會變更使用者帳戶的密碼或 SSH 金鑰。延伸項目會重新啟動 SSH 伺服器，並開啟虛擬機器上的 SSH 連接埠，然後將 SSH 組態重設為預設值。 

執行這些命令。

	$PrivateConfig = '{"reset_ssh": "True"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH 組態檔位於 /etc/ssh/sshd_config。

### <a name="delete"></a> 刪除使用者

填入要刪除的 Linux 使用者名稱，然後執行這些命令。

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## 其他資源

[Azure VM 延伸項目與功能][]

[透過 RDP 或 SSH 連接到 Azure 虛擬機器][]


<!--Link references-->
[Azure Linux 代理程式使用者指南]: ../virtual-machines-linux-agent-user-guide
[如何安裝和設定 Azure PowerShell]: ../install-configure-powershell
[Azure VM 延伸項目與功能]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[透過 RDP 或 SSH 連接到 Azure 虛擬機器]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
