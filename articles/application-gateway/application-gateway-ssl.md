<properties 
   pageTitle="為 SSL 卸載設定應用程式閘道 | Microsoft Azure"
   description="本文提供在 Azure 應用程式閘道上設定 SSL 卸載的指示。"
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
   ms.date="09/25/2015"
   ms.author="joaoma"/>

# 為 SSL 卸載設定應用程式閘道

應用程式閘道可以設定為在閘道終止 SSL 工作階段，如此可避免在 Web 伺服陣列上的高成本 SSL 解密。SSL 卸載也可以簡化應用程式的前端伺服器設定和管理。

## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。
2. 請確認您的運作中虛擬網路具有有效子網路。
3. 請確認您的後端伺服器位於虛擬網路中，或已指派公用 IP/VIP。

若要在應用程式閘道上設定 SSL 卸載，請依列出的順序執行下列步驟。

1. [建立新的應用程式閘道](#create-a-new-application-gateway)
2. [上傳 SSL 憑證](#upload-ssl-certificates) 
3. [設定閘道](#configure-the-gateway)
4. [設定閘道組態](#set-the-gateway-configuration)
5. [啟動閘道](#start-the-gateway)
6. [確認閘道狀態](#verify-the-gateway-status)


## 建立新的應用程式閘道

**若要建立閘道**，請使用 `New-AzureApplicationGateway` Cmdlet，並將值取代為您自己的值。請注意，此時不會開始為閘道計費。會在稍後的步驟中於成功啟動閘道之後開始計費。

這個範例的第一行顯示 Cmdlet，後面接著輸出。

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**若要驗證**已建立閘道，您可以使用 `Get-AzureApplicationGateway` Cmdlet。


在範例中，*Description*、*InstanceCount* 和 *GatewaySize* 是選用參數。*InstanceCount* 的預設值是 2，且最大值是 10。*GatewaySize* 的預設值是 Medium。Small 和 Large 也是可用的值。因為尚未啟動閘道，所以 *Vip* 和 *DnsName* 會顯示為空白。閘道處於執行中狀態之後，將會建立這些項目。

這個範例的第一行顯示 Cmdlet，後面接著輸出。

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## 上傳 SSL 憑證 

使用 `Add-AzureApplicationGatewaySslCertificate` 將伺服器憑證 (*pfx* 格式) 上傳至應用程式閘道。憑證名稱是使用者選擇的名稱，而且必須在應用程式閘道中是唯一的。應用程式閘道上的所有憑證管理作業會使用此名稱參考該憑證。

這個範例的第一行顯示 Cmdlet，後面接著輸出。將範例中的值取代為您自己的值。

	PS C:\> Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file> 
	
	VERBOSE: 5:05:23 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:06:29 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   21fdc5a0-3bf7-2c12-ad98-192e0dd078ef

接下來，驗證憑證上傳。使用 `Get-AzureApplicationGatewayCertificate`。

這個範例的第一行顯示 Cmdlet，後面接著輸出。

	PS C:\> Get-AzureApplicationGatewaySslCertificate AppGwTest 

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert 
	SubjectName    : CN=gwcert.app.test.contoso.com 
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A 
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned


## 設定閘道

應用程式閘道組態是由多個值所組成。可以將值繫結在一起，以建構組態。

值如下：
 
- **後端伺服器集區：**後端伺服器的 IP 位址清單。列出的 IP 位址應該屬於 VNet 子網路，或應該是公用 IP/VIP。 
- **後端伺服器集區設定：**每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質。這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。 
- **規則：**規則會繫結接聽程式和後端伺服器集區，並在符合特定接聽程式時定義應該將流量重新導向至其中的後端伺服器集區。目前，只支援*基本*規則。*基本*規則是循環配置資源負載散發。

**其他組態注意事項：**

針對 SSL 憑證組態，**HttpListener** 中的通訊協定應該變更為 *Https* (區分大小寫)。需要將 **SslCert** 元素加入至 **HttpListener**，並將值設為上述的上傳 SSL 憑證一節中所使用的相同名稱。前端連接埠應該更新為 443。

**若要啟用以 Cookie 為基礎的同質**：您可以設定應用程式閘道，以確保來自用戶端工作階段的要求一律會導向至 Web 伺服陣列中的相同 VM。這是透過插入允許閘道適當導向流量的工作階段 Cookie 來完成。若要啟用以 Cookie 為基礎的同質，請在 **BackendHttpSettings** 元素中將 **CookieBasedAffinity** 設定為 *Enabled*。



建立組態物件或使用組態 XML 檔案，即可建構組態。若要使用組態 XML 檔案建構組態，請使用下方範例。

**組態 XML 範例**


	    <?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
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
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
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


**注意：**`Start-AzureApplicationGateway`Cmdlet 可能需要 15-20 分鐘的時間才能完成。

   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b


## 確認閘道狀態

使用 `Get-AzureApplicationGateway` Cmdlet 來檢查閘道狀態。如果上一個步驟中的 *Start-AzureApplicationGateway* 成功，則狀態應該是 *Running*，而且 Vip 和 DnsName 應該具有有效的輸入。

這個範例所示範的應用程式閘道已啟動、執行中以及準備好要接受將流量

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	Name          : AppGwTest2
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## 後續步驟


如果您想進一步了解一般負載平衡選項，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Oct15_HO3-->