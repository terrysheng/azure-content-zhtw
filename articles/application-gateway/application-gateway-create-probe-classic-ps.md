<properties
   pageTitle="在傳統部署模型中使用 PowerShell 建立應用程式閘道的自訂探查 | Microsoft Azure"
   description="了解如何在傳統部署模型中使用 PowerShell 建立應用程式閘道的自訂探查"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# 使用 PowerShell 建立 Azure 應用程式閘道 (傳統) 的自訂探查


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md)。


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## 建立新的應用程式閘道

建立應用程式閘道：

1. 建立應用程式閘道資源。
2. 建立設定 XML 檔案或設定物件。
3. 認可新建立應用程式閘道資源的設定。

### 建立應用程式閘道資源

若要建立閘道，請使用 **New-AzureApplicationGateway** Cmdlet，並將值取代為您自己的值。請注意，此時不會開始為閘道計費。會在稍後的步驟中於成功啟動閘道之後開始計費。

下列範例會使用虛擬網路 (稱為 "testvnet1") 與子網路 (稱為 "subnet-1") 來建立新的應用程式閘道。


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*、*InstanceCount* 和 *GatewaySize* 為選用參數。


若要驗證已建立閘道，您可以使用 **Get-AzureApplicationGateway** Cmdlet。


	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]InstanceCount 的預設值是 2，最大值是 10。GatewaySize 的預設值是 Medium。您可以選擇 Small、Medium 和 Large。


 因為尚未啟動閘道，所以 *VirtualIPs* 和 *DnsName* 會顯示為空白。閘道處於執行中狀態之後，將會建立這些項目。

## 設定應用程式閘道

您可以使用 XML 或設定物件來設定應用程式閘道。

## 使用 XML 設定應用程式閘道

在下列範例中，您將使用 XML 檔案來設定所有的應用程式閘道設定，並將它們認可到應用程式閘道資源。

### 步驟 1  

將下列文字複製到 [記事本]。


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


編輯設定項目括號間的值。以 .xml 副檔名儲存檔案。

下列範例示範如何使用設定檔來設定應用程式閘道，使公用連接埠 80 上的 HTTP 流量達到負載平衡，並使用自訂探查將網路流量傳送到兩個 IP 位址之間的後端連接埠 80。

>[AZURE.IMPORTANT]通訊協定項目 Http 或 Https 會區分大小寫。


已新增用來設定自訂探查的新組態項目 <Probe>。

組態參數如下：

- **Name** - 自訂探查的參考名稱。
- **Protocol** - 所使用的通訊協定 (可能的值為 HTTP 或 HTTPS)。
- **Host** 和 **Path** - 應用程式閘道所叫用以判斷執行個體健全狀況的完整 URL 路徑。例如，若您擁有網站 http://contoso.com/，則可以為 "http://contoso.com/path/custompath.htm" 設定自訂探查，以便讓探查檢查有成功的 HTTP 回應。
- **Interval** - 以秒為單位設定探查間隔檢查。
- **Timeout** - 定義 HTTP 回應檢查的探查逾時。
- **UnhealthyThreshold** - 要將後端執行個體標記為*狀況不良*所需要的失敗 HTTP 回應數目。

<BackendHttpSettings> 組態中會參考探查名稱，以指派哪個後端集區會使用自訂探查設定。

## 將自訂探查組態新增至現有應用程式閘道

變更目前的應用程式閘道組態需要三個步驟：取得目前的 XML 組態檔、進行修改使其具有自訂探查，並以新的 XML 設定來設定應用程式閘道。

### 步驟 1

使用 get-AzureApplicationGatewayConfig 取得 XML 檔案。這會匯出要修改的組態 XML 以便新增探查設定。

	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### 步驟 2

在文字編輯器中開啟 XML 檔案。將 `<probe>` 區段新增到 `<frontendport>` 之後。

	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

在 XML 的 backendHttpSettings 區段中，新增如下列範例所示的探查名稱：

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

儲存 XML 檔案。


### 步驟 3

使用 **Set-AzureApplicationGatewayConfig** 以新的 XML 檔案更新應用程式閘道組態。這會以新的組態更新您的應用程式閘道。

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## 後續步驟

如果您想要設定安全通訊端層 (SSL) 卸載，請參閱[設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與內部負載平衡器搭配使用，請參閱[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

<!---HONumber=AcomDC_0114_2016-->