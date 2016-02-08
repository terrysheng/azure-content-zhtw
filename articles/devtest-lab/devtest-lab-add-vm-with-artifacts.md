    <properties
	pageTitle="Add a VM with artifacts to a DevTest Lab | Microsoft Azure"
	description="Create a new virtual machine with Artifacts in DevTest Lab."
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# 將具有構件的 VM 加入 Azure 研發/測試實驗室

## 概觀

您一開始可以使用 Azure 基本映像或使用您已上傳到實驗室的映像，在研發/測試實驗室中建立虛擬機器。

研發/測試實驗室的「構件」可讓您指定在建立 VM 時所執行的動作。構件動作可以執行諸如下列動作的程序：執行 Powershell 和 Bash 命令，以及安裝軟體。構件「參數」可讓您自訂適用於特定案例的構件。

您的實驗室包括來自公用研發/測試實驗室構件儲存機制的構件，以及已建立並加入您自己之構件儲存機制的構件。

本文將示範如何使用構件，在實驗室中建立 VM。

## 加入具有構件的 VM

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選您想要在其中建立新的 VM 的實驗室。

1. 在實驗室的刀鋒視窗上，點選 [+ 實驗室 VM]，如下圖所示。![研發/測試實驗室首頁刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. 在 [實驗室 VM] 刀鋒視窗的 [實驗室 VM 名稱] 文字方塊中，輸入新虛擬機器的名稱。

1. 點選 [基本 / 設定必要設定]，然後選取 VM 的基本映像。

    ![實驗室 VM 設定](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. 選取基本映像之後，[實驗室 VM] 刀鋒視窗會展開以包括 [使用者名稱] 和 [密碼] 項目。輸入**使用者名稱**，此名稱將被授與虛擬機器上的系統管理員權限。

    ![展開的實驗室 VM 刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. 輸入**密碼**。

1. 點選 [VM 大小]，然後選取其中一個預先定義的項目，這些項目可以指定處理器核心、RAM 大小，以及要建立之 VM 的硬碟大小。

1. 點選 [構件]，從構件清單中選取並設定您想要加入基本映像的構件。**附註：**如果您剛接觸到研發/測試實驗室或設定構件，請先跳到[選取和設定構件](#configuring-an-artifact)一節，完成該節之後再返回此處。

1. 點選 [建立]，將指定的 VM 加入實驗室。

1. [實驗室] 刀鋒視窗會顯示 VM 的建立狀態，其狀態會先是 [正在建立]，在啟動該 VM 之後才會變成 [正在執行]。若要連接到 VM，可點選 VM，然後從 VM 的刀鋒視窗點選 [連接]。

## 選取和設定構件

建立 VM 時，您可以從 [實驗室 VM] 刀鋒視窗點選 [構件] 來加入構件。這將會顯示 [加入構件] 刀鋒視窗，讓您能夠加入並設定來自官方研發/測試實驗室儲存機制的 VM 構件 (**官方儲存機制**)，以及來自小組儲存機制的構件。

![加入構件刀鋒視窗](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**將構件加入 VM**

針對您想要加入 VM 的每個構件，依照下列步驟執行：

1. 在 [加入構件] 刀鋒視窗中，點選所需的構件，以檢視可讓您指定構件參數的刀鋒視窗。  

2. 輸入必要的參數值以及任何您所需的選用參數。

3. 點選 [加入] 以加入構件，並返回 [加入構件] 刀鋒視窗。

**變更構件的執行順序**

當您將構件加入 VM 並加以設定時，即會在 [加入構件] 刀鋒視窗頂端出現一個連結，顯示目前的構件數目。根據預設，構件的動作是依照它們加入 VM 的順序來執行。若要變更構件的執行順序，只要在清單中拖放構件，並於完成時點選 [確定] 即可。

**檢視/修改選取的構件**

請遵循下列步驟來檢視或修改所選取構件的參數：

1. 在 [加入構件] 刀鋒視窗頂端，點選會指出已將多少構件加入 VM 的連結。

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 若要檢視或編輯特定構件的參數，請在 [選取的構件] 刀鋒視窗中點選該構件。

1. 進行任何所需的變更，然後點選 [確定] 以關閉 [加入構件] 刀鋒視窗。

1. 點選 [確定] 以關閉 [選取的構件] 刀鋒視窗。

1. 點選 [確定] 以關閉 [加入構件] 刀鋒視窗。

## 後續步驟

了解如何[為您的 VM 撰寫自訂構件](devtest-lab-artifact-author.md)。

<!---HONumber=AcomDC_0128_2016-->