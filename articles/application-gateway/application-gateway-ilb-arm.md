<properties 
   pageTitle="使用 Azure 資源管理員建立及設定搭配內部負載平衡器 (ILB) 的應用程式閘道器 | Microsoft Azure"
   description="本頁面提供使用 Azure 資源管理員建立、設定、啟動、刪除搭配內部負載平衡器 (ILB) 的 Azure 應用程式閘道器的指示。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/07/2015"
   ms.author="joaoma"/>


# 使用 Azure 資源管理員建立搭配內部負載平衡器 (ILB) 的應用程式閘道器

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)

可以使用面對網際網路的 VIP 或不會對網際網路公開的內部端點 (也稱為內部負載平衡器 (ILB) 端點) 來設定應用程式閘道。使用 ILB 設定閘道適合不會對網際網路公開的內部企業營運應用程式。對於位在不會對網際網路公開的安全性界限中的多層式應用程式內的服務/階層也很有用的，但仍需要循環配置資源負載散發、工作階段綁定或 SSL 終止。本文將逐步引導您完成使用 ILB 設定應用程式閘道。

## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。
2. 您將建立應用程式閘道器的虛擬網路和子網路。請確定沒有虛擬機器或是雲端部署正在使用子網路。應用程式閘道必須單獨在虛擬網路子網路中。
3. 您將要設定為使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是指派公用 IP/VIP。

## 建立應用程式閘道需要什麼？
 

- **後端伺服器集區：**後端伺服器的 IP 位址清單。列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。 
- **後端伺服器集區設定：**每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。 
- **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該重新導向至哪個後端伺服器集區。目前只支援「基本」規則。「基本」規則是循環配置資源的負載分散。


 
## 建立新的應用程式閘道

使用「Azure 傳統」和「Azure 資源管理員」的差別，在於您建立應用程式閘道器和需設定項目的順序。使用資源管理員，組成應用程式閘道器的所有項目都將個別設定，然後放在一起建立應用程式閘道器資源。


以下是建立應用程式閘道器所需的步驟：

1. 建立資源管理員的資源群組
2. 建立應用程式閘道器的虛擬網路和子網路
3. 建立應用程式閘道器組態物件
4. 建立應用程式閘道資源


## 建立資源管理員的資源群組

請確定您切換 PowerShell 模式來使用 ARM Cmdlet。可在〈[搭配使用 Windows PowerShell 與資源管理員](powershell-azure-resource-manager.md)〉取得詳細資訊。

### 步驟 1

    Switch-AzureMode -Name AzureResourceManager

### 步驟 2

登入您的 Azure 帳戶。


    Add-AzureAccount

系統會提示使用您的認證進行驗證。


### 步驟 3

選擇要使用哪一個 Azure 訂用帳戶。

    Select-AzureSubscription -SubscriptionName "MySubscription"

若要查看可用訂用帳戶的清單，請使用 ‘Get-AzureSubscription’ Cmdlet。


### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    New-AzureResourceGroup -Name appgw-rg -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。請確定所有用來建立應用程式閘道器的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-rg" 的資源群組，且位置為美國西部 ("West US")。

## 建立應用程式閘道器的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路：

### 步驟 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

指派用於建立虛擬網路的位址範圍 10.0.0.0/24 給子網路變數 subnet

### 步驟 2
	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 ("West US") 區域的 "appw-rg" 資源群組中建立名為 "appgwvnet" 的虛擬網路
	

## 建立應用程式閘道器組態物件

### 步驟 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

建立名為 "gatewayIP01" 的應用程式閘道器 IP 組態。當應用程式閘道器啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。請記住，每個執行個體需要一個 IP 位址。
 
### 步驟 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.10,10.0.0.11,10.0.0.12

這個步驟會設定名為 "pool01" 的後端 IP 位址集區，其 IP 位址有 "10.0.0.10,10.0.0.11, 10.0.0.12"。這些 IP 位址將接收來自前端 IP 端點的網路流量。您要取代上述 IP 位址，加入您自己的應用程式 IP 位址端點。

