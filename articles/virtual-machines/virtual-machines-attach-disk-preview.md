<properties
	pageTitle="連接資料磁碟 | Microsoft Azure"
	description="如何在 Azure 入口網站中，使用資源管理員部署模型，將新的或現有的資料磁碟連接至 VM。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# 如何在 Azure 入口網站連接資料磁碟

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](storage-windows-attach-disk.md)。

本文將說明如何透過 Azure入口網站將新的和現有的磁碟連接至虛擬機器。這麼做之前，請先檢閱下列提示：

- 虛擬機器的大小會控制您可以連接的資料磁碟數目。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-size-specs.md)。
- 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。您可以使用進階或標準儲存體帳戶的磁碟搭配這些虛擬機器。僅特定地區可用進階儲存體。如需詳細資訊，請參閱[高階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md)。
- 連接至虛擬機器的磁碟實際上是 Azure 儲存體帳戶中的 .vhd 檔案。如需詳細資訊，請參閱[有關虛擬機器的磁碟和 VHD](virtual-machines-disks-vhds.md)。
- 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。
- 對於現有的磁碟，該 .vhd 檔案必須可在 Azure 儲存體帳戶中取得。您可以使用現有的 .vhd 檔案 (若尚未連接至其他虛擬機器)，或上傳您自己的 .vhd 檔案至儲存體帳戶。

## 尋找虛擬機器

1. 登入 Azure 入口網站。

2. 在 [中樞] 功能表上，按一下 [虛擬機器]。

3.	然後從清單中選取虛擬機器。

4. 在右側的 [Essentials] 底下，按一下 [所有設定]，然後按一下 [磁碟]。

	![開啟磁碟設定](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

接著依照指示來連接新的磁碟或現有的磁碟。

## 選項 1：連接新的磁碟

1.	在 [磁碟] 刀鋒視窗上，按一下 [連接新項目]。

2.	檢閱預設設定，視需要進行更新，然後按一下 [確定]。

 	![檢閱磁碟設定](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。

## 選項 2：連接現有磁碟

1.	在 [磁碟] 刀鋒視窗上，按一下 [連接現有項目]。

2.	在 [連接現有磁碟] 底下，按一下 [VHD 檔案]。

	![連接現有磁碟](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	在 [儲存體帳戶] 底下，選取持有該 .vhd 檔案的帳戶和容器。

	![尋找 VHD 位置](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	選取 .vhd 檔案。

5.	在 [連接現有磁碟] 底下，您剛才選取的檔案會列在 [VHD 檔案] 底下。按一下 [確定]。

6.	Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。

## 後續步驟

新增磁碟之後，您必須準備將它用於虛擬機器的作業系統：

- 針對 Linux，請參閱這篇[文章](virtual-machines-linux-how-to-attach-disk.md)中的「做法：在 Linux 中初始化新的資料磁碟」。
- 針對 Windows，請參閱這篇[文章](storage-windows-attach-disk.md)中的「做法：在 Windows Server 中初始化新的資料磁碟」。

## 其他資源

[關於 Azure 儲存體帳戶]

<!--Link references-->

[關於 Azure 儲存體帳戶]: ../storage-whatis-account/

<!---HONumber=AcomDC_0302_2016-------->