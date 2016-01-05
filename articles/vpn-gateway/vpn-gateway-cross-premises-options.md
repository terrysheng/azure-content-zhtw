<properties 
   pageTitle="關於虛擬網路的安全跨單位連線 | Microsoft Azure"
   description="深入了解虛擬網路的安全跨單位連線類型，包括站對站、點對站、ExpressRoute 連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="cherylmc" />

# 關於虛擬網路的安全跨單位連線

如果您想要安全地將內部部署網站連接到虛擬網路，有三個選項可供使用：站對站、點對站和 ExpressRoute。

您選擇的選項可能取決於各種不同的考量，例如：


- 您的方案需要哪種輸送量?
- 您想讓通訊透過經由安全 VPN 的公用網際網路或透過私人連線?
- 您有可供使用的公用 IP 位址嗎?
- 您打算使用 VPN 裝置嗎? 如果是，它相容嗎？
- 您只連線幾台電腦或您想要網站持續連線?
- 您想要建立的方案需要什麼類型的 VPN 閘道?

下表可以協助您為方案決定最佳的連線選項。


| - | **點對站** | **站對站** | **ExpressRoute** |
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------| 
| **Azure 支援的服務** | 雲端服務和虛擬機器 | 雲端服務和虛擬機器 | [服務清單](../expressroute/expressroute-faqs.md#supported-services) | 
| **一般頻寬** | 通常是 < 100 Mbps 彙總 | 通常是 < 100 Mbps 彙總 | 50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps | 
| **支援的通訊協定** | 安全通訊端通道通訊協定 (SSTP) | IPsec | 透過 VLAN 直接連接、NSP 的 VPN 技術 (MPLS、VPLS...) | 
| **路由** | 路由型 (動態) | 我們支援原則型 (靜態路由)和路由型 (動態路由 VPN) | BGP | 
| **連接恢復能力** | 主動-被動 | 主動-被動 | 主動-主動 | 
| **一般使用案例** | 雲端服務和虛擬機器的原型設計、開發/測試/實驗室案例 | 雲端服務和虛擬機器的開發/測試/實驗室案例和小規模生產工作負載 | 存取所有 Azure 服務 (已驗證的清單)、企業級和關鍵任務工作負載、備份、巨量資料、Azure 即 DR 網站 | 
| **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | 
| **定價** | [定價](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [定價](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [定價](http://azure.microsoft.com/pricing/details/expressroute/) | 
| **技術文件** | [VPN 閘道文件](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [VPN 閘道文件](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [ExpressRoute 文件](https://azure.microsoft.com/documentation/services/expressroute/) | 
| **常見問題集** | [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md) | [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md) | [ExpressRoute 常見問題集](../expressroute/expressroute-faqs.md) |


## 站對站連接

站對站 VPN 可讓您在您的內部部署網站和虛擬網路之間建立安全的連線。若要建立站對站連線，位於內部部署網路上的 VPN 裝置要設定為以 Azure VPN 閘道建立安全連線。一旦建立連線，您區域網路上的資源以及您虛擬網路中的資源可以進行直接且安全的通訊。站對站連線不需要為區域網路上每個用戶端電腦的虛擬網路資源存取建立個別連線。

**使用站對站連線的時機：**

- 您想建立混合式解決方案。
- 您想要在內部部署位置與虛擬網路之間建立不需要用戶端設定的連線。
- 您想要持續不中斷的連線。 

**需求**

- 內部部署 VPN 裝置必須有連結網際網路的 IPv4 IP 位址。這不能在 NAT 後方。
- 您必須擁有相容的 VPN 裝置。請參閱[關於 VPN 裝置](http://go.microsoft.com/fwlink/p/?LinkID=615099)。 
- 您使用的 VPN 裝置必須與您的方案需要的閘道類型相容。請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。
- 閘道 SKU 也會影響彙總輸送量。如需詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpngateways.md#gateway-skus)。 

如需使用 Azure 傳統入口網站和傳統部署模型來設定站對站 VPN 閘道連接的相關資訊，請參閱[設定具有站對站 VPN 連接的虛擬網路](vpn-gateway-site-to-site-create.md)。如需使用資源管理員部署模型來設定站對站 VPN 的相關資訊，請參閱[建立具有站對站 VPN 連接的虛擬網路](vpn-gateway-create-site-to-site-rm-powershell.md)。


## 點對站連接

點對站 VPN 可讓您建立與虛擬網路的安全連線。在點對站設定中，連線是在您想要連線到虛擬網路的每個用戶端電腦上個別設定。點對站連線不需要 VPN 裝置。這種連線會使用您在每部用戶端電腦上安裝的 VPN 用戶端。VPN 的建立方式是從內部部署用戶端電腦手動啟動連線。

點對站和站對站設定可以同時存在，但與站對站連線不同的是，無法設定點對站連線同時與 ExpressRoute 連線到相同的虛擬網路。

**使用點對站連線的時機：**

- 您只想設定幾個用戶端連線到虛擬網路。

- 您想要從遠端位置連接至您的虛擬網路。例如，從咖啡館或會議室連線。

- 您有站對站連線，但有一些需要從遠端位置連線的用戶端。

- 您不能存取符合站對站連線最低需求的 VPN 裝置。

- 您的 VPN 裝置沒有連結網際網路的 IPv4 IP 位址。

如需設定點對站連接的詳細資訊，請參閱[設定虛擬網路的點對站 VPN 連接](vpn-gateway-point-to-site-create.md)。

## ExpressRoute 連線

Azure ExpressRoute 可讓您在 Azure 資料中心和內部部署或共置環境中的基礎結構之間建立私人連線。ExpressRoute 連線不會經過公用網際網路，相較於一般網際網路連線，它提供了更可靠、更快速、更低延遲、更安全的連線。

在某些情況下，使用 ExpressRoute 連線在內部部署裝置和 Azure 之間傳輸資料，可以產生重大的成本效益。有了 ExpressRoute，您可以在 ExpressRoute 位置 (交換服務提供者設備) 與 Azure 建立連線，或從您現有的 WAN 網路 (例如，網路服務提供者提供的 MPLS VPN) 直接與 Azure 連線。

如需 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。


## 後續步驟

如需詳細資訊，請參閱 [ExpressRoute 常見問題集](../expressroute/expressroute-faqs.md)和 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)。

<!---HONumber=AcomDC_1217_2015-->