### 步驟 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

設定後端集區中負載平衡網路流量的應用程式閘道設定 "poolsetting01"。

### 步驟 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

設定 ILB 的前端 IP 連接埠，名為 "frontendport01"。

### 步驟 5

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

建立名為 "fipconfig01" 的前端 IP 組態，並與目前虛擬網路子網路的私人 IP 產生關聯。

### 步驟 6

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

建立名為 "listener01" 的接聽程式，並將前端連接埠與前端 IP 組態產生關聯。

### 步驟 7 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

建立名為 "rule01" 的負載平衡器路由規則，設定負載平衡器的行為。

### 步驟 8

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

設定應用程式閘道器的執行個體大小

>[AZURE.NOTE]*InstanceCount* 的預設值是 2，最大值是 10。*GatewaySize* 的預設值是 Medium。您可以在 Standard\_Small、Standard\_Medium 和 Standard\_Large 之間選擇。

## 使用 New-AzureApplicationGateway 建立應用程式閘道器

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

從上述步驟的所有組態項目建立應用程式閘道器。範例中的應用程式閘道器名為 "appgwtest"。




## 啟動閘道

閘道器設定完成後，使用 `Start-AzureApplicationGateway` Cmdlet 來啟動閘道器。成功啟動閘道之後，會開始應用程式閘道計費。


**注意：**`Start-AzureApplicationGateway` Cmdlet 最多可能需要 15 到 20 分鐘的時間才能完成。

在以下範例中，應用程式閘道器名為 "appgwtest"，資源群組為 "appgw-rg"：


### 步驟 1

取得應用程式閘道器物件，並關聯至變數 "$getgw"：
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 步驟 2
	 
使用 `Start-AzureApplicationGateway` 啟動應用程式閘道器：

	PS C:\> Start-AzureApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## 確認應用程式閘道器狀態

使用 `Get-AzureApplicationGateway` Cmdlet 檢查閘道器狀態。如果 *Start-AzureApplicationGateway* 在上一個步驟成功，則狀態應該為 [正在執行]，且 Vip 和 DnsName 應該具備有效的項目。

這個範例示範已啟動、正在執行且準備好將流量傳送到 `http://<generated-dns-name>.cloudapp.net` 的應用程式閘道器。

	PS C:\> Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : appgwvnet 
	Subnets       : {Subnet01} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## 刪除應用程式閘道器

若要刪除應用程式閘道，您需要依序執行下列動作：

1. 使用 `Stop-AzureApplicationGateway` Cmdlet 停止閘道器。 
2. 使用 `Remove-AzureApplicationGateway` Cmdlet 移除閘道器。
3. 使用 `Get-AzureApplicationGateway` Cmdlet 確認已移除閘道器。

這個範例的第一行顯示 `Stop-AzureApplicationGateway` Cmdlet，後面接著輸出。

### 步驟 1

取得應用程式閘道器物件，並關聯至變數 "$getgw"：
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### 步驟 2
	 
使用 `Stop-AzureApplicationGateway` 停止應用程式閘道器：

	PS C:\> Stop-AzureApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

一旦應用程式閘道器處於 [已停止] 狀態，使用 `Remove-AzureApplicationGateway` Cmdlet 移除服務。


	PS C:\> Remove-AzureApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]選擇性的 "-force" 參數可用來隱藏移除確認訊息
>

若要確認已移除服務，可以使用 `Get-AzureApplicationGateway` Cmdlet。這不是必要步驟。


	PS C:\>Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName app-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## 後續步驟

如果您想要設定 SSL 卸載，請參閱〈[設定應用程式閘道器以進行 SSL 卸載](application-gateway-ssl.md)〉。

如果您想要將應用程式閘道器設為與 ILB 搭配使用，請參閱〈[建立搭配內部負載平衡器 (ILB) 的應用程式閘道器](application-gateway-ilb.md)〉。

如果您想取得一般負載平衡選項的詳細資訊，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO8-->