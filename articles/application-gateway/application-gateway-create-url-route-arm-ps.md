<properties
   pageTitle="使用 URL 路由規則建立應用程式閘道 |Microsoft Azure"
   description="本頁面提供使用 URL 路由規則建立和設定 Azure 應用程式閘道的指示。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# 使用 URL 型路由建立應用程式閘道 

URL 路徑型路由可讓您根據 Http 要求的 URL 路徑來關聯路由。它會檢查是否有路由連至針對應用程式閘道中的 URL 清單設定的後端集區，並將網路流量傳送至定義的後端集區。URL 型路由的常見用法是將不同內容類型的要求負載平衡至不同的後端伺服器集區。

URL 型路由會將新的規則類型引進應用程式閘道。應用程式閘道具有 2 種規則類型：基本和 PathBasedRouting。基本規則類型會針對後端集區提供循環配置資源服務，而 PathBasedRouting 除了循環配置資源發佈之外也會在選擇後端集區時將要求 URL 的路徑模式納入考慮。

>[AZURE.IMPORTANT] PathPattern：要比對的路徑模式清單。每個都必須以 / 開始，而且唯一允許出現 * 的地方是緊接在 '/' 之後的結尾處。傳送給路徑比對器的字串未在第一個 ? 或 # 之後包含任何文字，而這些字元是不允許的。

## 案例
在下列範例中，應用程式閘道會利用兩個後端伺服器集區來為 contoso.com 提供流量：視訊伺服器集區和映像伺服器集區。

對於 http://contoso.com/image* 的要求將會路由傳送到映像伺服器集區 (pool1)，而 http://contoso.com/video* 將會路由傳送到視訊伺服器集區 (pool2)。如果沒有任何路徑模式相符，則會選取預設的伺服器集區 (pool1)。

![URL 路由](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](https://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。
2. 您將建立應用程式閘道的虛擬網路和子網路。請確定沒有虛擬機器或是雲端部署正在使用子網路。應用程式閘道必須單獨位於虛擬網路子網路中。
3. 加入後端集區以使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是指派公用 IP/VIP。



## 建立應用程式閘道需要什麼？


- **後端伺服器集區：**後端伺服器的 IP 位址清單。列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。
- **後端伺服器集區設定：**每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。這些設定會繫結至集區，並套用至集區內所有伺服器。
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

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
或者，您也可以針對應用程式閘道建立資源群組的標記：
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

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

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 步驟 3
針對建立應用程式閘道的後續步驟，指派子網路變數。

	$subnet=$vnet.Subnets[0]

## 建立前端組態的公用 IP 位址
在美國西部區域的 "appgw-rg" 資源群組中建立公用 IP 資源 "publicIP01"。

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

在服務啟動時，系統會將 IP 位址指派至應用程式閘道。

## 建立應用程式閘道組態

您必須先設定所有組態項目，再建立應用程式閘道。下列步驟會建立應用程式閘道資源所需的組態項目。

### 步驟 1
建立名為 "gatewayIP01" 的應用程式閘道 IP 組態。當應用程式閘道啟動時，它會從設定的子網路取得 IP 位址，再將網路流量路由傳送到後端 IP 集區中的 IP 位址。請記住，每個執行個體需要一個 IP 位址。


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 步驟 2
設定名為 "pool01" 和 "pool2" 的後端 IP 位址集區，"pool1" 的 IP 位址為 "134.170.185.46、134.170.188.221、134.170.185.50"，而 "pool2" 的 IP 位址為 "134.170.186.46、134.170.189.221、134.170.186.50"。


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

此範例中會有兩個後端集區，根據 URL 路徑路由傳送網路流量。有一個集區會接收來自 URL 路徑 "/video" 的流量，而另一個集區將會接收來自路徑 "/image" 的流量。您必須取代上述的 IP 位址，以加入您自己的應用程式 IP 位址端點。

### 步驟 3
針對後端集區中負載平衡的網路流量，設定應用程式閘道設定 "poolsetting01" 和 "poolsetting02"。在此範例中，您會針對後端集區設定不同的後端集區設定。每個後端集區都可以有它自己的後端集區設定。

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### 步驟 4
利用公用 IP 端點設定前端 IP。

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### 步驟 5 
設定應用程式閘道的前端連接埠。

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### 步驟 6
設定接聽程式。這個步驟會針對用來接收連入網路流量的公用 IP 位址和連接埠設定接聽程式。
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### 步驟 7 
設定後端集區的 URL 規則路徑。這個步驟會設定應用程式閘道用來定義 URL 路徑間對應的相對路徑，而且將指派其中的後端集區來處理連入流量。

下列範例會建立兩個規則：一個適用於將流量路由傳送到後端 "pool1" 的 "/image/" 路徑，另一個則適用於將流量路由傳送到後端 "pool2" 的 "/video/" 路徑。
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting01

如果路徑不符合任何預先定義的路徑規則，規則路徑對應組態也會設定預設的後端位址集區。

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### 步驟 8 
建立規則設定。這個步驟會設定應用程式閘道來使用 URL 路徑型路由。

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### 步驟 9 
設定執行個體數目和應用程式閘道的大小。

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## 建立應用程式閘道
利用上述步驟中的所有組態物件來建立應用程式閘道。

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## 取得應用程式閘道
	$getgw =  Get-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname

<!---HONumber=AcomDC_0224_2016-->