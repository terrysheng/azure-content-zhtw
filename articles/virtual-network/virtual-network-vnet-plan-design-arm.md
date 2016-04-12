<properties
   pageTitle="Azure 虛擬網路 (VNet) 規劃與設計指南 | Microsoft Azure"
   description="了解如何根據您的隔離、連線和位置需求，在 Azure 中規劃及設計虛擬網路。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/08/2016"
   ms.author="telmos" />

# 規劃和設計 Azure 虛擬網路

建立要實驗的 VNet 很簡單，但關鍵在於，您會隨著時間部署多個 Vnet，以支援您的組織的生產需求。經過一些規劃和設計，您就能夠部署 Vnet 且更有效率地連接您所需的資源。如果您不熟悉 Vnet，建議您先[深入了解 Vnet](virtual-networks-overview.md) 和[如何部署](virtual-networks-create-vnet-arm-pportal.md) Vnet，再繼續執行。

## 規劃

徹底了解 Azure 訂用帳戶、區域和網路資源是成功的關鍵。您可以使用以下的考量事項清單做為起點。一旦了解這些考量，您即可定義網路設計需求。

### 注意事項

在回答底下的規劃問題之前，請先考慮下列各項：

- 您在 Azure 中建立的所有項目都是由一或多個資源所組成。虛擬機器 (VM) 是資源、VM 所用的網路介面卡 (NIC) 是資源、NIC 所用的公用 IP 位址是資源、NIC 所連接的 VNet 是資源。
- 您可在 [Azure 區域](https://azure.microsoft.com/regions/#services)和訂用帳戶中建立資源。而資源只能連接到存在於相同區域和訂用帳戶中的 VNet。 
- 您也可以使用 Azure [VPN 閘道](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)讓 Vnet 彼此連接。您也可以用這種方式連接各區域和訂用帳戶的 Vnet。
- 您可以使用 Azure 中提供的其中一個[連線選項](../vpn-gateway/vpn-gateway-cross-premises-options.md)將 VNet 連接到內部部署網路。 
- 不同的資源可以聚集在[資源群組](../resource-group-overview.md#resource-groups)中，更容易以單位形式管理資源。資源群組可以包含來自多個區域的資源，只要資源屬於相同的訂用帳戶即可。

### 定義需求

使用下列問題做為您的 Azure 網路設計起點。

1. 您會使用哪些 Azure 位置來託管 Vnet？
2. 您是否需要在這些 Azure 位置之間提供通訊？
3. 您是否需要提供 Azure VNet 與您的內部部署資料中心之間的通訊？
4. 您的解決方式需要多少個基礎結構即服務 (IaaS) VM、雲端服務角色和 Web 應用程式？
5. 您是否需要根據 VM 群組隔離流量 (也就是前端 Web 伺服器和後端資料庫伺服器)？
6. 您是否需要使用虛擬應用裝置來控制流量？
7. 使用者是否需要不同 Azure 資源的不同權限？

### 了解 VNet 和子網路屬性

VNet 和子網路資源有助於定義在 Azure 中執行之工作負載的安全性範圍。VNET 可由一組位址空間集合所界定 (此集合定義為 CIDR 區塊)。

>[AZURE.NOTE] 網路管理員都熟悉 CIDR 標記法。如果您不熟悉 CIDR，請[深入了解](http://whatismyipaddress.com/cidr)。

VNET 包含下列屬性：

|屬性|說明|條件約束|
|---|---|---|
|**name**|VNet 名稱|最多 80 個字元的字串。可以包含字母、數字、底線、句號或連字號。必須以字母或數字開頭。必須以字母、數字或底線結尾。可以包含大寫或小寫字母。|  
|**位置**|Azure 位置 (也稱為區域)。|必須是其中一個有效的 Azure 位置。|
|**addressSpace**|CIDR 標記法中，構成 VNET 的位址首碼的集合。|必須是有效的 CIDR 位址區塊陣列，包括公用 IP 位址範圍。|
|**子網路**|構成 VNET 的子網路的集合|請參閱下面的子網路屬性表格。||
|**dhcpOptions**|包含名為 **dnsServers** 之單一必要屬性的物件。||
|**dnsServers**|VNet 所使用的 DNS 伺服器陣列。如果未指定任何伺服器，則會使用 Azure 內部名稱解析。|必須是最多 10 個 DNS 伺服器的陣列 (依 IP 位址排列)。| 

子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。NIC 可以加入子網路和連接到 VM，提供各種工作負載的連線。

子網路包含下列屬性：

|屬性|說明|條件約束|
|---|---|---|
|**name**|子網路名稱|最多 80 個字元的字串。可以包含字母、數字、底線、句號或連字號。必須以字母或數字開頭。必須以字母、數字或底線結尾。可以包含大寫或小寫字母。|
|**位置**|Azure 位置 (也稱為區域)。|必須是其中一個有效的 Azure 位置。|
|**addressPrefix**|CIDR 標記法中，構成的子網路的單一位址首碼|必須是屬於 VNet 的其中一個位址空間的單一 CIDR 區塊。|
|**networkSecurityGroup**|套用至子網路的 NSG|請參閱 [NSG](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|套用至子網路的路由表|請參閱 [UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|NIC 用於連線至子網路的 IP 設定物件的集合|請參閱 [IP 設定](../resource-groups-networking.md#IP-configurations)|

### 名稱解析

根據預設，您的 VNet 會使用 [Azure 提供的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution)來解析 VNet 內部和公用網際網路上的名稱。不過，如果您要將 Vnet 連接到您的內部部署資料中心，您需要提供[自己的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server)來解析您網路之間的名稱。

### 限制

請確定您在設計方案之前，已檢視所有[在 Azure 中與網路服務相關的限制](../azure-subscription-service-limits#networking-limits)。您可以開啟支援票證來提高部分限制。

### 角色型存取控制 (RBAC)

您可以使用 [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) 來控制不同使用者對於 Azure 中不同資源的存取層級。如此一來，您可以根據小組需求分隔其所完成的工作。

只要已連接虛擬網路，具備**網路參與者**角色的使用者即可完全控制 Azure 資源管理員的虛擬網路資源。同樣地，具備**傳統網路參與者**角色的使用者可完全控制傳統的虛擬網路資源。

>[AZURE.NOTE] 您也可以[建立自己的角色](../active-directory/role-based-access-control-configure.md)，以區分您的系統管理需求。

## 設計

一旦您知道[計劃](#Plan)小節中問題的答案，在定義您的 VNet 之前，請檢閱下列項目。

### 訂用帳戶和 VNet 數目

您應該考慮在下列情況下建立多個 Vnet：

- **需要放在不同 Azure 位置的 VM**。Azure 中的 Vnet 有區域性。不能跨區使用。因此，對於您要託管 VM 的每個 Azure 位置，至少需要有一個 VNet。
- **工作負載必須彼此完全分隔**。您可以建立個別的 VNet (其甚至使用相同的 IP 位址空間)，以找出彼此不同的工作負載。 
- **避免平台限制**。如[限制](#Limits)一節所示，單一 VNet 中不能有超過 2048 個 VM。 

請記住，您在上面看見的限制是依每個區域、每個訂用帳戶而定。這表示您可以使用多個訂用帳戶，以增加能夠在 Azure 中維護的資源限制。您可以使用站對站 VPN 或 ExpressRoute 電路來連接不同訂用帳戶中的 Vnet。

### 訂用帳戶和 VNet 設計模式

下表顯示使用訂用帳戶和 Vnet 的一些常見的設計模式。

|案例|圖表|優點|缺點|
|---|---|---|---|
|單一訂用帳戶，每個應用程式有兩個 Vnet|![單一訂用帳戶](./media/virtual-network-vnet-plan-design-arm/figure1.png)|只要管理一個訂用帳戶。|每個 Azure 區域最多 25 個應用程式。您之後會需要更多訂用帳戶。|
|每個應用程式有一個訂用帳戶，每個應用程式有兩個 VNet|![單一訂用帳戶](./media/virtual-network-vnet-plan-design-arm/figure2.png)|每個訂用帳戶只使用兩個 VNet。|應用程式過多時會比較難以管理。|
|每個業務單位有一個訂用帳戶，每個應用程式有兩個 VNet。|![單一訂用帳戶](./media/virtual-network-vnet-plan-design-arm/figure3.png)|平衡訂用帳戶與 VNet 的數目。|每個業務單位 (訂用帳戶) 最多 25 個應用程式。|
|每個業務單位有一個訂用帳戶，每個應用程式群組有兩個 VNet。|![單一訂用帳戶](./media/virtual-network-vnet-plan-design-arm/figure4.png)|平衡訂用帳戶與 VNet 的數目。|必須使用子網路和 NSG 隔離應用程式。|


### 子網路數目

您應該考慮在下列情況下，在一個 VNet 中建立多個子網路：

- **子網路中的所有 NIC 沒有足夠的私人 IP 位址**。如果您的子網路位址空間包含的 IP 位址不足以支援子網路中的 NIC 數目，則需要建立多個子網路。請記住，Azure 會在每個子網路中保留 5 個不能使用的私人 IP 位址：位址空間的第一個和最後一個位址 (用於子網路位址，以及多點傳送) 以及要內部使用的 3 個位址 (用於 DHCP 和 DNS)。 
- **安全性**。您可以使用子網路分隔有多層結構的工作負載 VM 群組，然後對這些子網路套用不同的[網路安全性群組 (NSG)](virtual-networks-nsg.md#subnets)。
- **混合式連線**。您可以使用 VPN 閘道與 ExpressRoute 電路讓 VNet 彼此[連接](../vpn-gateway/vpn-gateway-cross-premises-options.md)，並連接至您的內部部署資料中心。VPN 閘道與 ExpressRoute 電路需要建立自己的子網路。
- **虛擬應用裝置**。您可以在 Azure VNet 中使用虛擬應用裝置，例如防火牆、WAN 加速器或 VPN 閘道。當您這樣做時，需要[將傳送流量路由](virtual-networks-udr-overview.md)到這些應用裝置，並將它們隔離在自己的子網路中。

### 子網路和 NSG 設計模式

下表顯示使用子網路的一些常見的設計模式。

|案例|圖表|優點|缺點|
|---|---|---|---|
|單一子網路，每個應用程式層的每個應用程式有多個 NSG|![單一子網路](./media/virtual-network-vnet-plan-design-arm/figure5.png)|只要管理一個子網路。|隔離每個應用程式所需的多個 NSG。|
|每個應用程式有一個子網路，每個應用程式層有多個 NSG|![每個應用程式的子網路](./media/virtual-network-vnet-plan-design-arm/figure6.png)|要管理的 NSG 比較少。|要管理多個子網路。|
|每個應用程式層有一個子網路，每個應用程式有多個 NSG。|![每一層級的子網路](./media/virtual-network-vnet-plan-design-arm/figure7.png)|平衡子網路與 NSG 的數目。|最多 100 個 NSG。如果每個應用程式需要 2 個不同的 NSG，則有 50 個應用程式。|
|每個應用程式層的每個應用程式有一個子網路，每個子網路有多個 NSG|![每個應用程式的每個層級的子網路](./media/virtual-network-vnet-plan-design-arm/figure8.png)|NSG 數目可能比較少。|要管理多個子網路。|

## 設計範例

為了說明本文中的資訊應用，請考慮下列案例。

您任職的公司在北美洲有 2 個資料中心，在歐洲也有兩個資料中心。您發現有 2 個不同的業務單位維護 6 個不同的客戶面向應用程式，而您想要將這些應用程式移轉至 Azure 做為先驅。應用程式的基本架構如下所示：

- App1、App2、App3 和 App4 是在執行 Ubuntu 的 Linux 伺服器上裝載的 Web 應用程式。每個應用程式會連接到個別應用程式伺服器，這些伺服器在 Linux 伺服器上託管 RESTful 服務。RESTful 服務會連接到後端 MySQL 資料庫。
- App5 和 App6 是在執行 Windows Server 2012 R2 的 Windows 伺服器上裝載的 Web 應用程式。每個應用程式都會連接到後端 SQL Server 資料庫。
- 所有應用程式目前都裝載於北美洲的其中一個公司資料中心。
- 內部部署資料中心會使用 10.0.0.0/8 位址空間。

您需要設計符合下列需求的虛擬網路解決方案：

- 每個業務單位不應該受其他業務單位的資源耗用量所影響。
- 您應該盡可能減少 VNet 和子網路的數量，以便管理。
- 每個業務單位應該具有適用於所有應用程式的單一測試/開發 VNet。
- 每個應用程式會裝載於每個大陸 (北美洲與歐洲) 的 2 個不同 Azure 資料中心。
- 每個應用程式會彼此完全隔離。
- 客戶可以使用 HTTP 透過網際網路存取每個應用程式。
- 使用者可以透過加密的通道，存取連接到內部部署資料中心的每個應用程式。
- 對內部部署資料中心的連線應使用現有的 VPN 裝置。
- 公司的網路群組應可完全控制 VNet 設定。
- 每個業務單位的開發人員應該只能將 VM 部署到現有的子網路。
- 所有應用程式將跟著移轉至 Azure (隨即轉移)。
- 每個位置的資料庫應該每天複寫到其他 Azure 地點一次。
- 每個應用程式應使用 5 個前端 Web 伺服器、2 個應用程式伺服器 (必要時) 以及 2 個資料庫伺服器。

### 規劃

您應該如下所示回答[定義需求](#Define-requirements)一節中的問題，以便開始您的設計規劃。

1. 您會使用哪些 Azure 位置來託管 Vnet？

	北美洲的 2 個位置，以及歐洲的 2 個位置。您應該根據現有內部部署資料中心的實體位置進行挑選。如此一來，從您的實體位置到 Azure 會有較佳的連線延遲性。

2. 您是否需要在這些 Azure 位置之間提供通訊？

	是。因為資料庫必須複寫到所有位置。

3. 您是否需要在 Azure VNet 與您的內部部署資料中心之間提供通訊？

	是。因為連接到內部部署資料中心的使用者必須能夠透過加密的通道來存取每個應用程式。
 
4. 您的解決方案需要多少部 IaaS VM？

	200 部 IaaS VM。App1、App2 和 App3 各需要 5 部 Web 伺服器每個、2 部應用程式伺服器，以及 2 部資料庫伺服器。總計每個應用程式有 9 部 IaaS VM，或 36 部 IaaS VM。App5 和 App6 各需要 5 部 Web 伺服器和 2 部資料庫伺服器。總計每個應用程式有 7 部 IaaS VM，或 14 部 IaaS VM。因此，每個 Azure 區域中的所有應用程式需要 50 部 IaaS VM。因為我們需要使用 4 個區域，所以會有 200 部 IaaS VM。

	您也必須在每個 VNet 或您的內部部署資料中心提供 DNS 伺服器，以解析 Azure IaaS VM 和內部部署網路之間的名稱。

5. 您是否需要根據 VM 群組隔離流量 (也就是前端 Web 伺服器和後端資料庫伺服器)？

	是。每個應用程式應該彼此完全隔離，而且每個應用程式層也應該隔離。

6. 您是否需要使用虛擬應用裝置來控制流量？

	否。虛擬應用裝置可用來提供更充分的流量控制，包括更多詳細的資料層記錄。

7. 使用者是否需要不同 Azure 資源的不同權限？

	是。網路小組必須能完全控制虛擬網路設定，而開發人員應該只能將其 VM 部署到現有的子網路。

### 設計

您應該遵循指定訂用帳戶、VNet、子網路和 NSG 使用的設計。我們會在此討論 NSG，但您應該先深入了解 [NSG](virtual-networks-nsg.md)，再完成您的設計。

**訂用帳戶和 VNet 數目**

下列需求與訂用帳戶和 VNet 相關：

- 每個業務單位不應該受其他業務單位的資源耗用量所影響。
- 您應該盡可能減少 VNet 和子網路的數量。
- 每個業務單位應該具有適用於所有應用程式的單一測試/開發 VNet。
- 每個應用程式會裝載於每個大陸 (北美洲與歐洲) 的 2 個不同 Azure 資料中心。

根據上述需求，每個業務單位都需有一個訂用帳戶。如此一來，業務單位的資源耗用量就不會計入其他業務單位的限制。而且因為您想要將 VNet 數目降到最低，所以應該考慮使用**每個業務單位有一個訂用帳戶，每個應用程式群組有兩部 VNet** 的模式，如下所示。

![單一訂用帳戶](./media/virtual-network-vnet-plan-design-arm/figure9.png)

您也需要為每個 VNet 指定位址空間。因為您需要內部部署資料中心與 Azure 區域之間的連線，所以用於 Azure VNet 的位址空間不與內部部署網路衝突，而每個 VNet 所用的位址空間不得與其他現有的 VNet 衝突。您可以使用下表中的位址空間，以滿足這些需求。

|**訂用帳戶**|**VNet**|**Azure 區域**|**位址空間**|
|---|---|---|---|
|BU1|ProdBU1US1|美國西部|172\.16.0.0/16|
|BU1|ProdBU1US2|美國東部|172\.17.0.0/16|
|BU1|ProdBU1EU1|北歐|172\.18.0.0/16|
|BU1|ProdBU1EU2|西歐|172\.19.0.0/16|
|BU1|TestDevBU1|美國西部|172\.20.0.0/16|
|BU2|TestDevBU2|美國西部|172\.21.0.0/16|
|BU2|ProdBU2US1|美國西部|172\.22.0.0/16|
|BU2|ProdBU2US2|美國東部|172\.23.0.0/16|
|BU2|ProdBU2EU1|北歐|172\.24.0.0/16|
|BU2|ProdBU2EU2|西歐|172\.25.0.0/16|

**子網路數和 NSG 的數目**

下列需求與子網路和 NSG 相關：

- 您應該盡可能減少 VNet 和子網路的數量。
- 每個應用程式會彼此完全隔離。
- 客戶可以使用 HTTP 透過網際網路存取每個應用程式。
- 使用者可以透過加密的通道，存取連接到內部部署資料中心的每個應用程式。
- 對內部部署資料中心的連線應使用現有的 VPN 裝置。
- 每個位置的資料庫應該每天複寫到其他 Azure 地點一次。

您可以根據這些需求，在每個應用程式層中使用一個子網路，並使用 NSG 來篩選每個應用程式的流量。如此一來，每個 VNet 只有 3 個子網路 (前端、應用程式層和資料層)，而每個子網路的每個應用程式有一個 NSG。在此情況下，您應該考慮使用**每個應用程式層有一個子網路，每個應用程式有多個 NSG** 設計模式。下圖說明如何使用代表 **ProdBU1US1** VNet 的設計模式。

![每一層級一個子網路，每一層級的每個應用程式一個 NSG](./media/virtual-network-vnet-plan-design-arm/figure11.png)

不過，您也需要針對 VNet 與內部部署資料中心之間的 VPN 連線，建立額外的子網路。而且需要為每個 VNet 指定位址空間。下圖顯示 **ProdBU1US1** VNet 的範例解決方案。您會為每個 VNet 複寫此案例。每個顏色都代表不同的應用程式。

![VNet 範例](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**存取控制**

下列需求與存取控制相關：

- 公司的網路群組應可完全控制 VNet 設定。
- 每個業務單位的開發人員應該只能將 VM 部署到現有的子網路。

根據上述需求，您可以將網路小組的使用者加入至每個訂用帳戶中的內建**網路參與者**角色，並且為每個訂用帳戶中的應用程式開發人員建立自訂角色，並賦予他們將 VM 加入至現有子網路的權限。

## 後續步驟

- 根據案例[部署虛擬網路](virtual-networks-create-vnet-arm-template-click.md)。
- 了解如何使 IaaS VM [負載平衡](../load-balancer/load-balancer-overview.md)及[管理多個 Azure 區域的路由傳送](../traffic-manager/traffic-manager-overview.md)。
- 深入了解[NSG，以及如何規劃和設計](virtual-networks-nsg.md) NSG 方案。
- 深入了解您的[跨單位和 VNet 連線選項](../vpn-gateway/vpn-gateway-cross-premises-options.md)。  

<!---HONumber=AcomDC_0330_2016-->