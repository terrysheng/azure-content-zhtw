<properties 
   pageTitle="將虛擬網路連結到 ExpressRoute 循環 |Microsoft Azure"
   description="本文件提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 循環的概觀。"
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="ganesr" />

# 將虛擬網路連結到 ExpressRoute 電路

> [AZURE.SELECTOR]
- [PowerShell - 傳統](expressroute-howto-linkvnet-classic.md)
- [PowerShell - 資源管理員](expressroute-howto-linkvnet-arm.md)
- [範本 - 資源管理員](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

本文提供如何將虛擬網路 (Vnet) 連結到 ExpressRoute 線路的概觀。虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。本文適用於使用資源管理員部署模型部署的 Vnet。如果您想要連結使用傳統部署模型部署的虛擬網路，請參閱[將虛擬網路連結到 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 組態必要條件

- 您需要最新版的 Azure PowerShell 模組 (版本 1.0 或更新版本)。如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。 
- 開始設定之前，請確定您已經檢閱過[必要條件](expressroute-prerequisites.md)頁面、[路由需求](expressroute-routing.md)頁面和[工作流程](expressroute-workflows.md)頁面。
- 您必須擁有作用中的 ExpressRoute 線路。 
	- 遵循指示來[建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)，並由您的連線提供者來啟用該線路。 
	- 確定您已針對循環設定了 Azure 私用對等。請參閱[設定路由](expressroute-howto-routing-arm.md)一文，以取得路由指示。 
	- Azure 私用對等必須設定，且在您的網路與 Microsoft 之間的 BGP 對等必須為您啟用端對端連線。
	- 您必須有已建立且完整佈建的虛擬網路和虛擬網路閘道。請依照指示來建立 [VPN 閘道](../articles/vpn-gateway-create-site-to-site-rm-powershell.md)，但務必要使用 `-GatewayType ExpressRoute`。

您最多可以將 10 個虛擬網路連結至 ExpressRoute 電路。所有的 ExpressRoute 循環都必須位於同一個地理區域。如果您已啟用 ExpressRoute 高階附加元件，則可將更大量的虛擬網路連結到您的 ExpressRoute 循環。如需進階附加元件的詳細資訊，請參閱[常見問題集](expressroute-faqs.md)。

## 將同一個 Azure 訂用帳戶中的 VNet 連結到 ExpressRoute 電路

您可以使用下列 Cmdlet，將虛擬網路閘道連結到 ExpressRoute 電路。執行 Cmdlet 之前，請確定您已建立虛擬網路閘道，並準備好進行連結。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## 將不同 Azure 訂用帳戶中的虛擬網路連結到 ExpressRoute 電路

多個訂用帳戶間可共用一個 ExpressRoute 循環。此圖顯示簡單的圖解，示範如何在多個訂用帳戶間共用 ExpressRoute 循環的方式。大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。組織內的每個部門都可以使用自己的訂用帳戶來部署它們的服務，但可共用單一 ExpressRoute 循環，以連接回內部部署網路。單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。組織內的其他訂用帳戶可以使用 ExpressRoute 電路。

>[AZURE.NOTE] ExpressRoute 循環擁有者需支付專用循環的連線和頻寬費用。所有虛擬網路都會共用相同的頻寬。

![跨訂用帳戶的連線能力](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### 系統管理

「電路擁有者」是 ExpressRoute 電路資源的已授權進階使用者。電路擁有者能夠建立可由「電路使用者」兌換的授權。「電路使用者」是虛擬網路閘道的擁有者 (它們會與 ExpressRoute 電路位於不同的訂用帳戶內)。「電路使用者」可以兌換授權 (每個虛擬網路一個授權)。

「電路擁有者」能夠隨時修改及撤銷授權。如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結連線均會被刪除。

### 循環擁有者作業 

#### 建立授權
	
電路擁有者會建立授權。這樣即會建立授權金鑰，讓電路使用者可用來將其虛擬網路閘道連接到 ExpressRoute 電路。一個授權僅適用於一個連線。

下列 Cmdlet 程式碼片段示範如何建立授權。

		Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
		$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

		$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization1"
		

對此動作的回應將包含授權金鑰和狀態

		Name                   : MyAuthorization1
		Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
		Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
		AuthorizationKey       : ####################################
		AuthorizationUseStatus : Available
		ProvisioningState      : Succeeded

		

#### 檢閱授權

循環擁有者可以藉由執行下列 Cmdlet，來檢閱特定循環上發出的所有授權。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit
	

#### 新增授權

電路擁有者可以使用下列 Cmdlet 來新增授權。

	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	Add-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit -Name "MyAuthorization2"
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
	
	$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
	$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -Circuit $circuit

	
#### 刪除授權

循環擁有者可以使用下列 Cmdlet 來撤銷/刪除使用者的授權。

	Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -Circuit $circuit
	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit	

### 循環使用者作業

電路使用者需要具備對等識別碼以及電路擁有者所提供的授權金鑰。電路金鑰看起來與下列內容類似：


授權金鑰是 GUID。

#### 兌換連線授權

循環使用者可以執行下列 Cmdlet 來兌換連結授權。

	$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"	
	$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### 釋出連線授權

您可以藉由刪除將 ExpressRoute 電路連結到虛擬網路的連線來釋出授權。

## 後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

<!---HONumber=AcomDC_0309_2016-->