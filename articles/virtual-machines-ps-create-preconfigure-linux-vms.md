<properties 
	pageTitle="使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器" 
	description="了解如何使用 Azure PowerShell 在 Azure 建立和預先設定以 Linux 為基礎的虛擬機器。" 
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
	ms.date="02/17/2015" 
	ms.author="josephd"/>

#使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器

下列步驟將示範如何使用建置組塊自訂一組 Azure PowerShell 命令，建立和預先設定以 Linux 為基礎的 Azure 虛擬機器。您可以使用此程序，對於以 Linux 為基礎的新虛擬機器建立命令集合，並擴充現有部署，或建立快速建置自訂開發/測試或 IT 專業環境的多個命令集。

這些步驟遵循建立 Azure PowerShell 命令集合的填空方法。如果您剛使用 PowerShell 或只想知道可指定哪些值來成功設定組態，這個方法相當實用。進階的 PowerShell 使用者可以使用命令並取代本身的變數值 (以「$」為開頭的行)。

關於設定以 Windows 為基礎的虛擬機器的系列主題，請參閱 [使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines-ps-create-preconfigure-windows-vms/)。

##步驟 1：安裝 Azure PowerShell

如果您尚未這樣做，請使用 [如何安裝和設定 Azure PowerShell 在本機電腦安裝 Azure PowerShell](../install-configure-powershell/) 中的指示。然後，開啟系統管理員層級 Azure PowerShell 命令提示字元。

##步驟 2：設定您的訂閱和儲存體帳戶

在 Azure PowerShell 命令提示字元下執行這些命令，設定您的 Azure 訂閱帳戶和儲存體帳戶。以正確的名稱取代括號中的所有內容，包括 < 和 > 字元。

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

您可以從 **Get-AzureSubscription** 命令輸出的 SubscriptionName 內容取得正確的訂閱帳戶名稱。您發出 **Select-azuresubscription** 命令後，可以從輸出的 **Get AzureStorageAccount**命令輸出的 Label 內容取得正確的儲存體帳戶名稱。您也可以將這些命令儲存在文字檔中，以供日後使用。

##步驟 3：決定 ImageFamily

接著，您必須對於與要建立的 Azure 虛擬機器相對應的特定映像決定 ImageFamily 值。您可以使用這個命令取得可用 ImageFamily 值的清單。

	Get-AzureVMImage | select ImageFamily -Unique

以下是以 Linux 為基礎的電腦本身 ImageFamily 值的一些範例：

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

開啟所選文字編輯器的新執行個體，並將下列內容複製到新的文字檔，以取代 ImageFamily 值。
 
	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

##步驟 4：建置命令集

