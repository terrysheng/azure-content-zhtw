<properties
	pageTitle="在研測實驗室中設定 Azure Marketplace 映像設定 | Microsoft Azure"
	description="設定在研測實驗室中建立 VM 時可以使用哪些 Azure Marketplace 映像"
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

# 在研測實驗室中設定 Azure Marketplace 映像設定

## 概觀

依據您設定 Azure Marketplace 映像以便在實驗室中使用的方式而定，研測實驗室支援根據 Azure Marketplace 映像來建立新的 VM。本文將說明在研測實驗室中建立新的 VM 時，如何指定要使用哪些 Azure Marketplace 映像 (如果有的話)。

## 選取在建立 VM 時允許使用哪些 Azure Marketplace 映像

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 所選實驗室的 [設定] 刀鋒視窗隨即顯示。

1. 在 [設定] 刀鋒視窗中，點選 [Marketplace 映像]

	![設定如何使用 Azure Marketplace 映像](./media/devtest-lab-configure-marketplace-images/lab-settings-marketplace-images.png)

1. 指定是否要讓所有合格的 Azure Marketplace 映像可用來做為新 VM 的基底。如果您選取 [是]，則實驗室中允許所有符合下列準則的 Azure Marketplace 映像︰

	- 映像會建立單一 VM，**而且**
	- 映像會使用 Azure Resource Manager 來佈建 VM，**而且**
	- 映像不需要購買額外的授權方案
	
	如果您不想允許任何映像，或者想要指定可以使用哪些映像，請選取 [否]。
 
	![允許使用所有 Marketplace 映像做為 VM 基底映像的選項](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
 
1. 如果您在上一個步驟中選取 [否]，將會啟用 [允許的映像/全選] 核取方塊。您可以將此選項與搜尋方塊一起使用，快速選取或取消選取清單中顯示的所有項目。您也可以藉由核取每個映像的對應核取方塊，個別選取您想要允許來建立 VM 的 Azure Marketplace 映像。如果您不想在實驗室中允許使用任何 Azure Marketplace 映像，請不要從清單中選取任何項目。

	![您可以指定可使用哪些 Marketplace 映像做為 VM 的基底映像](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

## 後續步驟

一旦您設定在建立 VM 時允許使用 Azure Marketplace 映像的方式之後，下一個步驟就是[將 VM 加入您的研測實驗室](./devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0316_2016-->