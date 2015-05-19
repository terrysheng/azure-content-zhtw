<properties 
	pageTitle="利用 PowerShell 和 Azure Service Manager 建立 Windows 虛擬機器" 
	description="使用 Azure PowerShell 快速建立新的 Windows 虛擬機器。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# 利用 PowerShell 和 Azure Service Manager 建立 Windows 虛擬機器

如果您已安裝 Azure PowerShell，則必須是 Azure PowerShell 0.8.0 或更新的版本。您可以在 Azure PowerShell 命令提示字元下使用這個命令來檢查已安裝的 Azure PowerShell 版本。

	Get-Module azure | format-table version

如果您尚未這樣做，請按照[如何安裝和設定 Azure PowerShell](install-configure-powershell.md) 中的操作方法，在本機電腦安裝 Azure PowerShell。然後，開啟 Azure PowerShell 命令提示字元。

首先，您必須使用此命令登入 Azure。

	Add-AzureAccount

在 \[Microsoft Azure 登入\] 對話方塊中，指定 Azure 帳戶的電子郵件地址和密碼。

接下來，如果您有多個 Azure 訂用帳戶，請設定 Azure 訂用帳戶。如果想查看目前的訂閱帳戶清單，請執行這個命令。

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

現在，將引號中的所有內容 \(包括 < and > 字元\) 換成正確的訂閱名稱，然後執行以下命令。

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

接下來，您需要一個儲存體帳戶您可以利用這個命令顯示目前的儲存體帳戶清單。

	Get-AzureStorageAccount | sort Label | Select Label

如果還沒有儲存體帳戶，請建立新的儲存體帳戶。您選取的名稱不可以和其他名稱重複而且只能使用小寫字母和數字。您可以使用這個命令測試儲存體帳戶名稱是否不重複。

	Test-AzureName -Storage <Proposed storage account name>

如果這個命令傳回「False」，表示您設定的名稱不重複。建立儲存體帳戶時，必須指定 Azure 資料中心的位置。要取得 Azure 資料中心清單，請執行這個命令。

	Get-AzureLocation | sort Name | Select Name

現在，使用以下命令建立並設定儲存體帳戶。填寫儲存體帳戶名稱，然後取代引號裡面的字元 \(包括 < and > 字元\)。

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

接下來，您需要雲端服務。如果您沒有雲端服務，必須建立一個。選取的名稱不可以和其他名稱重複，而且只能使用小寫字母、數字和連字號。欄位中的第一個和最後一個字元，必須是字母或數字。

例如，您可以將它命名為 TestCS-\*UniqueSequence\*，其中的 *UniqueSequence* 是貴公司的縮寫。例如，如果貴公司名稱為 Tailspin Toys，您可以將雲端服務命名為 TestCS-Tailspin。

您可以在本機電腦使用下列 Azure PowerShell 命令，測試名稱是否重複。

	Test-AzureName -Service <Proposed cloud service name>

如果這個命令傳回「False」，表示您設定的名稱不重複。使用以下命令建立雲端服務。

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

接下來，將以下的 PowerShell 命令複製到文字編輯器中，例如記事本。

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

在文字編輯器中，填寫虛擬機器名稱、雲端服務名稱和位置。

最後，將命令集複製到剪貼簿，然後以滑鼠右鍵按一下 \[開啟 Azure PowerShell 命令提示字元\]。這樣發出的命令集就像是一連串的 PowerShell 命令，系統會提示您輸入本機系統管理員帳戶的名稱和密碼，然後建立 Azure 虛擬機器。以下是執行這個命令之後，您會看到的畫面。

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	

## 其他資源

[利用 Azure Resource Manager 和 PowerShell 建立 Windows 虛擬機器](virtual-machines-create-windows-powershell-rm.md)

[利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器](virtual-machines-create-windows-powershell-rm-template-simple.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安裝和設定 Azure PowerShell](install-configure-powershell.md)

[使用 Azure PowerShell 建立和預先設定 Windows 型虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=52-->
