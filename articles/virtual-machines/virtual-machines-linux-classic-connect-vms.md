<properties
	pageTitle="連線雲端服務中的 Linux VM | Microsoft Azure"
	description="將利用傳統部署模型所建立的 Linux 虛擬機器連線至 Azure 雲端服務或虛擬網路。"
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
	ms.date="03/24/2016"
	ms.author="cynthn"/>

# 透過虛擬網路或雲端服務來連線利用傳統部署模型所建立的 Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

以傳統部署模型所建立的 Linux 虛擬機器一律會放置於雲端服務中。雲端服務能做為容器，並提供唯一的公用 DNS 名稱、公用 IP 位址，以及一組透過網際網路存取虛擬機器的端點。雲端服務可以位於虛擬網路，但這不是必要條件。您也可以[透過虛擬網路或雲端服務來連線 Windows 虛擬機器](virtual-machines-windows-classic-connect-vms.md)。

如果雲端服務不在虛擬網路中，就稱為*獨立*雲端服務。獨立雲端服務中的虛擬機器只能與使用其他虛擬機器之公用 DNS 名稱的其他虛擬機器通訊，該流量會透過網際網路傳送。如果雲端服務是在虛擬網路中，該雲端服務中的虛擬機器可與虛擬網路中的所有其他虛擬機器通訊，而不需要透過網際網路傳送任何流量。

如果您將虛擬機器放在同一個獨立雲端服務中，您仍然可以使用負載平衡和可用性設定組。如需詳細資訊，請參閱[負載平衡虛擬機器](virtual-machines-linux-load-balance.md)和[管理虛擬機器的可用性](virtual-machines-linux-manage-availability.md)。不過，您無法組織子網路上的虛擬機器，或將獨立雲端服務連線到內部部署網路。以下是範例：

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## 後續步驟

建立虛擬機器之後，最好[新增資料磁碟](virtual-machines-linux-classic-attach-disk.md)，讓服務與工作負載有存放資料的地方。

<!---HONumber=AcomDC_0330_2016-->