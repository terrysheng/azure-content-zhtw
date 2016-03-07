<properties
	pageTitle="定義實驗室原則 |Microsoft Azure"
	description="了解如何定義實驗室原則，例如 VM 大小、每位使用者的 VM 數目上限，以及自動關機。"
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
	ms.date="02/18/2016"
	ms.author="tarcher"/>

# 定義實驗室原則

> [AZURE.NOTE] 按一下下列連結可檢視本文隨附的影片：[如何撰寫自訂構件](/documentation/videos/how-to-set-vm-policies-in-a-devtest-lab)

## 概觀

研發/測試實驗室可讓您指定重要的原則，以控管使用您的實驗室及其 VM 的方式。這些原則包括成本臨界值、允許的 VM大小、每位使用者的 VM 數目上限，以及針對實驗室中的 VM 進行自動關機。

## 存取實驗室的原則

若要檢視 (及變更) 實驗室的原則，請遵循下列步驟：

1. 登入 [Azure Preview 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 點選 [設定]。

	![設定](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. 在 [設定] 刀鋒視窗上，有一個名為**原則**的設定群組。

	![Settings](./media/devtest-lab-set-lab-policy/policies.png)

	從下列清單中點選所需的原則，深入了解設定它的相關資訊：

	- 成本臨界值 - 目前不支援此原則。

	- [允許的 VM 大小](#set-allowed-vm-sizes) - 選取實驗室中允許的 VM 大小清單。使用者只能從此清單中建立 VM。

	- [VM 數目上限](#set-maximum-vms) - 指定可為實驗室建立的 VM 數目上限，以及使用者可建立的 VM 數目上限。

	- [自動關機](#set-auto-shutdown) - 指定目前實驗室的 VM 必須關機的時間。

## 設定允許的 VM 大小

設定允許的 VM 大小的原則有助於將實驗室的成本浪費降至最低，方式是讓您指定實驗室中允許的 VM 大小。如果啟用此原則，就只能使用此清單中的 VM 大小來建立 VM。

1. 在實驗室的 [設定] 刀鋒視窗上的 [原則] 下方，點選 [允許的 VM 大小]。

	![Settings](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果您已啟用此原則，請點選一或多個可在實驗室中建立的 VM 大小。

1. 點選 [儲存]。

## 設定 VM 數目上限

VM 數目上限的原則讓您能夠指定可為目前的實驗室建立的 VM 數目上限，以及使用者可建立的 VM 數目上限。如果使用者嘗試在達到該使用者限制或實驗室限制時建立新的 VM，將會顯示錯誤訊息，指出無法建立該 VM。

1. 在實驗室的 [設定] 刀鋒視窗上的 [原則] 下方，點選 [VM 數目上限]。

	![Settings](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. 在 [每位使用者的原則] 區段中：
 
	1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。
	
	1. 如果您已啟用此原則，可在 [每位使用者所允許的 VM 數目上限] 文字方塊中輸入數值，指出使用者可建立的 VM 數目上限。如果您輸入的數字無效，UI 將會顯示此欄位允許的最大數目。

1. 在 [每個實驗室的原則] 區段中：
 
	1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。
	
	1. 如果您已啟用此原則，可在 [這個實驗室中所允許的 VM 數目上限] 文字方塊中輸入數值，指出可針對目前實驗室建立的 VM 數目上限。如果您輸入的數字無效，UI 將會顯示此欄位允許的最大數目。

1. 點選 [儲存]。

## 設定自動關機

自動關機原則有助於將實驗室的成本浪費降至最低，方式是讓您指定這個實驗室中 VM 的關機時間。

1. 在實驗室的 [設定] 刀鋒視窗上的 [原則] 下方，點選 [自動關機]。

	![Settings](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. 點選 [開啟] 來啟用此原則，以及點選 [關閉] 來停用它。

1. 如果您已啟用此原則，可指定將目前實驗室中所有 VM 關機的時間。

1. 點選 [儲存]。

<!---HONumber=AcomDC_0224_2016-->