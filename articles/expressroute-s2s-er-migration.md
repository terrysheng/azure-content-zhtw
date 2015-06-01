<properties
   pageTitle="將您的站對站 VPN 移轉到 ExpressRoute"
   description="本文會協助將您的站對站 VPN 移轉到 ExpressRoute。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> 

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# 連線移轉：從站對站 VPN 到 ExpressRoute

Azure 客戶有兩種方式可以連線到其虛擬網路：站對站 VPN 和 ExpressRoute。站對站 VPN 連線會透過公用網際網路進行。IPsec 可用來加密網路流量。ExpressRoute 是 Azure 的私人連線。您可以透過 [Exchange 提供者 (EXP)](expressroute-exchange-providers.md) 或[網路服務提供者 (NSP)](expressroute-network-service-providers.md) 來與 Azure 連線。

如果您的虛擬網路已經有站對站 VPN 連線，您可以依照下列步驟將它移轉為透過 ExpressRoute 連線。

1) 登入 Azure 入口網站

2) 在導覽窗格中，依序按一下 [**網路**] 和具備 VPN 連線的 [虛擬網路]

3) 按一下 [**儀表板**]。按一下頁面底部的 [**刪除閘道**]，然後按一下 [**是**]。

  此步驟會將站對站 VPN 閘道移除。

4) 在入口網站的虛擬網路顯示 [未建立閘道] 之後，請按一下 [**設定**] 頁面。

a) 按一下 [使用 ExpressRoute] 核取方塊

b) 將閘道子網路的 CIDR 變更為 /28

![訂用帳戶共用](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5) 按一下頁面底部的 [**儲存**]，然後按一下 [**是**]。

6) 按一下 [**儀表板**]。按一下頁面底部的 [**建立閘道**]，然後按一下 [**是**]。

 此步驟會建立 ExpressRoute 閘道
 
建立新的閘道後，虛擬網路可以開始連線到 ExpressRoute 電路。在整個過程中，虛擬網路中的所有虛擬機器會正常運作。無需將他們移動或關閉。

前往透過 [Exchange 提供者](expressroute-configuring-exps.md) / [網路服務提供者](expressroute-configuring-nsps.md)設定 ExpressRoute 教學課程的最後一個步驟，將您的虛擬網路連結到 ExpressRoute 電路。



<!--HONumber=54-->