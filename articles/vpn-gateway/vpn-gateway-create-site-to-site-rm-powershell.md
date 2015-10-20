<properties
   pageTitle="使用 Azure 資源管理員和 PowerShell 建立具有站對站 VPN 連線的虛擬網路 |Microsoft Azure"
   description="本文會帶領您建立使用資源管理員模型的 VNet 並使用站對站 VPN 閘道連線將其連接到您的本機內部部署網路。包含額外的步驟來修改現有本機站台的 IP 位址首碼。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/13/2015"
   ms.author="cherylmc"/>

# 使用 PowerShell 建立具有站對站 VPN 連線的虛擬網路

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

本文將逐步引導您使用 Azure 資源管理員部署模型建立虛擬網路以及內部部署網路的站對站 VPN 連線。您可以使用上方的索引標籤，選取部署模型和部署工具的文章。

>[AZURE.NOTE]請務必了解 Azure 目前使用兩種部署模型：資源管理員模型和傳統模型。開始您的組態之前，請確定您瞭解部署模型和工具。如需部署模型的資訊，請參閱 [Azure 部署模型](../azure-classic-rm.md)。

## 開始之前

在開始設定之前，請確認您具備下列項目。

- 相容的 VPN 裝置 (以及能夠進行設定的人員)。請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。

- 您的 VPN 裝置對外開放的公用 IP 位址。此 IP 位址不能位於 NAT 後方。

>[AZURE.IMPORTANT]如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您將需要與能夠提供那些詳細資料的人協調。
	
- Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。

- 最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 Windows PowerShell 區段下載並安裝最新版本。本文是針對 Azure PowerShell *0.9.8* 撰寫。

