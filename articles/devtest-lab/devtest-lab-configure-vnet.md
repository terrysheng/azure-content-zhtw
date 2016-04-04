<properties
	pageTitle="設定適用於研測實驗室的虛擬網路 | Microsoft Azure"
	description="了解如何設定現有的虛擬網路和子網路，並在 VM 中使用它們"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="tarcher"/>

# 設定適用於研測實驗室的虛擬網路

## 概觀

如[將具有構件的 VM 加入 Azure 研測實驗室](devtest-lab-add-vm-with-artifacts.md)文章中所述，當您在實驗室中建立 VM 時，可為該 VM 指定已設定的虛擬網路 (和子網路)。有一個執行此動作的案例是，假設您想要能夠從使用 ExpressRoute 或站台對站台 VPN 設定的虛擬網路存取 VM 的公司網路資源。下列各節將說明如何將現有的虛擬網路加入至實驗室的虛擬網路設定，如此就可以在建立您的 VM 時加以選擇。

## 使用 Azure 入口網站設定適用於研測實驗室的虛擬網路
下列步驟會逐步引導您將現有虛擬網路 (和子網路) 加入至研測實驗室，以便在同一個實驗室中建立 VM 時加以使用。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研測實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 所選實驗室的 [設定] 刀鋒視窗隨即顯示。

1. 點選 [虛擬網路]。

	![虛擬網路可以從實驗室的 [設定] 刀鋒視窗中加以設定](./media/devtest-lab-configure-vnet/lab-settings-vnet.png)
	
1. 在 [虛擬網路] 刀鋒視窗中，您會看到您已針對目前的實驗室設定虛擬網路清單，以及為實驗室所建立的預設虛擬網路。

1. 點選 [+ 加入]。

	![將現有的虛擬網路加入至您的實驗室](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
	
1. 在 [虛擬網路] 刀鋒視窗中，點選 [選取虛擬網路]。

	![選取現有的虛擬網路](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
	
1. 在 [選擇虛擬網路] 刀鋒視窗中，選取所需的虛擬網路。刀鋒視窗會顯示訂用帳戶中與實驗室位於相同區域下方的所有虛擬網路。

1. 一旦選取虛擬網路並返回 [虛擬網路] 刀鋒視窗之後，您將會看到數個欄位已啟用。

	![選取現有的虛擬網路](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)

1. 指定虛擬網路 / 實驗室組合的描述。

1. 若要允許在實驗室 VM 建立期間使用子網路，請核取 [在 VM 建立中使用] 選項。

1. 若要允許子網路中的公用 IP 位址，請核取 [允許公用 IP] 選項。

1. 在 [每位使用者的最大 VM 數] 欄位中，針對每個子網路指定每位使用者的最大 VM 數。如果您想要不限數目的 VM 數，請將此欄位保留空白。

1. 點選 [儲存]。

1. 現在已設定虛擬網路，您可以在建立新的 VM 時加以選取。這將會在[將具有構件的 VM 加入 Azure 研測實驗室](devtest-lab-add-vm-with-artifacts.md)文章中加以說明。

## 後續步驟

一旦您在實驗室中加入所需的虛擬網路之後，下一個步驟就是[將 VM 加入至研測實驗室](devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0323_2016-->