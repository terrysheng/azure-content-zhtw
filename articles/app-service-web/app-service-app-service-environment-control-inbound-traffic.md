<properties 
	pageTitle="如何控制 App Service 環境的輸入流量" 
	description="了解如何設定網路安全性規則，以控制 App Service 環境的輸入流量。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# 如何控制 App Service 環境的輸入流量

## 概觀 ##
APP Service 環境會一律建立於區域傳統 "v1" [虛擬網路][virtualnetwork]的子網路中。建立 APP Service 環境時，可定義新的區域傳統 "v1" 虛擬網路和新的子網路。或者亦可在先前既存的區域傳統 "v1" 虛擬網路與子網路中，建立 APP Service 環境。如需建立 App Service 環境的詳細資訊，請參閱[如何建立 App Service 環境][HowToCreateAnAppServiceEnvironment]。

App Service 環境必須一律建立於子網路中，因為子網路可提供網路界限以便用來鎖定上游裝置和服務背後的輸入流量，因此只接受來自特定上游 IP 位址的 HTTP 和 HTTPS 流量。

使用[網路安全性群組][NetworkSecurityGroups]，可控制子網路上的輸入和輸出網路流量。控制輸入流量時，需要在網路安全性群組中建立網路安全性規則，然後將網路安全性群組指派給包含 App Service 環境的子網路。

將網路安全性群組指派給子網路後，會根據網路安全性群組中定義的允許和拒絕規則，允許/封鎖 App Service 環境中應用程式的輸入流量。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## App Service 環境中使用的網路連接埠 ##
利用網路安全性群組鎖定輸入網路流量之前，請務必知道 App Service 環境所用的必要和選用網路連接埠集合。意外關閉送至某些連接埠的流量，可能會導致在 App Service 環境中喪失功能。

以下是 App Service 環境所使用的連接埠清單：

- 454：Azure 基礎結構用來管理和維護 App Service 環境的**必要連接埠**。不會封鎖對此連接埠的流量。
- 455：Azure 基礎結構用來管理和維護 App Service 環境的**必要連接埠**。不會封鎖對此連接埠的流量。
- 80：對於在 App Service 環境的 App Service 方案中執行的應用程式，其輸入 HTTP 流量的預設連接埠。
- 443：對於在 App Service 環境的 App Service 方案中執行的應用程式，其輸入 SSL 流量的預設連接埠。
- 21：FTP 的控制通道。如果未使用 FTP，就可以安全地封鎖此連接埠。
- 10001-10020：FTP 的資料通道。在控制通道中若未使用 FTP，即可安全地封鎖這些連接埠 (**注意：**FTP 資料通道可能會在預覽期間變更。)
- 4016：用於 Visual Studio 2012 的遠端偵錯。如果未使用此功能，就可以安全地封鎖此連接埠。
- 4018：用於 Visual Studio 2013 的遠端偵錯。如果未使用此功能，就可以安全地封鎖此連接埠。
- 4020：用於 Visual Studio 2015 的遠端偵錯。如果未使用此功能，就可以安全地封鎖此連接埠。

## 輸出連線和 DNS 需求 ##
請注意，為了讓 App Service 環境正確運作，您也需要全球 Azure 儲存體以及相同 Azure 區域中 SQL Database 的輸出存取權。如果虛擬網路中封鎖輸出網際網路存取，則 App Service 環境將無法存取這些 Azure 端點。

客戶可能也已在虛擬網路中設定自訂 DNS 伺服器。App Service 環境需要可以解析 *.database.windows.net、*.file.core.windows.net 和 *.blob.core.windows.net 下的 Azure 端點。

也建議事先在虛擬網路上設定任何自訂 DNS 伺服器，再建立 App Service 環境。如果在建立 App Service 環境時變更虛擬網路的 DNS 組態，則會導致 App Service 環境建立程序失敗。同樣地，若自訂 DNS 伺服器存在於 VPN 閘道的另一端，且 DNS 伺服器無法連線或使用，則 App Service 環境建立程序也會失敗。

## 建立網路安全性群組 ##
如需有關網路安全性群組如何運作的完整詳細資訊，請參閱下列[資訊][NetworkSecurityGroups]。以下詳細資料是有關網路安全性群組的重點，著重於設定網路安全群組並套用到包含 App Service 環境的子網路。

**注意：**網路安全性群組只能使用如下所述的 Powershell Cmdlet 設定。由於新入口網站僅允許使用與 "v2" 虛擬網路關聯的 NSG 圖形化組態，因此無法使用新入口網站 (portal.azure.com) 採圖形化方式設定網路安全性群組。不過，App Service 環境目前僅支援處理傳統 "v1" 虛擬網路。因此，您僅可使用 Powershell Cmdlet 將網路安全性群組設為與 "v1" 虛擬網路關聯。

