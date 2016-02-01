<properties
   pageTitle="如何設定 ExpressRoute 線路的路由 | Microsoft Azure"
   description="本文將逐步引導您為 ExpressRoute 線路建立和佈建私用、公用及 Microsoft 對等。本文也示範如何檢查狀態、更新或刪除線路的對等。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# 使用 Azure 資源管理員及 PowerShell 建立和修改 ExpressRoute 線路的路由

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-routing-classic.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)

本文將逐步引導您使用 PowerShell Cmdlet 和 Azure 資源管理員部署模型，以建立和管理 ExpressRoute 線路的路由組態。下列步驟也會示範如何檢查狀態、更新或刪除和取消佈建 ExpressRoute 線路的對等。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組 (版本 1.0 或更新版本)。 
- 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)頁面、[路由需求](expressroute-routing.md)頁面和[工作流程](expressroute-workflows.md)頁面。
- 您必須擁有作用中的 ExpressRoute 線路。繼續之前，請遵循指示來[建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)，並由您的連線提供者來啟用該線路。ExpressRoute 線路必須處於已佈建和已啟用狀態，您才能執行如下所述的 Cmdlet。

>[AZURE.IMPORTANT]這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。如果您使用的服務提供者是提供受管理的第 3 層服務 (通常是 IPVPN，如 MPLS)，您的連線提供者會為您設定和管理路由。在此情況下，您無法建立或管理對等。

您可以為 ExpressRoute 線路設定一個、兩個或全部三個對等 (Azure 私用、Azure 公用和 Microsoft)。您可以依自己選擇的任何順序設定對等。不過，您必須確定一次只完成一個對等的設定。

## Azure 私用對等

本節提供如何為 ExpressRoute 線路建立、取得、更新和刪除 Azure 私用對等組態的指示。

### 建立 Azure 私用對等

