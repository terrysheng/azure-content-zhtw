<properties
   pageTitle="Azure 資源管理員中的公用和私人 IP 位址 |Microsoft Azure"
   description="了解 Azure 資源管理員中的公用和私人 IP 位址"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="telmos" />

# Azure 資源管理員中的 IP 位址
您可以將 IP 位址指派給 Azure 資源，來與其他 Azure 資源、內部部署網路和網際網路進行通訊。您可以在 Azure 中使用兩種類型的 IP 位址：公用和私人。

公用 IP 位址用於與網際網路通訊，包括 Azure 公眾對應服務。

私人 IP 位址用於 Azure 虛擬網路 (VNet) 內的通訊，而當您使用 VPN 閘道或 ExpressRoute 電路將網路擴充至 Azure 時，則使用於內部部署網路內的通訊。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md).

## 公用 IP 位址
Azure 資源可透過公用 IP 位址來與網際網路和 Azure 公眾對應服務 (例如 [Azure Redis Cache](https://azure.microsoft.com/services/cache)、[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs)、[SQL Database](sql-database-technical-overview.md) 和 [Azure 儲存體](storage-introduction.md)) 進行通訊。

在 Azure 資源管理員中，[公用 IP](resource-groups-networking.md#public-ip-address) 位址是有自己的屬性的資源。您可以將公用 IP 位址資源與下列任何資源建立關聯：

- VM
- 網際網路對應負載平衡器
- VPN 閘道
- 應用程式閘道

### 配置方法
有兩種方法可將 IP 位址配置給公用 IP 資源：「動態」或「靜態」。預設配置方法是「動態」，此方法**不會**在建立 IP 位址時進行配置。相反地，公用 IP 位址會在您啟動 (或建立) 相關聯的資源 (例如 VM 或負載平衡器) 時進行配置。此 IP 位址會在您停止 (或刪除) 資源時釋出 。這會導致 IP 位址在您停止並啟動資源時變更。

若要確保相關聯資源的 IP 位址維持不變，您可以明確地將配置方法設定為「靜態」。在此情況下會立即指派 IP 位址。只有在您刪除資源或將其配置方法變更為「動態」時，才會釋出 IP 位址。

>[AZURE.NOTE]即使將配置方法設定為「靜態」，您也無法指定已指派給「公用 IP 資源」的實際 IP 位址。相反地，它是從建立資源的 Azure 位置中可用的 IP 位址集區進行配置。

靜態公用 IP 位址常使用於下列案例：

- 使用者必須更新防火牆規則才能與 Azure 資源通訊。
- 您的 DNS 名稱解析，其中的 IP 位址變更會需要更新 A 記錄。
- Azure 資源與其他使用以 IP 為基礎的安全性模型的 Web 服務通訊。
- 您使用已連結到 IP 位址的 SSL 憑證。

>[AZURE.NOTE]將公用 IP 位址 (動態/靜態) 配置給 Azure 資源的 IP 範圍清單已發佈於 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

### DNS 主機名稱解析
您可以指定公用 IP 資源的 DNS 網域名稱標籤，以建立 *domainnamelabel*.*location*.cloudapp.azure.com 與 Azure 受管理 DNS 伺服器中的公用 IP 位址的對應。比方說，如果您建立公用 IP 資源並以 **contoso** 作為**美國西部** Azure *位置*中的*domainnamelabel*，則完整網域名稱 (FQDN) **contoso.westus.cloudapp.azure.com** 會解析為資源的公用 IP 位址。您可以使用此 FQDN 來建立自訂網域 CNAME 記錄，其指向 Azure 中的公用 IP 位址。

>[AZURE.IMPORTANT]所建立的每個網域名稱標籤必須是 Azure 位置中唯一的。

### VM
您可以將公用 IP 位址指派給其**網路介面卡** (NIC)，以建立其與 [虛擬機器](virtual-machines-about.md) (VM) 的關聯。如果是多個 NIC 的 VM，您可以只將它指派給主要 NIC。您可以將動態或靜態公用 IP 位址指派給 VM。

### 網際網路對應負載平衡器
您可以將公用 IP 位址指派給負載平衡器**前端**組態，以建立其與 [Azure 負載平衡器](load-balancer-overview.md)的關聯。此公用 IP 位址可做為負載平衡的虛擬 IP 位址 (VIP)。您可以將動態或靜態公用 IP 位址指派給負載平衡器前端。您也可以將多個公用 IP 位址指派給一個負載平衡器前端，以實現[多重 vip](load-balancer-multivip.md) 案例 (例如具有多個 SSL 架構網站的多租用戶環境)。

### VPN 閘道
[Azure VPN 閘道](vpn-gateway-about-vpngateways.md)用來將 Azure 虛擬網路 (VNet) 連接到其他 Azure Vnet 或內部部署網路。您需要將公用 IP 位址指派給其 **IP 設定**，以便與遠端網路通訊。您目前只可以將動態公用 IP 位址指派給 VPN 閘道。

### 應用程式閘道
您可以將公用 IP 位址指派給閘道的**前端**組態，以建立其與 Azure [應用程式閘道](application-gateway-introduction.md)的關聯。此公用 IP 位址可做為負載平衡的 VIP。您目前只可以將動態公用 IP 位址指派給應用程式閘道前端組態。您也可以指派多個公用 IP 位址，以實現多重 vip 案例。

### 快速總覽
下表顯示每個資源類型與可能的配置方法 (動態/靜態)，以及指派多個公用 IP 位址的能力。

|資源|動態|靜態|多個 IP 位址|
|---|---|---|---|
|(VM 的) 網路介面卡 (NIC)|是|是|否|
|負載平衡器前端|是|是|是|
|VPN 閘道|是|否|否|
|應用程式閘道前端|是|否|否|

## 私人 IP 位址
私人 IP 位址可讓 Azure 資源透過 VPN 閘道或 ExpressRoute 電路，與[虛擬網路](virtual-networks-overview.md)(VNet) 中或內部部署網路中的其他資源進行通訊，而不必使用可網際網路連線的 IP 位址。

在 Azure 資源管理員部署模型中，私人 IP 位址會與各種 Azure 資源相關聯。

- VM
- 內部負載平衡器 (ILB)
- 應用程式閘道

### 配置方法
私人 IP 位址是從附加資源的子網路位址範圍進行配置。子網路本身的位址範圍是 VNet 位址範圍的一部分。

私人 IP 位址有兩種配置方法：「動態」或「靜態」。預設配置方法是「動態」，此方法會從資源的子網路自動配置 IP 位址 (使用 DHCP)。此 IP 位址可在您停止並啟動資源時變更。

您可以將配置方法設定為「靜態」以確保 IP 位址維持不變。在此情況下，您也需要提供屬於資源子網路的有效 IP 位址。

靜態私人 IP 位址通常用於：

- 做為網域控制站或 DNS 伺服器的 VM。
- 需要使用 IP 位址的防火牆規則的資源。
- 其他應用程式/資源透過 IP 位址存取的資源。

### VM
私人 IP 位址會指派給[虛擬機器](virtual-machines-about.md)的**網路介面卡** (NIC)。如果是多個 NIC 的 VM，每個 NIC 都會有指派的私人 IP 位址。您可以將 NIC 的配置方法指定為動態或靜態。

#### 內部 DNS 主機名稱解析 (適用於 VM)
除非明確設定自訂 DNS 伺服器，否則所有 Azure VM 預設都會設定 [Azure 受管理 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)。這些 DNS 伺服器會針對位於相同 VNet 的 VM 提供內部名稱解析。

當您建立 VM 時，會將主機名稱與其私人 IP 位址的對應加入至 Azure 受管理 DNS 伺服器。如果是多個 NIC 的 VM，主機名稱會對應至主要 NIC 的私人 IP 位址。

使用 Azure 受管理 DNS 伺服器設定的 VM，能夠將其 VNet 內所有 VM 的主機名稱解析為其私人 IP 位址。

### 內部負載平衡器 (ILB) 與應用程式閘道
您可以將私人 IP 位址指派給 [Azure 內部負載平衡器](load-balancer-internal-overview.md) (ILB) 或 [Azure 應用程式閘道](application-gateway-introduction.md)的**前端**組態。此私人 IP 位址可做為內部端點，只能存取其虛擬網路 (VNet) 內的資源與連線到 VNet 的遠端網路。您可以將動態或靜態私人 IP 位址指派給前端組態。您也可以指派多個私人 IP 位址，以實現多重 vip 案例。

### 快速總覽
下表顯示每個資源類型與可能的配置方法 (動態/靜態)，以及指派多個私人 IP 位址的能力。

|資源|靜態|動態|多個 IP 位址|
|---|---|---|---|
|虛擬機器 (VM)/網路介面卡 (NIC)|是|是|是|
|內部負載平衡器前端|是|是|是|
|應用程式閘道前端|是|是|是|

## 資源管理員與傳統部署之間的比較
以下是資源管理員與傳統部署模型中的 IP 位址比較。

||資源|傳統|資源管理員|
|---|---|---|---|
|**公用 IP 位址**|VM|稱為 ILPIP (僅限動態)|稱為公用 IP (動態或靜態)|
|||指派給 IaaS VM 或 PaaS 角色執行個體|與 VM 的 NIC 相關聯|
||網際網路對應負載平衡器|稱為 VIP (動態) 或保留的 IP (靜態)|稱為公用 IP (動態或靜態)|
|||指派給雲端服務|與負載平衡器的前端組態相關聯|
||||
|**私人 IP 位址**|VM|稱為 DIP|稱為私人 IP 位址|
|||指派給 IaaS VM 或 PaaS 角色執行個體|指派給 VM 的 NIC|
||內部負載平衡器 (ILB)|指派給 ILB (動態或靜態)|指派給 ILB 的前端組態 (動態或靜態)|

## 後續步驟
- [使用靜態公用 IP 部署 VM](virtual-network-deploy-static-pip-arm-template.md)
- [使用 Azure CLI 建立網際網路對應負載平衡器的公用 IP 位址](load-balancer-get-started-internet-arm-cli.md#create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool)
- [使用 PowerShell 建立應用程式閘道的公用 IP 位址](application-gateway-create-gateway-arm.md#create-public-ip-address-for-front-end-configuration)
- [使用 PowerShell 建立 VPN 閘道的公用 IP 位址](vpn-gateway-create-site-to-site-rm-powershell.md#4-request-a-public-ip-address-for-the-gateway)
- [使用靜態私人 IP 位址部署 VM](virtual-networks-static-private-ip-arm-pportal.md)
- [使用 PowerShell 建立內部負載平衡器的前端靜態私人 IP 位址](load-balancer-get-started-ilb-arm-ps.md#create-front-end-ip-pool-and-backend-address-pool)
- [使用 PowerShell 建立具有應用程式閘道之靜態私人 IP 位址的後端集區](application-gateway-create-gateway-arm.md#create-an-application-gateway-configuration-object)

<!---HONumber=AcomDC_1223_2015-->
