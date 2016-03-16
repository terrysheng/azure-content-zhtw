<properties 
   pageTitle="關於虛擬網路跨單位連線的 VPN 閘道 | Microsoft Azure"
   description="了解可用於混合式組態之跨單位連線的 VPN 閘道。本文涵蓋閘道 SKU (基本、標準和高效能)、VPN 閘道與 ExpressRoute 共存組態、閘道路由類型 (靜態、動態、原則式、路由式)，以及傳統和資源管理員部署模型的虛擬網路連線的閘道需求。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# 關於 VPN 閘道

VPN 閘道用來傳送虛擬網路和內部部署位置。它們也用來傳送 Azure 內多個虛擬網路之間的流量 (VNet 對 VNet)。建立閘道時，有幾個因素必須納入考量。

- 您想要使用的閘道 SKU
- 您的連接的 VPN 類型
- VPN 裝置 (若您的連線需要)

**關於部署模型**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## 閘道 SKU

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## VPN 閘道類型

有兩種 VPN 類型：

- **原則式**：原則式閘道在傳統部署模型中稱為「靜態閘道」。雖然已變更名稱，但靜態閘道的功能未變更。這種類型的閘道支援原則式 VPN。原則式 VPN 會根據內部部署網路與 Azure VNet 之間的位址首碼組合，使用流量選取器透過 IPsec 通道來導向封包。流量選取器或原則通常定義為 VPN 組態中的存取清單。
 
- **路由式**：路由式閘道在傳統部署模型中稱為「動態閘道」。雖然已變更名稱，但動態閘道的功能未變更。路由式閘道實作路由式 VPN。路由式 VPN 會使用 IP 轉送或路由表中的「路由」，將封包導向至其對應的 VPN 通道介面。然後，通道介面會加密或解密輸入和輸出通道的封包。路由式 VPN 的原則或流量選取器會設定為任意對任意 (或萬用字元)。

某些連接 (例如點對站和 VNet 對 VNet) 只能使用特定的 VPN 類型。您會看到文章中所列對應您想建立的連線情況之閘道需求。

VPN 裝置也有組態限制。當您建立 VPN 閘道時，您將會選取連接所需的 VPN 類型，請務必確定預計要用的 VPN 裝置也支援該路由類型。如需詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。

例如，如果您打算同時使用「站對站」連線與「點對站」連線，則您將需要設定路由式 VPN 閘道。雖然「站對站」連線確實可以使用原則式閘道，但「點對站」連線需要路由式閘道類型因為二種連線會經過同一個閘道，所以您需要選取支援二者的閘道類型。此外，您使用的 VPN 裝置也必須支援路由式組態。


## 閘道需求


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## 後續步驟

選取您組態的 VPN 裝置。請參閱＜[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)＞。





 

<!---HONumber=AcomDC_0302_2016-->