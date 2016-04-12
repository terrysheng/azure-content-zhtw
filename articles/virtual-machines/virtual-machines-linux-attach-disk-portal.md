<properties
	pageTitle="將資料磁碟連結到 Linux VM | Microsoft Azure"
	description="如何在 Azure 入口網站中，使用資源管理員部署模型，將新的或現有的資料磁碟連結到 Linux VM。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="cynthn"/>

# 如何在 Azure 入口網站中將資料磁碟連結到 Linux VM

本文示範如何透過 Azure 入口網站將新的及現有的磁碟連結到 Linux 虛擬機器。您也可以[在 Azure 入口網站中將資料磁碟連結到 Windows VM](virtual-machines-windows-attach-disk-portal.md)。這麼做之前，請先檢閱下列提示：

- 虛擬機器的大小會控制您可以連接的資料磁碟數目。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。
- 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。您可以使用進階或標準儲存體帳戶的磁碟搭配這些虛擬機器。僅特定地區可用進階儲存體。如需詳細資訊，請參閱[高階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage-preview-portal.md)。
- 連接至虛擬機器的磁碟實際上是 Azure 儲存體帳戶中的 .vhd 檔案。如需詳細資訊，請參閱[有關虛擬機器的磁碟和 VHD](virtual-machines-linux-about-disks-vhds.md)。
- 對於新的磁碟，當您連接的時候 Azure 會建立該磁碟，所以您不需要建立它。
- 對於現有的磁碟，該 .vhd 檔案必須可在 Azure 儲存體帳戶中取得。您可以使用現有的 .vhd 檔案 (若尚未連接至其他虛擬機器)，或上傳您自己的 .vhd 檔案至儲存體帳戶。


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## 後續步驟

加入磁碟後，您需要準備它以便使用。請參閱此篇[文章](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux)中的虛擬機器作業系統：＜如何：在 Linux 中初始化新的資料磁碟＞。

<!---HONumber=AcomDC_0330_2016-->