網路安全性群組首次會建立為與訂用帳戶相關聯的獨立實體。由於網路安全性群組建立於 Azure 區域，所以請確保網路安全性群組建立於與 App Service 環境相同的區域中。

以下示範如何建立網路安全性群組：

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

建立網路安全性群組後，會加入一或多個網路安全性規則。由於這組規則會隨著時間改變，所以建議區隔用於規則優先順序的編號配置，以便陸續插入其他規則。

下列範例顯示的規則可明確授與以下連接埠的存取權：Azure 基礎結構管理和維護 App Service 環境所需的管理連接埠。請注意，所有管理流量都會透過 SSL 傳送並受用戶端憑證保護，因此即使連接埠已開啟，Azure 管理基礎結構以外的任何實體都無法予以存取。


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

鎖定對連接埠 80 和 443 的存取，以「隱藏」上游裝置或服務背後的 App Service 環境時，您必須知道上游 IP 位址。例如，如果您使用 Web 應用程式防火牆 (WAF)，則 WAF 將會有自己的 IP 位址，以便在將流量 Proxy 處理至下游 App Service 環境時使用。您必須在網路安全性規則的 *SourceAddressPrefix* 參數中使用此 IP 位址。

在下面範例中，明確允許來自特定上游 IP 位址的輸入流量。位址 *1.2.3.4* 會做為上游 WAF 的 IP 位址預留位置。變更此值，以符合您的上游裝置或服務所使用的位址。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
如果需要 FTP 支援，可以使用下列規則做為範本，以授與對 FTP 控制連接埠和資料通道連接埠的存取權。由於 FTP 是可設定狀態的通訊協定，所以您無法透過傳統 HTTP/HTTPS 防火牆或 Proxy 裝置路由傳送 FTP 流量。在此情況下，您將需要將 *SourceAddressPrefix* 設定為不同的值 -例如，FTP 用戶端執行所在開發人員或部署電腦的 IP 位址範圍。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**注意：**資料通道連接埠範圍可能會在預覽期間變更。)

如果使用 Visual Studio 遠端偵錯，則下列規則會示範如何授與存取權。因為每個支援的 Visual Studio 版本使用不同的連接埠進行遠端偵錯，所以每個版本會有個別的規則。如同 FTP 存取，遠端偵錯流量可能不會透過傳統 WAF 或 Proxy 裝置正確傳送。可將 *SourceAddressPrefix* 改為設定成執行 Visual Studio 之開發人員電腦的 IP 位址範圍。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## 將網路安全性群組指派給子網路 ##
網路安全性群組有拒絕存取所有外部流量的預設安全性規則。結合上述的網路安全性規則與用來封鎖輸入流量的預設安全性規則，結果就是只有來自與*允許*動作相關聯之來源位址範圍的流量，能夠傳送到在 App Service 環境中執行的應用程式。

在網路安全性群組填入安全性規則之後，必須將該群組指派給包含 App Service 環境的子網路。指派命令會同時參考 App Service 環境所在的虛擬網路名稱，以及 App Service 環境建立所在的子網路名稱。

下列範例顯示要指派給子網路和虛擬網路的網路安全性群組：


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

網路安全性群組指派成功後 (指派是一項長時間執行的作業並可能需要幾分鐘才能完成)，只有符合*允許*規則的輸入流量才能成功抵達 App Service 環境中的應用程式。

基於完整性，下列範例示範如何移除進而取消網路安全性群組與子網路的關聯：


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## 明確 IP-SSL 的特殊考量 ##
若以明確的 IP 位址設定應用程式，而非使用 App Service 環境的預設 IP 位址，則 HTTP 和 HTTPS 流量會透過與連接埠 80 和 443 不同的一組連接埠流入子網路。

若要尋找每個 IP-SSL 的位址所用的個別連接埠配對，您可從 App Service 環境的使用者介面刀鋒視窗， 依序按一下 [所有設定] --> [IP 位址]。「IP 位址」刀鋒視窗會顯示 App Service 環境的所有明確設定 IP-SSL 位址，以及用於路由與每個 IP-SSL 位址關聯之 HTTP 和 HTTPS 流量的特殊連接埠配對。在網路安全性群組中設定規則時，必須針對 DestinationPortRange 參數使用此連接埠配對。

## 開始使用

若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][IntroToAppServiceEnvironment]

如需安全地從 App Service 環境連接到後端資源的詳細資訊，請參閱 [安全地從 App Service 環境連接到後端資源][SecurelyConnecttoBackend]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/

<!-- IMAGES -->

<!---HONumber=Sept15_HO3-->