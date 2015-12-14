<properties
	pageTitle="登入 VM | Microsoft Azure"
	description="使用 Azure 傳統入口網站來登入以傳統部署模型建立的 Windows 虛擬機器。"
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
	ms.date="10/05/2015"
	ms.author="cynthn"/>


# 使用 Azure 傳統入口網站登入 Windows 虛擬機器



在 Azure 傳統入口網站中，使用 [連接] 按鈕來啟動遠端桌面工作階段，並登入 Windows VM。

您想要連線至 Linux VM 嗎？ 請參閱[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


## 影片逐步解說

以下是本教學課程的逐步解說影片。它還包括可用來連接到 Azure 中 Windows VM 的端點及公用和私人連接埠。

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## 連接至虛擬機器

1. 登入 Azure 傳統入口網站。

2. 按一下 [虛擬機器]，然後選取虛擬機器。

3. 在頁面底部的命令列中，按一下 [連接]。

	![登入虛擬機器](./media/virtual-machines-log-on-windows-server/connectwindows.png)
	
> [AZURE.TIP]如果 [連接] 按鈕無法使用，請參閱本文結尾處的疑難排解提示。

## 登入虛擬機器

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## 疑難排解秘訣

以下是一些可嘗試的方法：

-	如果 [連接] 按鈕無法使用，或是有其他「遠端桌面」連線問題，請嘗試重設組態。從虛擬機器儀表板的**快速概覽**底下，按一下 [重設遠端組態]。
-	如果是您的密碼有問題，請嘗試重設密碼。從虛擬機器儀表板的**快速概覽**底下，按一下 [重設密碼]。

如果這些秘訣無效，或者不是您所需要的，請參閱[針對以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線進行疑難排解](virtual-machines-troubleshoot-remote-desktop-connections.md)。本文會逐步帶領您診斷及解決常見的問題。

<!---HONumber=AcomDC_1203_2015-->