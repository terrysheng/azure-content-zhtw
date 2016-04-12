<properties
   pageTitle="使用 Azure Resource Manager 和 Azure 入口網站建立具有站對站 VPN 連線的虛擬網路 | Microsoft Azure"
   description="本文會帶領您建立使用資源管理員模型的 VNet 並使用 S2S VPN 閘道連線將其連接到您的本機內部部署網路。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/25/2016"
   ms.author="cherylmc"/>

# 使用 Azure 入口網站建立具有站對站 VPN 連線的資源管理員 VNet

> [AZURE.SELECTOR]
- [Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Azure 入口網站 - 傳統](vpn-gateway-site-to-site-create.md)
- [PowerShell - 資源管理員](vpn-gateway-create-site-to-site-rm-powershell.md)


本文將逐步引導您使用 Azure Resource Manager 部署模型和 Azure 入口網站，建立虛擬網路以及內部部署網路的站對站 VPN 連線。

**部署模型和部署工具**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

如果您想要將 VNet 連接在一起，但不要建立對內部部署位置的連線，請參閱[設定 VNet 對 VNet 連線](vpn-gateway-vnet-vnet-rm-ps.md)。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 開始之前

在開始設定之前，請確認您具備下列項目。

- 相容的 VPN 裝置以及能夠進行設定的人員。請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您將需要與能夠提供那些詳細資料的人協調。

- 您的 VPN 裝置對外開放的公用 IP 位址。此 IP 位址不能位於 NAT 後方。
	
- Azure 訂閱。如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](http://azure.microsoft.com/pricing/free-trial/)。

## 關於此組態


![網站間](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site2site.png)

在下列步驟中，您將建立 VNet、新增閘道子網路、閘道、本機站台及連線。此外，您也需要設定 VPN 裝置。

練習這些步驟時，您可以使用下列的值：



### <a name="values"></a>此練習的範例組態值

- VNet 名稱︰TestVNet1
- 位址空間︰10.11.0.0/16 和 10.12.0.0/16
- 子網路： 
	- FrontEnd：10.11.0.0/24
	- BackEnd：10.12.0.0/24
	- GatewaySubnet：10.12.255.0/27
- 資源群組︰TestRG1
- 位置：美國東部
- DNS 伺服器：8.8.8.8
- 閘道名稱：VNet1GW
- 公用 IP: VNet1GWIP
- VPN 類型︰路由式
- 連線類型︰站對站 (IPsec)
- 閘道類型：VPN
- 區域網路閘道名稱：Site2
- 連線名稱：VNet1toSite2



## 1\.建立虛擬網路 

如果您已建立虛擬網路，請確認設定是否與您的 VPN 閘道設計相容，應特別注意任何可能會與其他網路重疊的子網路。您可以跳到[指定 DNS 伺服器](#dns)。

如果您在練習中建立新的 VNet，您可以建立 VNet 時參考這些[值](#values)。

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## 2\.新增其他位址空間和子網路

您可以將其他位址空間和子網路新增至已建立的 Vnet。

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3.指定 DNS 伺服器

如果您在練習中建立此設定，請在指定 DNS 伺服器時參考這些[值](#values)。

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4\.建立閘道子網路

將虛擬網路連接到閘道之前，您必須先建立虛擬網路要連接的閘道子網路。您建立的閘道子網路必須命名為 *GatewaySubnet* 才能正常運作。

某些設定的閘道子網路首碼需要是 /28 或更大的子網路，才能容納集區中所需的 IP 位址個數。這表示閘道子網路首碼必須是 /28、/27、/26 等。您可以在此建立較大的子網路，以便容納未來可能的設定新增。

如果您在練習中建立此設定，請在建立閘道子網路時參考這些[值](#values)。

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5\.建立虛擬網路閘道

如果您在練習中建立此設定，請在建立閘道時參考這些[值](#values)。

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6\.建立區域網路閘道

區域網路閘道會參考您的內部部署位置。您將會賦予區域網路閘道一個可讓 Azure 參考它的名稱。

如果您在練習中建立此設定，請在新增本機站台時參考這些[值](#values)。

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7\.設定 VPN 裝置

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8\.建立站對站 VPN 連線

接下來，在虛擬網路閘道與 VPN 裝置之間建立站對站 VPN 連線。請務必將值取代為您自己的值。共用的金鑰必須符合您用於 VPN 裝置設定的值。

開始這一節之前，請確認虛擬網路閘道與區域網路閘道已完成建立。如果您在練習中建立此設定，請在建立連線時參考這些[值](#values)。


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9\.驗證 VPN 連線

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## 後續步驟

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。如需詳細資訊，請參閱虛擬機器[學習路徑](https://azure.microsoft.com/documentation/learning-paths/virtual-machines)。

<!---HONumber=AcomDC_0406_2016-->