<properties
	pageTitle="將具有構件的 VM 加入研測實驗室 | Microsoft Azure"
	description="了解如何將具有構件的 VM 加入研測實驗室"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# 將具有構件的 VM 加入研測實驗室

> [AZURE.NOTE] 按一下下列連結可檢視本文隨附的影片：[如何在研測實驗室中建立具有構件 VM](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## 概觀

您會在研測實驗室中，從一個基底映像 ([自訂映像](./devtest-lab-create-template.md)或 Marketplace 映像) 建立 VM。

研測實驗室的「構件」可讓您指定在建立 VM 時所執行的「動作」。

構件動作可以執行諸如下列動作的程序：執行 Windows Powershell 指令碼、執行 Bash 命令，以及安裝軟體。

構件「參數」可讓您自訂適用於特定案例的構件。

本文將示範如何使用構件，在實驗室中建立 VM。

## 加入具有構件的 VM

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選您想要在其中建立新的 VM 的實驗室。

1. 在實驗室的刀鋒視窗上，點選 [+ 實驗室 VM]，如下圖所示。![研測實驗室首頁刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. 在 [實驗室 VM] 刀鋒視窗的 [實驗室 VM 名稱] 文字方塊中，輸入新虛擬機器的名稱。

1. 點選 [基本 / 設定必要設定]，然後選取 VM 的基本映像。

    ![實驗室 VM 設定](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. 選取基底映像並點選 [確定] 之後，[實驗室 VM] 刀鋒視窗將會展開以包括用來指定使用者帳戶資訊的 UI 元素，包括**使用者名稱**、**驗證類型** (如果所選基底的作業系統類型是 Linux)，以及**密碼** (假設驗證類型為「密碼」)。

    ![展開的實驗室 VM 刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. 輸入**使用者名稱**，此名稱將被授與虛擬機器上的系統管理員權限。

1. 如果所選基底的作業系統類型為 Linux，請將驗證類型指定為「密碼」或「SSH 公開金鑰」。

1. 依據指定的驗證類型，輸入密碼或 SSH 公開金鑰。

1. 點選 [VM 大小]，然後選取其中一個預先定義的項目，這些項目可以指定處理器核心、RAM 大小，以及要建立之 VM 的硬碟大小。

1. 點選 [虛擬網路]，然後選取所需的 VNET。

1. 點選 [子網路]，然後選取子網路。

1. 如果將實驗室原則設定為允許所選子網路的公用 IP 位址，請選取 [是] 或 [否]，來指定您是否想要讓 IP 位址成為公用的。否則，這個選項會停用並選取為 [否]。

1. 點選 [構件]，從構件清單中選取並設定您想要加入基本映像的構件。**附註：**如果您剛接觸到研測實驗室或設定構件，請先跳到[將現有的構件加入至 VM](#add-an-existing-artifact-to-a-vm) 一節，完成該節之後再返回此處。

1. 點選 [建立]，將指定的 VM 加入實驗室。

1. [實驗室] 刀鋒視窗會顯示 VM 的建立狀態，其狀態會先是 [正在建立]，在啟動該 VM 之後才會變成 [正在執行]。

1. 請移至[後續步驟](#next-steps)一節。

## 將現有的構件加入至 VM

建立 VM 時，您可以加入現有的構件。每個實驗室都會包括來自公用研測實驗室構件儲存機制的構件，以及您已建立並加入至您自己之構件儲存機制的構件。若要了解如何建立構件，請參閱[了解如何撰寫您自己的構件以用於研測實驗室](devtest-lab-artifact-author.md)文章。

1. 在 [實驗室 VM] 刀鋒視窗中，點選 [構件]。 

1. 在 [加入構件] 刀鋒視窗中，點選所需的構件。

![加入構件刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. 輸入必要的參數值以及任何您所需的選用參數。  

1. 點選 [加入] 以加入構件，並返回 [加入構件] 刀鋒視窗。

1. 視需要繼續為您的 VM 加入構件。

1. 加入構件之後，您可以[變更構件的執行順序](#change-the-order-in-which-artifacts-are-run)。您也可以返回來[檢視或修改構件](#view-or-modify-an-artifact)。

## 變更構件的執行順序

根據預設，構件的動作是依照它們加入 VM 的順序來執行。下列步驟說明如何變更構件的執行順序。

1. 在 [加入構件] 刀鋒視窗頂端，點選會指出已加入 VM 的構件數目的連結。

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 拖放清單中的構件以反映所需的順序。**附註︰**如果您在拖曳構件時發生問題，請確定您是從構件左側進行拖曳。

1. 完成時點選 [確定]。

## 檢視或修改構件

下列步驟說明如何檢視或修改構件的參數︰

1. 在 [加入構件] 刀鋒視窗頂端，點選會指出已加入 VM 的構件數目的連結。

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 在 [選取的構件] 刀鋒視窗中，點選您想要檢視或編輯的構件。

1. 在 [加入構件] 刀鋒視窗中，進行任何所需的變更，然後點選 [確定] 以關閉 [加入構件] 刀鋒視窗 。

1. 點選 [確定] 以關閉 [選取的構件] 刀鋒視窗。

## 後續步驟

- 一旦建立 VM 之後，您可以依序點選 VM 刀鋒視窗上的 [連接] 以連接至 VM。
- 若要了解如何建立構件，請參閱[了解如何撰寫您自己的構件以用於研測實驗室](devtest-lab-artifact-author.md)文章。

<!---HONumber=AcomDC_0316_2016-->