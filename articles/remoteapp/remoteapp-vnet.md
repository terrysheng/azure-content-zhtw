
<properties
    pageTitle="驗證要搭配 Azure RemoteApp 使用的 Azure VNET | Microsoft Azure"
    description="了解如何確定 Azure VNET 已準備好搭配 Azure RemoteApp 使用"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2015"
    ms.author="elizapo" />



# 驗證要搭配 Azure RemoteApp 使用的 Azure VNET

在搭配 Azure RemoteApp 使用 Azure VNET 之前，您可能想要驗證 VNET。這有助於避免發生連線問題。

若要驗證 Azure VNET，請執行下列動作：

1. 在您想要搭配 Azure RemoteApp 使用之 Azure VNET 的子網路內建立 Azure 虛擬機器。

2. 使用管理入口網站中的 [**連接**] 選項連接到該 VM 。
3. 將您想要搭配 Azure RemoteApp 使用的虛擬機器加入至相同網域。如果您要建立連線到內部部署網路的混合式集合，請將此虛擬機器加入您的本機網域。

如果成功，Azure VNET 已準備好搭配 RemoteApp 使用。

如需端對端混合式收藏工作流程的詳細資訊，請參閱下列文章：

- [如何針對 Azure RemoteApp 規劃您的虛擬網路](remoteapp-planvpn.md)
- [建立混合式收藏](remoteapp-create-hybrid-deployment.md)
- [將 Azure RemoteApp 收藏部署到 Azure 虛擬網路 (支援 ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
 

<!---HONumber=Oct15_HO3-->