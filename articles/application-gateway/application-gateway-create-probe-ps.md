<properties
   pageTitle="在資源管理員中使用 PowerShell 建立應用程式閘道的自訂探查 | Microsoft Azure"
   description="了解如何在資源管理員中使用 PowerShell 建立應用程式閘道的自訂探查"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="joaoma" />

# 使用 Azure 資源管理員的 PowerShell 建立 Azure 應用程式閘道的自訂探查

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md)。


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### 步驟 1

使用 Login-AzureRmAccount 進行驗證。

	Login-AzureRmAccount

### 步驟 2

檢查帳戶的訂用帳戶。

		get-AzureRmSubscription

系統會提示使用您的認證進行驗證。<BR>

### 步驟 3

選擇其中一個要使用的 Azure 訂用帳戶。<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。

## 建立應用程式閘道的虛擬網路和子網路

下列步驟會建立應用程式閘道的虛擬網路和子網路。

### 步驟 1


將位址範圍 10.0.0.0/24 指派給用於建立虛擬網路的子網路變數。

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


## 使用自訂探查建立應用程式閘道組態物件

您必須先設定所有組態項目，再建立應用程式閘道。下列步驟會建立應用程式閘道資源所需的組態項目。


### 步驟 1

建立名為 "gatewayIP01" 的應用程式閘道 IP 組態。當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。請記住，每個執行個體需要一個 IP 位址。

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 步驟 2


設定名為 "pool01" 的後端 IP 位址集區，其 IP 位址有 "134.170.185.46, 134.170.188.221,134.170.185.50"。這些 IP 位址將接收來自前端 IP 端點的網路流量。您要取代上述 IP 位址，加入您自己的應用程式 IP 位址端點。

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### 步驟 3


此步驟會設定自訂探查。

所使用的參數如下：

- **-Interval** - 以秒為單位設定探查間隔檢查。
- **-Timeout** - 定義 HTTP 回應檢查的探查逾時。
- **-Hostname 和 -path** - 應用程式閘道所叫用以判斷執行個體健全狀況的完整 URL 路徑。例如，若您擁有網站 http://contoso.com/，則可以為 "http://contoso.com/path/custompath.htm" 設定自訂探查，以便讓探查檢查有成功的 HTTP 回應。
- **-UnhealthyThreshold** - 要將後端執行個體標記為*狀況不良*所需要的失敗 HTTP 回應數目。

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### 步驟 4

設定後端集區中流量的應用程式閘道設定 "poolsetting01"。此步驟中也有適用於應用程式閘道要求之後端集區回應的逾時組態。當後端回應觸及逾時限制時，應用程式閘道將會取消要求。這與僅適用於探查檢查之後端回應的探查逾時不同。

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### 步驟 5

設定公用 IP 端點的前端 IP 連接埠 "frontendport01"。

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### 步驟 6

建立名為 "fipconfig01" 的前端 IP 組態，並將公用 IP 位址與前端 IP 組態產生關聯。


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### 步驟 7

建立名為 "listener01" 的接聽程式，並將前端連接埠與前端 IP 組態產生關聯。

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### 步驟 8

建立名為 "rule01" 的負載平衡器路由規則，設定負載平衡器的行為。

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 步驟 9

設定應用程式閘道的執行個體大小。

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  InstanceCount 的預設值是 2，最大值是 10。GatewaySize 的預設值是 Medium。您可以在 Standard\_Small、Standard\_Medium 和 Standard\_Large 之間選擇。

## 使用 New-AzureRmApplicationGateway 建立應用程式閘道

以上述步驟中的所有組態項目建立應用程式閘道。此範例中的應用程式閘道名為 "appgwtest"。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## 將探查新增至現有應用程式閘道

要將自訂探查新增至現有應用程式閘道需要四個步驟。

### 步驟 1

使用 **Get-AzureRmApplicationGateway** 將應用程式閘道資源載入至 PowerShell 變數。

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 步驟 2

將探查新增至現有閘道組態。

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


範例中會將自訂探查設定為每 30 秒檢查一次 URL 路徑 contoso.com/path/custompath.htm。所設定的逾時臨界值是 120 秒，最多只能有 8 個失敗的探查要求。

### 步驟 3

使用 **-Set-AzureRmApplicationGatewayBackendHttpSettings** 將探查加入至後端集區設定組態和逾時。


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### 步驟 4

使用 **Set-AzureRmApplicationGateway** 將組態儲存至應用程式閘道。

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## 從現有應用程式閘道中移除探查

以下是從現有應用程式閘道中移除自訂探查的步驟。

### 步驟 1

使用 **Get-AzureRmApplicationGateway** 將應用程式閘道資源載入至 PowerShell 變數。

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### 步驟 2

使用 **Remove-AzureRmApplicationGatewayProbeConfig** 將探查設定從應用程式閘道移除。

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### 步驟 3

使用 **-Set-AzureRmApplicationGatewayBackendHttpSettings** 更新後端集區設定，將探查與逾時設定移除。


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### 步驟 4

使用 **Set-AzureRmApplicationGateway** 將組態儲存至應用程式閘道。

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0323_2016-->