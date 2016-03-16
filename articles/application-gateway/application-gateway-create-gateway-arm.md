<properties
   pageTitle="使用 Azure 資源管理員建立、啟動或刪除應用程式閘道 | Microsoft Azure"
   description="本頁面提供使用 Azure 資源管理員建立、設定、啟動和刪除 Azure 應用程式閘道的指示。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>


# 使用 Azure 資源管理員建立、啟動或刪除應用程式閘道

Azure 應用程式閘道是第 7 層負載平衡器。不論是在雲端或內部部署中，此閘道均提供在不同伺服器之間進行容錯移轉及效能路由傳送 HTTP 要求。應用程式閘道具有下列應用程式傳遞功能：HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性和安全通訊端層 (SSL) 卸載。


> [AZURE.SELECTOR]
- [Azure 傳統 PowerShell 步驟](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager 範本](application-gateway-create-gateway-arm-template.md)


<BR>


本文將逐步引導您完成建立、設定、啟動及刪除應用程式閘道的步驟。


>[AZURE.IMPORTANT] 使用 Azure 資源之前，請務必了解 Azure 目前有「資源管理員」和「傳統」兩種部署模型。在使用任何 Azure 資源之前，請先確認您了解[部署模型和工具](../azure-classic-rm.md)。您可以按一下本文頂端的索引標籤，檢視不同工具的文件。本文件將討論如何使用 Azure 資源管理員建立應用程式閘道。若要使用傳統的版本，請移至[使用 PowerShell 建立應用程式閘道傳統部署](application-gateway-create-gateway.md)。



## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](https://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。
2. 您將建立應用程式閘道的虛擬網路和子網路。請確定沒有虛擬機器或是雲端部署正在使用子網路。應用程式閘道必須單獨位於虛擬網路子網路中。
3. 您要設定為使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是已指派公用 IP/VIP。

## 建立應用程式閘道需要什麼？


- **後端伺服器集區：**後端伺服器的 IP 位址清單。列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。
- **後端伺服器集區設定：**每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
- **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。 



## 建立新的應用程式閘道

使用「Azure 傳統」和「Azure 資源管理員」的差別，在於您建立應用程式閘道和需設定項目的順序。

使用資源管理員，組成應用程式閘道的所有項目都將個別設定，然後放在一起建立應用程式閘道資源。


以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組。
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP。
3. 建立應用程式閘道組態物件。
4. 建立應用程式閘道資源。


## 建立資源管理員的資源群組

確定您使用最新版本的 Azure PowerShell。如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

### 步驟 1
登入 Azure Login-AzureRmAccount

系統會提示使用您的認證進行驗證。<BR>
### 步驟 2
檢查帳戶的訂用帳戶。

		Get-AzureRmSubscription

### 步驟 3
選擇其中一個要使用的 Azure 訂用帳戶。<BR>

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### 步驟 4
建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。

>[AZURE.NOTE] 如果您需要設定應用程式閘道的自訂探查，請參閱[使用 PowerShell 建立具有自訂探查的應用程式閘道](application-gateway-create-probe-ps.md)。請參閱[自訂探查和健康狀況監視](application-gateway-probe-overview.md)以取得詳細資訊。



## 建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路。

### 步驟 1

指派用於建立虛擬網路的位址範圍 10.0.0.0/24 給子網路變數。

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 步驟 2

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 ("West US") 區域的 "appgw-rg" 資源群組中建立名為 "appgwvnet" 的虛擬網路。

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 步驟 3

針對建立應用程式閘道的後續步驟，指派子網路變數。

	$subnet=$vnet.Subnets[0]

## 建立前端組態的公用 IP 位址

在美國西部區域的 "appgw-rg" 資源群組中建立公用 IP 資源 "publicIP01"。

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## 建立應用程式閘道組態物件

您必須先設定所有組態項目，再建立應用程式閘道。下列步驟會建立應用程式閘道資源所需的組態項目。

### 步驟 1

建立名為 "gatewayIP01" 的應用程式閘道 IP 組態。當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。請記住，每個執行個體需要一個 IP 位址。


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 步驟 2

設定名為 "pool01" 的後端 IP 位址集區，其 IP 位址有 "134.170.185.46, 134.170.188.221,134.170.185.50"。這些 IP 位址將接收來自前端 IP 端點的網路流量。您要取代上述 IP 位址，加入您自己的應用程式 IP 位址端點。

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### 步驟 3

設定後端集區中負載平衡網路流量的應用程式閘道設定 "poolsetting01"。

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### 步驟 4

設定公用 IP 端點的前端 IP 連接埠 "frontendport01"。

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### 步驟 5

建立名為 "fipconfig01" 的前端 IP 組態，並將公用 IP 位址與前端 IP 組態產生關聯。

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### 步驟 6

建立名為 "listener01" 的接聽程式，並將前端連接埠與前端 IP 組態產生關聯。

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### 步驟 7

建立名為 "rule01" 的負載平衡器路由規則，設定負載平衡器的行為。

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 步驟 8

設定應用程式閘道的執行個體大小。

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  InstanceCount 的預設值是 2，最大值是 10。GatewaySize 的預設值是 Medium。您可以在 Standard\_Small、Standard\_Medium 和 Standard\_Large 之間選擇。

## 使用 New-AzureRmApplicationGateway 建立應用程式閘道

以上述步驟中的所有組態項目建立應用程式閘道。此範例中的應用程式閘道名為 "appgwtest"。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## 刪除應用程式閘道

若要刪除應用程式閘道，請遵循下列步驟：

1. 使用 **Stop-AzureRmApplicationGateway** Cmdlet 停止閘道。
2. 使用 **Remove-AzureRmApplicationGateway** Cmdlet 移除閘道。
3. 使用 **Get-AzureRmApplicationGateway** Cmdlet 確認已移除閘道。

### 步驟 1

取得應用程式閘道物件，並關聯至變數 "$getgw"。

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 步驟 2

使用 **Stop-AzureRmApplicationGateway** 停止應用程式閘道。

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


應用程式閘道處於「已停止」狀態之後，請使用 **Remove-AzureRmApplicationGateway** Cmdlet 移除服務。


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] **-force** 參數可用來隱藏移除確認訊息。


若要確認已移除服務，您可以使用 **Get-AzureRmApplicationGateway** Cmdlet。這不是必要步驟。


	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## 後續步驟

如果您想要設定 SSL 卸載，請參閱[設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與內部負載平衡器搭配使用，請參閱[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0302_2016-->