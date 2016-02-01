<properties 
   pageTitle="ExpressRoute 簡介 | Microsoft Azure"
   description="此頁面提供 ExpressRoute 服務的概觀，包括 ExpressRoute 連線的運作方式。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# ExpressRoute 技術概觀

Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的專用私人連線，將內部部署網路擴充至 Microsoft 雲端。透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 CRM Online。從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或在共置設施上透過連線提供者的虛擬交叉連接，都可以進行連線。ExpressRoute 連線不會經過公用網際網路。相較於一般網際網路連線，這可讓 ExpressRoute 連線提供更可靠、更快速、延遲更短和更安全的連線。

![](./media/expressroute-introduction/expressroute-basic.png)

**主要優點包括：**

- 內部部署網路與 Microsoft Cloud 之間透過連線提供者的第 3 層連線能力。從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或透過乙太網路交換經由虛擬交叉連接，都可以進行連線。
- 跨地理政治區域中的所有區域連接到 Microsoft 雲端服務。
- 透過 ExpressRoute Premium 附加元件從全球連線到所有區域的 Microsoft 服務。
- 網路與 Microsoft 之間透過業界標準通訊協定 (BGP) 的動態路由。
- 每個對等位置有內建的備援性，可靠性更高。
- 連線執行時間 [SLA](http://azure.microsoft.com/support/legal/sla/)。
- QoS 且支援特殊應用程式的多種服務類別，例如商務用 Skype。

如需詳細資訊，請參閱〈[ExpressRoute 常見問題集](expressroute-faqs.md)〉。

## 如何使用 ExpressRoute 將我的網路連線到 Microsoft？

有三種方法可以在內部部署網路與 Microsoft 雲端之間建立連線。

1. **共置於雲端交換。** 如果您共置於具有雲端交換的設施中，您可以訂購虛擬交叉連接，透過共置提供者的乙太網路交換而連接至 Microsoft 雲端。共置提供者可以在您於共置設施中的基礎結構與 Microsoft 雲端之間，提供第 2 層交叉連接或受管理的第 3 層交叉連接。
2.	**點對點乙太網路連線。** 您可以透過點對點乙太網路連結，將內部部署資料中心/辦公室連接到 Microsoft 雲端。點對點乙太網路提供者可以在您的網路與 Microsoft 雲端之間，提供第 2 層連線或受管理的第 3 層連線。
3.	**任意點對任意點 (IPVPN) 網路。** 您可以整合 WAN 與 Microsoft 雲端。IPVPN 提供者 (通常是 MPLS VPN) 在您的分公司與資料中心之間提供任意點對任意點連線。Microsoft 雲端可以相互連接到您的 WAN，看起來就像任何其他分公司一樣。WAN 提供者通常會提供受管理的第 3 層連線能力。

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)

在上述所有連線模型中，ExpressRoute 功能與特性完全相同。連線提供者可以提供上述清單中的一或多個連線模型。您可以洽詢連線提供者來選擇最適合您的模型。

## ExpressRoute 特性

ExpressRoute 支援下列特性與功能。

### 第 3 層連線能力

Microsoft 採用業界標準動態路由通訊協定 (BGP)，在您的內部部署網路、Azure 中的執行個體和 Microsoft 公用位址之間交換路由。我們會針對不同的流量設定檔，與您的網路建立多個 BGP 工作階段。如需詳細資訊，請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)一文。

### 備援性

每個 ExpressRoute 線路有兩條連線，從連線提供者 / 您的網路邊緣連接到兩個 Microsoft Enterprise 邊緣路由器 (MSEE) 。Microsoft 需要有來自連線提供者 / 您這端的雙重 BGP 連線 – 各連接到每個 MSEE。您可以選擇不要在您這端部署備援裝置 / 乙太網路路線。不過，連線提供者會使用備援裝置，確保以備援方式將您的連線交給 Microsoft。備援第 3 層連線組態是我們的 [SLA](http://azure.microsoft.com/support/legal/sla/) 生效的條件。

### 連線到 Microsoft 雲端服務

透過 ExpressRoute 連線可存取下列服務。

- Microsoft Azure 服務
- Microsoft Office 365 服務
- Microsoft CRM Online 服務 
 
您可以瀏覽 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面，取得透過 ExpressRoute 所支援的服務的詳細清單。

### 連線到地理政治區域內的所有區域

您可以在我們的其中一個[對等位置](expressroute-locations.md)中連接到 Microsoft，就能夠存取地理政治區域內的所有區域。

例如，如果您在阿姆斯特丹透過 ExpressRoute 連接到 Microsoft，您就能夠存取在北歐和西歐裝載的所有 Microsoft 雲端服務。如需地理政治地區、相關聯的 Microsoft 雲端區域和對應的 ExpressRoute 對等位置的概觀，請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)一文。

### 使用 ExpressRoute Premium 附加元件從全球連線

您可以啟用 ExpressRoute Premium 附加功能，將連線能力延伸到跨越地理政治的界限。例如，如果您在阿姆斯特丹透過 ExpressRoute 連接到 Microsoft，您就能夠存取全球所有區域裝載的所有 Microsoft 雲端服務 (不包括國家雲端)。就像存取北歐和西歐區域一樣，您也可以存取部署在南美洲或澳大利亞的服務。

### 豐富的連線合作夥伴生態系統

ExpressRoute 的連線提供者和 SI 合作夥伴生態系統持續成長茁壯。如需最新資訊，請參閱 [ExpressRoute 提供者和位置](expressroute-locations.md)一文。

### 連線到國家雲端

Microsoft 為特殊的地理政治地區和客戶群提供隔離的雲端環境。如需國家雲端和提供者的清單，請參閱 [ExpressRoute 提供者和位置](expressroute-locations.md)頁面。

### 支援的頻寬選項

您可以購買各種頻寬的 ExpressRoute 線路。支援的頻寬清單如下。請務必洽詢您的連線提供者，以判斷他們支援的頻寬清單。

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 Gbps
- 2 Gbps
- 5 Gbps
- 10 Gbps

### 動態調整頻寬

您可以在不中斷連線的情況下增加 ExpressRoute 線路頻寬 (盡最大努力)。

### 彈性的計費模型

您可以挑選最適合的計費模型。請從下列計費模型中選擇。如需詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面。

- **無限制資料**。ExpressRoute 線路按月計費，所有輸入和輸出資料傳輸已免費包括在內。 
- **已計量資料**。ExpressRoute 線路按月計費。所有輸入資料傳輸都免費。輸出資料傳輸依每 GB 資料傳輸計費。資料傳輸費率依區域而不同。
- **ExpressRoute Premium 附加元件**。ExpressRoute Premium 是 ExpressRoute 線路上的附加元件。ExpressRoute Premium 附加元件提供下列功能： 
	- Azure 公用和 Azure 私用對等的路由限制從 4,000 個路由提高到 10,000 個路由。
	- 從全球連線到服務。任何區域中建立的 ExpressRoute 線路 (不包括國家雲端) 能夠存取全球其他任何區域的資源。例如，透過在美國矽谷佈建的 ExpressRoute 線路，可存取在西歐建立的虛擬網路。
	- 每個 ExpressRoute 線路的 VNet 連結數目已從原本 10 個放寬限制，視線路的頻寬而定。

## 後續步驟

- 了解 ExpressRoute 連線和路由網域。請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)。
- 尋找服務提供者。請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
- 請確定符合所有必要條件。請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
- 請參閱[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的需求。
- 設定 ExpressRoute 連線。
	- [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)
	- [設定路由](expressroute-howto-routing-classic.md)
	- [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0121_2016-->