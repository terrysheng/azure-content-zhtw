<properties 
   pageTitle="Azure 網路資源提供者中的公用和私人 IP 位址 | Microsoft Azure"
   description="了解 Azure 資源管理員中網路資源提供者的公用和私人 IP 位址"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Azure 虛擬網路中的 IP 位址
本文涵蓋虛擬機器、負載平衡器、VPN 閘道和應用程式閘道的 IP 位址。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]。

## 公用 IP 位址
Azure 資源可透過公用 IP 位址來與網際網路和 [Azure Redis 快取](https://azure.microsoft.com/services/cache)、[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs)等其他 Azure 公用端服務進行通訊。公用 IP 位址是獨立的資源，而且可以關聯到不同類型的 Azure 資源，例如[虛擬機器](virtual-machines-about.md) (VM) 和[負載平衡器](load-balancer-overview.md) (LB)。

### 公用 IP 位址配置方法
所能配置的公用 IP 位址是來自給定位置所提供的 IP 位址集區。[Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)有發佈 Microsoft Azure 資料中心所使用的 IP 位址範圍完整清單。

有兩種方法可將 IP 位址配置給公用 IP 資源：*動態*或*靜態*。預設配置方法是*動態*。如果是*動態*，系統**不會**在建立*公用 IP 位址資源*時就將 IP 位址配置給資源。在建立或啟動與*公用 IP 位址資源*相關聯的資源 (例如 VM 或負載平衡器) 時，從會從 IP 位址集區配置 IP 位址。當相關聯的資源遭到刪除或停止時，便會取消配置此 IP 位址並將其釋出到可用集區。

如果使用*靜態*配置方法，則會在建立*公用 IP 位址資源*時就將 IP 位址配置給資源。在此情況下，不論相關聯的資源狀態為何，仍會繼續配置同一個 IP 位址。只有在*公用 IP 位址資源*遭到刪除或其配置方法修改成*動態*時，才會將此 IP 位址釋出到可用集區。

### DNS 解析
您可以為公用 IP 資源指定 DNS 網域名稱標籤，以在 Azure DNS 伺服器中建立對應的 DNS 項目。對於配置給公用 IP 資源的 IP 位址來說，對應的 FQDN *domainnamelabel*.*location*.cloudapp.azure.com 是可全域解析的。

我們將會在下面探討可與公用 IP 位址相關聯的不同資源類型。

### 虛擬機器
公用 IP 位址會透過網路介面卡 (NIC) 來與[虛擬機器](virtual-machines-about.md) (VM) 關聯。不論 VM 有多少個關聯的 NIC，它都只能有一個公用 IP 位址。只有*動態*配置的*公用 IP 位址資源*可以與 VM NIC 相關聯。如果是有多個 NIC 的 VM，*公用 IP 位址資源*只可以與主要 NIC 相關聯。

### Azure 負載平衡器
公用 IP 位址可以與 [Azure 負載平衡器](load-balancer-overview.md) (LB) 前端組態相關聯，以做為可透過網際網路來進行連線的負載平衡虛擬 IP 位址 (VIP)。*動態*或*靜態*公用 IP 資源都可以與 LB 相關聯。您可以將多個*公用 IP 位址資源*關聯到 LB 前端組態，以實現具有多個 SSL 架構網站的多租用戶環境之類的案例。

### 應用程式閘道
公用 IP 位址可以與 [Azure 應用程式閘道](application-gateway-introduction.md)前端組態相關聯，以對其設定可網際網路連線的負載平衡虛擬 IP (VIP)。目前只有*動態*配置的公用 IP 資源可以與應用程式閘道相關聯。不過，可以關聯多個公用 IP 位址。

### VPN 閘道
在 Azure 虛擬網路與內部部署網路或其他 Azure 虛擬網路之間建立 VPN 連線時，公用 IP 位址必須與 [Azure VPN 閘道](vpn-gateway-about-vpngateways.md) IP 組態相關聯。目前只有*動態*配置的公用 IP 資源可以與 VPN 閘道相關聯。

### 快速總覽

|資源|靜態配置|動態配置|多個 IP 位址|
|---|---|---|---|
|虛擬機器 (VM)/網路介面卡 (NIC)|是|否|否|
|負載平衡器前端|是|是|是|
|應用程式閘道前端|是|否|是|
|VPN 閘道|是|否|否|

## 私人 IP 位址
私人 IP 位址可讓虛擬網路中的資源彼此通訊，而不必使用可網際網路連線的 IP 位址。所配置的這個 IP 位址是來自虛擬網路內的子網路位址範圍。

### 私人 IP 位址配置方法
IP 位址有兩種配置方法：*動態*或*靜態*。預設配置方法是*動態*，此方法會使用 DHCP 配置 IP 位址。或者，也可以明確地將配置方法指定為*靜態*，並同時指定 IP 位址。在此情況下，只要指定的 IP 位址是在子網路的位址範圍內且為可用狀態 (未配置給任何其他資源)，就能將該位址配置給資源。

您可以對不同的資源類型指派私人 IP 位址。請注意，兩種配置方法 (*靜態*或*動態*) 皆適用於所有這些資源類型。

### 虛擬機器
對於[虛擬機器](virtual-machines-about.md) (VM) 的網路介面卡 (NIC)，系統會指派私人 IP 位址。VM 有多少個相關聯的 NIC，就能有多少個私人 IP 位址。

#### 內部 DNS 主機名稱解析
除非在建立期間有明確指定，否則所有 Azure VM 都會設定 [Azure 管理的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)。如果使用 Azure 管理的 DNS 伺服器，就會自動建立 DNS 記錄，以將 VM 的主機名稱解析為 VM 的私人 IP 位址。如果是多個 NIC 的 VM，主機名稱會解析為主要 NIC 的私人 IP 位址。

### 內部負載平衡器
私人 IP 位址可以指派給 [Azure 內部負載平衡器](load-balancer-internal-overview.md) (ILB) 前端，以做為其虛擬網路內資源的負載平衡虛擬 IP 位址 (VIP)。這樣就能進行負載平衡，但不必對網際網路公開 IP 位址。

### 應用程式閘道
私人 IP 位址可以指派給 [Azure 應用程式閘道](application-gateway-introduction.md)前端，以對其設定不會對網際網路公開的內部端點 (也稱為內部負載平衡器 (ILB) 端點)。其行為和配置方法類似上述的 ILB。

### 快速總覽
|資源|靜態配置|動態配置|多個 IP 位址|
|---|---|---|---|
|虛擬機器 (VM)/網路介面卡 (NIC)|是|是|是|
|負載平衡器前端|是|是|是|
|應用程式閘道前端|是|是|是|

## 後續步驟


[Azure PowerShell 網路參考](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure 資源管理員範本語言](../resource-group-authoring-templates.md)

[Azure 網路 - 常用範本](https://github.com/Azure/azure-quickstart-templates)

[計算資源提供者](../virtual-machines-azurerm-versus-azuresm)

[Azure 資源管理員概觀](../resource-group-overview)

[Azure 資源管理員中的角色存取控制](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[在 Azure 資源管理員中使用標記](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[範本部署](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!-------HONumber=AcomDC_1210_2015--->