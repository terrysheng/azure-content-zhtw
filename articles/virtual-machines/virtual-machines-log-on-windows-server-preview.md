<properties
	pageTitle="登入執行 Windows Server 的虛擬機器"
	description="了解如何使用 Azure Preview 入口網站來登入執行 Windows Server 的虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# 如何登入執行 Windows Server 的虛擬機器#

您會使用 Azure Preview 入口網站中的 [連接] 按鈕啟動「遠端桌面」工作階段。首先您要連接至虛擬機器，然後登入。

## 連接至虛擬機器

1. 如果您未曾執行過這項操作，請登入 [Azure Preview 入口網站](https://portal.azure.com/)。

2.	在 [中樞] 功能表上，按一下 [瀏覽]。

3.	在搜尋刀鋒視窗中，向下捲動然後按一下 [虛擬機器]。

	![搜尋虛擬機器](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	然後從清單中選取虛擬機器。

5. 在 [虛擬機器] 刀鋒視窗中，按一下 [連接]。

	![連接至虛擬機器](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## 登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 疑難排解

如果有關登入的秘訣沒有幫助，或者不是您所需要的，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)。這篇文章會逐步帶領您診斷及解決常見的問題。

<!---HONumber=Sept15_HO3-->