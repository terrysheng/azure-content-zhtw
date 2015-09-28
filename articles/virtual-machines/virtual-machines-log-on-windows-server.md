<properties
	pageTitle="登入執行 Windows Server 的虛擬機器"
	description="了解如何使用 Azure 入口網站來登入執行 Windows Server 的虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# 如何登入執行 Windows Server 的虛擬機器#

您會使用 Azure 入口網站中的 [連線] 按鈕啟動「遠端桌面」工作階段。首先您要連線至虛擬機器，然後登入。

想要連線至 Linux VM 嗎？ 請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。

## 連線至虛擬機器。

以下是本教學課程的逐步解說。

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. 如果您尚未登入 [Azure 入口網站](http://manage.windowsazure.com)，請先登入。

2. 按一下 [虛擬機器]，然後選取適當的虛擬機器。

3. 按一下命令列上的 [連接]。

	![登入虛擬機器](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## 登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 疑難排解秘訣

以下是入口網站中一些可以快速嘗試的方法：

-	如果是遠端桌面連線的問題，請嘗試重設組態。從虛擬機器儀表板的**快速概覽**底下，按一下 [重設遠端組態]。
-	如果是您的密碼有問題，請嘗試重設密碼。從虛擬機器儀表板的**快速概覽**底下，按一下 [重設密碼]。

如果這些秘訣沒效，或者不是您所需要的，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)。本文會逐步帶領您診斷及解決常見的問題。

<!---HONumber=Sept15_HO3-->