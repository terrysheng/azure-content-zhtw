<properties 
   pageTitle="建立具有多個 NIC 的 VM"
   description="如何建立具有多個 NIC 的 VM"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/30/2015"
   ms.author="telmos" />

# 建立具有多個 NIC 的 VM

多個 NIC 功能可讓您建立和管理 Azure 虛擬機器 (VM) 上的多個虛擬網路介面卡 (NIC)。多個 NIC 是許多網路虛擬應用裝置的需求，例如，應用程式傳遞和 WAN 最佳化解決方案。多個 NIC 也會提供更多網路流量管理功能，包括前端 NIC 與後端 NIC 之間的流量隔離，或者從管理平面流量中將資料平面流量分隔開來。

![適用於 VM 的多個 NIC](./media/virtual-networks-multiple-nics/IC757773.png)

上圖顯示具有三個 NIC 的 VM，每個都連接到不同的子網路。

## 需求和限制

多個 NIC 目前具有下列需求和限制：

- 多個 NIC 的 VM 必須建立於 Azure 虛擬網路 (VNet) 中。不支援非 VNet 的 VM。 
- 在單一雲端服務中，僅允許下列設定： 
	- 該雲端服務中的所有 VM 必須已啟用多個 NIC 功能，或者 
	- 該雲端服務中的每一個 VM 都必須具有單一 NIC 

>[AZURE.IMPORTANT]如果您嘗試將多個 NIC 的 VM 加入已經包含單一 NIC 的 VM (反之亦然) 的部署 (雲端服務)，您將會收到下列錯誤：同一個部署中不支援同時具有次要網路介面的虛擬機器和沒有次要網路介面的虛擬機器，而且也無法更新沒有任何次要網路介面的虛擬機器，使其具有次要網路介面，反之亦然。
 
- 只有「預設」VIP 上才支援網際網路對向的 VIP。只有一個  VIP 可以連接到預設 NIC 的 IP。 
- 執行個體層級的公用 IP 位址目前不支援多個 NIC 的 VM。 
- VM 內部的 NIC 順序是隨機的，而且也可透過 Azure 基礎結構更新來變更。不過，IP 位址及對應的乙太網路 MAC 位址會維持不變。例如，假設 **Eth1** 具有 IP 位址 10.1.0.100 和 MAC 位址 00-0D-3A-B0-39-0D；在 Azure 基礎結構更新並重新開機之後，就無法變更為 Eth2，但是 IP 和 MAC 配對會保持相同。當客戶起始重新啟動時，NIC 順序會保持相同。 
- 每個 VM 上的每個 NIC 位址都必須位於子網路中，單一 VM 上的多個 NIC 每個都可以指派位於相同子網路的位址。 
- VM 大小會決定您可以為 VM 建立的 NIC 數目。下表列出與 VM 大小相對應的 NIC 數目： 

|VM 大小 (標準 SKU)|NIC 數目 (每個 VM 允許的最大值)|
|---|---|
|所有基本大小|1|
|A0\超小型|1|
|A1\小型|1|
|A2\中型|1|
|A3\大型|2|
|A4\超大型|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|所有的其他大小|1|

## 網路安全性群組
VM 上的任何 NIC 可能會關聯至網路安全性群組 (NSG)，包括已啟用多個 NIC 功能的 VM 上的任何 NIC。如果已為 NIC 指派子網路 (該子網路會關聯至 NSG) 內的位址，則子網路 NSG 中的規則也適用於該 NIC。除了將子網路關聯至 NSG，您也可以將 NIC 關聯至 NSG。

如果將子網路關聯至 NSG，而且該子網路內的 NIC 會個別關聯至 NSG，則相關聯的 NSG 規則會根據傳入或傳出 NIC 的流量方向套用「流程順序」****：

- **連入流量**的目的地是有問題的流量中第一次流經子網路的 NIC，會在傳入 NIC 之前觸發子網路的 NSG 規則，然後觸發 NIC 的 NSG 規則。- **連出流量**的來源是有問題的流量中第一次從 NIC 流出的 NIC，會在流經子網路之前觸發 NIC 的 NSG 規則，然後觸發子網路的 NSG 規則。 

上圖顯示如何根據流量來完成 NSG 規則的應用 (從 VM 到子網路，或從子網路到 VM)。

## 如何設定多個 NIC 的 VM

下列指示將協助您建立多個 NIC 的 VM，其中包含 3 個 NIC：一個預設的 NIC 和兩個其他的 NIC。組態步驟將建立 VM，此 VM 將根據下列服務組態檔片段來設定：

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
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
	… Skip over the remainder section …
	</VirtualNetworkSite>


嘗試執行此範例的 PowerShell 命令之前，您需要具備下列必要條件。

- Azure 訂閱。
- 已設定的虛擬網路。如需 VNet 的詳細資訊，請參閱[虛擬網路概觀](https://msdn.microsoft.com/library/azure/jj156007.aspx)。
- 已下載並安裝最新版的 Azure PowerShell。請參閱[如何安裝和設定 Azure PowerShell](../install-configure-powershell)。

1. 從 Azure VM 映像庫中選取 VM 映像請注意，映像經常變更且可依區域取得。以下範例中指定的映像可能會變更，或者可能不在您的區域中，因此，請務必指定您需要的映像。 

	    $image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. 建立 VM 組態。

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. 建立預設的系統管理員登入。

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. 在 VM 組態中加入其他 NIC。

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. 指定預設 NIC 的子網路和 IP 位址。

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm Set-AzureStaticVNetIP  `
			-IPAddress "10.1.0.100" -VM $vm

1. 在虛擬網路中建立 VM。

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]您在此處指定的 VNet 必須已經存在 (如必要條件中所提及)。下列範例會指定名為 "MultiNIC VNet" 的虛擬網路。

## 另請參閱

[虛擬網路概觀](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[虛擬網路設定工作](https://msdn.microsoft.com/library/azure/jj156206.aspx)

[部落格文章 -  Azure 中具有多個 VM 的 NIC 和 VNet 應用裝置](../multiple-vm-nics-and-network-virtual-appliances-in-azure)

<!---HONumber=58--> 