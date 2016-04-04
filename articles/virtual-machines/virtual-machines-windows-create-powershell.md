<properties
	pageTitle="使用 Powershell 來建立 VM | Microsoft Azure"
	description="使用 PowerShell 與資源管理員部署模型建立並設定 Azure 虛擬機器。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="cynthn"/>

# 利用資源管理員和 Azure PowerShell 建立及設定 Windows 虛擬機器

> [AZURE.SELECTOR]
- [入口網站 - Windows](virtual-machines-windows-hero-tutorial.md)
- [PowerShell](virtual-machines-windows-create-powershell.md)
- [PowerShell - 範本](virtual-machines-windows-ps-template.md)
- [入口網站 - Linux](virtual-machines-linux-portal-create.md)
- [CLI](virtual-machines-linux-cli-create.md)

<br>



[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-create-powershell.md)。

下列步驟將示範如何建構一組 Azure PowerShell 命令，以建立和設定 Azure 虛擬機器。您可以使用此建置組塊程序，對於以 Windows 為基礎的新虛擬機器快速建立命令集，並擴充現有部署。您也可以用它來建立多個命令集以快速建置自訂開發/測試或 IT 專業人員環境。

這些步驟遵循建立 Azure PowerShell 命令集合的填空方法。如果您剛使用 PowerShell 或只想知道可指定哪些值來成功設定組態，這個方法相當實用。如果您是進階 PowerShell 的使用者，您可以使用命令並取代本身的變數值 (以 "$" 開頭的行)。

## 步驟 1：安裝 Azure PowerShell

有兩個主要的安裝選項：[PowerShell 資源庫](https://www.powershellgallery.com/profiles/azure-sdk/)和 [WebPI](http://aka.ms/webpi-azps)。WebPI 每個月都會更新。PowerShell 資源庫將持續更新。

如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/zh-TW/blog/azps-1-0/)。

## 步驟 2：設定您的訂用帳戶

首先，開啟 PowerShell 提示字元。

登入您的帳戶。

	Login-AzureRmAccount

使用下列命令取得您的訂用帳戶名稱。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定您的 Azure 訂用帳戶以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current


## 步驟 3：建立資源

在這一節我們將說明如何為您的新虛擬機器建立每個資源。

### 資源群組


使用資源管理員部署模型建立的 VM 需要資源群組。如果需要，為新虛擬機器建立新資源群組。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmResourceGroup -Name $rgName -Location $locName

您可以使用此命令列出現有的資源群組。

	Get-AzureRmResourceGroup | Sort ResourceGroupName | Select ResourceGroupName


### 儲存體帳戶


以資源管理員部署模型建立的 VM，需要以資源管理員建立的儲存體帳戶。如果需要，請使用這些命令建立新虛擬機器的新儲存體帳戶。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

您必須為儲存體帳戶挑選只包含小寫字母和數字的全域唯一名稱。您可以使用此命令列出現有的儲存體帳戶。

	Get-AzureRmStorageAccount

若要測試選擇的儲存體帳戶名稱是否為全域唯一的，您必須執行 **Test-AzureName** 命令。

	Test-AzureName -Storage <Proposed storage account name>

如果 Test-AzureName 命令顯示 "False"，表示您提出的名稱是唯一的。


### 公用網域名稱標籤


以資源管理員部署模型建立的虛擬機器可以使用公用網域名稱標籤。標籤只能包含字母、數字和連字號。第一個和最後一個字元必須是字母或數字。

若要測試選擇的網域名稱標籤是否為全域唯一的，請使用這些命令。

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $loc

如果 DNSNameAvailability 為 "True"，表是您提出的名稱是全域唯一的。

### 可用性集合


如果需要，請使用這些命令建立新虛擬機器的新可用性集合。

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

使用此命令列出現有的可用性集合。

	Get-AzureRmAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

### NAT 規則

使用輸入 NAT 規則可設定以資源管理員為基礎的虛擬機器，允許來自網際網路的連入流量並放在負載平衡集中。在這兩種情況下，您必須指定負載平衡器執行個體和其他設定。如需詳細資訊，請參閱[使用 Azure 資源管理員建立負載平衡器](../load-balancer/load-balancer-arm-powershell.md)。

以資源管理員部署模型建立的 VM，需要以資源管理員建立的虛擬網路。如果需要，請使用新虛擬機器的至少一個子網路建立以資源管理員為基礎的新虛擬網路。以下是具有 **frontendSubnet** 和 **backendSubnet** 兩個子網路的新虛擬網路 **TestNet** 的範例。

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

使用這些命令列出現有的虛擬網路。

	$rgName="<resource group name>"
	Get-AzureRmVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## 步驟 4：建置命令集

開啟您所選的或 PowerShell 整合式指令碼環境 (ISE) 的文字編輯器之新執行個體，並複製下列行以啟動您的命令集。指定這個新的虛擬機器的資源群組名稱、Azure 位置和儲存體帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

在虛擬網路中，您必須指定以資源管理員為基礎之虛擬網路的名稱和子網路的索引編號。使用這些命令列出虛擬網路的子網路。

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

子網路索引是此命令所顯示的子網路編號，從左至右依序為它們編號，從 0 開始。

關於此範例：

	PS C:\> Get-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

FrontendSubnet 的子網路索引為 0，而 backendSubnet 的子網路索引為 1。

將這幾行複製到您的命令集，並指定現有的虛擬網路名稱和虛擬機器的子網路索引。

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

