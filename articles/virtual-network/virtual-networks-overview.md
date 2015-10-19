<properties
   pageTitle="Azure 虛擬網路 (VNet) 概觀"
   description="了解 Azure 中的虛擬網路 (VNet)"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# 虛擬網路概觀

Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。您可以控制 Azure 網路設定，以及定義 DHCP 位址區塊、DNS 設定、安全性原則和路由。您也可以進一步將 VNet 分成子網路，並部署 Azure IaaS 虛擬機器 (VM) 和 PaaS 角色執行個體，方式就與您將實體和虛擬機器部署至內部部署資料中心一樣。基本上，您可以將網路擴展至 Azure，帶來您自己的 IP 位址區塊。

若要進一步了解 VNet，請看下圖顯示的簡化內部部署網路。

![內部部署網路](./media/virtual-networks-overview/figure01.png)

上圖顯示透過路由器連線至公用網際網路的內部部署網路。您也可以看到在路由器與裝載 DNS 伺服器和 Web 伺服器陣列的 DMZ 之間有一道防火牆。Web 伺服器陣列採用對網際網路公開的硬體負載平衡器來進行負載平衡，並且會耗用內部子網路的資源。內部子網路藉由另一道防火牆來與 DMZ 分開，並裝載 Active Directory 網域控制站伺服器、資料庫伺服器和應用程式伺服器。

相同的網路可以裝載在 Azure 中，如下圖所示。

![Azure 虛擬網路](./media/virtual-networks-overview/figure02.png)

請注意 Azure 基礎結構擔任路由器角色的方式，允許從 VNet 存取公用網際網路，而不需要任何組態。防火牆可以換成套用至每個個別子網路的網路安全性群組 (NSG)。而實體負載平衡器可以換成 Azure 中面向網際網路的內部負載平衡器。

## 虛擬網路

VNet 提供下列服務給 IaaS VM 以及 PaaS 角色執行個體部署至 IaaS VM 的角色：

- **隔離**。VNet 與彼此完全隔離。這樣可以讓您為使用相同的 CIDR 位址區塊的開發、測試和生產環境建立個別的 VNet。

- **內含項目**。VNet 無法跨越多個 Azure 區域。

    >[AZURE.NOTE]Azure 中有兩種部署模式：傳統 (也稱為服務管理) 和 Azure 資源管理員 (ARM)。傳統 VNet 可以加入同質群組，或建立成區域 VNet。如果您有 VNet 在同質群組中，建議[將其移轉至區域 VNet](./virtual-networks-migrate-to-regional-vnet.md)。

- **存取公用網際網路**。依預設，VNet 中的所有 IaaS VM 和 PaaS 角色執行個體都可以存取公用網際網路。您可以使用網路安全性群組 (NSG) 來控制存取。

- **存取 VNet 中的 VM**。位於相同 VNet 中的 IaaS VM 和 PaaS 角色執行個體可以彼此連線，即使位於不同的子網路也一樣，而且不需要設定閘道或使用公用 IP 位址，即可將 PaaS 和 IaaS 環境結合在一起。

- **名稱解析**。Azure 會針對部署在 VNet 中的 IaaS VM 和 PaaS 角色執行個體提供內部名稱解析。您也可以部署自己的 DNS 伺服器，並設定 VNet 來使用它們。

- **連線能力**。透過站對站 VPN 連線或 ExpressRoute 連線，VNet 可以彼此連線，甚至連線至您的內部部署資料中心。若要深入了解 VPN 閘道，請造訪[關於 VPN 閘道](./vpn-gateway-about-vpngateways.md)。若要深入了解 ExpressRoute，請參閱 [ExpressRoute 技術概觀](./expressroute-introduction.md)。

    >[AZURE.NOTE]在將任何 IaaS VM 或 PaaS 角色執行個體部署至 Azure 環境之前，請確定您已建立 VNet。ARM 架構的 VM 需要 VNet，而且如果沒有指定現有的 VNet，Azure 建立的預設 VNet 可能會與您的內部部署網路產生 CIDR 位址區塊衝突，而使您無法將 VNet 連線至內部部署網路。

## 子網路

您可以針對組織和安全性，將 VNet 分割成多個子網路。同一 VNet 中的子網路可以彼此通訊，而不需要任何額外的設定。您也可以在子網路層級變更路由設定，並可將 NSG 套用至子網路。

## IP 位址

有兩種類型的 IP 位址會指派給 Azure 中的元件：公用和私用。針對部署至 Azure 子網路的 IaaS VM 和 PaaS 角色執行個體，都會根據指派至子網路的 CIDR 位址區塊，自動將私人 IP 位址指派至其每個 NIC。您也可以指派公用 IP 位址給 IaaS VM 和 PaaS 角色執行個體。

這些 IP 位址是動態的，這表示它們可以隨時變更。若要針對特定服務，確保 IP 位址永遠保持不變，您可以保留 IP 位址，使其成為靜態。

## Azure 負載平衡器

您可以在 Azure 中使用兩種類型的負載平衡器：

- **外部負載平衡器**。您可以使用外部負載平衡器，以提供從公用網際網路存取 IaaS VM 和 PaaS 角色執行個體時的高可用性。

- **內部負載平衡器**。您可以使用內部負載平衡器，以提供從 VNet 中其他服務存取 IaaS VM 和 PaaS 角色執行個體時的高可用性。

若要深入了解 Azure 中的負載平衡，請參閱[負載平衡器概觀](../load-balancer-overview.md)。

## 網路安全性群組 (NSG)

您可以建立 NSG 來控制對網路介面 (NIC)、VM 和子網路的輸入和輸出存取。每個 NSG 都有包含一個或多個規則，指定是否要根據來源 IP 位址、來源連接埠、目的地 IP 位址和目的地連接埠來核准或拒絕流量。若要深入了解 NSG，請參閱[什麼是網路安全性群組](../virtual-networks-nsg.md)。

## 虛擬應用裝置

虛擬應用裝置只是 VNet 中的另一個 VM，負責執行以軟體為基礎的應用裝置功能，例如防火牆、WAN 最佳化或入侵偵測。您可以在 Azure 中建立路由，將 VNet 流量的路由設定為透過虛擬應用裝置來使用其功能。

比方說，NSG 可以用來在 VNet 上提供安全性。不過，NSG 會提供第 4 層存取控制清單 (ACL) 給傳入和傳出的封包。如果您想要使用第 7 層安全性模型，則需要使用防火牆應用裝置。

虛擬應用裝置相依於[使用者定義的路由和 IP 轉送](../virtual-networks-udr-overview.md)。

## 後續步驟

- [建立 VNet](../virtual-networks-create-vnet-arm-pportal.md) 和子網路。
- [在 VNet 中建立 VM](../virtual-machines-windows-tutorial.md)。
- 了解 [NSG](../virtual-networks-nsg.md)。
- 了解[負載平衡器](../load-balancer-overview.md)。
- [保留內部 IP 位址](../virtual-networks-reserved-private-ip.md)
- [保留公用 IP 位址](../virtual-networks-reserved-public-ip.md)。
- 了解[使用者定義的路由和 IP 轉送](virtual-networks-udr-overview.md)。

<!---HONumber=Oct15_HO2-->