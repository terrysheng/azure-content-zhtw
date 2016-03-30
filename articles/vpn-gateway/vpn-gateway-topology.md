<properties 
   pageTitle="VPN 閘道連線拓撲 | Microsoft Azure"
   description="檢視 VPN 閘道連線拓撲、可用的組態工具和部署模型。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Azure VPN 閘道連線拓撲

本文說明基準 VPN 閘道連線拓撲。您可以使用圖形和描述來協助選取符合您需求的組態拓撲。雖然本文旨在逐步說明主要基準拓撲，但您也可以使用這些圖表做為指導方針來建置更複雜的拓撲。

每個拓撲都有一份資料表，裡面會列出拓撲適用的部署模型、可用來設定每個拓撲的部署工具，並且會直接連結至相關文章 (如果有)。我們會頻繁地在有新文章和部署工具可供您使用時更新這些資料表。

如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。



## 站對站和多站台

站對站連線是透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道建立的連線。此類型的連線需要 VPN 裝置或 Windows Server RRAS 內部部署。站對站連線可以用於跨部署與混合式組態。


**S2S 圖表**

![S2S 連線](./media/vpn-gateway-topology/site2site.png "站對站")

如果您使用 Azure 路由式 VPN，您可以對內部部署網路建立和設定多個 S2S VPN 連線。這種組態通常稱為「多站台」連線。
 

**多站台圖表**

![多站台連線](./media/vpn-gateway-topology/multisite.png "多站台")


**可用的部署模型和方法**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## VNet 對 VNet

將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，非常類似於將 VNet 連接至內部部署網站位置。這兩種連線類型都使用 Azure VPN 閘道提供使用 IPsec/IKE 的安全通道。您所連線的 Vnet 可位於不同區域或不同訂用帳戶。您甚至可以將多網站組態與 VNet 對 VNet 通訊結合。這可讓您建立結合了跨單位連線與內部虛擬網路連線的網路拓撲。

Azure 目前有兩種部署模式：Azure 服務管理 (稱為傳統) 和 Azure Resource Manager。如果您已使用 Azure 一段時間，您可能已擁有在傳統 VNet 上執行的 Azure VM 和執行個體角色，但您較新的 VM 和角色執行個體卻可能是在以 ARM 建立的 VNet 上執行。您可以在即使虛擬網路位於不同部署模型時在它們之間建立連線。


**VNet2VNet 圖表**

![VNet 對 VNet 連線](./media/vpn-gateway-topology/vnet2vnet.png "VNet 對 VNet")


**可用的部署模型和方法**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]



## 站對站及 ExpressRoute 並存連線

ExpressRoute 是從 WAN 直接連往 Microsoft 服務 (包括 Azure) 的專用連線，而非透過公用網際網路的連線。站對站 VPN 流量會以加密方式透過公用網際網路進行傳輸。能夠對相同的虛擬網路設定站對站 VPN 和 ExpressRoute 連線有諸多好處。您可以將站對站 VPN 設定為 ExpressRoute 的安全容錯移轉路徑，或使用站對站 VPN 來連線至不在您網路中但透過 ExpressRoute 連接的網站。


**並存連線圖表**

![並存連線](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**可用的部署模型和方法**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## 點對站

點對站設定可讓您從用戶端電腦個別地建立與虛擬網路的安全連線。VPN 連線的建立方式是從用戶端電腦啟動連線。當您想要從遠端位置 (例如從住家或會議) 連接到您的 VNet 時，或您只有幾個需要連線至虛擬網路的用戶端時，這是很實用的解決方案。

點對站連線是透過 SSTP (安全通訊端通道通訊協定) 的 VPN 連線。點對站連線不需要 VPN 裝置或公眾對應 IP 位址即可運作。

**P2S 圖表**

![點對站連線](./media/vpn-gateway-topology/point2site.png "點對站")

**可用的部署模型和方法**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## 後續步驟

您會想要先熟悉[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)和 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)文章中的項目以更佳了解 VPN 閘道，再繼續進行連線的規劃和設計。





 

<!---HONumber=AcomDC_0323_2016-->