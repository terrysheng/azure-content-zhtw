<properties
   pageTitle="使用 Azure 資源管理員和 PowerShell 建立具有站對站 VPN 連線的虛擬網路 |Microsoft Azure"
   description="使用 Azure 資源管理員和 PowerShell，建立從虛擬網路至內部部署位置的站對站 VPN 連線"
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
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# 使用 Azure 資源管理員和 PowerShell 建立具有站對站 VPN 連線的虛擬網路

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


本主題將逐步引導您建立 Azure 資源管理員虛擬網路以及內部部署網路的站對站 VPN 連線。

Azure 目前有兩種部署模型：傳統部署模型和 Azure 資源管理員部署模型。視用來部署虛擬網路的模型而定，站對站設定會有所不同。這些指示將套用至資源管理員。如果您要使用傳統部署模型來建立站對站 VPN 閘道連線，請參閱[在管理入口網站中建立站對站 VPN 連線](vpn-gateway-site-to-site-create.md)。


## 開始之前

開始之前，請確認您具備下列條件：

- 相容的 VPN 裝置 (以及能夠進行設定的人員)。請參閱[關於 VPN 裝置](vpn-gateway-vpn-devices.md)。
- 您的 VPN 裝置對外開放的公用 IP 位址。此 IP 位址不能位於 NAT 後方。
- 最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的〈Windows PowerShell〉一節下載並安裝最新版本。 
- Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。
	

## 連線至您的訂用帳戶 


開啟 PowerShell 主控台並切換至 Azure 資源管理員模式。使用下列範例來協助您連接：

		Add-AzureAccount

使用 Select-AzureSubscription 以連接至您想要使用的訂用帳戶。

		Select-AzureSubscription "yoursubscription"

接下來，切換到 ARM 模式。這會切換為可讓您使用 ARM Cmdlet 的模式。

		Switch-AzureMode -Name AzureResourceManager


## 建立虛擬網路和閘道器子網路

- 如果已有具備閘道器子網路的虛擬網路，您可以往前跳至[新增您的本機站台](#add-your-local-site)。 
- 如果您有虛擬網路且想要將閘道器子網路新增至您的 VNet，請參閱[將閘道器子網路新增至 VNet](#gatewaysubnet)。

### 建立虛擬網路和閘道器子網路

使用下列範例來建立虛擬網路和閘道器子網路。取代為您自己的值。

首先，建立資源群組：

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

接著，建立您的虛擬網路。下列範例會建立一個名為 *testvnet* 的虛擬網路和兩個子網路：一個名為 *GatewaySubnet*，另一個名為 *Subnet1*。請務必特別建立一個名為 *GatewaySubnet* 的子網路。如果您將它命名為其他名稱，您的連線設定將會失敗。

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>將閘道器子網路新增至 VNet

如果您已有現有的虛擬網路，而且想要加入閘道器子網路，您可以使用下面範例來建立閘道器子網路。請務必將閘道器子網路命名為 'GatewaySubnet'。如果您將它命名其他名稱，則 VPN 設定將不會如預期般運作。


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 新增您的本機站台

在虛擬網路中，*本機站台* 通常是指您的內部部署位置。您將會賦予該網站 Azure 可以參考它的名稱。

您也會指定本機站台的位址空間前置詞。Azure 會使用您指定的 IP 位址前置詞來識別要傳送至本機站台的流量。這表示您必須指定您要與本機站台相關聯的每個位址前置詞。如果您的內部部署網路變更，您可以輕鬆地更新這些前置詞。使用下列 PowerShell 範例來指定您的本機站台。

	
- *GatewayIPAddress* 是內部部署 VPN 裝置的 IP 位址。VPN 裝置不能位於 NAT 後方。 
- *AddressPrefix* 是您的內部部署位址空間。

使用此範例來新增您的本機站台：

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

## 要求 VNet 閘道的公用 IP 位址

接下來，您將要求要配置給 Azure VNet VPN 閘道的公用 IP 位址。這不是指派給 VPN 裝置的相同 IP 位址，而是指派給 Azure VPN 閘道本身的 IP 位址。您無法指定您要使用的 IP 位址；它會以動態方式配置給您的閘道器。設定內部部署 VPN 裝置以連接到閘道器時，您將使用此 IP 位址。

使用下列 PowerShell 範例。此位址的配置方法必須為「動態」。

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 建立閘道器 IP 位址組態

閘道器組態定義要使用的子網路和公用 IP 位址。使用以下的範例來建立閘道器組態。


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## 建立閘道器

在此步驟中，您將建立虛擬網路閘道。輸入下列值：

- 閘道類型為 *Vpn*。
- VpnType 可以是 RouteBased \* (在某些文件中稱為動態閘道)，或*以原則為基礎* (在某些文件中稱為靜態閘道)。如需 VPN 閘道類型的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## 設定 VPN 裝置

此時，您需要虛擬網路閘道的公用 IP 位址，以便設定內部部署 VPN 裝置。向您的裝置製造商取得特定的組態資訊。或者，請參閱 [ VPN 裝置](http://go.microsoft.com/fwlink/p/?linkid=615099)以取得詳細資訊。

若要尋找虛擬網路閘道的公用 IP 位址，請使用下面範例：

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 建立 VPN 連線

接下來，在虛擬網路閘道與 VPN 裝置之間建立站對站 VPN 連線。請務必取代為您自己的值。共用的金鑰必須符合您用於 VPN 裝置設定的值。

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

請稍候幾分鐘，應該就會建立連線。此時，入口網站中不會顯示使用資源管理員建立的站對站 VPN 連線。


## 後續步驟

將虛擬機器新增至虛擬網路。[建立虛擬機器](../virtual-machines/virtual-machines-windows-tutorial.md)。

<!---HONumber=July15_HO5-->