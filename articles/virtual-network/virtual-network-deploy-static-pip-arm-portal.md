<properties 
   pageTitle="在資源管理員中使用預覽入口網站部署使用靜態公用 IP 的 VM | Microsoft Azure"
   description="了解如何在資源管理員中使用預覽入口網站部署使用靜態公用 IP 的 VM"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2015"
   ms.author="telmos" />

# 使用 Azure 入口網站部署使用靜態公用 IP 的 VM

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## 使用靜態公用 IP 建立 VM 

若要在 Azure 入口網站中使用靜態公用 IP 位址建立 VM，請遵循下列步驟。

1. 透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com)，並使用您的 Azure 帳戶登入。
2. 在入口網站左上角，按一下 [新增] >> [計算] > [Windows Server 2012 R2 Datacenter]。
3. 在 [選取部署模型] 清單中，選取 [資源管理員]，然後按一下 [建立]。
4. 在 [基本] 刀鋒視窗中，輸入如下所示的 VM 資訊，然後按一下 [確定]。

	![Azure 入口網站 - 基本](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. 在 [大小選擇] 刀鋒視窗中，按一下如下所示的 [A1 標準]，再按一下 [選取]。

	![Azure 入口網站 - 選擇大小](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. 在 [設定] 刀鋒視窗中，按一下 [公用 IP 位址]，接著在 [建立公用 IP 位址] 刀鋒視窗的 [指派] 底下，按一下如下所示的 [靜態]。然後按一下 [確定]。

	![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. 在 [設定] 刀鋒視窗中，按一下 [確定]。
8. 檢視如下所示的 [摘要] 刀鋒視窗，然後按一下 [確定]。

	![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. 請注意您儀表板中新的磚。

	![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. VM 建立後，[設定] 刀鋒視窗隨即出現，如下所示

	![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0114_2016-->