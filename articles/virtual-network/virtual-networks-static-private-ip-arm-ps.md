<properties 
   pageTitle="如何在 ARM 模式中使用 PowerShell 設定靜態私人 IP| Microsoft Azure"
   description="了解靜態 IP (DIP) 以及如何在 ARM 模式下使用 PowerShell 管理它們"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# 如何在 PowerShell 中設定靜態私人 IP 位址

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[管理傳統部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-classic-ps.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下的範例 PowerShell 命令會預期已根據上述案例建立簡單的環境。如果您想要執行如本文件中所顯示的命令，請先建置[建立 vnet](virtual-networks-create-vnet-arm-ps.md) 中所說明的測試環境。

## 建立 VM 時如何指定靜態私人 IP 位址
若要在名為 *TestVNet* 之 VNet 的*前端*子網路中建立名為 *DNS01* 的 VM，且靜態私人 IP 為 *192.168.1.101*，請遵循下列步驟：

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 在 Azure PowerShell 提示字元中執行 **Switch-AzureMode** Cmdlet，以切換至資源管理員模式，如下所示。

		Switch-AzureMode AzureResourceManager
	
	預期的輸出：

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Switch-AzureMode Cmdlet 即將被汰除。屆時將重新命名所有的資源管理員 Cmdlet。
1. 針對儲存體帳戶、位置、 資源群組和要使用的認證設定變數。您必須輸入 VM 的使用者名稱和密碼。儲存體帳戶和資源群組必須已經存在。

		$stName = "vnetstorage"
		$locName = "Central US"
		$rgName = "TestRG"
	    $cred = Get-Credential -Message "Type the name and password of the local administrator account."

3. 擷取虛擬網路以及您想要在其中建立 VM 的子網路。

	    $vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet	
	    $subnet = $vnet.Subnets[0].Id

4. 必要時，請建立公用 IP 位址從網際網路存取 VM。

		$pip = New-AzurePublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

5. 使用您想要指派給 VM 的靜態私人 IP 位址建立 NIC。請確定該 IP 來自您要新增 VM 的子網路範圍。這是本文中將私人 IP 設定為靜態的主要步驟。

		$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101

6. 使用上述建立的 NIC 建立 VM。

		$vm = New-AzureVMConfig -VMName DNS01 -VMSize "Standard_A1"
		$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
		$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
		$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
		$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
		New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm 

	預期的輸出：

		EndTime             : 9/8/2015 2:32:09 PM -07:00
		Error               : 
		Output              : 
		StartTime           : 9/8/2015 2:27:42 PM -07:00
		Status              : Succeeded
		TrackingOperationId : 249e4cc9-1257-42a7-a2fd-6962243c5b6d
		RequestId           : 8dd60f6d-6453-49c4-8f6a-d3c6356dd2a4
		StatusCode          : OK 


## 如何擷取 VM 的靜態私人 IP 位址資訊
若要檢視使用上述指令碼建立之 VM 的靜態私人 IP 位址資訊，請執行下列 PowerShell 命令並觀察 *PrivateIpAddress* 和 *PrivateIpAllocationMethod* 的值：

	Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG

預期的輸出：

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"ee300945-3bb1-4eda-bc90-3a6d65fad60a"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/DNS01"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"ee300945-3bb1-4eda-bc90-3a6d65fad60a"",
	                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Static",
	                           "Subnet": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## 如何移除 VM 的靜態私人 IP 位址
若要移除上述指令碼中新增至 VM 的靜態私人 IP 位址，請執行下列 PowerShell 命令：
	
	$nic=Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
	Set-AzureNetworkInterface -NetworkInterface $nic

預期的輸出：

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"45ccb5a5-934e-485f-b2fd-e4a014131032"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/WindowsVM"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"45ccb5a5-934e-485f-b2fd-e4a014131032"",
	                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Dynamic",
	                           "Subnet": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述指令碼建立之 VM，請執行下列命令：

	$nic=Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
	$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
	Set-AzureNetworkInterface -NetworkInterface $nic

## 後續步驟

- 深入了解[保留的公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解[執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

<!---HONumber=Sept15_HO3-->