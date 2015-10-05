<properties 
   pageTitle="如何在 Azure 將傳統 VNet 連接到 ARM Vnet"
   description="了解如何在傳統 VNet 和新的 VNet 之間建立 VPN 連線"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/18/2015"
   ms.author="telmos" />

# 將傳統 VNet 連接到新的 VNet

Azure 目前有兩種管理模式：Azure 服務管理員 (稱為傳統) 和 Azure 資源管理員 (ARM)。如果您已使用 Azure 一段時間，則可能具有傳統 VNet 上執行的 Azure VM 和執行個體角色。此外，較新的 VM 和角色執行個體可能在 ARM 中建立的 VNet 上執行。

在這種情況下，您將想要確保新的基礎結構能夠與您的傳統資源進行通訊。作法為您可以在兩個 VNet 之間建立 VPN 連線。

在本文中，您將學習如何在傳統 VNet 和 ARM VNet 之間建立站台對站台 (S2S) VPN 連線。

>[AZURE.NOTE]本文假設您已有傳統 Vnet 和 ARM Vnet，而且您熟悉如何設定傳統 VNet 的 S2S VPN 連線。如需傳統 VNet 和 ARM VNet 之間的 S2S VPN 連線能力的端對端解決方案，請造訪[解決方案指南 - 使用 S2S VPN 將傳統 VNet 連接到 ARM VNet](../virtual-networks-arm-asm-s2s.md)。

您可以大致了解要完成哪些工作，才能使用下列 Azure 閘道，在傳統 VNet 和 ARM VNet 之間建立 S2S VPN 連線。

1 - [建立傳統 VNet 的 VPN 閘道](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 - [建立 ARM VNet 的 VPN 閘道](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 - [在閘道之間建立連線](#Step-3:-Create-a-connection-between-the-gateways)

## 步驟 1：建立傳統 VNet 的 VPN 閘道

若要建立傳統 VNet 的 VPN 閘道，請遵循下列指示。

1. 從 https://manage.windowsazure.com 開啟傳統的入口網站，並在需要時輸入您的認證。
2. 在畫面左下角，依序按一下 [**新增**] 按鈕、[**網絡服務**]、[**虛擬網路**] 和 [**新增區域網路**]。
3. 在 [**指定您的區域網路詳細資料**] 視窗中，輸入您想要連接到 ARM VNet 名稱，然後在視窗的右下角，按一下箭號按鈕。
3. 在位址空間的 [**起始 IP**] 文字方塊中，輸入您想要連接到的 ARM VNet 的網路首碼。 
4. 在 [**CIDR (位址計數)**] 下拉式清單中，選取您想要連接到的 ARM VNet 所使用的 CIDR 區塊的網路部分所使用的位元數。
5. 在 [**VPN 裝置 IP 位址 (選擇性)**] 中，輸入任何有效的公用 IP 位址。我們將在稍後變更此 IP 位址。然後，按一下畫面右下方的核取記號按鈕。下圖顯示此頁面的範例設定。

	![區域網路設定](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. 在 [**網路**] 頁面上，依序按一下 [**虛擬網路**]、您的傳統 VNet 和 [**設定**]。
6. 在 [**站對站連線能力**] 下，啟用 [**連線到區域網路**] 核取方塊。
7. 從 [**區域網路**] 下拉式清單的可用網路清單中，選取您在步驟 4 建立的區域網路，然後按一下 [**儲存**]。
8. 一旦儲存了設定，請按一下 [**儀表板**]，然後在頁面底部，依序按一下 [**建立閘道**]、[**動態路由**] 和 [**是**]。
9. 等候閘道完成建立並複製其公用 IP 位址。您將需要它，才能在 ARM VNet 中設定閘道。

## 步驟 2：建立 ARM VNet 的 VPN 閘道

若要建立 ARM VNet 的 VPN 閘道，請遵循下列指示。

1. 從 PowerShell 主控台中，執行下列命令來切換到 ARM 模式。

		Switch-AzureMode AzureResourceManager

2. 執行下列命令來建立區域網路。區域網路必須使用您想要連接到的傳統 VNet 的 CIDR 區塊，以及在上述步驟 1 中所建立之閘道的公用 IP 位址。

		New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. 執行下列命令，來建立閘道的公用 IP 位址。

		$ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. 執行下列命令，來擷取用於閘道的子網路。

		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT]閘道子網路必須已經存在，而且必須命名為 GatewaySubnet。

5. 執行下列命令，來建立閘道的 IP 組態物件。請注意閘道子網路的識別碼。該子網路必須存在於 VNet 中。

		$ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT]必須分別從子網路和 IP 位址物件傳遞 id 屬性給 *SubnetId* 和 *PublicIpAddressId* 參數。您無法使用簡單的字串。
	
5. 執行下列命令，來建立 ARM VNet 閘道。

		New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. 一旦建立 VPN 閘道時，請執行下列命令來擷取其公用 IP 位址。複製 IP 位址，您必將需要它，來設定傳統 VNet 的區域網路。

		Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## 步驟 3：在閘道之間建立連線

1. 從 https://manage.windowsazure.com 開啟傳統的入口網站，並在需要時輸入您的認證。
2. 在傳統的入口網站中，向下捲動並依序按一下 [**網路**]、[**區域網路**]、您想要連接到的 ARM VNet 和 [**編輯**] 按鈕。
3. 在 [**VPN 裝置 IP 位址 (選擇性)**] 中，輸入上述步驟 2 中擷取的 ARM VNet 閘道的 IP 位址，然後按一下右下角的向右箭號，再按一下核取記號按鈕。
4. 從 PowerShell 主控台中，執行下列命令切換到 Azure 服務管理員模式。

		Switch-AzureMode AzureServiceManager

5. 執行下列命令，來設定共用金鑰。確定您將 VNet 的名稱變更為您自己的 VNet 名稱。

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

6. 從 PowerShell 主控台中，執行下列命令切換到 Azure 資源管理員模式。

		Switch-AzureMode AzureResourceManager

7. 執行下列命令來建立 VPN 連線。

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## 後續步驟

- 深入了解 [ARM 的網路資源提供者 (NRP)](../resource-groups-networking.md)。
- 建立[使用 S2S VPN 將傳統 VNet 連接到 ARM VNet 的端對端解決方案](../virtual-networks-arm-asm-s2s.md)。

<!---HONumber=Sept15_HO4-->