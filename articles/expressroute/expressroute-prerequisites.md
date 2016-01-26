<properties
   pageTitle="採用 ExpressRoute 的必要條件 | Microsoft Azure"
   description="本頁面提供在您可以訂購 Azure ExpressRoute 電路之前必須符合的需求清單。"
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
   ms.date="01/16/2016"
   ms.author="cherylmc"/>


# ExpressRoute 必要條件   

若要使用 ExpressRoute 連線到 Microsoft 雲端服務，您必須確認是否符合以下各節中所列的下列需求。

## 帳戶必要條件

- 使用中的有效 Microsoft Azure 帳戶。需要有此帳戶才能設定 ExpressRoute 循環。ExpressRoute 循環是 Azure 訂用帳戶內的資源。即使連線只限於非 Azure Microsoft 雲端服務 (例如 Office 365 服務和線上 CRM)，Azure 訂用帳戶還是一項需求。
- 使用中的 Office 365 訂用帳戶 (如果使用的是 Office 365 服務)。如需詳細資訊，請參閱本文的 [Office 365 的特定需求](#office-365-specific-requirements)一節。

## 連線提供者的關係

- 必須透過連線促進與支援清單中連線提供者的關係。您目前必須與連線提供者具備業務關係。您必須確定您透過連線提供者所擁有的服務會與 ExpressRoute 相容。
- 如果您想要使用不在支援清單中的連線提供者，您仍然可以透過交換來建立與 Microsoft 雲端服務的連線。
	- 請洽詢您的連線提供者，以確認他們是否出現在支援清單中所列的任何交換位置上。
	- 讓您的連線提供者將您的網路延伸到選擇的交換位置。
	- 利用與連線提供者的交換來排列 ExpressRoute 循環的順序。

## 位於您的網路與連線服務提供者之間的實體連線

如需連線模型的詳細資訊，請參閱＜連線模型＞一節。客戶必須確保其內部部署基礎結構會透過其中一個所述的模型，實際連線到服務提供者基礎結構。

## 連線的備援性需求

對於在客戶基礎結構和服務提供者基礎結構之間的實體連線，並沒有任何備援需求。Microsoft 不會在第 3 層上要求備援。Microsoft 不會要求在 Microsoft Edge 與客戶網路之間，透過服務提供者，針對要啟用的每個對等項目設定備援路由。如果未以備援方式設定路由工作階段，服務的可用性 SLA 將會失效。

## IP 位址和路由考量

客戶/連線提供者會負責使用 Microsoft Edge 基礎結構來設定備援 BGP 工作階段。選擇透過 IP VPN 提供者連線的客戶，通常會依賴連線提供者來管理路由組態。利用交換或透過點對點乙太網路提供者連線到 Microsoft 的客戶，必須針對每個對等備援 BGP 工作階段，以符合可用性 SLA 需求。連線提供者可能會提供此項目來做為增值服務。如需限制的詳細資訊，請參閱 [ExpressRoute 循環和路由網域](expressroute-circuit-peerings.md)一文中的路由網域表。

## 安全性和防火牆

如需安全性和防火牆資訊，請參閱 [Microsoft 雲端服務和網路安全性](../best-practices-network-security.md)一文。

## 適用於 Azure 公開與 Microsoft 對等項目的 NAT 組態

如需關於需求和組態的詳細指引，請參閱 [ExpressRoute NAT 需求](expressroute-nat.md)。請洽詢您的連線提供者，以查看它們是否會為您管理 NAT 設定和管理。通常第 3 層連線提供者將會為您管理 NAT。

## Office 365 的特定需求

請檢閱下列資源，以取得 Office 365 需求的詳細資訊。

- [適用於 Office 365 的網路規劃和效能調整](http://aka.ms/tune)
- [Office 365 網路流量管理](https://support.office.com/article/Office-365-network-traffic-management-e1da26c6-2d39-4379-af6f-4da213218408)
- 如需關於 QoS 需求和組態的詳細指引，請參閱 [ExpressRoute 品質 (QoS) 服務需求](expressroute-qos.md)一文。請洽詢您的連線服務提供者，以查看他們是否為您的 VPN 提供了多個類別的服務。 

## 後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
- 尋找服務提供者。請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
- 請參閱適用於[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的需求。
- 設定 ExpressRoute 連線。
	- [建立 ExpressRoute 線路](expressroute-howto-circuit-classic.md)
	- [設定路由](expressroute-howto-routing-classic.md)
	- [將 VNet 連結到 ExpressRoute 循環](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0121_2016-->