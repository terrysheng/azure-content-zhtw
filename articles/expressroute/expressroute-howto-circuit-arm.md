<properties
   pageTitle="使用資源管理員及 PowerShell 建立和修改 ExpressRoute 線路 | Microsoft Azure"
   description="本文說明如何建立和佈建 ExpressRoute 線路。本文也會示範如何檢查線路、對它進行更新，或是對它進行刪除及取消佈建。"
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
   ms.date="12/04/2015"
   ms.author="cherylmc"/>

# 使用資源管理員及 PowerShell 建立和修改 ExpressRoute 線路

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

本文說明如何使用 Windows PowerShell Cmdlet 和 Azure Resource Manager 部署模型建立 Azure ExpressRoute 線路。下列步驟也會示範如何檢查線路的狀態、對它進行更新，或是對它進行刪除及取消佈建。

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 組態必要條件

若要建立 ExpressRoute 線路，您必須：

- 取得最新版的 Azure PowerShell 模組 (版本 1.0 或更新版本)。遵循[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 頁面上的指示，來取得如何設定您的電腦以使用 PowerShell 模組的逐步指引。
- 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)頁面和[工作流程](expressroute-workflows.md)頁面。

## 建立和佈建 ExpressRoute 線路

**步驟 1：匯入 ExpressRoute 的 PowerShell 模組。**

若要開始使用 ExpressRoute Cmdlet，您必須從 [PowerShell 資源庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式，並將資源管理員模組匯入至 PowerShell 工作階段。您將以系統管理員身分執行 PowerShell。

```
Install-Module AzureRM

Install-AzureRM
```

匯入已知語意版本範圍內所有的 AzureRM 模組：

```
Import-AzureRM
```

您也可以匯入已知語意版本範圍內選取的模組：

```
Import-Module AzureRM.Network
```

登入您的帳戶：

```
Login-AzureRmAccount
```

選取您想要建立 ExpressRoute 線路的訂用帳戶：

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**步驟 2：取得支援的提供者、位置和頻寬清單。**

建立 ExpressRoute 線路之前，您需要有連線提供者、支援的位置和頻寬選項等清單。PowerShell 的 *Get-AzureRmExpressRouteServiceProvider* Cmdlet 會傳回此資訊，在稍後的步驟中將會用到。

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

請檢查是否列出您的連線服務提供者。請記下下列資訊，因為您稍後在建立線路時將會用到：

- 名稱
- PeeringLocations
- BandwidthsOffered

您現在已經準備好建立 ExpressRoute 線路。

**步驟 3：建立 ExpressRoute 線路。**

若您還沒有資源群組，您必須在建立 ExpressRoute 線路之前建立一個。您可以執行下列命令來這麼做：

```
New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"
```

以下示範如何透過矽谷的 Equinix 建立 200-Mbps ExpressRoute 線路。如果您使用不同的提供者和不同的設定，請在您提出要求時替換成該資訊。下列是新服務金鑰的要求範例：

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

請確定您指定正確的 SKU 層和 SKU 系列：

- SKU 層會判斷 ExpressRoute 標準或 ExpressRoute 高階附加元件是否啟用。您可以指定 [標準] 來取得標準 SKU 或 [進階] 來取得進階附加元件。
- SKU 系列決定計費類型。您可以針對計量付費數據傳輸方案選取 [metereddata]，針對無限行動數據方案選取 [unlimiteddata]。**注意：**在您建立線路之後，您將無法變更計費類型。

回應包含服務金鑰。您可以執行下列命令來取得所有參數的詳細描述：

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**步驟 4：列出所有 ExpressRoute 線路。**

若要取得您已建立之所有 ExpressRoute 線路的清單，您可以執行 *Get-AzureRmExpressRouteCircuit* 命令：

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

回應看起來將會如下列範例所示：

```
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
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                      "ServiceProviderName": "Equinix",
                                      "PeeringLocation": "Silicon Valley",
                                      "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

您可以透過使用 *Get-AzureRmExpressRouteCircuit* Cmdlet，隨時擷取這項資訊。執行呼叫時，若未指定任何參數，將會列出所有線路。*ServiceKey* 欄位會列出您的服務金鑰。

```
Get-AzureRmExpressRouteCircuit
```

回應看起來將會如下列範例所示：

```
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
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Equinix",
                                     "PeeringLocation": "Silicon Valley",
                                     "BandwidthInMbps": 200
   		                           }
ServiceKey                       : **************************************
Peerings                         : []
```

您可以執行下列命令來取得所有參數的詳細描述：

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**步驟 5：將服務金鑰傳送給連線提供者以進行佈建。**

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState* 提供目前在服務提供者端的佈建狀態，Status 提供 Microsoft 端的狀態。若要能夠使用 ExpressRoute 線路，它必須處於下列狀態：

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

當連線提供者正在為您啟用線路時，線路會變更為下列狀態：

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**步驟 6：定期檢查線路金鑰的情況和狀態。**

檢查線路金鑰的情況和狀態將能讓您得知提供者是否已啟用您的線路。設定線路之後，*ServiceProviderProvisioningState* 會顯示為 *Provisioned*，如下列範例所示：

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

回應看起來將會如下列範例所示：

```
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Name = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

請注意，此操作若要成功，您必須取消連結 ExpressRoute 線路的所有虛擬網路，如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。

如果已啟用 ExpressRoute 線路服務提供者佈建狀態，狀態會從已啟用狀態變成 [正在停用]。您必須與服務提供者一起合作，取消佈建他們那邊的線路。Microsoft 將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。

若服務提供者在您執行上述 Cmdlet 之前已取消佈建線路 (服務提供者佈建狀態設定為 [未佈建])，Microsoft 將會取消佈建線路並停止向您收費。

## 後續步驟

建立好線路後，請務必執行下列作業：

- [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-arm.md)
- [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0302_2016-->