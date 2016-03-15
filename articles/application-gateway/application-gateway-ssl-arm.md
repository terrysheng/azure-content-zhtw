<properties
   pageTitle="使用 Azure 資源管理員設定適用於 SSL 的應用程式閘道 | Microsoft Azure"
   description="本頁面提供使用 Azure 資源管理員範本，建立具有 SSL 卸載之應用程式閘道的指示。"
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
   ms.date="03/03/2016"
   ms.author="joaoma"/>

# 使用 Azure 資源管理員設定適用於 SSL 的應用程式閘道

> [AZURE.SELECTOR]
-[Azure Classic PowerShell](application-gateway-ssl.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)

 Azure 應用程式閘道可以設定為在閘道終止安全通訊端層 (SSL) 工作階段，以避免 Web 伺服陣列發生高成本的 SSL 解密工作。SSL 卸載也可以簡化 Web 應用程式的前端伺服器設定和管理。


## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](https://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。
2. 您將建立應用程式閘道的虛擬網路和子網路。請確定沒有虛擬機器或是雲端部署正在使用子網路。應用程式閘道必須單獨在虛擬網路子網路中。
3. 您要設定為使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是已指派公用 IP/VIP。

## 建立應用程式閘道需要什麼？


- **後端伺服器集區：**後端伺服器的 IP 位址清單。列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。
- **後端伺服器集區設定：**每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
- **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。目前只支援*基本*規則。「基本」規則是循環配置資源的負載分配。

**其他組態注意事項**

針對 SSL 憑證組態，**HttpListener** 中的通訊協定應該變更為 Https (區分大小寫)。需要將 **SslCertificate** 元素加入至 **HttpListener**，並針對 SSL 憑證設定變數值。前端連接埠應該更新為 443。

**若要啟用以 Cookie 為基礎的同質**：您可以設定應用程式閘道，以確保來自用戶端工作階段的要求一律會導向至 Web 伺服陣列中的相同 VM。這是透過插入允許閘道適當導向流量的工作階段 Cookie 來完成。若要啟用以 Cookie 為基礎的同質，請在 **BackendHttpSettings** 元素中將 **CookieBasedAffinity** 設定為 *Enabled*。


## 建立新的應用程式閘道

使用「Azure 傳統」部署模型和「Azure 資源管理員」的差別，在於您建立應用程式閘道和需設定項目的順序。

使用資源管理員，組成應用程式閘道的所有項目都將個別設定，然後放在一起建立應用程式閘道資源。


以下是建立應用程式閘道所需的步驟：

1. 建立資源管理員的資源群組
2. 建立應用程式閘道的虛擬網路、子網路和公用 IP
3. 建立應用程式閘道組態物件
4. 建立應用程式閘道資源


## 建立資源管理員的資源群組

請確定您切換 PowerShell 模式以使用 Azure 資源管理員 Cmdlet。如需詳細資訊，可在[搭配使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)取得。

### 步驟 1

		PS C:\> Login-AzureRmAccount



### 步驟 2

檢查帳戶的訂用帳戶。

		PS C:\> get-AzureRmSubscription

系統會提示使用您的認證進行驗證。<BR>

### 步驟 3

選擇其中一個要使用的 Azure 訂用帳戶。<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 步驟 4

建立新的資源群組 (若使用現有的資源群組，請略過此步驟)。

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。請確定所有用來建立應用程式閘道的命令都使用同一個資源群組。

在上述範例中，我們建立名為 "appgw-RG" 的資源群組，且位置為美國西部 ("West US")。

## 建立應用程式閘道的虛擬網路和子網路

下面的範例說明如何使用資源管理員建立虛擬網路：

### 步驟 1

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

這會將位址範圍 10.0.0.0/24 指派給用於建立虛擬網路的子網路變數。

### 步驟 2
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

這會使用前置詞 10.0.0.0/16 搭配子網路 10.0.0.0/24，在美國西部 ("West US") 區域的 "appgw-rg" 資源群組中建立名為 "appgwvnet" 的虛擬網路。

### 步驟 3

	$subnet=$vnet.Subnets[0]

這會將子網路物件指派給下一個步驟的變數 $subnet。

## 建立前端組態的公用 IP 位址

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

這會在美國西部區域的 "appgw-rg" 資源群組中建立公用 IP 資源 "publicIP01"。


## 建立應用程式閘道組態物件

### 步驟 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

這會建立名為 "gatewayIP01" 的應用程式閘道 IP 組態。當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。請記住，每個執行個體需要一個 IP 位址。

### 步驟 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

這會設定名為 "pool01" 的後端 IP 位址集區，其 IP 位址有 "134.170.185.46, 134.170.188.221,134.170.185.50"。 這些 IP 位址將接收來自前端 IP 端點的網路流量。以您的 Web 應用程式端點的 IP 位址取代上述範例中的 IP 位址。

### 步驟 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

這會設定後端集區中負載平衡網路流量的應用程式閘道設定 "poolsetting01"。

### 步驟 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

這會設定公用 IP 端點的前端 IP 連接埠 "frontendport01"。

### 步驟 5

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

這會設定 SSL 連接所使用的憑證。憑證必須是 .pfx 格式，而密碼則必須介於 4 到 12 個字元。

### 步驟 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

這會建立名為 "fipconfig01" 的前端 IP 組態，並將公用 IP 位址與前端 IP 組態產生關聯。

### 步驟 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


這會建立名為 "listener01" 的接聽程式，並將前端連接埠與前端 IP 組態和憑證產生關聯。

### 步驟 8

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

這會建立名為 "rule01" 的負載平衡器路由規則，設定負載平衡器的行為。

### 步驟 9

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

這會設定應用程式閘道的執行個體大小。

>[AZURE.NOTE]  InstanceCount 的預設值是 2，最大值是 10。GatewaySize 的預設值是 Medium。您可以在 Standard\_Small、Standard\_Medium 和 Standard\_Large 之間選擇。

## 使用 New-AzureApplicationGateway 建立應用程式閘道

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

這會使用上述步驟中的所有組態項目建立應用程式閘道。範例中的應用程式閘道名為 "appgwtest"。

## 後續步驟

如果您想要將應用程式閘道設為與內部負載平衡器 (ILB) 搭配使用，請參閱[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0309_2016-->