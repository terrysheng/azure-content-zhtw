<properties 
   pageTitle="使用 VPN 閘道將多個內部部署網站連線至虛擬網路"
   description="本文將逐步引導您使用傳統部署模型的 VPN 閘道，將多個本機內部部署網站連線至虛擬網路。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="cherylmc" />

# 將多個內部部署網站連線到虛擬網路

本文適用於將多個內部部署站台連接到使用傳統部署模型 (也稱為「服務管理」) 建立的 VNet。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]當我們有使用「資源管理員」模型建立 VNet 之步驟的文章時，將會在這個頁面提供該文章的連結。

## 關於連線

您可以將多個內部部署網站連線到單一虛擬網路。這對建立混合式雲端解決方案特別具有吸引力。建立多網站連線至 Azure 虛擬網路閘道，與建立其他站對站連線非常類似。事實上，您可以使用現有的 Azure VPN 閘道，只要該閘道是動態的 (路由式)。

如果已經有連線至虛擬網路的靜態閘道，您可以將閘道類型變更為動態，而不需要為了容納多個網站重建虛擬網路。在變更路由類型之前，請確定您的內部部署 VPN 閘道支援路由式 VPN 組態。

![多網站 VPN](./media/vpn-gateway-multi-site/IC727363.png)

## 考慮事項

**您無法使用 Azure 傳統入口網站來變更此虛擬網路。** 針對此版本，您必須變更網路組態檔案，而非使用 Azure 傳統入口網站。如果您在 Azure 傳統入口網站中進行變更，這些變更將會覆寫此虛擬網路的多網站參考設定。在完成多網站程序時，您應該會覺得使用網路組態檔很輕鬆。不過，如果有多人在處理您的網路組態，您必須確定每個人都知道這項限制。這並不表示您完全無法使用 Azure 傳統入口網站。除了無法對這個特定虛擬網路進行組態變更，您可以用它來執行所有其他事情。

## 開始之前

開始設定之前，請確認您具有下列必要條件：

- Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。

- 每個內部部署位置都有相容的 VPN 硬體。查看[關於虛擬網路連線的 VPN 裝置](http://go.microsoft.com/fwlink/p/?linkid=615099)，確認您想要使用的裝置是否為已知相容的項目。

- 每個 VPN 裝置都有對外的公用 IPv4 IP 位址。此 IP 位址不能位於 NAT 後方。這是必要條件。

- 最新版的 Azure PowerShell Cmdlet。您可以從[下載](http://azure.microsoft.com/downloads/)頁面的＜Windows PowerShell＞區段下載並安裝最新版本。

- 熟悉如何設定 VPN 硬體的人員。您無法使用從 Azure 傳統入口網站自動產生的 VPN 指令碼來設定 VPN 裝置。這表示您必須非常了解如何設定 VPN 裝置，或是與了解的人員一起進行。

- 您想要用於虛擬網路的 IP 位址範圍 (如果尚未建立的話)。

- 您要連線之每個區域網路網站的 IP 位址範圍。您必須確定您要連線之每個區域網路網站的 IP 位址範圍沒有重疊。否則，Azure 傳統入口網站或 REST API 將會拒絕所上傳的組態。例如，如果您有兩個區域網路網站都包含 IP 位址範圍 10.2.3.0/24，而您有一個目的地位址為 10.2.3.3 的封裝，Azure 就會不知道您想要將封裝傳送到哪個網站，因為位址範圍重疊了。為了防止路由問題，Azure 不允許您上傳具有重疊範圍的組態檔。

## 建立您的虛擬網路和閘道

1. **使用動態 (路由式) 路由閘道來建立站對站 VPN。** 如果您已經有了，那很好！ 您可以繼續[匯出虛擬網路組態設定](#export)。如果沒有，請執行下列動作：

	**如果您已經有「站對站」虛擬網路，但它有靜態 (原則式) 路由閘道：**步驟 1：將您的閘道類型變更為動態路由。多網站 VPN 需要動態路由閘道。若要變更閘道類型，您必須先刪除現有的閘道，然後建立新的閘道。如需相關指示，請參閱[變更 VPN 閘道路由類型](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type)。步驟 2：設定新的閘道，並建立 VPN 通道。如需指示，請參閱[在 Azure 傳統入口網站中設定 VPN 閘道](vpn-gateway-configure-vpn-gateway-mp.md)。首先，將閘道類型變更為動態路由。

	**如果您沒有「站對站」虛擬網路：**步驟 1：使用下列指示建立「站對站」虛擬網路：[使用「站對站」VPN 連線，在 Azure 傳統入口網站中建立虛擬網路](vpn-gateway-site-to-site-create.md)。步驟 2：使用下列指示設定動態路由閘道：[設定 VPN 閘道](vpn-gateway-configure-vpn-gateway-mp.md)。閘道類型務必選取 [動態路由]。

2. **<a name="export"></a>匯出虛擬網路組態設定。** 若要匯出網路組態檔案，請參閱[匯出網路設定](../virtual-network/virtual-networks-using-network-configuration-file.md)。您匯出的檔案將會用來設定新的多網站設定。

3. **開啟您的網路組態檔。** 開啟您在最後一個步驟下載的網路組態檔。使用您喜歡的任何 xml 編輯器。檔案看起來應該像下面這樣：

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

4. **新增多個網站參考資訊至網路組態檔**。當您新增或移除網站參考資訊時，將會對 ConnectionsToLocalNetwork/LocalNetworkSiteRef 進行組態變更。加入新的本機網站參考資訊會促使 Azure 建立新的通道。下列範例中的網路組態適用於單一網站連線。

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	若要加入其他網站參考資訊 (建立多網站組態)，只要另外加入 "LocalNetworkSiteRef" 字行，如下列範例所示：

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

5. **儲存網路組態檔並將它匯入。** 若要匯入網路組態檔，請參閱[匯入您的網路設定](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal)。當您匯入含有變更的這個檔案時，就會加入新的通道。這些通道會使用您稍早建立的動態閘道。

6. **下載 VPN 通道的預先共用金鑰。** 您的新通道加入之後，使用 PowerShell Cmdlet Get-AzureVNetGatewayKey，以取得每個通道的 IPsec/IKE 預先共用金鑰。

	例如：

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	如果您想要，也可以使用*取得虛擬網路閘道共用金鑰* REST API 來取得預先共用金鑰。

## 確認您的連線

**檢查多網站通道狀態。** 下載每個通道的金鑰之後，您會想要確認連線。使用 *Get-AzureVNetConnection* 來取得虛擬網路通道清單，如下列範例所示。VNet1 是 VNet 的名稱。

		Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## 後續步驟

若要深入了解 VPN 閘道，請參閱[關於 VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。

<!---HONumber=AcomDC_1223_2015-->