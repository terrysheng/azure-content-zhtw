<properties
   pageTitle="將 ExpressRoute 電路從傳統移至資源管理員 | Microsoft Azure"
   description="此頁面提供銜接傳統與資源管理員部署模型的相關概觀。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# 將 ExpressRoute 電路從傳統移至資源管理員部署模型

本文提供將 ExpressRoute 電路從傳統移至資源管理員部署模型的概觀。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

單一 ExpressRoute 電路可以用來連接到在傳統和資源管理員部署模型中部署的虛擬網路 (Vnet)。無論 ExpressRoute 電路的建立方式為何，現在都可以連結至兩種部署模型中的虛擬網路。

![](./media/expressroute-move/expressroute-move-1.png)

## 在傳統部署模型中建立的 ExpressRoute 電路

在傳統部署模型中建立的 ExpressRoute 電路必須先移至資源管理員部署模型，才能連接至傳統和資源管理員部署模型。移動連線時，不會發生連線中斷的情況。將會保留傳統部署模型中的所有電路 Vnet 連結 (在相同訂用帳戶內和跨越訂用帳戶)。順利完成移動後，ExpressRoute 電路的外觀、執行及操作方式會與在資源管理員部署模型中建立的 ExpressRoute 電路完全相似。您現在可以在資源管理員部署模型中建立虛擬網路的連線。

ExpressRoute 電路移至資源管理員部署模型後，您只能利用資源管理員部署模型來管理 ExpressRoute 電路的生命週期。這表示有些作業 (例如新增 / 更新 / 刪除對等互連、更新電路屬性 (如頻寬、SKU 和計費類型) 及刪除電路) 只能在資源管理員部署模型中執行。如需如何有關管理這兩種部署模型存取的進一步詳細資訊，請參閱下一節有關在資源管理員部署模型中建立的電路。

您不必牽連您的連線提供者，即可執行移動。

## 在資源管理員部署模型中建立的 ExpressRoute 電路

您可讓在資源管理員部署模型中建立的 ExpressRoute 電路能夠從這兩種部署模型進行存取。您訂用帳戶中的 ExpressRoute 電路均可從這兩種部署模型進行存取。

- 根據預設，在資源管理員部署模型中建立的 ExpressRoute 電路無法存取傳統部署模型。
- 根據預設，無法從這兩種部署模型存取從傳統部署模型移至資源管理員部署模型的 ExpressRoute 電路。
- 無論是建立於資源管理員部署模型或傳統部署模型中，ExpressRoute 電路永遠都可以存取資源管理員部署模型。這表示您可以依照[如何連結虛擬網路](expressroute-howto-linkvnet-arm.md)中的指示執行，對在資源管理員部署模型中建立的虛擬網路建立連線。 
- 傳統部署模型的存取權是由 ExpressRoute 電路中的 "allowClassicOperations" 參數所控制。 

>[AZURE.IMPORTANT] 將會套用記載於[服務限制](../azure-subscription-service-limits.md)頁面中的所有配額。例如，標準電路最多可以有 10 個跨傳統與資源管理員部署模型的 VNet 連結 / 連線。


### 控制傳統部署模型的存取權

設定 ExpressRoute 電路的 "allowClassicOperations" 參數，即可讓單一 ExpressRoute 電路連結至這兩種部署模型中的 Vnet。

將 "allowClassicOperations" 設定為 TRUE，可讓您從這兩種部署模型中的 Vnet 連結到 ExpressRoute 電路。遵循[如何連結傳統部署模型中的虛擬網路](expressroute-howto-linkvnet-classic.md)上的指導方針，即可連結至傳統部署模型中的 Vnet。遵循[如何連結資源管理員部署模型中的虛擬網路](expressroute-howto-linkvnet-arm.md)上的指導方針，即可連結至資源管理員部署模型中的 Vnet。

