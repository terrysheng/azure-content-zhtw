<properties
   pageTitle="使用 Azure 資源管理員和 PowerShell 為相同訂用帳戶中的虛擬網路設定 VNet 對 VNet 連接 | Microsoft Azure"
   description="本文將逐步引導您使用 Azure 資源管理員和 PowerShell，將虛擬網路連線在一起"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/06/2015"
   ms.author="cherylmc"/>

# 使用 Azure 資源管理員和 PowerShell 為相同訂用帳戶中的虛擬網路設定 VNet 對 VNet 連接

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

本文將引導您使用資源管理員部署模型的步驟。您可以使用上方的索引標籤，選取部署模型和部署工具的文章。目前，針對不同訂用帳戶中使用「資源管理員」部署方法來建立之虛擬網路的 VNet 對 VNet 連接，我們還沒有解決方案。小組目前正在設想解決方案，我們預期在年底會有相關的步驟。當有解決方案可用時，本文將會反映這些步驟。以下步驟適用於相同訂用帳戶中的 Vnet。

如果您的虛擬網路是使用「傳統」部署模型來建立的，請參閱[建立 VNet 對 VNet 連接](virtual-networks-configure-vnet-to-vnet-connection.md)。「傳統」部署模型支援連接位於不同訂用帳戶中的 VNet。

如果您想要將在「傳統」部署模型中建立的虛擬網路連接到使用「Azure 資源管理員」模型建立的虛擬網路，請參閱[將傳統 VNet 連接到新的 VNet](../virtual-network/virtual-networks-arm-asm-s2s.md)。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，非常類似於將 VNet 連接至內部部署網站位置。這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。您所連接的 VNet 可位於不同的區域。您甚至可以將多網站組態與 VNet 對 VNet 通訊結合。如下圖所示，這可讓您建立能將跨單位連線與內部虛擬網路連線結合的網路拓撲。


![VNet 對 VNet 連線能力圖表](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 
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

- Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。

- Azure PowerShell 0.9.8 Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 Windows PowerShell 區段下載並安裝此版本。雖然您可以使用這些步驟 (稍微修改 Cmdlet) PowerShell 1.0 Preview 搭配，但是本文章是針對 0.9.8 所撰寫。

**關於使用這些步驟與 Azure PowerShell 1.0 Preview 搭配**

	[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)] 


## 1\.規劃 IP 位址範圍


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

## 2\.連線至您的訂用帳戶 

開啟 PowerShell 主控台並連接到您的帳戶。使用下列範例來協助您連接：

		Add-AzureAccount

如果您有多個訂用帳戶，請使用 *Select-AzureSubscription* 以連接到您想要使用的訂用帳戶。

		Select-AzureSubscription "yoursubscription"

接下來，切換至 Azure 資源管理員模式。
		
		Switch-AzureMode -Name AzureResourceManager

## 3\.建立虛擬網路


使用下列範例來建立虛擬網路和閘道器子網路。取代為您自己的值。在此範例中，我們將建立 VNet1。稍後請重複步驟，建立 VNet2。

首先，建立資源群組。

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

接著，建立您的虛擬網路。下列範例會建立一個名為 *VNet1* 的虛擬網路，以及名為 *GatewaySubnet* 和 *Subnet1* 的兩個子網路。請務必建立一個明確命名為 *GatewaySubnet* 的子網路。如果您將它命名為其他名稱，您的連線設定將會失敗。在下列範例中，閘道子網路為 /28。您可以選擇使用最小的 /29 閘道子網路。請注意某些功能 (例如 ExpressRoute /站對站並存連接) 需要較大的 /27 閘道子網路，因此您可能需建立自己的閘道子網路，以處理未來可能使用的其他功能的流量。

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## 4\.要求公用 IP 位址


接下來，需要求一個公用 IP 位址，以配置給您將建立給 VNet 使用的閘道。您無法指定想要的 IP 位址；該 IP 位址會以動態方式配置給您的閘道。下一個組態章節將使用此 IP 位址。

使用下面的範例。此位址的配置方法必須為「動態」。


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic

## 5\.建立閘道組態


閘道器組態定義要使用的子網路和公用 IP 位址。使用以下的範例來建立閘道器組態。


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\.建立閘道器


此步驟將帶您建立 VNet 的虛擬網路閘道。VNet 對 VNet 組態需要 RouteBased VpnType。建立閘道可能需要一些時間，請耐心等候。

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\.建立 VNet2


設定好 VNet1 之後，請重複上述步驟，以設定 VNet2 和其閘道組態。完成這兩個 VNet 及其個別閘道的組態設定之後，請進行**步驟 8.連接閘道**。

## 8\.連接閘道

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

## 確認連線

此時，Preview 入口網站中不會顯示使用資源管理員建立的 VPN 連線。不過，您可以使用 *Get-AzureVirtualNetworkGatewayConnection –Debug* 來確認您的連線是否成功。在未來，我們將具備其 cmdlet，以及在 Preview 入口網站中檢視連線的能力。

您可以使用下列 Cmdlet 範例。請務必變更其中的值，以符合您要確認的每個連線。出現提示時，請選取 [A] 以執行「全部」。

		Get-AzureVirtualNetworkGatewayConnection -Name vnet2connection -ResourceGroupName vnet2vnetrg -Debug 

 完成此 cmdlet 之後，請捲動以檢視值。在下列範例中，連接狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。

	Body:
	{
	  "name": "Vnet2Connection",
	  "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/connections/Vnet2Connection",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "3c0bc049-860d-46ea-9909-e08098680a8bdef",
	    "virtualNetworkGateway1": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet2Gw"
	    },
	    "virtualNetworkGateway2": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet1Gw"
	    },
	    "connectionType": "Vnet2Vnet",
	    "routingWeight": 3,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 3359044,
	    "egressBytesTransferred": 4142451
	  }
	} 

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## 連接現有的 VNet

若您已經在 Azure 資源管理員模式中建立虛擬網路，而且想要連接這些虛擬網路，請確認下列事項：

- 每個 VNet 的閘道子網路都至少設為 /29 以上的值。
- 虛擬網路的位址範圍沒有重疊。
- VNet 在相同的訂用帳戶中。

若您需要將閘道子網路加入 VNet，請利用下列範例以您自己的值取代提供的值。請務必將閘道器子網路命名為 'GatewaySubnet'。如果您將它命名其他名稱，則 VPN 設定將不會如預期般運作。

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

確認閘道子網路的設定正確之後，請繼續進行**步驟 4.要求公用 IP 位址**，並依照步驟執行。


## 後續步驟

您可以將虛擬機器加入您的虛擬網路。[建立虛擬機器](../virtual-machines/virtual-machines-windows-tutorial.md)。

如需有關「VPN 閘道」的詳細資訊，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)。

如需 REST API 資訊，請參閱 [Azure 網路閘道 REST API 參考](https://msdn.microsoft.com/library/azure/mt163859.aspx)。

如需有關「虛擬網路」的詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

<!---HONumber=Nov15_HO3-->