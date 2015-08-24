<properties 
   pageTitle="建立、啟動或刪除應用程式閘道 | Microsoft Azure"
   description="本頁面提供建立、設定、啟動和刪除 Azure 應用程式閘道的指示。"
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
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# 建立、啟動或刪除應用程式閘道

在此版本中，您可以使用 PowerShell 或 REST API 呼叫來建立應用程式閘道。即將推出的版本將提供入口網站和 CLI 支援。本文將逐步引導您完成建立和設定、啟動及刪除應用程式閘道的步驟。

## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。
2. 請確認您有運作中的虛擬網路，且其子網路有效。
3. 請確認您的後端伺服器位於虛擬網路中，或已指派公用 IP/VIP。


若要建立新的應用程式閘道，請依列出的順序執行下列步驟。

1. [建立新的應用程式閘道](#create-a-new-application-gateway)
2. [設定閘道](#configure-the-gateway)
3. [設定閘道組態](#set-the-gateway-configuration)
4. [啟動閘道](#start-the-gateway)
4. [確認閘道狀態](#verify-the-gateway-status)

如果您想要刪除應用程式閘道，請移至[刪除應用程式閘道](#delete-an-application-gateway)。

## 建立新的應用程式閘道

**若要建立閘道**，請使用 `New-AzureApplicationGateway` Cmdlet，以您的值取代其值。請注意，此時不會開始為閘道計費。成功啟動閘道之後，會在後續步驟中開始計費。

這個範例示範後面接著輸出之第一行上的 Cmdlet。
    
	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**若要驗證**閘道已建立，您可以使用 `Get-AzureApplicationGateway` Cmdlet。

在範例中，*Description*、*InstanceCount* 和 *GatewaySize* 是選擇性參數。*InstanceCount* 的預設值是 2，最大值是 10。*GatewaySize* 的預設值是 Medium。Small 和 Large 也是可用的值。因為尚未啟動閘道，所以 *Vip* 和 *DnsName* 會顯示為空白。一旦閘道處於執行狀態，就會建立這些項目。




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


## 設定閘道

應用程式閘道組態是由多個值所組成。可以將值繫結在一起，以建構組態。

值如下：

- **後端伺服器集區：**後端伺服器的 IP 位址清單。列出的 IP 位址應該屬於 VNet 子網路，或應該是公用 IP/VIP。 
- **後端伺服器集區設定：**每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果在設定 SSL 卸載)。 
- **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該重新導向至哪個後端伺服器集區。目前只支援「基本」規則。「基本」規則是循環配置資源的負載分散。

建立組態物件或使用組態 XML 檔案，即可建構組態。若要使用組態 XML 檔案建構組態，請使用下方範例。

 **組態 XML 範例**

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

## 設定閘道組態

接下來，您將設定應用程式閘道。您可以搭配使用 `Set-AzureApplicationGatewayConfig` Cmdlet 與組態物件或組態 XML 檔案。


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## 啟動閘道

設定閘道之後，請使用 `Start-AzureApplicationGateway` Cmdlet 來啟動閘道。成功啟動閘道之後，會開始應用程式閘道計費。


**注意：**`Start-AzureApplicationGateway` Cmdlet 最多可能需要 15 到 20 分鐘的時間才能完成。



	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## 確認閘道狀態

使用 `Get-AzureApplicationGateway` Cmdlet 檢查閘道狀態。如果 *Start-AzureApplicationGateway* 在上一個步驟成功，則狀態應該為「正在執行」，且 Vip 和 DnsName 應該具備有效的項目。

這個範例示範已啟動、正在執行且準備好將流量傳送到 `http://<generated-dns-name>.cloudapp.net` 的應用程式閘道。

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : testvnet1 
	Subnets       : {Subnet-1} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## 刪除應用程式閘道

若要刪除應用程式閘道，您需要依序執行下列動作：

1. 使用 `Stop-AzureApplicationGateway` Cmdlet 停止閘道。 
2. 使用 `Remove-AzureApplicationGateway` Cmdlet 移除閘道。
3. 使用 `Get-AzureApplicationGateway` Cmdlet 確認已移除閘道。

這個範例示範後面接著輸出之第一行上的 `Stop-AzureApplicationGateway` Cmdlet。

	PS C:\> Stop-AzureApplicationGateway AppGwTest 

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

應用程式閘道處於 [已停止] 狀態之後，請使用 `Remove-AzureApplicationGateway` Cmdlet 移除服務。


	PS C:\> Remove-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

若要確認已移除服務，您可以使用 `Get-AzureApplicationGateway` Cmdlet。這不是必要步驟。


	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## 後續步驟

如果您想要設定 SSL 卸載，請參閱[設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與 ILB 搭配使用，請參閱[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想取得一般負載平衡選項的詳細資訊，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO7-->