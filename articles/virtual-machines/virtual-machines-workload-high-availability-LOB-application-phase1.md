<properties 
	pageTitle="企業營運應用程式第 1 階段 | Microsoft Azure" 
	description="在 Azure 企業營運應用程式第 1 階段中，建立虛擬網路和其他 Azure 基礎結構項目。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="josephd"/>

# 企業營運應用程式工作負載第 1 階段：設定 Azure
 
[AZURE.INCLUDE [learn-about-deployment-models-rm-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。
 
這個階段會在 Azure 基礎結構服務中，部署內部網路專屬、高可用性的企業營運應用程式，而您將在這個階段中建置 Azure 網路功能與儲存體基礎結構。您必須先完成這個階段才能前往[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)。如需所有階段的相關資訊，請參閱[在 Azure 中部署高可用性的企業營運應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)。

您必須使用下列基本網路元件來佈建 Azure：

- 具有一個子網路的跨單位虛擬網路，可用來裝載 Azure 虛擬機器
- 兩個 Azure 儲存體帳戶，可用來儲存 VHD 磁碟映像和額外的資料磁碟。
- 三個可用性集合

## 開始之前

開始設定 Azure 元件之前，請先填寫下列表格。為了協助您進行設定 Azure 的程序，請列印本節並寫下必要資訊，或者將本節複製到文件並完成填寫。

針對虛擬網路 (VNet) 的設定，請填寫表格 V。

項目 | 設定元素 | 說明 | 值 
--- | --- | --- | --- 
1\. | VNet 名稱 | 要指派給 Azure 虛擬網路的名稱 (例如 SPFarmNet)。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | VNet 位置 | 將包含虛擬網路的 Azure 資料中心。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | 區域網路名稱 | 要指派給組織網路的名稱。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | VPN 裝置 IP 位址 | 您的 VPN 裝置介面在網際網路上的公用 IPv4 位址。與您的 IT 部門合作來決定這個位址。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | VNet 位址空間 | 適用於虛擬網路的位址空間 (定義於單一私人位址首碼中)。與您的 IT 部門合作來決定這個位址空間。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | 虛擬網路的第一個 DNS 伺服器 | 第四個適用於虛擬網路之第二個子網路位址空間的可能 IP 位址 (請參閱資料表 S)。與您的 IT 部門合作來決定這個位址。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | 虛擬網路的第二個 DNS 伺服器 | 第五個適用於虛擬網路之第二個子網路位址空間的可能 IP 位址 (請參閱資料表 S)。與您的 IT 部門合作來決定這個位址。 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | IPsec 共用金鑰 | 32 個字元的隨機英數字元字串，將用來驗證網站間 VPN 連接的兩端。與您的 IT 或安全性部門合作來決定此金鑰值。或者，請參閱[建立適用於 IPsec 預先共用金鑰的隨機字串](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx)。| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表格 V：跨單位虛擬網路設定**

針對這個解決方案的子網路填寫表格 S。

- 為第一個子網路決定適用於 Azure 閘道器子網路的 29 位元位址空間 (首碼長度為 /29)。
- 為第二個子網路指定易記名稱，根據虛擬網路位址空間來指定單一 IP 位址空間，並提供描述性用途。 

與您的 IT 部門合作，從虛擬網路位址空間來決定這些位址空間。這些位址空間格式應該是無類別網域間路由選擇 (CIDR) 格式，亦稱為網路首碼格式。例如，10.24.64.0/20。

項目 | 子網路名稱 | 子網路位址空間 | 目的 
--- | --- | --- | --- 
1\. | 閘道器子網路 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Azure 閘道器虛擬機器所使用的子網路。
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表格 S：虛擬網路中的子網路**

> [AZURE.NOTE]為求簡化，這個預先定義的架構會使用單一子網路。如果您想要涵蓋一組流量篩選器來模擬子網路隔離，可以使用 Azure [網路安全性群組](virtual-networks-nsg.md)。

如果這兩部內部部署的 DNS 伺服器是您最初在虛擬網路上設定網域控制站時想要使用的伺服器，請填寫表格 D。請為每部 DNS 伺服器指定易記名稱和單一 IP 位址。這個易記名稱不需要與 DNS 伺服器的主機名稱或電腦名稱相符。請注意，其中列出兩個空白項目，但您可以增加更多項目。與您的 IT 部門合作來決定這份清單。

項目 | DNS 伺服器 IP 位址 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**表格 D：內部部署 DNS 伺服器**

若要透過網站間 VPN 連接，將封包從 Azure 虛擬網路路由傳送到您的組織網路，您必須設定含有區域網路的虛擬網路，其中包含適用於組織內部部署網路上所有可連線位置的位址空間清單 (CIDR 標記法)。定義區域網路的位址空間清單必須為唯一，且不得與用於其他虛擬網路或其他區域網路的位址空間重疊。

針對本機網路位址空間組合，請填寫表格 L。請注意，其中列出三個空白項目，但您通常需要更多項目。與您的 IT 部門合作來決定這個位址空間清單。

項目 | 區域網路位址空間 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表格 L：適用於區域網路的位址首碼**

首先，開啟 Azure PowerShell 提示字元。

> [AZURE.NOTE]下列命令集使用 Azure PowerShell 1.0 版和更新版本。如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

首先，開啟 Azure PowerShell 提示字元並登入您的帳戶。

	Login-AzureRMAccount

使用下列命令取得您的訂用帳戶名稱。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定您的 Azure 訂用帳戶以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

接下來，建立企業營運應用程式的新資源群組。若要判斷唯一的資源群組名稱，請使用此命令列出現有的資源群組。

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

使用下列命令建立新的資源群組。

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

以資源管理員為基礎的虛擬機器需要以資源管理員為基礎的儲存體帳戶。

項目 | 儲存體帳戶名稱 | 目的 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | SQL 伺服器虛擬機器所使用的進階儲存體帳戶。
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | 工作負載中所有其他虛擬機器所使用的標準儲存體帳戶。 

**資料表 ST：儲存體帳戶**

當您在第 2、3 及 4 階段中建立虛擬機器時，需要使用這些名稱。

您必須為每個儲存體帳戶挑選只包含小寫字母和數字的全域唯一名稱。您可以使用此命令列出現有的儲存體帳戶。

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

若要建立第一個儲存體帳戶，請執行這些命令。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

若要建立第二個儲存體帳戶，請執行這些命令。

	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

接下來，您將建立 Azure 虛擬網路，用來裝載企業營運應用程式。

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

接下來，使用這些命令來建立站對站 VPN 連接的閘道器。

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

接下來，設定內部部署 VPN 裝置，以連接到 Azure VPN 閘道器。如需詳細資訊，請參閱[設定 VPN 裝置](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device)。

若要設定內部部署 VPN 裝置，您將需要下列項目：

- 適用於虛擬網路之 Azure VPN 閘道器的公用 IPv4 位址 (從 **Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName** 命令顯示)。
- 適用於站對站 VPN 連接的 IPsec 預先共用金鑰 (資料表 V- 項目 8 – 值資料行)。

接著，確定可從您的內部部署網路連線到虛擬網路的位址空間。這通常是藉由將對應到虛擬網路位址空間的路由新增到您的 VPN 裝置，然後將該路由公告至組織網路中路由基礎結構的剩餘部分。與您的 IT 部門合作來決定如何執行這個動作。

接下來，定義三個可用性集合的名稱。填寫表格 A。

項目 | 目的 | 可用性設定組名稱 
--- | --- | --- 
1\. | 網域控制站 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Web 伺服器 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**表格 A：可用性設定組名稱**

當您在第 2、3 及 4 階段中建立虛擬機器時，需要使用這些名稱。

使用這些 Azure PowerShell 命令建立這些新的可用性集合。

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

此處顯示已成功完成此階段的設定。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## 後續步驟

- 依照[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)指示繼續此工作負載的設定。

<!---HONumber=AcomDC_1217_2015-->