將 "allowClassicOperations" 設定為 FALSE，將會封鎖從傳統部署模型存取電路。不過，將會保留傳統部署模型中的所有 Vnet 連結。在此情況下，ExpressRoute 電路不會顯示在傳統部署模型中。

### 傳統部署模型中支援的作業

當 "allowClassicOperations" 設定為 TRUE 時，ExpressRoute 電路支援下列傳統作業。

 - 取得 ExpressRoute 線路資訊
 - 建立 / 更新 / 取得 / 刪除傳統 Vnet 的 VNet 連結
 - 建立 / 更新 / 取得 / 刪除跨訂用帳戶連線的 VNet 連結授權

當 "allowClassicOperations" 設定為 TRUE 時，您將無法執行下列傳統作業。

 - 建立 / 更新 / 取得 / 刪除 Azure 私人、Azure 公用和 Microsoft 對等互連的 BGP 對等互連
 - 刪除 ExpressRoute 電路

## 傳統與資源管理員的通訊

ExpressRoute 電路的作用就像是傳統與資源管理員部署模型之間的橋樑。傳統部署模型的 Vnet 中部署的虛擬機器與資源管理員部署模型的 Vnet 中部署的虛擬機器之間的流量將會流經 ExpressRoute，前提是這兩個 Vnet 連結至相同的 ExpressRoute 電路。彙總輸送量受限於虛擬網路閘道的輸送量容量。在這類情況下，流量不會進入連線提供者或您的網路。Vnet 之間的流量將會完全包含在 Microsoft 網路內。

## Azure 公用和 Microsoft 對等互連資源的存取權

您可以繼續存取通常可透過 Azure 公用對等互連和 Microsoft 對等互連存取的資源，而不需要中斷。

## 支援的項目

本節說明經由這項功能支援的項目

 - 單一 ExpressRoute 電路可以用來存取在傳統和資源管理員部署模型中部署的 Vnet。
 - 您可以將 ExpressRoute 電路從傳統移至資源管理員部署模型。一旦移動，ExpressRoute 電路的外觀、操作及執行方式會與在資源管理員部署模型中建立的任何其他 ExpressRoute 電路相似。
 - 只有 ExpressRoute 線路可以移動。電路連結、Vnet 和 VPN 閘道不會透過這項作業移動。
 - ExpressRoute 電路移至資源管理員部署模型後，您只能利用資源管理員部署模型來管理 ExpressRoute 電路的生命週期。這表示有些作業 (例如新增 / 更新 / 刪除對等互連、更新電路屬性 (如頻寬、SKU 和計費類型) 及刪除電路) 只能在資源管理員部署模型中執行。
 - ExpressRoute 電路的作用就像是傳統與資源管理員部署模型之間的橋樑。傳統部署模型的 Vnet 中部署的虛擬機器與資源管理員部署模型的 Vnet 中部署的虛擬機器之間的流量將會流經 ExpressRoute，前提是這兩個 Vnet 連結至相同的 ExpressRoute 電路。 
 - 傳統和資源管理員部署模型中的跨訂用帳戶連線。

## 不支援的項目

本節說明不會經由這項功能支援的項目

 - 將電路連結、閘道和虛擬網路從傳統移至資源管理員部署模型。
 - 從傳統部署模型管理 ExpressRoute 電路的生命週期。
 - 傳統部署模型的 RBAC 支援。您無法對傳統部署模型中的電路執行 RBAC 控制。訂用帳戶的任何管理員 / 共同管理員都能夠將 Vnet 連結 / 取消連結至電路。

## 組態

請依照[將 ExpressRoute 電路從傳統移至資源管理員](expressroute-howto-move-arm.md)中所述的指示執行。

## 後續步驟

- 如需工作流程資訊，請參閱 [ExpressRoute 線路佈建工作流程和線路狀態](expressroute-workflows.md)。
- 設定 ExpressRoute 連線。

	- [建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)
	- [設定路由](expressroute-howto-routing-arm.md)
	- [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->