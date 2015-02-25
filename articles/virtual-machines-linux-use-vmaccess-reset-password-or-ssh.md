<properties pageTitle="如何針對 Linux 虛擬機器使用 VMAccess" description="如何針對 Linux 使用 VMAccess，以重設密碼、SSH 金鑰與 SSH 設定" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="kathydav"/>

#如何針對 Linux 虛擬機器重設密碼或 SSH#

若因為忘記密碼/SSH 金鑰或 SSH 設定有問題而無法連線到 Linux VM，請使用 VMAccessforLinux 延伸模組來重設密碼、SSH 金鑰或 SSH 設定。 


##需求

- Microsoft Azure Linux 代理程式 2.0.5 版或更新版本。虛擬機器組件庫中的大部分 Linux 映像都包含 2.0.5 版。若要找出所安裝的版本，請執行  `waagent -version`。若要更新代理程式，請依照 [Azure Linux 代理程式使用者手冊]中的指示執行。

- Azure PowerShell 模組。此模組包含 **Set-AzureVMExtension** Cmdlet，您會搭配此 Cmdlet 執行命令，以使用 **VMAccessForLinux** 延伸模組。如需有關設定模組的詳細資訊，請參閱[如何安裝及設定 Azure PowerShell]。

- 新的密碼或 SSH 金鑰，若您要重設其中之一。若要修正 SSH 設定，則不需要那些。 

##不需要安裝

VMAccess 不需安裝便可使用。只要安裝 Linux 代理程式與 Azure 模組，當您執行會呼叫 **Set-AzureVMExtension** Cmdlet 的命令時，就會自動載入該延伸模組。 

##使用該延伸模組來重設密碼、SSH 金鑰或設定，或新增使用者

您可以使用 **Set-AzureVMExtension** Cmdlet 來進行 VMAccess 所允許的變更。在所有案例中，先使用雲端服務名稱與虛擬機器名稱來取得虛擬機器物件並將它儲存在變數中。   

開啟 Azure PowerShell，並在命令提示字元中輸入下列命令。請務必使用實際名稱取代 MyServiceName 與 MyVMName 預留位置：

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

接著，您可以執行下列工作：

+ [重設密碼](#password)
+ [重設 SSH 金鑰](#SSHkey)
+ [重設密碼與 SSH 金鑰](#both)
+ [重設 SSH 設定](#config)

### <a name="password"></a>重設密碼
輸入使用者名稱與密碼並將它們儲存在變數中，接著建立單一變數儲存那些值，以便可以在後續的命令中使用。

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

在變數中儲存名稱、發行者與版本號碼： 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

將所有必要值儲存在變數中之後，執行下列命令：

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 若要為現有的使用者帳戶重設密碼或 SSH 金鑰，請務必輸入正確的使用者名稱。若輸入不同的名稱，VMAccess 延伸模組會建立新的使用者帳戶，並指派密碼給該帳戶。

### <a name="SSHkey"></a>重設 SSH 金鑰

輸入新的公開 SSH 金鑰的使用者名稱與路徑，並將它們儲存在變數中：

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

執行下列命令：

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>重設密碼與 SSH 金鑰

針對目前的使用者，輸入新密碼與新憑證的路徑及 SSH 公開金鑰，然後將它們儲存在變數中： 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

執行下列命令：

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>重設 SSH 設定

SSH 設定錯誤會導致您無法存取 VM。您可以透過將設定重設為其預設值，以修正此問題。這樣會移除設定中的所有新存取參數 (使用者名稱、密碼或 SSH 金鑰)。這樣不會變更使用者帳戶的密碼或 SSH 金鑰。延伸模組會重新啟動 SSH 伺服器、在您的 VM 上開啟 SSH 連接埠，並將 SSH 設定重設為預設值。  

設定指出您要重設設定的旗標，並將它儲存在變數中： 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

執行下列命令：

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH 設定檔位於 /etc/ssh/sshd_config。

## 疑難排解

當您使用 **Set-AzureVMExtension** Cmdlet 時，您可能會遇到此錯誤：「設定 IaaS VM 存取延伸模組之前，必須先在 VM 物件上啟用佈建客體代理程式。」 

若使用「管理入口網站」來建立 Linux VM，就會發生這個情況，因為客體代理程式的值可能不是設定為 "True"。若要修正此問題，請執行下列命令：

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#其他資源
[Azure VM 延伸模組與功能] []

[使用 RDP 或 SSH 連線到 Azure 虛擬機器] []


<!--Link references-->
[Azure Linux 代理程式使用者手冊]: ../virtual-machines-linux-agent-user-guide
[如何安裝及設定 Azure PowerShell]: ../install-configure-powershell
[Azure VM 延伸模組與功能]: http://msdn.microsoft.com/zh-tw/library/azure/dn606311.aspx
[使用 RDP 或 SSH 連線到 Azure 虛擬機器]: http://msdn.microsoft.com/zh-tw/library/azure/dn535788.aspx

<!--HONumber=42-->
