    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
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

# 建立 VM 範本

## 概觀

一旦[建立實驗室](devtest-lab-create-lab.md)之後，您就可以從 VM 範本清單中[將 VM 加入該實驗室](devtest-lab-add-vm-with-artifacts.md)。在本文中，您將了解如何上傳和設定虛擬硬碟 (VHD) 映像檔，以做為可用來建立您 VM 的範本。如果您不熟悉 VHD 映像，請參閱[建立並上傳 Windows Server VHD 到 Azure](../virtual-machines-create-upload-vhd-windows-server.md) 一文，了解如何建立 VHD 映像。一旦建立或取得 VHD 映像的存取權之後，本文將逐步帶領您上傳映像，並從中建立範本。

## 建立 VM 範本

1. 登入 [Azure Preview 入口網站](http://portal.azure.com)。

1. 點選 [瀏覽]，然後從清單中點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 在實驗室刀鋒視窗上，點選 [設定]。

    ![實驗室設定](./media/devtest-lab-create-template/lab-blade-settings.png)

1. 在實驗室的 [設定] 刀鋒視窗上，點選 [範本]。

    ![範本選項](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. 在 [範本] 刀鋒視窗上，點選 [+ 範本]。

    ![加入範本](./media/devtest-lab-create-template/add-template.png)

1. 在 [加入範本] 刀鋒視窗上：

	1. 輸入範本的名稱。這個名稱會在建立新的 VM 時顯示於範本清單中。

	1. 輸入範本的描述。這個描述會在建立新的 VM 時顯示於範本清單中。

	1. 點選 [映像]。

	1. 如果未列出您想要的映像但您想要將它加入，請先跳至[加入新的範本映像](#add-a-new-template-image)一節，並於完成之後返回此處。

	1. 選取所需的映像。

	1. 點選 [確定] 以關閉 [加入範本] 刀鋒視窗。

1. 點選 [作業系統組態]。

1. 在 [作業系統組態] 索引標籤上，選取 [Windows] 或 [Linux]。

1. 如果選取 [Windows]，請透過核取方塊來指定 *Sysprep* 是否已在電腦上執行。

1. 輸入電腦的**使用者名稱**。

1. 輸入電腦的**密碼**。**附註：**密碼將以純文字顯示。

1. 點選 [確定] 以關閉 [作業系統組態] 刀鋒視窗。

1. 指定 [位置]。

1. 點選 [確定] 以建立範本。

##加入新的範本映像

若要加入新的範本映像，您必須具有 VHD 映像檔案的存取權。

1. 在 [加入範本映像] 刀鋒視窗上，點選 [使用 PowerShell 上傳映像]。

    ![上傳映像](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. 下一個刀鋒視窗將顯示修改和執行 PowerShell 指令碼的指示，該指令碼會將 VHD 映像檔案上傳到您的 Azure 訂用帳戶。**附註：**根據映像檔案的大小與您的連接速度而定，這個程序的時間可能非常冗長。

##後續步驟

一旦加入要在建立 VM 時使用的 VM 範本之後，下一個步驟就是[將 VM 加入研發/測試實驗室](devtest-lab-add-vm-with-artifacts)。

<!---HONumber=AcomDC_1203_2015-->