<properties
	pageTitle="從 VHD 檔案建立研測實驗室自訂映像 | Microsoft Azure"
	description="了解如何從 VHD 檔案建立自訂映像，接著可使用該映像在研測實驗室中建立 VM"
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

# 從 VHD 檔案建立研測實驗室自訂映像

## 概觀

當您[建立研測實驗室](devtest-lab-create-lab.md)之後，就可以[將虛擬機器 (VM) 加入該實驗室](devtest-lab-add-vm-with-artifacts.md)。當您建立 VM 時，您會指定「基底」，這可以是「自訂映像」或「Marketplace 映像」。在本文中，您將看到如何從 VHD 檔案建立自訂映像的方式。請注意，您必須能夠存取有效的 VHD 檔案，才能執行本文中的所有步驟。

## 建立自訂映像

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 所選實驗室的 [設定] 刀鋒視窗隨即顯示。

1. 在實驗室的 [設定] 刀鋒視窗上，點選 [自訂映像]。

    ![自訂映像選項](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. 在 [自訂映像] 刀鋒視窗上，點選 [+自訂映像]。

    ![加入自訂映像](./media/devtest-lab-create-template/add-custom-image.png)

1. 輸入自訂映像的名稱。這個名稱會在建立新的 VM 時顯示於基底映像清單中。

1. 輸入自訂映像的描述。這個描述會在建立新的 VM 時顯示於基底映像清單中。

1. 點選 [VHD 檔案]。

1. 如果未列出您有權存取的 VHD 檔案，請依照[上傳 VHD 檔案](#upload-a-vhd-file)一節中的指示來加入該檔案，完成之後再回到這裡。

1. 選取所需的 VHD 檔案。

1. 點選 [確定] 以關閉 [VHD 檔案] 刀鋒視窗。

1. 點選 [作業系統組態]。

1. 在 [作業系統組態] 索引標籤上，選取 [Windows] 或 [Linux]。

1. 如果選取 [Windows]，請透過核取方塊來指定 *Sysprep* 是否已在電腦上執行。

1. 點選 [確定] 以關閉 [作業系統組態] 刀鋒視窗。

1. 點選 [確定] 以建立自訂映像。

1. 請移至[後續步驟](#next-steps)一節。

##上傳 VHD 檔案

若要加入新的自訂映像，您必須具有 VHD 檔案的存取權。

1. 在 [VHD 檔案] 刀鋒視窗中，點選 [使用 PowerShell 上傳 VHD 檔案]。

    ![上傳映像](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 下一個刀鋒視窗將顯示用來修改和執行 PowerShell 指令碼的指示，該指令碼會將 VHD 檔案上傳到您的 Azure 訂用帳戶。**附註：**根據 VHD 檔案的大小與您的連接速度而定，這個程序的時間可能非常冗長。

##後續步驟

一旦加入要在建立 VM 時使用的自訂映像之後，下一個步驟就是[將 VM 加入研測實驗室](./devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=AcomDC_0316_2016-->