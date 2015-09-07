<properties 
	pageTitle="使用 ExpressRoute 的網路組態詳細資料"
	description="在連接至 ExpressRoute 循環之虛擬網路中執行 App Service 環境的網路組態詳細資料。"
	services="app-service\web"
	documentationCenter=""
	authors="stefsch"
	manager="nirma"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="stefsch"/>

# 使用 ExpressRoute 之 App Service 環境的網路組態詳細資料 

## 概觀 ##
客戶可以將 [Azure ExpressRoute][ExpressRoute] 循環連接至虛擬網路基礎結構，因而將其內部部署網路延伸至 Azure。您可以在這個[虛擬網路][virtualnetwork]基礎結構的子網路中建立 App Service 環境。在 App Service 環境上執行的應用程式接著可以建立與後端資源的安全連線，而後端資源只能透過 ExpressRoute 連線來存取。

## 需要的網路連線 ##
在連接至 ExpressRoute 的虛擬網路中，可能一開始會不符合 App Service 環境的一些網路連線需求。

App Service 環境需要下列所有項目，才能正確運作：


-  與 App Service 環境位於相同區域之 Azure 儲存體和 SQL 資料庫資源的輸出網路連線。這個網路路徑無法通過內部公司 Proxy，因為，這麼做可能會變更輸出網路流量的有效 NAT 位址。變更在 Azure 儲存體和 SQL 資料庫端點上導向之 App Service 環境輸出網路流量的 NAT 位址會導致連線失敗。
-  虛擬網路的 DNS 組態必須可以解析下列 Azure 控制網域內的端點：**.file.core.windows.net*、**.blob.core.windows.net*、**.database.windows.net*。
-  在建立 App Service 環境後，以及重新設定和調整 App Service 環境變更的期間，虛擬網路的 DNS 組態必須保持穩定。   
-  必須如此[文章][requiredports]所述，允許 App Service 環境之必要連接埠的輸入網路存取。

確定虛擬網路的有效 DNS 組態，即可符合 DNS 需求。

在 App Service 環境的子網路上設定[網路安全性群組][NetworkSecurityGroups]以允許必要存取權，即可符合輸入網路存取的需求 (如此[文章][requiredports]所述)。

## 啟用 App Service 環境的輸出網路連線##
新建立的 ExpressRoute 循環預設會通告允許輸出網際網路連線的預設路由。使用此組態，App Service 環境將可以連接至其他 Azure 端點。

不過，常見的客戶組態是定義其專屬預設路由，以強制輸出網際網路流量透過內部部署方式流過客戶的 Proxy/防火牆基礎結構。此流量流程一定會中斷 App Service 環境，因為已透過內部部署方式封鎖輸出流量，或 NAT 至無法再使用各種 Azure 端點的一組無法辨識位址。

解決方法是在子網路上定義包含 App Service 環境的一 (或多個) 使用者定義路由 (UDR)。UDR 會定義將使用的子網路特有路由，而非預設路由。

如果使用者定義路由的背景資訊，請參閱此[概觀][UDROverview]。

如需建立和設定使用者定義路由的詳細資料，請參閱此〈[作法指南][UDRHowTo]〉。

## App Service 環境的範例 UDR 組態 ##

**必要條件**

1. 從 [Azure 下載頁面][AzureDownloads]安裝最新 Azure Powershell (日期為 2015 年 6 月或更新版本)。在 [命令列工具] 的 [Windows Powershell] 下，有一個 [安裝] 連結可安裝最新的 Powershell Cmdlet。

2. 建議您建立唯一的子網路，以專供 App Service 環境使用。這可確保套用至子網路的 UDR 只會開啟 App Service 環境的輸出流量。
3. **重要事項**：除非是在進行下列組態步驟**之後**，否則請不要部署 App Service 環境。這可確保輸出網路連線可用，再嘗試部署 App Service 環境。

**步驟 1：建立具名路由表**

下列程式碼片段會在 West US Azure 區域中建立稱為 "DirectInternetRouteTable" 的路由表：

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**步驟 2：在路由表中建立一或多個路由**

您必須將一或多個路由新增至路由表中，才能啟用輸出網際網路存取。下列範例會新增足夠的路由，以涵蓋 West US 區域中使用的所有可能 Azure 位址。

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


如需可供 Azure 使用之 CIDR 範圍的完整和已更新清單，您可以從 [Microsoft 下載中心][DownloadCenterAddressRanges]下載內含所有範圍的 XML 檔案

**注意：**在某個時間點，縮寫的 CIDR 簡稱 0.0.0.0/0 將可用於 *AddressPrefix* 參數。這個簡稱等同於「所有網際網路位址」。現在，開發人員必須改為使用一組足以涵蓋所有可能 Azure 位址範圍的更廣 CIDR 範圍，而 Azure 位址範圍用於已部署 App Service 環境的區域中。

**步驟 3：建立路由表與包含 App Service 環境之子網路的關聯**

最後一個設定步驟是建立路由表與將部署 App Service 環境之子網路的關聯。下列命令會建立 "DirectInternetRouteTable" 與最後將包含 App Service 環境之 "ASESubnet" 的關聯。

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**步驟 4：最後步驟**

路由表繫結至子網路之後，建議您先測試並確認預期的效果。例如，將虛擬機器部署至子網路，並確認：


- Azure 端點的輸出流量不會流到 ExpressRoute 循環。
- 已正確解析 Azure 端點的 DNS 查閱。 

確認上述步驟之後，即可繼續建立 App Service 環境！

## 開始使用

若要開始使用 App Service 環境，請參閱 [App Service 環境簡介][IntroToAppServiceEnvironment]

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service][AzureAppService]。

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=August15_HO9-->