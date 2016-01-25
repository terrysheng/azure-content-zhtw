<properties
   pageTitle="Azure 中的公用和私人 IP 位址 (傳統) |Microsoft Azure"
   description="了解 Azure 中的公用和私人 IP 位址"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/12/2015"
   ms.author="telmos" />

# Azure 中的 IP 位址 (傳統)
您可以將 IP 位址指派給 Azure 資源，來與其他 Azure 資源、內部部署網路和網際網路進行通訊。您可以在 Azure 中使用兩種類型的 IP 位址：公用和私人。

公用 IP 位址用於與網際網路通訊，包括 Azure 公眾對應服務。

當您使用 VPN 閘道或 ExpressRoute 電路將網路擴充至 Azure 時，私人 IP 位址用於 Azure 虛擬網路 (VNet)、雲端服務和內部部署網路內的通訊。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員部署模型](virtual-network-ip-addresses-overview-arm.md)。

## 公用 IP 位址
Azure 資源可透過公用 IP 位址來與網際網路和 Azure 公眾對應服務 (例如 [Azure Redis Cache](https://azure.microsoft.com/services/cache)、[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs)、[SQL Database](sql-database-technical-overview.md) 和 [Azure 儲存體](storage-introduction.md)) 進行通訊。

公用 IP 位址與下列任何資源相關聯：

- 雲端服務
- IaaS 虛擬機器 (VM)
- PaaS 角色執行個體
- VPN 閘道
- 應用程式閘道

### 配置方法
當公用 IP 位址需要指派給 Azure 資源時，會從資源建立位置內的可用公用 IP 位址集區進行動態配置。此 IP 位址會在停止資源後釋出。就雲端服務而言，這會發生在所有角色執行個體都被停止的時候，而使用「靜態」(保留) IP 位址即可避免這種情形 (請參閱[雲端服務](#Cloud-services))。

>[AZURE.NOTE]將公用 IP 位址配置給 Azure 資源的 IP 範圍清單已發佈於 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

### DNS 主機名稱解析
當您建立雲端服務或 IaaS VM 時，您必須提供在 Azure 的所有資源中唯一的雲端服務 DNS 名稱。這會在 Azure 受管理 DNS 伺服器中建立 *dnsname*.cloudapp.net 與資源公用 IP 位址的對應。例如，當您建立雲端服務 DNS 名稱為 **contoso** 的雲端服務，則完整網域名稱 (FQDN) **contoso.cloudapp.net** 會解析為雲端服務的公用 IP 位址 (VIP)。您可以使用此 FQDN 來建立自訂網域 CNAME 記錄，其指向 Azure 中的公用 IP 位址。

### 雲端服務
雲端服務一律有公用 IP 位址，稱為虛擬 IP 位址 (VIP)。您可以在雲端服務中建立端點，以便讓 VIP 中不同的連接埠與 VM 上的內部連接埠和雲端服務內的角色執行個體建立關聯。

您可以將[多個公用 VIP 指派給一項雲端服務](load-balancer-multivip.md)，以實現多重 vip 案例 (例如具有多個 SSL 架構網站的多租用戶環境)。

即使所有角色執行個體都已停止，您可以使用「靜態」公用 IP 位址 (稱為 [保留 IP](virtual-networks-reserved-public-ip.md))，確保雲端服務的公用 IP 位址維持不變。您可以在特定位置建立靜態 (保留) IP 資源，並將它指派給該位置中的任何雲端服務。您無法指定保留 IP 的實際 IP 位址，它會從資源建立位置中可用的 IP 位址集區進行配置。直到您明確刪除，此 IP 位址才會釋出。

靜態 (保留) 公用 IP 位址常使用於下列案例，其中雲端服務：

- 需要由使用者設定防火牆規則。
- 取決於外部 DNS 名稱解析，而動態 IP 會需要更新 A 記錄。
- 會取用使用以 IP 為基礎之安全性模型的外部 Web 服務。
- 使用已連結到 IP 位址的 SSL 憑證。

### IaaS VM 和 PaaS 角色執行個體
您可以將公用 IP 位址指派給雲端服務內的 IaaS [VM](virtual-machines-about.md) 和 PaaS 角色執行個體。這也稱為執行個體層級公用 IP 位址 ([ILPIP](virtual-networks-instance-level-public-ip.md))。此公用 IP 位址只能是靜態。

### VPN 閘道
[VPN 閘道](vpn-gateway-about-vpngateways.md)可用來將 Azure VNet 連接到其他 Azure Vnet 或內部部署網路。VPN 閘道已被動態指派公用 IP 位址，以便與遠端網路通訊。

### 應用程式閘道
Azure [應用程式閘道](application-gateway-introduction.md)可用於 Layer7 負載平衡，以便路由傳送以 HTTP 為基礎的網路流量。應用程式閘道已被動態指派公用 IP 位址，該 IP 位址可做為負載平衡的 VIP。

### 速覽
下表顯示每個資源類型與可能的配置方法 (動態/靜態)，以及指派多個公用 IP 位址的能力。

|資源|動態|靜態|多個 IP 位址|
|---|---|---|---|
|雲端服務|是|是|是|
|IaaS VM 或 PaaS 角色執行個體|是|否|否|
|VPN 閘道|是|否|否|
|應用程式閘道|是|否|否|

## 私人 IP 位址
私人 IP 位址可讓 Azure 資源與雲端服務或[虛擬網路](virtual-networks-overview.md)(VNet) 或內部部署網路中的其他資源進行通訊 (透過 VPN 閘道或 ExpressRoute 電路)，而不必使用可網際網路連線的 IP 位址。

在 Azure 傳統部署模型中，可將私人 IP 位址指派給下列 Azure 資源：

- IaaS VM 和 PaaS 角色執行個體
- 內部負載平衡器
- 應用程式閘道

### IaaS VM 和 PaaS 角色執行個體
使用傳統部署模型建立的虛擬機器 (VM) 一律放置在雲端服務中 (類似於 PaaS 角色執行個體)。私人 IP 位址的行為因此類似於這些資源。

請務必注意，有兩種方式可以部署雲端服務：

- 成為獨立雲端服務，其不在虛擬網路中。
- 成為虛擬網路的一部分。

#### 配置方法
如果是獨立雲端服務，資源會取得從 Azure 資料中心私人 IP 位址範圍動態配置的私人 IP 位址。它只能用於與相同雲端服務內的其他 VM 通訊。此 IP 位址可在停止並啟動資源時變更。

如果是虛擬網路中部署的雲端服務，資源會取得從相關聯子網路的位址範圍配置的私人 IP 位址 (如其網路組態中指定)。此私人 IP 位址可用於 VNet 中所有 VM 之間的通訊。

此外，如果是 VNet 中的雲端服務，預設會動態配置私人 IP 位址 (使用 DHCP)。此 IP 位址可在停止並啟動資源時變更。若要確保 IP 位址維持不變，您需要將配置方法設為 [靜態]，並提供對應位址範圍內的有效 IP 位址。

靜態私人 IP 位址通常用於：

 - 做為網域控制站或 DNS 伺服器的 VM。
 - 需要使用 IP 位址的防火牆規則的 VM。
 - 正在執行其他應用程式透過 IP 位址存取之服務的 VM。

#### 內部 DNS 主機名稱解析
除非明確設定自訂 DNS 伺服器，否則所有 Azure VM 和 PaaS 角色執行個體預設都會設定 [Azure 受管理 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)。這些 DNS 伺服器會針對位於相同 VNet 或雲端服務內的 VM 和角色執行個體提供內部名稱解析。

當您建立 VM 時，會將主機名稱與其私人 IP 位址的對應加入至 Azure 受管理 DNS 伺服器。如果是多個 NIC 的 VM，主機名稱會對應至主要 NIC 的私人 IP 位址。不過，此對應資訊僅限於相同雲端服務或 VNet 內的資源。

如果是獨立雲端服務，您只能夠解析相同雲端服務內所有 VM/角色執行個體的主機名稱。如果是 VNet 內的雲端服務，您將能夠解析 VNet 內所有 VM/角色執行個體的主機名稱。

### 內部負載平衡器 (ILB) 與應用程式閘道
您可以將私人 IP 位址指派給 [Azure 內部負載平衡器](load-balancer-internal-overview.md) (ILB) 或 [Azure 應用程式閘道](application-gateway-introduction.md)的**前端**組態。此私人 IP 位址可做為內部端點，只能存取其虛擬網路 (VNet) 內的資源與連線到 VNet 的遠端網路。您可以將動態或靜態私人 IP 位址指派給前端組態。您也可以指派多個私人 IP 位址，以實現多重 vip 案例。

### 速覽
下表顯示每個資源類型與可能的配置方法 (動態/靜態)，以及指派多個私人 IP 位址的能力。

|資源|動態|靜態|多個 IP 位址|
|---|---|---|---|
|VM (位於獨立雲端服務中)|是|是|是|
|PaaS 角色執行個體 (位於獨立雲端服務中)|是|否|是|
|VM 或 PaaS 角色執行個體 (位於 VNet 中)|是|是|是|
|內部負載平衡器前端|是|是|是|
|應用程式閘道前端|是|是|是|

## 限制

下表顯示在 Azure 中，針對每一訂用帳戶在 IP 定址上所加諸的限制。您可以[連絡支援人員](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以根據您的業務需求將預設上限調升到最高上限。

||預設上限|最高上限|
|---|---|---|
|公用 IP 位址 (動態)|5|聯絡支援人員|
|保留的公用 IP 位址|20|聯絡支援人員|
|每一部署 (雲端服務) 的公用 VIP|5|聯絡支援人員|
|每一部署 (雲端服務) 的私人 VIP (ILB)|1|1|

請務必閱讀 Azure 中的整套[網路限制](azure-subscription-service-limits.md#networking-limits)。

## 定價

在大多數情況下，公用 IP 位址是免費的。使用額外和 (或) 靜態公用 IP 位址則會有少許費用。請務必了解[公用 IP 的定價結構](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## 資源管理員與傳統部署之間的差異
以下是「資源管理員」與傳統部署模型中 IP 定址功能的比較。

||資源|傳統|資源管理員|
|---|---|---|---|
|**公用 IP 位址**|VM|稱為 ILPIP (僅限動態)|稱為公用 IP (動態或靜態)|
|||指派給 IaaS VM 或 PaaS 角色執行個體|與 VM 的 NIC 相關聯|
||網際網路對應負載平衡器|稱為 VIP (動態) 或保留的 IP (靜態)|稱為公用 IP (動態或靜態)|
|||指派給雲端服務|與負載平衡器的前端組態相關聯|
|||
|**私人 IP 位址**|VM|稱為 DIP|稱為私人 IP 位址|
|||指派給 IaaS VM 或 PaaS 角色執行個體|指派給 VM 的 NIC|
||內部負載平衡器 (ILB)|指派給 ILB (動態或靜態)|指派給 ILB 的前端組態 (動態或靜態)|

## 後續步驟
- 使用傳統入口網站[部署使用靜態私人 IP 位址的 VM](virtual-networks-static-private-ip-classic-pportal.md)。

<!---HONumber=AcomDC_0114_2016-->