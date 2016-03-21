<properties 
   pageTitle="IPv6 概觀 | Microsoft Azure"
   description="了解 Azure 中的 IPv6 位址"
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
   ms.date="03/04/2016"
   ms.author="telmos" />

# Azure 中的 IPv6 支援

IPv6 通訊協定最初是在 1998 年由網際網路工程任務推動小組 (IETF) 推出，以解決 32 位元的 IPv4 位址在公用網際網路上的限制。隨著連線到網際網路的行動裝置不斷增加，以及物聯網 (IoT) 的興起，使 IPv6 位址更頻繁地用於公用網際網路。

>[AZURE.IMPORTANT] IPv6 在 Azure 中目前是供私人預覽，並非提供給所有客戶。一旦可供所有客戶使用時，此頁面就會更新。

您可以在資源管理員部署中，於您的 Azure IaaS 虛擬機器上使用 IPv6 位址，以用於對公用網際網路的輸入及輸出。以下清單說明 Azure 中 IPv6 的功能。

- **資源管理員中的 IaaS VM**
	- 虛擬機器同時擁有 IPv4 和 IPv6 端點。
	- 對於輸入 (負載平衡器和 NAT) 連線及輸出 (SNAT) 連線，虛擬機器可透過負載平衡器公用 IP 連線到網際網路。
	- 虛擬機器使用其 IPv4 端點連接到所有 Azure 服務。
	- 虛擬機器只將 IPv4 端點用於橫向通訊。
- **Azure DNS**
	- Azure DNS 支援 IPv6，並提供 AAAA 紀錄以及 IPv4 A 紀錄。
	- 當兩者都存在時，Azure DNS 以 AAAA 回應查詢，而 A 紀錄用於查閱。 
- **Office365**
	- Office365 服務支援 IPv6。
	- 您可以使用 ExpressRoute 線路從內部部署網路連接到 Office365 中的 IPv6 資源。

## 資源管理員中的 IaaS VM

只有使用「資源管理員」部署模型才能利用 IPv6 連線。雖然資源管理員部署中的每部虛擬機器都有 IPv4 和 IPv6 端點，但 IPv6 僅用於透過負載平衡器對公用網際網路的連線，如下所示。

![IPv6 網路連線](./media/virtual-network-ipv6-overview/figure1.png)

## Azure DNS

您可以在 Azure DNS 中託管 IPv6 AAAA 記錄。當 Azure DNS 收到資源的查詢時，它會以所有可用的主機名稱來回應。比方說，如果您擁有主機名稱 (例如 www.contoso.com) 的 A 紀錄和 AAAA 記錄，Azure DNS 傳送兩個位址來回應查詢。一但 DNS 用戶端收到 Azure DNS (或任何其他支援 AAAA 記錄的 DNS 伺服器) 的回應，將由其決定要使用 IPv4 或 IPv6 端點來連線。

>[AZURE.NOTE] Windows 電腦一律會先嘗試使用 IPv6 連線，再嘗試使用 IPv4 連線。

## Office 365

藉由使用 ExpressRoute 電路和 Microsoft 對等設定，您可以在內部部署網路及 Office365 之間使用 IPv6 網路連線。請確定您了解和 Microsoft 對等設定連接時 [ExpressRoute 的運作方式](../expressroute/expressroute-introduction.md)。

## 後續步驟

- 了解 [Azure DNS](../dns/dns-overview.md)。
- 了解 [ExpressRoute](../expressroute/expressroute-introduction.md)。

<!---HONumber=AcomDC_0309_2016-->