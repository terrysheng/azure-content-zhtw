<properties
	pageTitle="在 Azure 中建立執行 Windows 的虛擬機器"
	description="了解如何在 Azure 的傳統入口網站中建立 Windows 虛擬機器 (VM)。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-classic-portal"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="kathydav"/>



# 建立執行 Windows 的虛擬機器

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-machines-windows-tutorial.md)
- [Azure 傳統入口網站](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

本教學課程示範建立 Azure 虛擬機器 (VM) 有多麼容易。這個教學課程使用 Windows Server 映像，不過這是透過 Azure 提供的許多映像之一。這包括 Windows 作業系統、Linux 型作業系統和預先安裝應用程式的映像。您可以選擇的映像取決於您擁有的訂閱類型。例如，桌面映像可能可供 MSDN 訂閱者使用。

您也可以使用[自己的映像](virtual-machines-create-upload-vhd-windows-server-classic-portal.md)來建立  Windows VM。若要深入了解 Azure VM，請參閱 [Azure 虛擬機器概觀](http://msdn.microsoft.com/library/azure/jj156143.aspx)。

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>如何建立虛擬機器

本節說明如何使用 Azure 傳統入口網站中的 [從組件庫]**** 選項建立虛擬機器。此選項提供的組態選擇比 [快速建立]**** 選項還多。例如，如果您要將虛擬機器加入虛擬網路中，您必須使用 [從組件庫]**** 選項。

> [AZURE.NOTE]您也可以使用更豐富而可自訂的 [Azure 入口網站](https://portal.azure.com)來建立虛擬機器、將多重 VM 應用程式範本的部署自動化、使用增強的 VM 監控和診斷功能，以及其他作業。兩個入口網站中的可用 VM 組態選項有許多重疊之處，但並不完全相同。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## 後續步驟

- 登入虛擬機器。如需指示，請參閱[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。

- 附加磁碟來儲存資料。您可以附加空的磁碟和含有資料的磁碟。如需指示，請參閱[附加資料磁碟教學課程](storage-windows-attach-disk.md)。

## 其他資源

若要深入了解您可以為 VM 設定什麼功能以及何時可以設定，請參閱[有關 Azure VM 設定](http://msdn.microsoft.com/library/azure/dn763935.aspx)。

<!--HONumber=52-->
