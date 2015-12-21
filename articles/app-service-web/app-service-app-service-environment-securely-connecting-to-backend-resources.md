<properties 
	pageTitle="安全地從 App Service 環境連接到後端資源" 
	description="了解如何安全地從 App Service 環境連接到後端資源。" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="stefsch"/>

# 安全地從 App Service 環境連接到後端資源 #

## 概觀 ##
由於 App Service 環境一律建立於區域傳統 "v1" [虛擬網路][virtualnetwork]的子網路，所以從 App Service 環境送至其他後端資源的輸出連線可以獨佔方式透過虛擬網路傳送。

**注意：**在「v2」ARM 管理的虛擬網路中，無法建立 App Service 環境。

例如，SQL Server 可能會在已鎖定連接埠 1433 的虛擬機器叢集上執行。此端點可能已納入 ACL，只允許從相同虛擬網路上的其他資源進行存取。

另一個例子則是，敏感性端點可能會在內部執行並透過[站台對站台][SiteToSite]或 [Azure ExpressRoute][ExpressRoute] 連線連接至 Azure。因此，只有虛擬網路中連接到站台對站台或 ExpressRoute 通道的資源能夠存取內部部署端點。

在上述這些案例中，在 App Service 環境上執行的應用程式將能夠安全地連接到各種伺服器和資源。從 App Service 環境中執行之應用程式送至相同虛擬網路中私密端點 (或連接到相同的虛擬網路) 的輸出流量，只會透過虛擬網路傳送。送至私密端點的輸出流量不會透過公用網際網路傳送。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 輸出連線和 DNS 需求 ##
請注意，為了讓 App Service 環境正確運作，需要全球 Azure 儲存體的輸出存取權，以及相同 Azure 區域中的 SQL Database 連線。如果虛擬網路中封鎖輸出網際網路存取，則 App Service 環境將無法存取這些 Azure 端點。

客戶可能也已在虛擬網路中設定自訂 DNS 伺服器。App Service 環境需要可以解析 *.database.windows.net、*.file.core.windows.net 和 *.blob.core.windows.net 下的 Azure 端點。

也建議事先在虛擬網路上設定任何自訂 DNS 伺服器，再建立 App Service 環境。如果在建立 App Service 環境時變更虛擬網路的 DNS 組態，則會導致 App Service 環境建立程序失敗。如果自訂的 DNS 伺服器存在於 VPN 閘道的另一端，而且 DNS 伺服器無法連線或無法使用，則 App Service 環境建立程序也會失敗。

## 連接至 SQL Server
常見的 SQL Server 組態會有在連接埠 1433 上接聽的端點：

![SQL Server 端點][SqlServerEndpoint]

有兩種方法可限制送至此端點的流量：


- [網路存取控制清單][NetworkAccessControlLists] (網路 ACL)

- [網路安全性群組][NetworkSecurityGroups]


## 利用網路 ACL 限制存取

使用網路存取控制清單可以保護連接埠 1433。下列範例將源自虛擬網路內部的用戶端位址列入白名單，並封鎖對所有其他用戶端的存取。

![網路存取控制清單範例][NetworkAccessControlListExample]

在與 SQL Server 相同虛擬網路的 App Service 環境中執行的所有應用程式，都將能夠使用 SQL Server 虛擬機器的 **VNet 內部** IP 位址連接到 SQL Server 執行個體。

下列連接字串範例會使用其私密 IP 位址參考 SQL Server。

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

雖然虛擬機器也有公用端點，但使用公用 IP 位址的連接嘗試將會因為網路 ACL 而遭到拒絕。

## 利用網路安全性群組限制存取
另一種保護存取安全的方法是利用網路安全性群組。網路安全性群組可以套用到個別的虛擬機器，或含有虛擬機器的子網路。

首先需要建立網路安全性群組：

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

利用網路安全性群組來限制僅只存取 VNet 內部流量極為容易。網路安全性群組中的預設規則只允許從相同虛擬網路中的其他網路用戶端存取。

因此鎖定 SQL Server 的存取權，就如同將網路安全性群組及其預設規則套用到執行 SQL Server 的虛擬機器或含有虛擬機器的子網路一樣簡單。

下列範例將網路安全性群組套用至包含的子網路：

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
最終結果是一組可封鎖外部存取，同時允許 VNet 內部存取的安全性規則：

![預設網路安全性規則][DefaultNetworkSecurityRules]


## 開始使用

若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][IntroToAppServiceEnvironment]

如需控制 App Service 環境輸入流量的詳細資訊，請參閱 [控制 App Service 環境的輸入流量][ControlInboundASE]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!-------HONumber=AcomDC_1210_2015--->