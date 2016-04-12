<properties
	pageTitle="在傳統 Linux VM 上設定端點 | Microsoft Azure"
	description="了解如何在 Azure 傳統入口網站中設定端點，以允許與 Azure 中的 Linux 虛擬機器進行通訊"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# 如何在 Azure 中的傳統 Linux 虛擬機器上設定端點

在 Azure 中使用傳統部署模型建立的所有 Linux 虛擬機器，都可以自動透過私人網路通道與同一雲端服務或虛擬網路中的其他虛擬機器通訊。不過，網際網路或其他虛擬網路上的電腦需要端點，才能將傳入網路流量導向至虛擬機器。如需 Windows VM 上端點的相關資訊，請參閱 [如何在 Azure 中的傳統 Windows 虛擬機器上設定端點](virtual-machines-windows-classic-setup-endpoints.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

當您在 Azure 傳統入口網站中建立 Linux 虛擬機器時，一般會自動為您建立安全殼層 (SSH) 等通用端點。建立虛擬機器或日後有需要時，您可以設定其他端點。
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## 後續步驟

* 也可以在[服務管理模式](../virtual-machines-command-line-tools.md)中使用 Azure 命令列介面建立 VM 端點。執行 **azure vm endpoint create** 命令。

<!---HONumber=AcomDC_0330_2016-->