將下列適當的區塊集合複製到新的文字檔，然後填入變數的值，並移除 < 和 > 字元，以建置命令集的其餘部分。請參閱本文結尾的這兩個 [範例](#examples) 了解最終產生的結果。

選擇兩個命令區塊的其中一個，啟動命令集 (必要)。

選項 1：指定虛擬機器名稱和大小。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

選項 2：指定名稱、大小和可用性設定組名稱。

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

關於 D、DS 或 G 系列虛擬機器的 InstanceSize 值，請參閱 [Azure 的虛擬機器和雲端服務大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。

指定初始的 Linux 使用者名稱和密碼 (必要)。選擇強式密碼。若要檢查其強度，請參閱 [密碼檢查程式：使用強式密碼](https://www.microsoft.com/security/pc-security/password-checker.aspx)。

	$username="<user account name>"
	$pass="<user account password>"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

如果您要將產生的命令集儲存為檔案，請確定您將檔案儲存於安全之處，以保護帳戶名稱和密碼。

也可選擇指定已在訂閱中部署的 SSH 金鑰組。

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

如需詳細資訊，請參閱 [如何對 Azure 上的 Linux 使用 SSH](../virtual-machines-linux-use-ssh-key/)。

也可選擇指定已在訂閱中部署的 SSH 公開金鑰清單。

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

如需以 Linux 為基礎的虛擬機器其他的預先組態選項，請參閱 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) 中的 **Linux** 參數集語法。

也可選擇將特定 IP 位址 (稱為靜態 DIP) 指派給虛擬機器。

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

您可以確認特定 IP 位址可用於：

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

也可選擇將虛擬機器指派給 Azure 虛擬網路中的特定子網路。

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

也可選擇將單一資料磁碟加入至虛擬機器。 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

也可選擇將虛擬機器加入至外部流量的現有負載平衡集。

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

最後，選擇下列其中一個命令區塊，啟動虛擬機器建立程序 (必要)。

選項 1：在新的雲端服務中建立虛擬機器。 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

您可以取得 Azure 位置的清單：

	Get-AzureLocation | Select DisplayName

選項 2：在現有的雲端服務中建立虛擬機器。 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

雲端服務的簡短名稱是 Azure 管理入口網站的雲端服務清單或 Azure Preview 入口網站的資源群組清單中顯示的名稱。 

選項 3：在現有的雲端服務和虛擬網路中建立虛擬機器。

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##步驟 5：執行命令集

檢閱在步驟 4 中使用文字編輯器以多個命令區塊建立的 Azure PowerShell 命令集。確定您已指定所需的所有變數，並且這些變數具有正確的值。也確定已移除所有 < 和 > 字元。

將命令集複製到剪貼簿，然後以滑鼠右鍵按一下 [開啟 Azure PowerShell 命令提示字元]。這將發出命令集作為一系列的 PowerShell 命令，並建立 Azure 虛擬機器。如果您在錯誤的訂閱、儲存體帳戶、雲端服務、可用性設定組、虛擬網路或子網路中建立虛擬機器，請刪除虛擬機器，並更正命令區塊語法，然後執行更正的命令集。 

建立虛擬機器之後，請參閱 [如何登入執行 Linux 的虛擬機器](../virtual-machines-linux-how-to-log-on/)。 

如果您將再次建立這個虛擬機器或類似的虛擬機器，您可以： 

- 將這個命令集儲存為文字檔或 PowerShell 指令碼檔 (*.ps1)
- 在 Azure 管理入口網站的 **自動化** 區段中，將這個命令集儲存為 Azure 自動化 Runbook。 

##<a id="examples"></a>範例

以下是使用上述步驟建置 Azure PowerShell 命令集的兩個範例，這些命令集將在 Azure 中建立以 Linux 為基礎的虛擬機器。

###範例 1

我需要 PowerShell 命令集建立 MySQL 伺服器的初始 Linux 虛擬機器：

- 使用 Ubuntu Server 12.10 映像
- 名稱為 AZMYSQL1 
- 有 500 GB 的額外資料磁碟
- 靜態 IP 位址為 192.168.244.4
- 在 AZDatacenter 虛擬網路的後端子網路中
- 在 Azure TailspinToys 雲端服務中

以下是建立這個虛擬機器的相對應 Azure PowerShell 命令集，各個區塊之間均有空白行，以求編排清晰。

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin397A"
	$pass="3A#q291{Y"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

###範例 2

我需要 PowerShell 命令集建立 Apache 伺服器的 Linux 虛擬機器：

- 使用 SUSE Linux Enterprise Server 12 映像
- 名稱為 LOB1
- 有 50 GB 的額外資料磁碟 
- 是標準 Web 流量的 LOBServers 負載平衡器集合成員
- 在 AZDatacenter 虛擬網路的前端子網路中
- 在 Azure TailspinToys 雲端服務中

以下是建立這個虛擬機器的相對應 Azure PowerShell 命令集。

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin261Z"
	$pass="9Z2:3Wqp1~"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

##其他資源

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure 虛擬機器常見問題集](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure 虛擬機器的概觀](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell/)

[如何登入執行 Linux 的虛擬機器](../virtual-machines-linux-how-to-log-on/)

[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../virtual-machines-ps-create-preconfigure-windows-vms/)


<!--HONumber=45--> 