接下來，建立網路介面卡 (NIC)。將下列其中一個選項複製到命令集，並填入所需的資訊。

### 選項 1：指定 NIC 名稱並指派公用 IP 位址

將下列幾行複製到您的命令集，並指定 NIC 的名稱。

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### 選項 2：指定 NIC 名稱和 DNS 網域名稱標籤

將下列幾行複製到您的命令集，並指定 NIC 的名稱與全域唯一的網域名稱標籤。

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### 選項 3：指定 NIC 名稱並指派靜態、私人 IP 位址

將下列幾行複製到您的命令集，並指定 NIC 的名稱。

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### 選項 4：指定 NIC 名稱和輸入 NAT 規則的負載平衡器執行個體。

若要建立 NIC 並將它指派給輸入 NAT 規則的負載平衡器執行個體，您需要：

- 先前建立的負載平衡器執行個體名稱，其具有轉送至虛擬機器之流量適用的輸入 NAT 規則。
- 要指派給 NIC 的負載平衡器執行個體的後端位址集區的索引編號
- 要指派給 NIC 之輸入 NAT 規則的索引編號。

如需關於如何使用輸入 NAT 規則建立負載平衡器執行個體的詳細資訊，請參閱[使用 Azure 資源管理員建立負載平衡器](../load-balancer/load-balancer-arm-powershell.md)。

將這幾行複製到您的命令集，並指定所需的名稱和索引編號。

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

$NicName 字串必須是資源群組中獨特的字串。最佳作法是將虛擬機器名稱併入字串中，例如 "LOB07-NIC"。

### 選項 5：指定 NIC 名稱和負載平衡集的負載平衡器執行個體。

若要建立 NIC 並將它新增至負載平衡集的負載平衡器執行個體，您需要：

- 先前建立的負載平衡器執行個體名稱，其具有負載平衡流量適用的規則。
- 要指派給 NIC 的負載平衡器執行個體的後端位址集區的索引編號

如需關於如何使用負載平衡流量適用規則建立負載平衡器執行個體的詳細資訊，請參閱[使用 Azure 資源管理員建立負載平衡器](../load-balancer/load-balancer-arm-powershell.md)。

將這幾行複製到您的命令集，並指定所需的名稱和索引編號。

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

接下來，建立本機 VM 物件，並選擇性地將它新增至可用性集合。將下列兩個選項的其中一個複製到命令集，並填入名稱、大小和可用性集合名稱。

選項 1：指定虛擬機器名稱和大小。

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

若要判斷選項 1 之 VM 大小字串的可能值，請使用這些命令。

	$locName="<Azure location of your resource group>"
	Get-AzureRmVMSize -Location $locName | Select Name

選項 2：指定虛擬機器名稱和大小，並將它新增至可用性集合。

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

若要判斷選項 2 之 VM 大小字串的可能值，請使用這些命令。

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureRmVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE] 利用資源管理員，您目前只能在建立虛擬機器期間，將其新增至可用性集合。

若要將其他資料磁碟新增至 VM，請將這幾行複製到您的命令集，並指定磁碟設定。

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

接下來，您必須判斷虛擬機器發行者、優惠以及映像的 SKU。以下是常用的、以 Windows 為基礎的映像資料表。

|發行者名稱 | 優惠名稱 | SKU 名稱
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

如果未列出您需要的虛擬機器映像，請使用[這裡](virtual-machines-linux-cli-ps-findimage.md#powershell)的指示來判斷發行者、優惠以及 SKU 名稱。

將這些命令複製到您的命令集，並填入發行者、優惠和 SKU 名稱。

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

最後，將這些命令複製到您的命令集，並填入 VM 作業系統磁碟的名稱識別項。

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 步驟 5：執行命令集

檢閱在步驟 4 中使用文字編輯器或 PowerShell ISE 建立的 Azure PowerShell 命令集。確定您已指定所有變數，且這些變數具有正確的值。也確定已移除所有 < and > 字元。

如果您將您的命令放入文字編輯器中，將命令複製到剪貼簿，然後以滑鼠右鍵按一下 Azure PowerShell 提示字元。這會將命令集設為一系列的 PowerShell 命令，並建立 Azure 虛擬機器。或者，從 Azure PowerShell ISE 執行命令集。

如果您要重複使用這項資訊來建立其他虛擬機器，可以將這個命令集儲存為 PowerShell 指令碼檔案 (*.ps1)。

## 範例

我需要 PowerShell 命令集以建立 web 式企業營運工作負載的其他虛擬機器：

- 位於現有的 LOBServers 資源群組
- 使用 Windows Server 2012 R2 Datacenter 映像
- 名稱為 LOB07，且位於現有的 WEB\_AS 可用性集合
- 在現有 AZDatacenter 虛擬網路的 FrontEnd 子網路 (子網路索引 0) 中具有內附公用 IP 位址的 NIC
- 有 200 GB 的額外資料磁碟

以下是建立這個虛擬機器的 Azure PowerShell 命令集。

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 其他資源

[Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者](virtual-machines-windows-compare-deployment-models.md)

[Azure 資源管理員概觀](../resource-group-overview.md)

[使用資源管理員範本和 PowerShell 部署以及管理 Azure 虛擬機器](virtual-machines-windows-ps-manage.md)

[利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md)

[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)

<!---HONumber=AcomDC_0323_2016-->