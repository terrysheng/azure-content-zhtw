<properties
   pageTitle="使用 Azure Resource Manager 和 PowerShell 為 VNet 建立 VNet 對 VNet 的 VPN 閘道連接 | Microsoft Azure"
   description="本文將逐步引導您使用 Azure 資源管理員和 PowerShell，將虛擬網路連接在一起。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="cherylmc"/>

# 使用 Azure Resource Manager 和 PowerShell 來設定 VNet 對 VNet 連線

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

本文將逐步引導您使用 Resource Manager 部署模型及 PowerShell，建立虛擬網路間的連線。虛擬網路可位於來自相同或不同訂用帳戶的相同或不同區域。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**VNet 對 VNet 的部署模型和工具**

可以在這兩種部署模型中，使用不同的工具，設定 VNet 至 VNet 的連接。如需詳細資訊，請參閱以下的資料表。當此組態有新文章、新的部署模型和額外工具可以使用時，我們就會更新此資料表。當文章可用時，我們會直接從資料表連結至該文章。

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## 關於 VNet 對 VNet 連線

將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，非常類似於將 VNet 連接至內部部署網站位置。這兩種連線類型都使用 Azure VPN 閘道提供使用 IPsec/IKE 的安全通道。您所連接的 VNet 可位於不同的區域。或位於不同的訂用帳戶。您甚至可以將多網站組態與 VNet 對 VNet 通訊結合。如下圖所示，這可讓您建立能將跨單位連線與內部虛擬網路連線結合的網路拓撲。


![關於連線](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### 為什麼要連接虛擬網路？

針對下列原因，您可能希望連接虛擬網路：

- **跨區域的異地備援和異地目前狀態**
	- 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
	- 您可以使用 Azure 流量管理員和負載平衡器，利用異地備援跨多個 Azure 區域設定高度可用的工作負載。其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組來設定 SQL Always On。

- **具有隔離或管理界限的區域性多層式應用程式**
	- 在相同區域中，您可以因為隔離或管理需求，設定將多層式應用程式與多個虛擬網路連線在一起。


### VNet 對 VNet 常見問題集

- 虛擬網路可位於相同或不同的 Azure 區域 (位置)。

- 即使雲端服務或負載平衡端點與虛擬網路連線在一起，也「無法」橫跨虛擬網路。

- 除非需要跨部署連線，否則將多個 Azure 虛擬網路連線在一起不需要使用任何內部部署 VPN 閘道。

- VNet 對 VNet 支援連接虛擬網路。但是不支援連線「不」在虛擬網路中的虛擬機器或雲端服務。

- VNet 對 VNet 需要 VPN 類型為 RouteBased (前稱為動態路由) 的 Azure VPN 閘道。

- 虛擬網路連線可以和多網站 VPN 同時使用，最多可以將 10 個 (預設/標準閘道) 或 30 個 (高效能閘道) 虛擬網路 VPN 閘道的 VPN 通道連接至其他虛擬網路，或內部部署網站。

- 虛擬網路與內部部署區域網路網站的位址空間不得重疊。位址空間重疊會導致 VNet 對 VNet 連線建立失敗。

- 不支援成對虛擬網路之間的備援通道。

- 虛擬網路的所有 VPN 通道一起共用 Azure VPN 閘道上可用的頻寬，以及 Azure 中相同的 VPN 閘道運作時間 SLA。

- VNet 對 VNet 流量會透過 Microsoft 網路傳輸，而非透過網際網路。

- 相同區域內的 VNet 對 VNet 流量雙向皆為免費；跨區域 VNet 對 VNet 輸出流量會根據來源區域的輸出 VNet 間資料傳輸費率收費。如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/vpn-gateway/)。


## 我應該使用哪個步驟集？

本文中有不同的步驟集，適用於使用 Resource Manager 部署模型所建立的 VNet。VNet 對 VNet 設定步驟取決於 VNet 是否位於相同的訂用帳戶中。

