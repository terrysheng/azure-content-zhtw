<properties 
   pageTitle="使用 PowerShell 在資源管理員中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 PowerShell 在資源管理員中建立網際網路面向的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

# 開始使用 PowerShell 在資源管理員中建立網際網路面向的負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋之內容包括資源管理員部署模型。您也可以[了解如何使用 Azure 資源管理員建立網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-cli.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

下列步驟將說明如何搭配 PowerShell 使用 Azure 資源管理員，建立網際網路面向的負載平衡器。使用 Azure 資源管理員時，會個別設定建立網際網路面向的負載平衡器所需的項目，然後放置在一起，以建立資源。

這一頁中會說明必須完成才能建立負載平衡器的個別工作順序，並詳細說明要達到的目標，需要做些什麼。

## 若要建立網際網路面向的負載平衡器，需要哪些項目？

您需要建立和設定下列物件以部署負載平衡器：

- 前端 IP 組態 - 包含傳入網路流量的公用 IP 位址。 

- 後端位址集區 - 包含從負載平衡器接收網路流量的虛擬機器網路介面 (NIC)。

- 負載平衡規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中連接埠的規則。

- 輸入 NAT 規則 - 包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器之連接埠的規則。

- 探查 - 包含用來檢查後端位址集區中虛擬機器執行個體可用性的健全狀態探查。

您可以在 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)中取得關於負載平衡器元件與 Azure 資源管理員的詳細資訊。


## 設定 PowerShell 以使用資源管理員

請確定您的 PowerShell 的 Azure 模組具有最新的實際執行版本，以及正確設定 PowerShell 以存取您的 Azure 訂用帳戶。

### 步驟 1

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 在 Azure PowerShell 提示字元中執行 **Switch-AzureMode** Cmdlet，以切換至資源管理員模式，如下所示。

		Switch-AzureMode AzureResourceManager
	
	預期的輸出：

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]Switch-AzureMode Cmdlet 即將被汰除。屆時將重新命名所有的資源管理員 Cmdlet。



### 步驟 2

登入您的 Azure 帳戶。


    PS C:\> Add-AzureAccount

系統會提示使用您的認證進行驗證。


### 步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

若要查看可用訂用帳戶的清單，請使用 ‘Get-AzureSubscription’ Cmdlet。

## 建立資源群組

在「美國西部」Azure 位置中建立名為 *NRP-RG* 的新資源群組。

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## 建立前端 IP 集區的虛擬網路和公用 IP 位址

### 步驟 1

建立子網路和虛擬網路。

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### 步驟 2

建立名為 *PublicIP* 的公用 IP 位址 (PIP)，讓具有 DNS 名稱 *loadbalancernrp.westus.cloudapp.azure.com* 的前端 IP 集區使用。下列命令會使用靜態配置類型。

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]負載平衡器將使用公用 IP 的網域標籤做為其 FQDN。這樣會變更傳統部署模型，該部署使用雲端服務作為負載平衡器 FQDN。在此範例中，FQDN 是 *loadbalancernrp.westus.cloudapp.azure.com*。

## 建立前端 IP 集區與後端位址集區

### 步驟 1 

建立名為 *LB-Frontend* 的前端 IP 集區，它使用 *PublicIp* PIP。

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 步驟 2 

建立名為 *LB-backend* 的後端位址集區。

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## 建立 LB 規則、NAT 規則、探查及負載平衡器

上述範例會建立下列項目：

- NAT 規則，將連接埠 3441 上的所有傳入流量轉譯至連接埠 3389<sup>1</sup>
- NAT 規則，將連接埠 3442 上的所有傳入流量轉譯至連接埠 3389。
- 負載平衡器規則，將連接埠 80 上的所有傳入流量，負載平衡至後端集區中位址的連接埠 80。
- 探查規則，將在名為 *HealthProbe.aspx* 的頁面上檢查健全狀態。
- 負載平衡器，使用上述所有物件。


<sup>1</sup> NAT 規則會關聯到在負載平衡器後方的特定虛擬機器執行個體。傳入到連接埠 3341 的網路流量，將會使用與下方範例中 NAT 規則關聯的連接埠 3389，傳送至特定的虛擬機器。您必須針對 NAT 規則選擇 UDP 或 TCP 通訊協定。無法將兩種通訊協定指派到相同的連接埠。

### 步驟 1

建立 NAT 規則。

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### 步驟 2

建立負載平衡器規則。

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### 步驟 3

建立健全狀況探查。

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### 步驟 4

使用上方建立的物件建立負載平衡器。

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## 建立 NIC

您需要建立 NIC (或修改現有的) 並將它們關聯至 NAT 規則、負載平衡器規則和探查。

### 步驟 1 

取得需要在其中建立 NIC 的 VNet 和子網路。

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### 步驟 2

建立名為 *lb-nic1-be* 的 NIC，並將它與第一個 NAT 規則以及第一個 (唯一) 後端位址集區產生關聯。
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 步驟 3

建立名為 *lb-nic2-be* 的 NIC，並將它與第二個 NAT 規則以及第一個 (唯一) 後端位址集區產生關聯。

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### 步驟 4

檢查 NIC。


	PS C:\> $backendnic1

預期的輸出：

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### 步驟 5

使用 `Add-AzureVMNetworkInterface` Cmdlet 以將 NIC 指派給不同的 VM。

您可以在以下文件中找到建立虛擬機器並指派 NIC 的指引：[利用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)，使用範例中的選項 5。

## 更新現有負載平衡器


### 步驟 1

使用上述範例中的負載平衡器，透過 Get-AzureLoadBalancer 將負載平衡器物件指派給變數 $slb

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### 步驟 2

在下列範例中，您會使用前端的連接埠 81 和後端集區的連接埠 8181，將新的輸入 NAT 規則加入現有的負載平衡器

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### 步驟 3

使用 Set-AzureLoadBalancer 儲存新組態

	$slb | Set-AzureLoadBalancer

## 移除負載平衡器

使用命令 Remove-AzureLoadBalancer 刪除資源群組 "NRP-RG" 中先前建立的負載平衡器 "NRP-LB"

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]您可以使用選擇性參數 -Force 以避免出現刪除提示。

## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO4-->