<properties
   pageTitle="ExpressRoute 線路的 NAT 需求 | Microsoft Azure"
   description="此頁面提供為 ExpressRoute 線路設定和管理 NAT 的詳細需求。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="cherylmc"/>

# ExpressRoute NAT 需求

若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須設定和管理 NAT。有些連線提供者會以受管理的服務來支援設定和管理路由。請洽詢您的連線服務提供者，以查看他們是否提供這類服務。如果沒有，您必須遵守以下所述的需求。

如需不同路由網域的概觀，請檢閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)頁面。為了符合 Azure 公用和 Microsoft 對等的公用 IP 位址需求，建議在您的網路與 Microsoft 之間設定 NAT。本節提供您需要設定的 NAT 基礎結構的詳細描述。

## Azure 公用對等的 NAT 需求

Azure 公用對等路徑可讓您連接到裝載於 Azure 中的所有服務的公用 IP 位址。其中包括 [ExpessRoute 常見問題集](expressroute-faqs.md)列出的所有服務，以及由 ISV 裝載於 Microsoft Azure 上的任何服務。連線到公用對等的 Microsoft Azure 服務時，一律是從您的網路出發到 Microsoft 網路。以公用對等的 Microsoft Azure 為目的地的流量，必須由 SNAT 轉譯成有效的公用 IPv4 位址，才能進入 Microsoft 網路。下圖提供如何設定 NAT 以符合上述需求的高階圖片。

![](./media/expressroute-nat/expressroute-nat-azure-public.png)

### NAT IP 集區和路由通告

您必須確定流量進入的 Azure 公用對等路徑具備有效的公用 IPv4 位址。Microsoft 必須能夠向區域路由網際網路登錄 (RIR) 和網際網路路由登錄 (IRR) 驗證 IPv4 NAT 位址集區的擁有權。將會根據所比對的 AS 編號和用於 NAT 的 IP 位址執行檢查。如需路由登錄的詳細資訊，請參閱 [ExpressRoute 路由需求](expressroute-routing.md)頁面。
 
透過此對等公告的 NAT IP 首碼長度沒有限制。您必須監視 NAT 集區，確保您未耗盡 NAT 工作階段。

>[AZURE.IMPORTANT]向 Microsoft 公告的 NAT IP 集區不得向網際網路公告。這樣會中斷其他 Microsoft 服務的連線。

## Microsoft 對等的 NAT 需求

Microsoft 對等路徑可讓您連接到不支援透過 Azure 公用對等路徑存取的 Microsoft 雲端服務。服務清單包括 Office 365 服務，例如 Exchange Online、SharePoint Online、商務用 Skype 和 CRM Online。Microsoft 預計在 Microsoft 對等上支援雙向連線能力。以 Microsoft 雲端服務為目的地的流量，必須由 SNAT 轉譯成有效的公用 IPv4 位址，才能進入 Microsoft 網路。從 Microsoft 雲端服務到您的網路的流量，必須經過 SNAT 轉譯，才會進入您的網路。下圖提供如何為 Microsoft 對等設定 NAT 的高階圖片。
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png)


#### 從您的網路出發到 Microsoft 的流量

- 您必須確定流量進入的 Microsoft 對等路徑具備有效的公用 IPv4 位址。Microsoft 必須能夠向區域路由網際網路登錄 (RIR) 和網際網路路由登錄 (IRR) 驗證 IPv4 NAT 位址集區的擁有者。將會根據所比對的 AS 編號和用於 NAT 的 IP 位址執行檢查。如需路由登錄的詳細資訊，請參閱 [ExpressRoute 路由需求](expressroute-routing.md)頁面。

- 用於 Azure 公用對等設定和其他 ExpressRoute 線路的 IP 位址，不得透過 BGP 工作階段向 Microsoft 公告。透過此對等公告的 NAT IP 首碼長度沒有限制。

	>[AZURE.IMPORTANT]向 Microsoft 公告的 NAT IP 集區不得向網際網路公告。這樣會中斷其他 Microsoft 服務的連線。

#### 從 Microsoft 出發到您的網路的流量

- 在某些情況下，需要由 Microsoft 對您網路中裝載的服務端點起始連線。常見的例子就是從 Office 365 連接到您網路中裝載的 ADFS 伺服器。在這種情況下，必須將您網路中適當的首碼透露給 Microsoft 對等。 

- 您必須以 SNAT 轉譯從 Microsoft 到您網路內的 IP 位址的流量。

## 後續步驟

- 請參閱[路由](expressroute-routing.md)和 [QoS](expressroute-qos.md) 的需求。
- 如需工作流程資訊，請參閱 [ExpressRoute 線路佈建工作流程和線路狀態](expressroute-workflows.md)。
- 設定 ExpressRoute 連線。

	- [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)
	- [設定路由](expressroute-howto-routing-classic.md)
	- [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO2-->