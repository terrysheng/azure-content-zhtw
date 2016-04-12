<properties
	pageTitle="Windows VM 預定進行的維修 | Microsoft Azure"
	description="了解什麼是 Azure 預定進行的維修，以及其對 Azure 中執行的 Windows 虛擬機器的影響為何。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="drewm"/>

# Azure 中 Windows 虛擬機器預定進行的維修

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

了解什麼是 Azure 預定進行的維修，以及其對 Windows 虛擬機器可用性的影響為何。您也可以閱讀 [Linux 虛擬機器預定進行的維修](virtual-machines-linux-planned-maintenance.md)。

## Azure 執行計劃性維護的原因

為提升虛擬機器之基礎主機基礎結構的可靠性、效能和安全性，Microsoft Azure 會全球定期執行更新。許多這些更新在執行時並不會對虛擬機器或雲端服務造成任何影響，包括記憶體保留的更新。

不過，有些更新的確需要將虛擬機器重新開機，才能將必要更新套用至基礎結構。我們會在修補基礎結構時將虛擬機器關機，然後再將虛擬機器重新啟動。

請注意，有兩種類型的維護會對虛擬機器的可用性造成影響：規劃和非規劃性。本頁面說明 Microsoft Azure 會如何執行計劃性維護。如需非計劃性維護的詳細資訊，請參閱[了解計劃性與非計劃性維護](virtual-machines-windows-manage-availability.md)。

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0330_2016-->