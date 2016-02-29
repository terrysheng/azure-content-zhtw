<properties
   pageTitle="開始使用 Azure 資源管理員設定網際網路面向的負載平衡器 | Microsoft Azure"
   description="如何建立 Azure 資源管理員的負載平衡器規則、NAT 規則、探查。逐步示範建立負載平衡器資源的端對端流程。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# 開始使用 Azure 資源管理員設定網際網路面向的負載平衡器


> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internet-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-arm-powershell.md)


下列步驟將說明如何搭配 PowerShell 使用 Azure 資源管理員，建立網際網路面向的負載平衡器。使用 Azure 資源管理員時，會個別設定建立網際網路面向的負載平衡器所需的項目，然後放置在一起，以建立資源。

我們會在這一頁中說明必須完成才能建立負載平衡器的個別工作順序，並詳細說明要達到建立負載平衡器的目標，需要做些什麼。


## 若要建立網際網路面向的負載平衡器，需要哪些項目？

必須在建立負載平衡器之前先設定下列項目：

- 前端 IP 組態 - 會將公用 IP 位址加入至前端 IP 集區，以讓傳入網路流量能夠達到負載平衡。 

- 後端位址集區 - 會設定可接收來自前端 IP 集區之負載平衡流量的網路介面。

- 負載平衡規則 - 負載平衡器的來源與本機連接埠組態。

- 探查 - 設定虛擬機器執行個體的健全狀態探查。

- 傳入 NAT 規則 - 設定直接存取其中一個虛擬機器執行個體的連接埠規則。

您可以在 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md)中取得關於負載平衡器元件與 Azure 資源管理員的詳細資訊。

下列步驟將示範如何在 2 部虛擬機器之間設定負載平衡器。


## 使用 PowerShell 逐步進行


### 建立負載平衡器的資源群組


### 步驟 1
請確定您切換 PowerShell 模式來使用 ARM Cmdlet。如需詳細資訊，可在[搭配使用 Windows PowerShell 與資源管理員](powershell-azure-resource-manager.md)取得。


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### 步驟 2

登入您的 Azure 帳戶。


    PS C:\> Add-AzureAccount

系統會提示使用您的認證進行驗證。


### 步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

若要查看可用訂用帳戶的清單，請使用 ‘Get-AzureSubscription’ Cmdlet。


### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。請確定所有建立負載平衡器的命令都是使用同一個資源群組。

在上述範例中，我們已建立名為 "NRP-RG" 的資源群組，且位置為「美國西部」。

## 建立前端 IP 集區的虛擬網路和公用 IP 位址


### 步驟 1

建立虛擬網路：

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

建立虛擬網路的子網路，並指派給 $backendSubnet

	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

建立虛擬網路，並將子網路 lb-subnet-be 加入至虛擬網路 NRPVNet。

### 步驟 2

建立前端 IP 集區要使用的公用 IP 位址：

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip 

>[AZURE.NOTE]公用 IP 位址網域名稱標籤屬性會是負載平衡器之 FQDN 的前置詞。

## 建立前端 IP 集區和後端位址集區

設定傳入負載平衡器網路流量的前端 IP 集區以及後端位址集區，以接收負載平衡流量。

### 步驟 1 

使用公用 IP 變數 ($publicIP) 建立前端 IP 集區。

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 


### 步驟 2 

設定用來從前端 IP 集區接收傳入流量的後端位址集區：

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## 建立 LB 規則、NAT 規則、探查及負載平衡器

建立前端 IP 集區和後端位址集區之後，您必須建立將屬於負載平衡器資源的規則：

### 步驟 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


上述範例會建立下列項目：

- NAT 規則，連接埠 3441 的所有傳入流量都會移至連接埠 3389。
- 第二個 NAT 規則，連接埠 3442 的所有傳入流量都會移至連接埠 3389。
- 負載平衡器規則，將公用連接埠 80 上的所有傳入流量，負載平衡至後端位址集區中的本機連接埠 80。
- 探查規則，檢查路徑 "HealthProbe.aspx" 的健全狀態。



### 步驟 2

建立負載平衡器，並將所有物件 (NAT 規則、負載平衡器規則、探查組態) 加在一起：

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## 建立網路介面

建立負載平衡器之後，您需要定義要由哪些網路介面接收傳入的負載平衡網路流量，以及定義 NAT 規則和探查。在此情況下需個別設定網路介面，並在稍後指派給虛擬機器。


### 步驟 1 


取得資源的虛擬網路和子網路以建立網路介面：

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


在此步驟中，我們會建立屬於負載平衡器後端集區的網路介面，並針對此網路介面的 RDP 與第一個 NAT 規則建立關聯：
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 步驟 2

建立第二個網路介面，稱為 LB-Nic2-BE：

在此步驟中，我們會建立第二個網路介面，並指派給同一個負載平衡器後端集區，然後與針對 RDP 所建立的第二個 NAT 規則建立關聯：

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


最終結果會顯示如下：


PS C:\> $backendnic1


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



### 步驟 3 

使用 Add-AzureVMNetworkInterface 命令將 NIC 指派給虛擬機器。

您可以在以下文件中找到建立虛擬機器並指派給 NIC 的逐步解說：[使用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)的選項 4 或 5。

## 更新現有負載平衡器


### 步驟 1

使用上述範例中的負載平衡器，透過 Get-AzureLoadBalancer 將負載平衡器物件指派給變數 $slb

	$slb=get-azureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

### 步驟 2

在下列範例中，您會使用前端的連接埠 81 和後端集區的連接埠 8181，將新的輸入 NAT 規則加入現有的負載平衡器

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### 步驟 3

使用 Set-AzureLoadBalancer 儲存新組態

	$slb | Set-AzureLoadBalancer

## 移除負載平衡器

使用命令 Remove-AzureLoadBalancer 刪除資源群組 "NRP-RG" 中先前建立的負載平衡器 "NRP-LB"

	Remove-AzureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

>[AZURE.NOTE] 您可以使用選擇性參數 -Force 以避免出現刪除提示。


## 另請參閱

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_0218_2016-->