1. **匯入 ExpressRoute 的 PowerShell 模組。**
	
 	您必須從 [PowerShell 資源庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式並將 Azure 資源管理員模組匯入至 PowerShell 工作階段，以開始使用 ExpressRoute Cmdlet。您必須以系統管理員身分執行 PowerShell。

	    Install-Module AzureRM

		Install-AzureRM

	匯入已知語意版本範圍內所有的 AzureRM.* 模組

		Import-AzureRM

	您也可以只匯入已知語意版本範圍內選取的模組
		
		Import-Module AzureRM.Network 

	登入您的帳戶

		Login-AzureRmAccount

	選取您想要建立 ExpressRoute 線路的訂用帳戶
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **建立 ExpressRoute 線路。**
	
	請遵循指示建立 [ExpressRoute 線路](expressroute-howto-circuit-arm.md)，並由連線提供者佈建它。

	如果您的連線提供者是提供受管理的第 3 層服務，您可以要求連線提供者為您啟用 Azure 私用對等。在此情況下，您不需要遵循後續幾節所列的指示。不過，如果您的連線提供者不會為您管理路由，請在建立線路之後遵循下列指示。

3. **檢查 ExpressRoute 線路以確定已佈建。**

	您必須先檢查 ExpressRoute 線路是否為 Provisioned 和 Enabled。請參閱下方的範例。

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	回應如下列範例所示：

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


4. **設定線路的 Azure 私用對等。**

	繼續執行接下來的步驟之前，請確定您有下列項目：

	- 主要連結的 /30 子網路。這不能在保留給虛擬網路的任何位址空間中。
	- 次要連結的 /30 子網路。這不能在保留給虛擬網路的任何位址空間中。
	- 供建立此對等的有效 VLAN ID。請確定線路有沒有其他對等使用相同的 VLAN ID。
	- 對等的 AS 編號。您可以使用 2 位元組和 4 位元組 AS 編號。您可以將私用 AS 編號用於此對等。請確定您不是使用 65515。
	- MD5 雜湊 (如果選擇使用)。**這是選擇性的**。
	
	您可以執行下列 Cmdlet 來為線路設定 Azure 私用對等。

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	如果您選擇使用 MD5 雜湊，您可以使用下列 Cmdlet。

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	>[AZURE.IMPORTANT]請確定您將 AS 編號指定為對等 ASN，而不是客戶 ASN。

### 取得 Azure 私用對等詳細資料

您可以使用下列 Cmdlet 來取得組態詳細資料

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt	


### 更新 Azure 私用對等組態

您可以使用下列 Cmdlet 來更新組態的任何部分。在下列範例中，線路的 VLAN ID 從 100 更新為 500。

	Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### 刪除 Azure 私用對等

您可以執行下列 Cmdlet 來移除對等組態。

>[AZURE.WARNING]執行此 Cmdlet 之前，您必須確定所有虛擬網路都已經與 ExpressRoute 取消連結。

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## Azure 公用對等

本節提供如何為 ExpressRoute 線路建立、取得、更新和刪除 Azure 公用對等組態的指示。

### 建立 Azure 公用對等

1. **匯入 ExpressRoute 的 PowerShell 模組。**
	
 	您必須從 [PowerShell 資源庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式並將 Azure 資源管理員模組匯入至 PowerShell 工作階段，以開始使用 ExpressRoute Cmdlet。您必須以系統管理員身分執行 PowerShell。

	    Install-Module AzureRM

		Install-AzureRM

	匯入已知語意版本範圍內所有的 AzureRM.* 模組

		Import-AzureRM

	您也可以只匯入已知語意版本範圍內選取的模組
		
		Import-Module AzureRM.Network 

	登入您的帳戶

		Login-AzureRmAccount

	選取您想要建立 ExpressRoute 線路的訂用帳戶
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **建立 ExpressRoute 線路**
	
	請遵循指示建立 [ExpressRoute 線路](expressroute-howto-circuit-arm.md)，並由連線提供者佈建它。

	如果您的連線提供者是提供受管理的第 3 層服務，您可以要求連線提供者為您啟用 Azure 私用對等。在此情況下，您不需要遵循後續幾節所列的指示。不過，如果您的連線提供者不會為您管理路由，請在建立線路之後遵循下列指示。

3. **檢查 ExpressRoute 線路以確定已佈建**

	您必須先檢查 ExpressRoute 線路是否為 Provisioned 和 Enabled。請參閱下方的範例。

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	回應如下列範例所示：

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	

4. **設定線路的 Azure 公用對等**

	進一步執行之前，請確定您具有下列資訊。

	- 主要連結的 /30 子網路。這必須是有效的公用 IPv4 首碼。
	- 次要連結的 /30 子網路。這必須是有效的公用 IPv4 首碼。
	- 供建立此對等的有效 VLAN ID。請確定線路有沒有其他對等使用相同的 VLAN ID。
	- 對等的 AS 編號。您可以使用 2 位元組和 4 位元組 AS 編號。您必須將公用 AS 編號用於此對等。
	- MD5 雜湊 (如果選擇使用)。**這是選擇性的**。
	
	您可以執行下列 Cmdlet 來為線路設定 Azure 私用對等

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

	如果您選擇使用 MD5 雜湊，您可以使用下列 Cmdlet

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


	>[AZURE.IMPORTANT]請確定您將 AS 編號指定為對等 ASN，而不是客戶 ASN。

### 取得 Azure 公用對等詳細資料

您可以使用下列 Cmdlet 來取得組態詳細資料

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### 更新 Azure 公用對等組態

您可以使用下列 Cmdlet 來更新組態的任何部分

	Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

在上面的範例中，線路的 VLAN ID 從 200 更新為 600。

### 刪除 Azure 公用對等

您可以執行下列 Cmdlet 來移除對等組態

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Microsoft 對等互連

本節提供如何為 ExpressRoute 線路建立、取得、更新和刪除 Microsoft 對等組態的指示。

### 建立 Microsoft 對等

1. **匯入 ExpressRoute 的 PowerShell 模組。**
	
 	您必須從 [PowerShell 資源庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式並將 Azure 資源管理員模組匯入至 PowerShell 工作階段，以開始使用 ExpressRoute Cmdlet。您必須以系統管理員身分執行 PowerShell。

	    Install-Module AzureRM

		Install-AzureRM

	匯入已知語意版本範圍內所有的 AzureRM.* 模組

		Import-AzureRM

	您也可以只匯入已知語意版本範圍內選取的模組
		
		Import-Module AzureRM.Network 

	登入您的帳戶

		Login-AzureRmAccount

	選取您想要建立 ExpressRoute 線路的訂用帳戶
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. **建立 ExpressRoute 線路**
	
	請遵循指示建立 [ExpressRoute 線路](expressroute-howto-circuit-arm.md)，並由連線提供者佈建它。

	如果您的連線提供者是提供受管理的第 3 層服務，您可以要求連線提供者為您啟用 Azure 私用對等。在此情況下，您不需要遵循後續幾節所列的指示。不過，如果您的連線提供者不會為您管理路由，請在建立線路之後遵循下列指示。

3. **檢查 ExpressRoute 線路以確定已佈建**

	您必須先檢查 ExpressRoute 線路是否為 Provisioned 和 Enabled。請參閱下方的範例。

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	回應如下列範例所示：

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []	
4. **設定線路的 Microsoft 對等**

	繼續之前，請確定您擁有下列資訊：

	- 主要連結的 /30 子網路。這必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 首碼。
	- 次要連結的 /30 子網路。這必須是您所擁有且註冊在 RIR / IRR 中的有效公用 IPv4 首碼。
	- 供建立此對等的有效 VLAN ID。請確定線路有沒有其他對等使用相同的 VLAN ID。
	- 對等的 AS 編號。您可以使用 2 位元組和 4 位元組 AS 編號。您必須只使用公用 AS 編號。您必須擁有 AS 編號。
	- 公告的首碼：您必須提供一份您打算在 BGP 工作階段上公告的所有首碼的清單。只接受公用 IP 位址首碼。如果您打算傳送一組首碼，您可以傳送逗號分隔清單。這些首碼必須在 RIR / IRR 中註冊給您。
	- 客戶 ASN：如果您要公告的首碼未註冊給對等 AS 編號，您可以指定它們所註冊的 AS 編號。**這是選擇性的**。
	- 路由登錄名稱：您可以指定可供註冊 AS 編號和首碼的 RIR / IRR。
	- MD5 雜湊 (如果選擇使用)。**這是選擇性。**
	
	您可以執行下列 Cmdlet 來為線路設定 Microsoft 對等

		Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### 取得 Microsoft 對等詳細資料

您可以使用下列 Cmdlet 來取得組態詳細資料。

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt


### 更新 Microsoft 對等組態

您可以使用下列 Cmdlet 來更新組態的任何部分。

		Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -Circuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MircosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MircosoftConfigCustomerAsn 23 -MircosoftConfigRoutingRegistryName "ARIN"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
		

### 刪除 Microsoft 對等

您可以執行下列 Cmdlet 來移除對等組態。

	Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## 後續步驟

接著，將 VNet 連結到 ExpressRoute 線路。當使用 Azure 資源管理員部署模式時，您可以使用[此範本](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)。我們目前正在進行 PowerShell 步驟。


-  如需 ExpressRoute 工作流程的詳細資訊，請參閱 [ExpressRoute 工作流程](expressroute-workflows.md)。

-  如需線路對等的詳細資訊，請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)。

-  如需使用虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

<!---HONumber=AcomDC_0121_2016-->