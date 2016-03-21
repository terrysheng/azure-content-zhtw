<properties
   pageTitle="使用資源管理員和 PowerShell 將 VPN 閘道加入至 ExpressRoute 的虛擬網路 |Microsoft Azure"
   description="本文會逐步引導您完成將 VPN 閘道加入至已經建立的 ExpressRoute 的資源管理員 VNet"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# 將 VPN 閘道加入至 ExpressRoute 的資源管理員 VNet 組態 

本文將逐步引導您完成加入閘道子網路，並為已經存在的 VNet 建立 VPN 閘道。此組態的步驟是使用**資源管理員部署模型**建立的 VNet 專用，且將用於 ExpressRoute 組態中。如果您需要使用傳統部署模型建立適用於 VNet 的閘道相關資訊，請參閱[使用傳統入口網站設定 ExpressRoute 的虛擬網路](expressroute-howto-vnet-portal-classic.md)。

## 開始之前

確認您已安裝此組態所需的 Azure PowerShell Cmdlet (1.0.2 或更新版本)。如果您尚未安裝 Cmdlet，您必須先安裝，然後才能開始進行組態步驟。如需安裝 Azure PowerShell 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## 確認已建立閘道

使用下列命令，確認已建立閘道。

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## 後續步驟

建立 VPN 閘道之後，您可以將 VNet 連結至 ExpressRoute 電路。請參閱[將虛擬網路連結到 ExpressRoute 電路](expressroute-howto-linkvnet-arm.md)。

<!---HONumber=AcomDC_0309_2016-->