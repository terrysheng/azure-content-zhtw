<properties 
   pageTitle="關於虛擬網路跨單位連線的 VPN 閘道 | Microsoft Azure"
   description="了解可用於混合式組態站對站跨單位連線、VNet 對 VNet 連線和點對站連線的 VPN 閘道。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# 關於 VPN 閘道

VPN 閘道 (也稱為 Azure 虛擬網路閘道) 可用來傳送虛擬網路與內部部署位置之間的網路流量。它們也用來傳送 Azure 內多個虛擬網路之間的流量 (VNet 對 VNet)。下列各節將討論與 VPN 閘道相關的項目。

用來建立 VPN 閘道的指示將取決於用來建立虛擬網路的部署模型。例如，如果您使用傳統部署模型建立 VNet，您將使用傳統部署模型的指導方針和指示來建立和設定 VPN 閘道。您無法建立傳統部署模型虛擬網路的 Resource Manager VPN 閘道。

如需部署模型的詳細資訊，請參閱[了解 Resource Manager 和傳統部署模型](../resource-manager-deployment-model.md)。


## <a name="gwsub"></a>閘道子網路

若要設定 VPN 閘道，您必須先為 VNet 建立閘道子網路。所有閘道子網路都必須命名為 GatewaySubnet 才能正常運作。

閘道子網路的最小大小完全取決於您想要建立的組態。雖然某些組態的閘道子網路最小可以建立為 /29，但建議您建立 /28 或更大 (/28、/27、/26 等) 的閘道子網路。

建立較大的閘道可防止違反閘道大小限制。例如，如果您建立閘道子網路大小為 /29 的閘道，而且想要設定站對站/ExpressRoute 並存組態，則必須刪除閘道、刪除閘道子網路、將閘道子網路建立為 /28 或更大，然後重新建立閘道。

藉由從一開始就建立較大的閘道子網路，就能在稍後對網路環境新增組態功能時節省時間。

下列範例顯示命名為 GatewaySubnet 的閘道子網路。您可以看到 CIDR 標記法指定 /27，這可提供足夠的 IP 位址供大多數現有組態使用。

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27


## <a name="gwtype"></a>閘道類型

閘道類型會指定閘道本身如何連接以及為何它是 Resource Manager 部署模型的必要組態設定。請勿將閘道類型與 VPN 類型混為一談，後者所指定的是 VPN 的路由類型。`-GatewayType` 的可用值為：

- Vpn
- ExpressRoute


此 Resource Manager 部署模型範例將 -GatewayType 指定為 Vpn。在建立閘道時，您必須確定用於組態的閘道類型是正確的。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>閘道 SKU

在建立 VPN 閘道時，您必須指定想要使用的閘道 SKU。有 3 種 VPN 閘道 SKU：

- 基本
- 標準
- HighPerformance

下列範例會將 `-GatewaySku` 指定為「標準」。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

### 依 SKU 和閘道類型列出的估計彙總輸送量


下方資料表顯示閘道類型，以及估計的彙總輸送量。閘道 SKU 之間的定價並不相同。如需定價資訊，請參閱＜[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway/)＞。此資料表適用於資源管理員與傳統部署模型。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>VPN 類型

每個組態皆需要特定 VPN 類型才能運作。如果您要結合兩個組態，例如建立連往相同 VNet 的站對站連線和點對站連線，您必須使用同時符合這兩個連線需求的 VPN 類型。在點對站和站對站並存連線的情況下，使用 Azure Resource Manager 部署模型時必須使用路由式 VPN 類型，若使用傳統部署模式則必須使用動態閘道。

在建立組態時，您會選取連線所需的 VPN 類型。

有兩種 VPN 類型：

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

此 Resource Manager 部署模型範例將 `-VpnType` 指定為 RouteBased。在建立閘道時，您必須確定用於組態的 -VpnType 是正確的。

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>連線類型

每個組態皆需要特定的連線類型。`-ConnectionType` 的可用 Resource Manager PowerShell 值為：

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

在下列範例中，我們會建立站對站連線，其需要 "IPsec" 連線類型。

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="lng"></a>區域網路閘道

區域網路閘道通常是指您的內部部署位置。在傳統部署模型中，區域網路閘道被稱為本機站台。您會指定區域網路閘道的名稱 (即內部部署 VPN 裝置的公用 IP 位址)，並指定位於內部部署位置的位址首碼。Azure 會查看網路流量的目的地位址首碼、查閱您為區域網路閘道指定的組態，並據以路由傳送封包。您可以視需要修改這些位址首碼。



### 修改位址首碼 - Resource Manager

修改位址首碼的程序會因為是否已建立 VPN 閘道而有所不同。請參閱下列文章章節：[修改區域網路閘道的位址首碼](vpn-gateway-create-site-to-site-rm-powershell.md#modify)。

在下列範例中，您可以看到將會指定名為 MyOnPremiseWest 的區域網路閘道，而且此閘道會包含兩個 IP 位址首碼。

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### 修改位址首碼 - 傳統部署

如果您需要在使用傳統部署模型時修改本機站台，目前您可以使用傳統入口網站中的 [區域網路] 組態頁面，或直接修改網路組態檔 NETCFG.XML。


## VPN 裝置

您必須確定您打算使用的 VPN 裝置支援您的組態所需的 VPN 類型。如需相容 VPN 裝置的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。

## 閘道需求


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## 後續步驟

請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)以了解詳細資訊後，再繼續進行組態的規劃和設計。





 

<!---HONumber=AcomDC_0330_2016-->