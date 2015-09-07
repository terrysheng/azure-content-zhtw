<properties
   pageTitle="透過 Azure 資源管理員和 PowerShell 建立 VNet 對 VNet 連接 | Microsoft Azure"
	description="本文將逐步引導您使用 Azure 資源管理員和 PowerShell，將虛擬網路連線在一起"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>

# 使用 Azure 資源管理員和 PowerShell 設定 VNet 對 VNet 連接

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，非常類似於將 VNet 連接至內部部署網站位置。這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。您所連接的 VNet 可位於不同的區域。您甚至可以將多網站組態與 VNet 對 VNet 通訊結合。如下圖所示，這可讓您建立能將跨單位連線與內部虛擬網路連線結合的網路拓撲。


![VNet 對 VNet 連線能力圖表](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 

>[AZURE.NOTE]Azure 目前有兩種部署模式：傳統部署模式和 Azure 資源管理員部署模式。組態的 Cmdlet 和步驟會因部署模式而有所不同。本主題將引導您將透過 Azure 資源管理員模式建立的虛擬網路連接在一起。若您想要透過傳統部署模式建立 VNet 對 VNet 連接，請參閱[透過 Azure 入口網站，設定 VNet 對 VNet 連接](virtual-networks-configure-vnet-to-vnet-connection.md) (英文)。如果您想要將在傳統模式中建立的虛擬網路，連接至在 Azure 資源管理員中建立的虛擬網路，請參閱[將傳統 VNet 連接至新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)。

## 為什麼要連接虛擬網路？

針對下列原因，您可能希望連接虛擬網路：

- **跨區域的異地備援和異地目前狀態**
	- 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
	- 您可以使用 Azure 負載平衡器和 Microsoft 或協力廠商叢集技術，利用異地備援跨多個 Azure 區域設定高度可用的工作負載。其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組設定 SQL Always On。

- **具有嚴密隔離界限的區域性多層式應用程式**
	- 在相同區域中，您可以使用嚴密的隔離和安全的層次間通訊，設定多層式應用程式與多個虛擬網路連線在一起。



### 注意事項

本文將引導您連接兩個虛擬網路：VNet1 和 VNet2。您必須熟悉網路連線，才能替換與您的網路設計需求相容的 IP 位址範圍。


![連線 VNet 對 VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)



- 虛擬網路可位於相同或不同的 Azure 區域 (位置)。

- 即使雲端服務或負載平衡端點與虛擬網路連線在一起，也「無法」橫跨虛擬網路。

- 除非需要跨部署連線，否則將多個 Azure 虛擬網路連線在一起不需要使用任何內部部署 VPN 閘道。

- VNet 對 VNet 支援連接虛擬網路。但是不支援連線「不」在虛擬網路中的虛擬機器或雲端服務。

- VNet 對 VNet 需要 VPN 類型為 RouteBased (前稱為動態) 的 Azure VPN 閘道。

- 虛擬網路連線可以和多網站 VPN 同時使用，最多可以將 10 個虛擬網路 VPN 閘道的 VPN 通道連接至其他虛擬網路，或內部部署網站。

- 虛擬網路與內部部署區域網路網站的位址空間不得重疊。位址空間重疊會導致虛擬網路建立失敗。

- 不支援成對虛擬網路之間的備援通道。

- 虛擬網路的所有 VPN 通道一起共用 Azure VPN 閘道上可用的頻寬，以及 Azure 中相同的 VPN 閘道運作時間 SLA。

- VNet 對 VNet 流量會經過 Azure 的骨幹。


## 開始之前


開始之前，請確認您具備下列條件：


- 最新版的 Azure PowerShell Cmdlet。您可以從 [[下載] 頁面](http://azure.microsoft.com/downloads/)的＜Windows PowerShell＞一節，下載並安裝最新版本。 
- Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。
- 如果已建立虛擬網路，請參閱[連接現有的 VNet](#connecting-existing-vnets)。
	


建立和設定 VNet 對 VNet 連接需要進行多個步驟。請以下方列出的順序設定每個區段。


1. [規劃 IP 位址範圍](#plan-your-ip-address-ranges)
2. [連線至您的訂用帳戶](#connect-to-your-subscription)
3. [建立虛擬網路](#create-a-virtual-network)
4. [要求您閘道的公用 IP 位址](#request-a-public-ip-address)
5. [建立閘道組態](#create-the-gateway-configuration)
6. [建立閘道器](#create-the-gateway)
7. [重複步驟以設定 VNet2](#create-vnet2)
8. [連線 VPN 閘道](#connect-the-gateways)


## 規劃 IP 位址範圍

### 步驟 1

請務必決定用來設定網路組態的範圍。從 VNet1 的角度來說，VNet2 只是 Azure 平台中定義的另一個 VPN 連線。而對 VNet2 來說，VNet1 只是另一個 VPN 連線。請記住，您必須先確定您的 VNet 範圍或區域網路範圍沒有以任何方式重疊。


下列步驟將帶您建立兩個虛擬網路，以及它們各自的閘道子網路和組態。接著會建立兩個 VNet 之間的 VPN 閘道連接。

在這個練習中，我們對 VNet 使用下列的值：

VNet1 的值：

- 虛擬網路名稱 = VNet1
- 資源群組 = testrg1
- 位址空間 = 10.1.0.0/16 
- 地區 = 美國西部
- GatewaySubnet = 10.1.0.0/28
- Subnet1 = 10.1.1.0/28

VNet2 的值：

- 虛擬網路名稱 = VNet2
- 資源群組 = testrg2
- 位址空間 = 10.2.0.0/16
- 地區 = 日本東部
- GatewaySubnet = 10.2.0.0/28
- Subnet1 = 10.2.1.0/28

## 連線至您的訂用帳戶 

### 步驟 2


開啟 PowerShell 主控台並切換至 Azure 資源管理員模式。使用下列範例來協助您連接：

		Add-AzureAccount

使用 Select-AzureSubscription 以連接至您想要使用的訂用帳戶。

		Select-AzureSubscription "yoursubscription"

接下來，切換到 ARM 模式。這會切換為可讓您使用 ARM Cmdlet 的模式。

		Switch-AzureMode -Name AzureResourceManager

## 建立虛擬網路

### 步驟 3


使用下列範例來建立虛擬網路和閘道器子網路。取代為您自己的值。在此範例中，我們將建立 VNet1。稍後請重複步驟，建立 VNet2。

首先，建立資源群組。

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

接著，建立您的虛擬網路。下列範例會建立一個名為 *VNet1* 的虛擬網路，和兩個子網路：*GatewaySubnet* 和 *Subnet1*。請務必建立一個特別命名為 *GatewaySubnet* 的子網路。如果您將它命名為其他名稱，您的連線設定將會失敗。在下列範例中，閘道子網路為 /28。您可以選擇使用最小的 /29 閘道子網路。請注意某些功能 (例如 ExpressRoute /站對站並存連接) 需要較大的 /27 閘道子網路，因此您可能需建立自己的閘道子網路，以處理未來可能使用的其他功能的流量。

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzurevirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## 要求公用 IP 位址

### 步驟 4

接下來，需要求一個公用 IP 位址，以配置給您將建立給 VNet 使用的閘道。您無法指定想要的 IP 位址；該 IP 位址會以動態方式配置給您的閘道。下一個組態章節將使用此 IP 位址。

使用下面的範例。此位址的配置方法必須為「動態」。


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic


## 建立閘道組態

### 步驟 5

閘道器組態定義要使用的子網路和公用 IP 位址。使用以下的範例來建立閘道器組態。


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## 建立閘道器

### 步驟 6

此步驟將帶您建立 VNet 的虛擬網路閘道。VNet 對 VNet 組態需要 RouteBased VpnType。建立閘道可能需要一些時間，請耐心等候。

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 建立 VNet2

### 步驟 7

設定好 VNet1 之後，請重複上述步驟，以設定 VNet2 和其閘道組態。完成這兩個 VNet 和其各自的閘道的組態設定後，請前往[連接閘道](#connect-the-gateways)。


## 連接閘道

### 步驟 8

此步驟將帶您建立兩個虛擬網路閘道之間的 VPN 閘道連接。　您會看到範例使用共用金鑰。您可以使用自己的值，作為共用金鑰。但請務必確認該共用金鑰必須適用於這兩個組態。

請注意，建立連接需要一些時間才能完成。

**VNet1 對 VNet2**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**VNet2 對 VNet1**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    


請稍候幾分鐘，應該就會建立連線。請注意，此時 Preview 入口網站還不會顯示透過 Azure 資源管理員建立的閘道和連接。　

## 連接現有的 VNet

若您已經在 Azure 資源管理員模式中建立虛擬網路，而且想要連接這些虛擬網路，請確認下列事項：

- 每個 VNet 的閘道子網路都至少設為 /29 以上的值。
- 虛擬網路的位址範圍沒有重疊。

若您需要將閘道子網路加入 VNet，請利用下列範例以您自己的值取代提供的值。請務必將閘道器子網路命名為 'GatewaySubnet'。如果您將它命名其他名稱，則 VPN 設定將不會如預期般運作。

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

確認閘道子網路的設定正確無誤後，請前往＜[要求公用 IP 位址](#request-a-public-ip-address)＞，並依照步驟執行。

## 後續步驟

您可以將虛擬機器加入您的虛擬網路。[建立虛擬機器](../virtual-machines/virtual-machines-windows-tutorial.md)。

<!---HONumber=August15_HO9-->