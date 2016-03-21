## 組態概觀

適用於此工作的步驟會根據下列值來使用 VNet。其他設定和名稱也會概述於這份清單中。雖然我們會根據這份清單中的值加入變數，但不會在任何步驟中直接使用這份清單。您可以複製清單以供參考，並使用您自己的值來取代其中的值。

組態參考清單︰
	
- 虛擬網路名稱 = "TestVNet"
- 虛擬網路位址空間 = 192.168.0.0/16
- 資源群組 = "TestRG"
- Subnet1 名稱 = "FrontEnd" 
- Subnet1 位址空間 = "192.168.0.0/16"
- 閘道子網路名稱："GatewaySubnet" 一律將閘道子網路命名為 *GatewaySubnet*。
- 閘道子網路位址空間 = "192.168.200.0/26"
- 區域 = "East US"
- 閘道名稱 = "GW"
- 閘道 IP 名稱 = "GWIP"
- 閘道 IP 組態名稱 = "gwipconf"
- VPN 類型 ="ExpressRoute" ExpressRoute 組態需要有這個 VPN 類型。
- 閘道公用 IP 名稱 = "gwpip"


## 新增閘道

1. 連接到您的 Azure 訂用帳戶。 

		Login-AzureRmAccount
		Get-AzureRmSubscription 
		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. 宣告您在本練習中使用的變數。這個範例將會在下列範例中使用變數。請務必編輯此範例，以反映您要使用的設定。
		
		$RG = "TestRG"
		$Location = "East US"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
		$VNetName = "TestVNet"

3. 將虛擬網路物件儲存為變數。

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. 將閘道子網路加入至虛擬網路。閘道子網路必須命名為 "GatewaySubnet"。您想要建立 /27 或更大 (/26、/25 等) 的閘道。
			
		Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. 設定組態。

			Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. 將閘道子網路儲存為變數。

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. 要求公用 IP 位址。建立閘道之前需要有 IP 位址。您無法指定想要使用的 IP 位址；該 IP 位址會以動態方式進行配置。下一個組態章節將使用此 IP 位址。AllocationMethod 必須是動態的。

		$pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

8. 建立適用於閘道的組態。閘道器組態定義要使用的子網路和公用 IP 位址。在此步驟中，您要指定在建立閘道將使用的組態。這個步驟不會實際建立閘道物件。使用以下的範例來建立閘道器組態。

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -SubnetId $subnet.Id -PublicIpAddressId $pip.Id 

9. 建立閘道。在此步驟中，**-GatewayType** 特別重要。您必須使用值 **ExpressRoute**。請注意，執行這些 Cmdlet 之後，閘道需要花費 20 分鐘或更久時間來建立。

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute

<!---HONumber=AcomDC_0309_2016-->