![兩個連線](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


對於設定步驟，兩者之間的主要差異在於您是否可以在相同的 PowerShell 工作階段內建立和設定所有的虛擬網路和閘道資源。這份文件會包含每一項的步驟。上方圖顯示相同訂用帳戶的 VNet 對 VNet 連線，以及跨訂用帳戶的 VNet 對 VNet 連線。


- [位於相同訂用帳戶中的 VNet](#samesub)
- [位於不同訂用帳戶中的 VNet](#difsub)


## <a name ="samesub"/>如何連接相同訂用帳戶中的 Vnet

這項設定適用於相同訂用帳戶中的虛擬網路，如下圖所示︰

![VNet2VNet 相同訂用帳戶](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### 開始之前

- 請確認您有 Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
	
- 您必須安裝 Azure 資源管理員 PowerShell Cmdlet。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

### <a name ="Step1"/>步驟 1 - 規劃 IP 位址範圍


請務必決定用來設定網路組態的範圍。請記住，您必須先確定您的 VNet 範圍或區域網路範圍沒有以任何方式重疊。

下列步驟將帶您建立兩個虛擬網路，以及它們各自的閘道子網路和組態。接著會建立兩個 VNet 之間的 VPN 閘道連接。

在這個練習中，我們對 VNet 使用下列的值：

**TestVNet1 的值︰**

- VNet 名稱︰TestVNet1
- 資源群組︰TestRG1
- 位置：美國東部
- TestVNet1：10.11.0.0/16 和 10.12.0.0/16
- FrontEnd：10.11.0.0/24
- BackEnd：10.12.0.0/24
- GatewaySubnet：10.12.255.0/27
- DNS 伺服器：8.8.8.8
- GatewayName：VNet1GW
- 公用 IP: VNet1GWIP
- VPNType：RouteBased
- Connection(1to4)：VNet1toVNet4
- Connection(1to5)：VNet1toVNet5
- ConnectionType：VNet2VNet


**TestVNet4 的值︰**

- VNet 名稱︰TestVNet4
- TestVNet2：10.41.0.0/16 和 10.42.0.0/16
- FrontEnd：10.41.0.0/24
- BackEnd：10.42.0.0/24
- GatewaySubnet：10.42.255.0.0/27
- 資源群組：TestRG4
- 位置：美國西部
- DNS 伺服器：8.8.8.8
- GatewayName：VNet4GW
- 公用 IP：VNet4GWIP
- VPNType：RouteBased
- 連線︰VNet4toVNet1
- ConnectionType：VNet2VNet



### <a name ="Step2"/>步驟 2 - 建立及設定 TestVNet1

1. 宣告變數

	對於此練習，我們一開始先宣告我們的變數。下列範例會使用此練習中的值來宣告變數。請務必在設定生產環境時，使用您自己的值來取代該值。若您執行這些步驟是為了熟悉此類型的設定，則可以使用這些變數。修改變數，然後將其複製並貼到您的 PowerShell 主控台中。

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. 連線到訂用帳戶 1

	請確定您切換為 PowerShell 模式以使用資源管理員 Cmdlet。如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

	開啟 PowerShell 主控台並連接到您的帳戶。使用下列範例來協助您連接：

		Login-AzureRmAccount

	檢查帳戶的訂用帳戶。

		Get-AzureRmSubscription 

	指定您要使用的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. 建立新的資源群組

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. 建立 TestVNet1 的子網路設定

	下列範例會建立一個名為 TestVNet1 的虛擬網路和三個子網路：一個名為 GatewaySubnet、一個名為 Fro|ntEnd，另一個名為 Backend。替代值時，務必一律將您的閘道子網路特定命名為 GatewaySubnet。如果您將其命名為其他名稱，閘道將會建立失敗。

	在下列範例中，閘道子網路為 /27。雖然技術上您可以使用小至 /29 的子網路來建立閘道子網路，但是不建議您這麼做。建議您使用較大的子網路，例如 /27 或 /26，以便您想要利用現有或未來可能需要較大閘道子網路的設定。


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. 建立 TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. 要求公用 IP 位址

	接下來，需要求一個公用 IP 位址，以配置給您將建立給 VNet 使用的閘道。您無法指定想要的 IP 位址；該 IP 位址會以動態方式配置給您的閘道。下一個組態章節將使用此 IP 位址。請使用下方的範例。此位址的配置方法必須為「動態」。

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. 建立閘道組態

	閘道器組態定義要使用的子網路和公用 IP 位址。使用以下的範例來建立閘道器組態。

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. 建立 TestVNet1 的閘道

	在此步驟中，您將建立 TestVNet1 的虛擬網路閘道。VNet 對 VNet 組態需要 RouteBased VpnType。建立閘道可能需要花費一段時間 (30 分鐘或更久)。

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 步驟 3 - 建立及設定 TestVNet4

設定 TestVNet1 後，您將重複相同的步驟來建立 TestVNet4。遵循下方步驟，視需要替換成您自己的值。此步驟可以在相同的 PowerShell 工作階段中完成，因為其位於相同的訂用帳戶中。

1. 宣告變數

	請務必使用您想用於設定的值來取代該值。

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	請確定您仍然與訂用帳戶 1 保持連線，然後再繼續。

2. 建立新的資源群組

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. 建立 TestVNet4 的子網路設定

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. 建立 TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. 要求公用 IP 位址

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. 建立閘道組態

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. 建立 TestVNet4 閘道

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 步驟 4 - 連接閘道

1. 取得這兩個虛擬網路閘道

	在此範例中，由於這兩個閘道皆位於相同的訂用帳戶中，因此這個步驟可以在相同的 PowerShell 工作階段中完成。

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. 建立 TestVNet1 至 TestVNet4 的連線

	在此步驟中，您將從 TestVNet1 建立連線至 TestVNet4。您會看到範例使用共用金鑰。您可以使用自己的值，作為共用金鑰。但請務必確認該共用金鑰必須適用於這兩個連線。建立連線可能需要一段時間才能完成。

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. 建立 TestVNet4 至 TestVNet1 的連線

	此步驟類似上面的步驟，只不過您是建立 TestVNet4 至 TestVNet1 的連線。請確認共用的金鑰相符。

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	請稍候幾分鐘，應該就會建立連線。

## <a name ="Verify"/>如何驗證 VNet 對 VNet 連線

下方範例將示範如何驗證您的連線。請務必變更值，以符合您的環境。

### 使用 Azure 入口網站驗證您的連線

您可以在 Azure 入口網站中驗證 VPN 連線，方法是瀏覽至 [虛擬網路閘道] -> 按一下您的閘道名稱 -> [設定] -> [連線]。您可以選取連線名稱，以在 [連線] 刀鋒視窗中檢視詳細資訊。


### 使用 PowerShell 驗證您的連線

您也可以使用 Get-AzureRmVirtualNetworkGatewayConnection –Debug 來驗證您的連接是否成功。您可以使用下列範例，並將值變更為符合您自己的值。出現提示時，請選取 [A] 以執行全部。

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

完成此 cmdlet 之後，請捲動以檢視值。在下方 PowerShell 輸出範例中，連線狀態會顯示為 [已連接]，且您可以看見輸入和輸出位元組。



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name ="difsub"/>如何連接不同訂用帳戶中的 Vnet

下列設定步驟加入其他的 VNet 對 VNet 連線，以將 TestVNet1 連線至 TestVNet5，其位於不同的訂用帳戶中。此處的差別為在第二個訂用帳戶的內容中 (尤其是當兩個訂用帳戶分屬不同組織時)，部分設定步驟需在不同的 PowerShell 工作階段中執行。完成下方步驟後，下圖中會顯示產生的設定︰

![VNet2VNet 不同訂用帳戶](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

下面的指示是從上方所列的前一個步驟繼續執行。您必須完成[步驟 1](#Step1) 和[步驟 2](#Step2)，以建立並設定 TestVNet1 和 TestVNet1 的 VPN 閘道。如果您只打算連接不同訂用帳戶中的 VNet，則無需在意先前練習的步驟 3 和 4，且可以移至下方的步驟 5。

### 步驟 5 - 驗證其他 IP 位址範圍

請務必確定新虛擬網路的 IP 位址空間 TestVNet5 不會與任何 VNet 範圍或區域網路閘道範圍重疊。

在此範例中，虛擬網路可能屬於不同的組織。在這個練習中，您可以對 TestVNet5 使用下列的值：

**TestVNet5 的值︰**

- VNet 名稱︰TestVNet5
- 資源群組：TestRG5
- 位置：日本東部
- TestVNet5：10.51.0.0/16 和 10.52.0.0/16
- FrontEnd：10.51.0.0/24
- BackEnd：10.52.0.0/24
- GatewaySubnet：10.52.255.0.0/27
- DNS 伺服器：8.8.8.8
- GatewayName：VNet5GW
- 公用 IP：VNet5GWIP
- VPNType：RouteBased
- 連線︰VNet5toVNet1
- ConnectionType：VNet2VNet

**TestVNet1 的其他值︰**

- 連線︰VNet1toVNet5


### 步驟 6 - 建立及設定 TestVNet5

在新訂用帳戶的內容中，必須完成這個步驟。此部分可能會由不同組織的系統管理員執行，其擁有訂用帳戶。

1. 宣告變數

	請務必使用您想用於設定的值來取代該值。

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. 連線到訂用帳戶 5

	開啟 PowerShell 主控台並連接到您的帳戶。使用下列範例來協助您連接：

		Login-AzureRmAccount

	檢查帳戶的訂用帳戶。

		Get-AzureRmSubscription 

	指定您要使用的訂用帳戶。

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. 建立新的資源群組

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. 建立 TestVNet4 的子網路設定
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. 建立 TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. 要求公用 IP 位址

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. 建立閘道組態

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig 

8. 建立 TestVNet5 閘道

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 步驟 7 - 連接閘道

在此範例中，因為閘道會在不同的訂用帳戶中，所以我們已將此步驟分作兩個 PowerShell 工作階段，其標示為 [訂用帳戶 1] 和 [訂用帳戶 5]。

1. [訂用帳戶 1] 取得訂用帳戶 1 的虛擬網路閘道

	請確定您已登入並連接到訂用帳戶 1。

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	複製下列項目的輸出，並透過電子郵件或其他方法將其傳送到訂用帳戶 5 的系統管理員。

		$vnet1gw.Name
		$vnet1gw.Id

	這兩個元素的值會類似下列範例的輸出︰

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. [訂用帳戶 5] 取得訂用帳戶 5 的虛擬網路閘道

	請確定您已登入並連接到訂用帳戶 5。

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	複製下列項目的輸出，並透過電子郵件或其他方法將其傳送到訂用帳戶 1 的系統管理員。

		$vnet5gw.Name
		$vnet5gw.Id

	這兩個元素的值會類似下列範例的輸出︰

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. [訂用帳戶 1] 建立 TestVNet1 至 TestVNet5 的連線

	在此步驟中，您將從 TestVNet1 建立連線至 TestVNet5。此處的差別為直接取得 $vnet5gw，因為其位於不同的訂用帳戶中。您必須使用上述步驟中從訂用帳戶 1 通訊的值來建立新的 PowerShell 物件。請以您自己的值來取代名稱、識別碼和共用金鑰。但請務必確認該共用金鑰必須適用於這兩個連線。建立連線可能需要一段時間才能完成。

	請確定您已連接到訂用帳戶 1。
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. [訂用帳戶 5] 建立 TestVNet5 至 TestVNet1 的連線

	此步驟類似上面的步驟，只不過您是建立 TestVNet5 至 TestVNet1 的連線。針對基於從訂用帳戶 1 所取得的值來建立 PowerShell 物件，該程序也適用於此處。在此步驟中，請確認共用金鑰相符。

	請確定您已連接到訂用帳戶 5。

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. 驗證您的連線

	完成這些步驟後，您可以透過使用[如何驗證 VNet 對 VNet 連線](#Verify)中的方法，驗證您的連線。

## 後續步驟

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。請參閱[建立網站的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)以取得相關步驟。

<!---HONumber=AcomDC_0323_2016-->