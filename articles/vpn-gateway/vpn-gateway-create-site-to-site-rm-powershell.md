<properties
   pageTitle="使用 Azure Resource Manager 和 PowerShell 建立具有站對站 VPN 連線的虛擬網路 | Microsoft Azure"
   description="本文會帶領您建立使用資源管理員模型的 VNet 並使用 S2S VPN 閘道連線將其連接到您的本機內部部署網路。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/16/2016"
   ms.author="cherylmc"/>

# 使用 PowerShell 和 Azure Resource Manager 建立具有站對站 VPN 連線的虛擬網路

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](vpn-gateway-site-to-site-create.md)
- [PowerShell - 資源管理員](vpn-gateway-create-site-to-site-rm-powershell.md)

本文將逐步引導您使用 **Azure Resource Manager** 部署模型建立虛擬網路以及內部部署網路的站對站 VPN 連線。站對站連線可以用於跨部署與混合式組態。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

![站對站圖表](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "站對站")

**站對站連線的部署模型和工具**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## 開始之前

在開始設定之前，請確認您具備下列項目。

- 相容的 VPN 裝置 (以及能夠進行設定的人員)。請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您將需要與能夠提供那些詳細資料的人協調。

- 您的 VPN 裝置對外開放的公用 IP 位址。此 IP 位址不能位於 NAT 後方。
	
- Azure 訂閱。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
	
- 您必須安裝最新版的 Azure 資源管理員 PowerShell Cmdlet。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。


## 1\.連線至您的訂用帳戶 

請確定您切換為 PowerShell 模式以使用資源管理員 Cmdlet。如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

開啟 PowerShell 主控台並連接到您的帳戶。使用下列範例來協助您連接：

	Login-AzureRmAccount

檢查帳戶的訂用帳戶。

	Get-AzureRmSubscription 

指定您要使用的訂用帳戶。

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\.建立虛擬網路和閘道器子網路

以下範例示範 /28 的閘道器子網路。雖然您可以建立與 /29 一樣小的閘道器子網路，但是我們不建議這麼做。我們建議建立閘道器子網路 /27 或更大的子網路 (/26、/25 等)，以容納額外的功能需求。如果已有具備 /29 或更大的閘道器子網路的虛擬網路，您可以往前跳至[步驟 3 - 新增您的區域網路閘道](#localnet)。

### 建立虛擬網路和閘道器子網路

使用下列範例來建立虛擬網路和閘道器子網路。取代為您自己的值。

首先，建立資源群組：
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

接著，建立您的虛擬網路。請確認您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。

下列範例會建立一個名為 testvnet 的虛擬網路和兩個子網路：一個名為 GatewaySubnet，另一個名為 Subnet1。請務必建立一個特別命名為 GatewaySubnet 的子網路。如果您將它命名為其他名稱，您的連線設定將會失敗。

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>將閘道器子網路加入至您已建立的虛擬網路

只有在您要將先前建立的閘道器子網路新增至 VNet 時才需要此步驟。

您可以使用以下的範例來建立閘道器子網路。請務必將閘道器子網路命名為 'GatewaySubnet'。如果您將其命名為其他名字，您會建立子網路，但 Azure 不會將它視為閘道器子網路。

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

現在，設定組態。

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\.<a name="localnet"></a>新增區域網路閘道

在虛擬網路中，區域網路閘道通常是指您的內部部署位置。您會指定該站台的名稱以供 Azure 參考，並且也會指定區域網路閘道的位址空間前置詞。

Azure 會使用您指定的 IP 位址前置詞來識別要傳送至內部部署位置的流量。這表示您必須指定您要與區域網路閘道相關聯的每個位址前置詞。如果您的內部部署網路變更，您可以輕鬆地更新這些前置詞。

使用 PowerShell 範例時，請注意下列各項：
	
- GatewayIPAddress 是內部部署 VPN 裝置的 IP 位址。VPN 裝置不能位於 NAT 後方。 
- AddressPrefix 是您的內部部署位址空間。

若要新增具有單一位址前置詞的區域網路閘道：

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

若要新增具有多個位址前置詞的區域網路閘道：

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### 修改區域網路閘道的 IP 位址首碼

有時候您的區域網路閘道首碼會有所變更。若要修改您的 IP 位址首碼，採取的步驟取決於您是否已建立 VPN 閘道連接。請參閱本文的[修改區域網路閘道的 IP 位址首碼](#modify)一節。


## 4\.要求 VPN 閘道的公用 IP 位址

接下來，您將要求要配置給 Azure VNet VPN 閘道的公用 IP 位址。這不是指派給 VPN 裝置的相同 IP 位址，而是指派給 Azure VPN 閘道本身的 IP 位址。您無法指定您要使用的 IP 位址；它會以動態方式配置給您的閘道器。設定內部部署 VPN 裝置以連接到閘道器時，您將使用此 IP 位址。

使用下列 PowerShell 範例。此位址的配置方法必須為「動態」。

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] 資源管理員部署模型的 Azure VPN 閘道目前使用動態配置方法，僅支援公用 IP 位址。但是，這不代表 IP 位址會變更。Azure VPN 閘道 IP 位址只會在刪除或重新建立閘道時變更。閘道公用 public IP 位址不會因為重新調整、重設或其他 Azure VPN 閘道內部維護/升級而變更。

## 5\.建立閘道器 IP 位址組態

閘道器組態定義要使用的子網路和公用 IP 位址。使用以下的範例來建立閘道器組態。

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\.建立虛擬網路閘道

在此步驟中，您將建立虛擬網路閘道。請注意，建立閘道器需要一些時間才能完成。通常 20 分鐘以上。

輸入下列值：

- 站對站組態的 **-GatewayType** 是 **Vpn**。閘道器類型永遠是您實作的組態的特定類型。例如，其他閘道器組態可能需要 -GatewayType ExpressRoute。 

- **-VpnType** 可以是 **RouteBased** (在某些文件中稱為動態閘道器)，或 **PolicyBased** (在某些文件中稱為靜態閘道器)。如需 VPN 閘道類型的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md#vpntype)。
- **-GatewaySku** 可以是**基本**、**標準**或**高效能**。 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7\.設定 VPN 裝置

此時，您需要虛擬網路閘道的公用 IP 位址，以便設定內部部署 VPN 裝置。向您的裝置製造商取得特定的組態資訊。或者，請參閱 [VPN 裝置](vpn-gateway-about-vpn-devices.md)以取得詳細資訊。

若要尋找虛擬網路閘道的公用 IP 位址，請使用下面範例：

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\.建立 VPN 連線

接下來，在虛擬網路閘道與 VPN 裝置之間建立站對站 VPN 連線。請務必將值取代為您自己的值。共用的金鑰必須符合您用於 VPN 裝置設定的值。請注意，站對站的 `-ConnectionType` 為 **IPsec**。

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

過一會兒，連接將會建立。

## 9\.驗證 VPN 連線

VPN 連線有幾種不同的驗證方式。下面我們將討論如何使用 Azure 入口網站和使用 PowerShell 執行基本驗證。

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>修改區域網路閘道的 IP 位址首碼

如果您需要變更區域網路閘道首碼，請使用下列指示。所提供的指示有兩組。要選擇哪組指示取決於您是否已建立 VPN 閘道連線。

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## 後續步驟

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。請參閱[建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)以取得相關步驟。

<!---HONumber=AcomDC_0330_2016-->