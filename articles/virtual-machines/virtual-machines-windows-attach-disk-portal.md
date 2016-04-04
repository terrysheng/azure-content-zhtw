<properties
	pageTitle="連接資料磁碟 | Microsoft Azure"
	description="如何在 Azure 入口網站中，使用資源管理員部署模型，將新的或現有的資料磁碟連接至 VM。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# 如何在 Azure 入口網站連接資料磁碟

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>如何：在 Windows Server 中初始化新的資料磁碟

1. 連接至虛擬機器。如需指示，請參閱[如何登入執行 Windows Server 的虛擬機器](virtual-machines-windows-log-on.md)。

2. 登入虛擬機器之後，開啟 [伺服器管理員]。在左窗格中，選取 [File and Storage Services]。

	![開啟伺服器管理員](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. 展開功能表，然後選取 [**磁碟**]。

4. [磁碟] 區段會列出磁碟。在大部分情況下，會有磁碟 0、磁碟 1 和磁碟 2。磁碟 0 是作業系統磁碟、磁碟 1 是暫存磁碟，磁碟 2 則是您剛連接至 VM 的資料磁碟。新的資料磁碟會將磁碟分割列為 [未知]。使用滑鼠右鍵按一下磁碟，然後選取 [初始化]。

5.	初始化磁碟時，您會收到將清除所有資料的通知。按一下 [**是**]，認可此警告並初始化磁碟。完成之後，即會將磁碟分割列為 [GPT]。再次以滑鼠右鍵按一下磁碟，然後選取 [新增磁碟區]。

6.	使用預設值完成精靈。當精靈完成時，[**磁碟區**] 區段會列出新的磁碟區。磁碟此時將上線，可用來儲存資料。


	![成功初始化磁碟區](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] VM 的大小會決定它可以附加的磁碟數目。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。


## 後續步驟

如果您的應用程式需要使用 D: 磁碟機來儲存資料，您可以 [變更 Windows 暫存磁碟的磁碟機代號]。

<!---HONumber=AcomDC_0323_2016-->