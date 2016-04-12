<properties 
	pageTitle="模擬混合式雲端測試環境 | Microsoft Azure" 
	description="使用兩個 Azure 虛擬網路和 VNet 對 VNet 連接，建立 IT 專業或開發測試的模擬混合式雲端環境。" 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="josephd"/>

# 設定用於測試的模擬混合式雲端環境

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](../ virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)。

本文會引導您使用兩個不同的 Azure 虛擬網路逐步建立 Microsoft Azure 的模擬混合式雲端環境進行測試。當您沒有直接的網際網路連線和可用的公用 IP 位址時，使用此組態做為[設定用於測試的混合式雲端環境](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)的替代方案。以下是產生的組態。

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

這會模擬混合式雲端生產環境。其中包括：

- Azure 虛擬網路 (TestLab 虛擬網路) 中代管的模擬和簡化內部部署網路。
- Azure (TestVNET) 代管的模擬跨單位部署虛擬網路。
- 兩個虛擬網路之間的 VNet 對 VNet 連線。
- TestVNET 虛擬網路中的第二個網域控制站。

這一般可供您開始：

- 在模擬混合式雲端環境中開發和測試應用程式。
- 建立電腦的測試組態，一些在 TestLab 虛擬網路內，一些在 TestVNET 虛擬網路內，模擬以雲端為基礎的混合式 IT 工作負載。

設定此混合式雲端測試環境分為四個主要階段：

1.	設定 TestLab 虛擬網路。
2.	建立跨單位虛擬網路。
3.	建立 VNet 對 VNet VPN 連線。
4.	設定 DC2。 

如果您仍沒有 Azure 訂用帳戶，可以在[試用 Azure](https://azure.microsoft.com/pricing/free-trial/) 上註冊免費試用版。如果您有 MSDN 訂閱，請參閱 [MSDN 訂閱者的 Azure 權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

>[AZURE.NOTE] Azure 中的虛擬機器和虛擬網路閘道會在執行時持續耗用成本。這項成本是按照您的免費試用版、MSDN 訂閱或付費訂閱進行計算。Azure VPN 閘道會實作為一組兩個的 Azure 虛擬機器。為了減少成本，請建立測試環境，並盡快執行所需的測試和示範。

## 第 1 階段：設定 TestLab 虛擬網路

使用[基底組態測試環境](virtual-machines-windows-test-config-env.md)中的指示，在名稱為 TestLab 的 Azure 虛擬網路中設定 DC1、APP1 和 CLIENT1 電腦。

接下來，開啟 Azure PowerShell 提示字元。

> [AZURE.NOTE] 下列命令集使用 Azure PowerShell 1.0 版和更新版本。如需詳細資訊，請參閱 Azure PowerShell 1.0。

登入您的帳戶。

	Login-AzureRMAccount

使用下列命令取得您的訂用帳戶名稱。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定您的 Azure 訂用帳戶使用您用來建置基本組態的同一個訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

接下來，將閘道子網路加入基本組態的 TestLab 虛擬網路，其將用來裝載 Azure 閘道。

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

接下來，要求一個公用 IP 位址以指派給 TestLab 虛擬網路的閘道器。

	$gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

接下來，建立您的閘道器。

	$vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
	New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

請留意，完成新的閘道器可能需要 20 分鐘或更久。

從本機電腦上的 Azure 入口網站中，使用 CORP\\User1 認證連線到 DC1。若要設定 CORP 網域，讓電腦和使用者使用其本機網域控制站進行驗證，請從系統管理員層級 Windows PowerShell 命令提示字元執行這些命令。

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

這是您目前的組態。

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## 第 2 階段：建立 TestVNET 虛擬網路

首先，建立 TestVNET 虛擬網路並使用網路安全性群組加以保護。

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
	$testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
	$gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

接下來，要求配置給 TestVNET 虛擬網路閘道器的公用 IP 位址，並建立您的閘道器。

	$gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
	New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

這是您目前的組態。

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##第 3 階段：建立 VNet 對 VNet 連線

首先，從您的網路或安全性管理員取得隨機且以強式密碼編譯之 32 個字元的預先共用金鑰。或者，使用 [Create a random string for an IPsec preshared key (建立 IPsec 預先共用金鑰的隨機字串)](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) 中的資訊取得預先共用金鑰。

接下來，使用這些命令來建立站對站 VPN 連線，這可能需要一些時間來完成。

	$sharedKey="<pre-shared key value>"
	$gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
	$gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
	New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
	New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

請稍候幾分鐘，應該就會建立連線。請注意，此時 Azure 入口網站還不會顯示透過 Azure 資源管理員建立的閘道器和連線。

這是您目前的組態。

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## 第 4 階段：設定 DC2

首先，建立 DC2 的 Azure 虛擬機器。在本機電腦的 Azure PowerShell 命令提示字元下執行下列命令：

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name for the base configuration>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接著，從 Azure 入口網站登入新的 DC2 虛擬機器。

接著，設定 Windows 防火牆規則，允許基本連線測試的流量。從 DC2 的系統管理員層級 Windows PowerShell 命令提示字元下，執行這些命令。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 命令應該會收到來自 IP 位址 10.0.0.4 的 4 次成功回覆。這是 Vnet 對 Vnet 連線的流量測試。

接著，將額外的資料磁碟新增為磁碟機代號 F: 的新磁碟區。

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2.	在 [內容] 窗格的 [磁碟] 群組中，按一下 [磁碟 2] \([磁碟分割] 設為 [不明])。
3.	按一下 [工作]，然後按一下 [新增磁碟區]。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5.	在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]，然後按 [下一步]。出現提示時，按一下 **[確定]**。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]。
9.	在 [確認選取項目] 頁面上，按一下 [建立]。
10.	完成時，按一下 [關閉]。

接著，將 DC2 設定為 corp.contoso.com 網域的複本網域控制站。從 DC2 的 Windows PowerShell 命令提示字元執行下列命令。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

請注意，系統會提示您提供 CORP\\User1 密碼和目錄服務還原模式 (DSRM) 密碼，並重新啟動 DC2。

由於 TestVNET 虛擬網路有自己的 DNS 伺服器 (DC2)，因此您必須設定 TestVNET 的虛擬網路使用這個 DNS 伺服器。

1.	在 Azure 入口網站的左窗格中，按一下虛擬網路的圖示，然後按一下 [TestVNET]。
2.	在 [設定] 索引標籤中，按一下 [DNS 伺服器]。
3.	在 [主要 DNS 伺服器] 中，輸入 **192.168.0.4** 以取代 10.0.0.4。
4.	按一下 [儲存]。

這是您目前的組態。

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
模擬混合式雲端環境到此準備就緒，可以進行測試。

## 後續步驟

- [加入虛擬機器](virtual-machines-windows-create-powershell.md)到 TestVNET 子網路中，例如執行 Microsoft SQL Server 的虛擬機器。

<!---HONumber=AcomDC_0323_2016-->