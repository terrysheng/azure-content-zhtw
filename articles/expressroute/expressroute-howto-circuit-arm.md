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
   ms.date="03/03/2016"
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
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
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

```

**步驟 7。建立路由組態。**

請參閱 [ExpressRoute 線路路由組態 (建立和修改線路對等)](expressroute-howto-routing-arm.md) 頁面，取得逐步指示。

**步驟 8將虛擬網路連結到 ExpressRoute 電路。**

接下來，將虛擬網路連結到 ExpressRoute 線路。當使用資源管理員部署模式時，您可以使用 [此範本](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)。我們目前正在準備使用 PowerShell 完成此動作的步驟。

## 取得 ExpressRoute 線路的狀態

您可以透過使用 Get-AzureRmExpressRouteCircuit Cmdlet，隨時擷取這項資訊。執行呼叫時，若未指定任何參數，將會列出所有線路。

```
Get-AzureRmExpressRouteCircuit
```

回應將如以下範例所示：

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
```

您可以透過將資源群組名稱和線路名稱做為參數傳遞至呼叫，取得特定 ExpressRoute 線路的資訊：

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
```

您可以執行下列命令來取得所有參數的詳細描述：

```
get-help get-azurededicatedcircuit -detailed
```

## 修改 ExpressRoute 線路

您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。

您可以執行下列工作，而無需中途停機：

- 啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。
- 增加 ExpressRoute 線路的頻寬。

如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面。

### 啟用 ExpressRoute 進階附加元件

您可以使用下列 PowerShell 程式碼片段，為現有的線路啟用 ExpressRoute 進階附加元件：

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

線路現在將啟用 ExpressRoute 進階附加功能。請注意，順利執行命令後，Microsoft 會開始向您收取進階附加元件功能的費用。

### 停用 ExpressRoute 進階附加元件

您可以使用下列 PowerShell Cmdlet，為現有的線路停用 ExpressRoute 進階附加元件：

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

線路現在已經停用進階附加元件。

請注意，如果您使用的資源超出標準線路所允許的數量，這項作業可能會失敗。

- 從進階降級為標準之前，您必須確定連結至線路的虛擬網路數目小於 10。如果您不這樣做，更新要求就會失敗，且 Microsoft 會以進階費率計費。
- 您必須取消連結其他地理政治區域中的所有虛擬網路。如果您不這樣做，更新要求就會失敗，且 Microsoft 會以進階費率計費。
- 就私用對等設定而言，路由表必須少於 4000 個路由。如果路由表大小超過 4000 個路由，BGP 工作階段將會中斷，而且在通告的首碼數目降到 4000 以下之前不會重新啟用。

### 更新 ExpressRoute 線路頻寬

請查閱 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面，以取得提供者支援的頻寬選項。您可以挑選任何比現有線路規模還大的大小。一旦決定需要的大小後，可以使用下列命令來重新調整線路大小。

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

我們會在 Microsoft 端調整線路的大小。接下來，您必須連絡連線提供者，將他們的設定更新為符合這項變更。通知之後，Microsoft 會開始以更新的頻寬選項計費。

**重要**：降低 ExpressRoute 線路的頻寬時必須中斷運作。頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。

## 刪除和佈建 ExpressRoute 線路

您可以執行下列命令來刪除 ExpressRoute 線路：

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

請注意，此操作若要成功，您必須取消連結 ExpressRoute 線路的所有虛擬網路，如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。

如果已啟用 ExpressRoute 線路服務提供者佈建狀態，狀態會從已啟用狀態變成 [正在停用]。您必須與服務提供者一起合作，取消佈建他們那邊的線路。Microsoft 將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。

若服務提供者在您執行上述 Cmdlet 之前已取消佈建線路 (服務提供者佈建狀態設定為 [未佈建])，Microsoft 將會取消佈建線路並停止向您收費。

## 後續步驟

建立好線路後，請務必執行下列作業：

- [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-arm.md)
- [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0309_2016-->