>[AZURE.NOTE]如果您執行的是關鍵任務應用程式，請繼續使用 Azure PowerShell 0.9.8。在大部分情況下，這二個版本間的唯一差異是 1.0 Preview 的 Cmdlet 命名遵循 {動詞}-AzureRm{名詞} 模式，而 0.9.8 的命名不包含 Rm。例如，New-AzureRmResourceGroup 而不是 New-AzureResourceGroup。如需 Azure PowerShell 1.0 Preview 的詳細資訊，請參閱此[部落格文章](https://azure.microsoft.com/blog/azps-1-0-pre/)。如需 Azure PowerShell 1.0 Preview Cmdlet 的詳細資訊，請參閱 [Azure 資源管理員 Cmdlet](https://msdn.microsoft.com/library/mt125356.aspx) (英文)。


## 1\.連線至您的訂用帳戶 


開啟 PowerShell 主控台並連接到您的帳戶。

**注意：**以下指示是根據 0.9.8 版的 Azure PowerShell Cmdlet。

使用下列範例來協助您連接：

		Add-AzureAccount

如果您有多個訂用帳戶，請使用 *Select-AzureSubscription* 以連接至您想要使用的訂用帳戶。

		Select-AzureSubscription "yoursubscription"

接下來，切換至 Azure 資源管理員模式。
		
		Switch-AzureMode -Name AzureResourceManager

## 2\.建立虛擬網路和閘道器子網路

- 如果已有具備閘道器子網路的虛擬網路，您可以往前跳至**步驟 3 - 新增您的本機站台**。 
- 如果您已有虛擬網路且想要將閘道器子網路新增至您的 VNet，請參閱[將閘道器子網路新增至 VNet](#gatewaysubnet)。

### 建立虛擬網路和閘道器子網路

使用下列範例來建立虛擬網路和閘道器子網路。取代為您自己的值。

首先，建立資源群組：

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

接著，建立您的虛擬網路。請確認您指定的位址空間沒有與您在內部部署網路上所擁有的任何位址空間重疊。

下列範例會建立一個名為 *testvnet* 的虛擬網路和兩個子網路：一個名為 *GatewaySubnet*，另一個名為 *Subnet1*。請務必建立一個特別命名為 *GatewaySubnet* 的子網路。如果您將它命名為其他名稱，您的連線設定將會失敗。

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>將閘道器子網路新增至 VNet (選用)

只有在您要將閘道器子網路新增至 VNet 時才需要此步驟。

如果您已有現有的虛擬網路，而且想要加入閘道器子網路，您可以使用下面範例來建立閘道器子網路。請務必將閘道器子網路命名為 'GatewaySubnet'。如果您將檔案命名為其他名字，您將建立子網路，但它不會被 Azure 視為閘道器子網路。

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

現在，設定組態。

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 3\.新增您的本機站台

在虛擬網路中，「本機站台」 通常是指您的內部部署位置。您將會賦予該網站 Azure 可以參考它的名稱。

您也會指定本機站台的位址空間前置詞。Azure 會使用您指定的 IP 位址前置詞來識別要傳送至本機站台的流量。這表示您必須指定您要與本機站台相關聯的每個位址前置詞。如果您的內部部署網路變更，您可以輕鬆地更新這些前置詞。

使用 PowerShell 範例時，請注意下列各項：
	
- *GatewayIPAddress* 是內部部署 VPN 裝置的 IP 位址。VPN 裝置不能位於 NAT 後方。 
- *AddressPrefix* 是您的內部部署位址空間。

新增具有單一位址首碼的本機站台：

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

新增具有多個位址首碼的本機站台：

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### 修改本機站台的 IP 位址首碼

有時候會變更您的本機站台首碼。若要修改您的 IP 位址首碼，採取的步驟取決於您是否已建立 VPN 閘道連接。請參閱[修改本機站台的 IP 位址首碼](#to-modify-ip-address-prefixes-for-a-local-site)。


## 4\.要求閘道器的公用 IP 位址

接下來，您將要求要配置給 Azure VNet VPN 閘道的公用 IP 位址。這不是指派給 VPN 裝置的相同 IP 位址，而是指派給 Azure VPN 閘道本身的 IP 位址。您無法指定您要使用的 IP 位址；它會以動態方式配置給您的閘道器。設定內部部署 VPN 裝置以連接到閘道器時，您將使用此 IP 位址。

使用下列 PowerShell 範例。此位址的配置方法必須為「動態」。

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\.建立閘道器 IP 位址組態

閘道器組態定義要使用的子網路和公用 IP 位址。使用以下的範例來建立閘道器組態。


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\.建立閘道器

在此步驟中，您將建立虛擬網路閘道。請注意，建立閘道器會花一些時間才能完成。

輸入下列值：

- 閘道器類型為 *Vpn*。
- VpnType 可以是 RouteBased* (在某些文件中稱為動態閘道器)，或以*原則為基礎* (在某些文件中稱為靜態閘道器)。如需 VPN 閘道類型的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\.設定 VPN 裝置

此時，您需要虛擬網路閘道的公用 IP 位址，以便設定內部部署 VPN 裝置。向您的裝置製造商取得特定的組態資訊。或者，請參閱 [VPN 裝置](http://go.microsoft.com/fwlink/p/?linkid=615099)以取得詳細資訊。

若要尋找虛擬網路閘道的公用 IP 位址，請使用下面範例：

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\.建立 VPN 連線

接下來，在虛擬網路閘道與 VPN 裝置之間建立站對站 VPN 連線。請務必取代為您自己的值。共用的金鑰必須符合您用於 VPN 裝置設定的值。

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

過一會兒，連接將會建立。

## 9\.驗證 VPN 連線

此時，Preview 入口網站中不會顯示使用資源管理員建立的站對站 VPN 連線。不過，您可以使用 *Get-AzureVirtualNetworkGatewayConnection –Debug* 確認您的連線是否成功。在未來，我們將具備其 cmdlet，以及在 Preview 入口網站中檢視連線的能力。

您可以使用下列 cmdlet 範例，設定符合您自己的值。出現提示時，選取 [A] 才能執行「全部」。

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 完成此 cmdlet 之後，請捲動以檢視值。在下列範例中，連線狀態會顯示為 [已連線]，且您可以看見輸入和輸出位元組。

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## 修改本機站台的 IP 位址首碼

如果您需要變更您的本機站台首碼，請使用下列指示。提供兩組指示，並取決於您是否已建立 VPN 閘道連線。

### 新增或移除沒有 VPN 閘道連線的首碼


- 若要「新增」其他位址首碼到您建立的本機站台，但還沒有 VPN 閘道連線，請使用下列範例。

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- 若要從沒有 VPN 連線的本機站台「移除」位址首碼，請使用以下範例。省略您不再需要的首碼。此範例中不再需要首碼 20.0.0.0/24 (來自先前的範例)，因此我們將更新本機站台，並排除該首碼。

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### 新增或移除有 VPN 閘道連線的首碼

如果您已建立 VPN 連線並想要新增或移除包含在本機站台中的 IP 位址首碼，您必須依序執行下列步驟。這會導致 VPN 連線的一些停機時間，因為您需要移除並重新建立閘道器。不過，因為您已要求連線的 IP 位址，所以您不需要重新設定您的內部部署 VPN 路由器，除非您決定要變更您先前使用的值。

 
1. 移除閘道器連線。 
2. 修改本機站台的首碼。 
3. 建立新的閘道器連線。 

您可以使用下列範例當做指導方針。


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## 後續步驟

將虛擬機器新增至虛擬網路。[建立虛擬機器](../virtual-machines/virtual-machines-windows-tutorial.md)。

<!---HONumber=Oct15_HO3-->