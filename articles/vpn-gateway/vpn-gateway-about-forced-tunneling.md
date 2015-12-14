<properties pageTitle="針對使用 PowerShell | Microsoft Azure 的 VPN 閘道設定強制通道" description="如果您擁有使用跨單位 VPN 閘道的虛擬網路，您可以重新導向或「強制」所有網際網路繫結流量傳回內部部署位置。本文適用於使用傳統部署模型所建立的 VPN 閘道 " services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-service-management"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc" />

# 設定強制通道

本文適用於使用傳統部署模型所建立的 VNet 和 VPN 閘道。如果您想要針對使用資源管理員部署模型所建立的 VNet 和 VPN 閘道設定強制通道，請參閱[針對使用 PowerShell 和 Azure 資源管理員設定強制通道](vpn-gateway-forced-tunneling-rm.md)。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 有關強制通道

強制通道可讓您透過站對站 VPN 通道，重新導向或「強制」所有網際網路繫結流量傳回內部部署位置，以便進行檢查和稽核。這是多數企業 IT 原則的重要安全性需求。若不使用強制通道，則 Azure 中來自 VM 的網際網路繫結流量會永遠從 Azure 網路基礎結構直接向外周遊到網際網路，而您無法選擇檢查或稽核流量。未經授權的網際網路存取可能會導致資訊洩漏或其他類型的安全性漏洞。

下圖說明如何使強制通道正常運作。

![強制通道](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

在上述範例中，前端子網路不會使用強制通道。前端子網路中的工作負載可以直接從網際網路繼續接受並回應客戶要求。中間層和後端的子網路會使用強制通道。任何從這兩個子網路到網際網路的輸出連接會強制或重新導向回 S2S VPN 通道的其中一個內部部署網站。這可讓您在 Azure 中限制並檢查來自虛擬機器或雲端服務的網際網路存取，同時繼續啟用您所需的多層式服務架構。如果虛擬網路中沒有任何網際網路對向工作負載，您也可以選擇將強制通道套用至整個虛擬網路。

## 需求和考量

Azure 中的強制通道會透過虛擬網路使用者定義的路由進行設定。將流量重新導向至在內部部署網站時，會表示為至 Azure VPN 閘道的「預設路由」。下節列出 Azure 虛擬網路路由表和路由目前的限制：



-  每個虛擬網路的子網路皆有內建的系統路由表。系統路由表具有下列 3 個路由群組：

	- **本機 VNet 路由：**直接連接至相同虛擬網路中的目的地 VM
	
	- **內部部署路由：**連接至 Azure VPN 閘道
	
	- **預設路由：**直接連接至網際網路。請注意，系統將會卸除尚未由前兩個路由涵蓋之私人 IP 位址目的地的封包。



-  隨著「使用者定義路由」的發行，您可以建立路由表以加入預設路由，然後將路由表關聯至 VNet 子網路，以便啟用這些子網路上的強制通道。

- 強制通道必須與具有動態路由 VPN 閘道 (非靜態閘道) 的 VNet 相關聯。您需要在連接到虛擬網路的內部部署本機網站間設定「預設網站」。

- 請注意，ExpressRoute 強制通道不會透過這項機制進行設定，相反地，將由透過 ExpressRoute BGP 對等互連工作階段的廣告預設路由進行啟用。請參閱 [ExpressRoute 文件](https://azure.microsoft.com/documentation/services/expressroute/)以取得詳細資訊。

## 組態概觀

下方程序將協助您指定虛擬網路的強制通道。設定步驟會對應至下方的虛擬網路 Netcfg 檔範例。

在此範例中，多層式 VNet 虛擬網路具有 3 個子網路：*前端*、*中層*，和*後端*子網路，包含 4 個跨單位連接：*DefaultSiteHQ*，以及 3 個*分支*。程序步驟會將 *DefaultSiteHQ* 設定為強制通道的預設網站連接，並設定*中層*和*後端*子網路以使用強制通道。

	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

### 必要條件

- Azure 訂閱

- 已設定的虛擬網路。

- 最新版的 Azure PowerShell Cmdlet 使用 Web Platform Installer。您可以從[下載頁面](http://azure.microsoft.com/downloads/)的 **Windows PowerShell** 區段下載並安裝最新版本。

## 設定強制通道

使用下列程序來設定強制通道。

1. 建立路由表。使用下列 Cmdlet 來建立路由表。

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

1. 將預設路由加入至路由表。

	下方 Cmdlet 範例會將預設路由加入至步驟 1 中所建立的路由表。請注意，唯一支援的路由是到「VPNGateway」Nexthop 的「0.0.0.0/0」目的地前置詞。
 
		Set-AzureRoute –RouteTableName "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

1. 將路由表關聯至子網路。

	建立路由表並加入路由之後，使用下方 Cmdlet 將路由表加入或關聯至 VNet 子網路。下方範例會將路由表「MyRouteTable」加入至 VNet 多層式 VNet 中的中層和後端子網路。

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

1. 為強制通道指派預設站台。

	在前述步驟中，範例 Cmdlet 指令碼已建立路由表，並將路由表關聯至兩個 VNet 子網路。剩餘步驟是選取虛擬網路多網站連接之間的本機網站作為預設的網站或通道。

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## 其他的 PowerShell Cmdlet

以下是一些額外 PowerShell Cmdlet，使用強制通道組態時可能會很有幫助。

**若要刪除路由表：**

	Remove-AzureRouteTable -RouteTableName <routeTableName>

**若要列出路由表：**

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

**若要從路由表刪除路由：**

	Remove-AzureRouteTable –Name <routeTableName>

**若要從子網路移除路由：**

	Remove-AzureSubnetRouteTable –VNetName <virtualNetworkName> -SubnetName <subnetName>

**若要列出與子網路相關聯的路由表：**
	
	Get-AzureSubnetRouteTable -VNetName <virtualNetworkName> -SubnetName <subnetName>

**若要從 VNet VPN 閘道移除預設網站：**

	Remove-AzureVnetGatewayDefaultSites -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_1203_2015-->