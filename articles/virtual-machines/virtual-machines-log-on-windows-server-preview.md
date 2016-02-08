<properties
	pageTitle="登入 Windows Server VM | Microsoft Azure"
	description="了解如何使用 Azure 入口網站和資源管理員部署模型登入 Windows Server VM。"
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
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# 如何登入執行 Windows Server 的虛擬機器 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md)。

您會使用 Azure 入口網站中的 [連線] 按鈕啟動「遠端桌面」工作階段。首先您要連接至虛擬機器，然後登入。

## 連接至虛擬機器

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com/)，請先登入。

2.	在 [中樞] 功能表上，按一下 [虛擬機器]。

3.	然後從清單中選取虛擬機器。

4. 在虛擬機器的刀鋒視窗中，按一下 [**連線**]。

	![連接至虛擬機器](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## 登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 疑難排解

如果有關登入的秘訣沒有幫助，或者不是您所需要的，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)。本文會逐步帶領您診斷及解決常見的問題。

<!---HONumber=AcomDC_0128_2016-->