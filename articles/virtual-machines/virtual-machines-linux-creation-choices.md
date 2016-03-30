<properties
	pageTitle="建立 Linux VM 的不同方式 | Microsoft Azure"
	description="列出在 Azure 建立 Linux 虛擬機器的不同方式，並提供進一步指示的連結"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/07/2016"
	ms.author="dkshir"/>

# 使用 Resource Manager 建立 Linux 虛擬機器的不同方式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

Azure 提供建立 VM 的不同方式，以供不同的使用者和用途使用。本文章將摘要說明這些差異，以及您建立 Linux 虛擬機器時可做的選擇。

我們最近推出 Azure 資源管理員範本做為一種方式，用以建立虛擬機器，並將它與其不同資源當成一個邏輯部署單位來管理。以下提供這個可用方法的指示。若要深入了解 Azure 資源管理員，以及如何將資源當成一個單位來管理，請參閱[概觀](../resource-group-overview.md)。

## 工具選項

### GUI：Azure 入口網站

[Azure 入口網站](https://portal.azure.com)的圖形化使用者介面是用來嘗試設定虛擬機器的簡單方法，特別是在您剛開始使用 Azure 時。使用 Azure 入口網站來建立 VM：

* [使用 Azure 入口網站建立執行 Linux 的虛擬機器](virtual-machines-linux-portal-create.md) 

### 命令殼層︰Azure CLI 

如果您偏好在命令殼層中工作，請使用適用於 Mac、Linux 和 Windows 使用者的 Azure 命令列介面 (CLI)。

針對 Azure CLI，請參閱下列教學課程︰

* [從 CLI 建立用於開發和測試的 Linux VM](virtual-machines-linux-quick-create-cli.md) 

* [使用 Azure 範本建立受保護的 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

## 作業系統和映像選項

根據您想要執行的作業系統來選擇映像。Azure 與其合作夥伴提供許多映像，其中有些包括應用程式和工具。或者，使用您自己的其中一個映像。

### Azure 映像

在上述的所有文章中，您可以輕鬆地使用現有的 Azure 映像來建立虛擬機器，並針對網路、負載平衡及更多功能來自訂。入口網站有提供 Azure Marketplace，其中網羅了 Azure 提供的映像。您可以使用命令列取得類似的清單。例如，在 Azure CLI 中執行 `azure vm image list` 來取得所有可用映像的清單 (根據位置和發行者提供)。請參閱[使用 Azure CLI 巡覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。

### 使用您自己的映像

「擷取」現有的 Azure 虛擬機器，根據該 VM 來使用映像，或者，上傳您自己的映像 (儲存於虛擬硬碟 (VHD) 中)。如需詳細資訊，請參閱：

* [Azure 背書的散發套件](virtual-machines-linux-endorsed-distros.md)

* [非背書散發套件的資訊](virtual-machines-linux-create-upload-generic.md)

* [如何擷取 Linux 虛擬機器以做為 Resource Manager 範本](virtual-machines-linux-capture-image.md)。

## 後續步驟

* 嘗試其中一個教學課程，以透過[入口網站](virtual-machines-linux-portal-create.md)、[CLI](virtual-machines-linux-quick-create-cli.md) 或 Azure Resource Manager [範本](virtual-machines-linux-cli-deploy-templates.md)建立 Linux VM。

* 在建立 Linux VM 後，您可以輕鬆地[新增資料磁碟](virtual-machines-linux-add-disk.md)。

<!---HONumber=AcomDC_0323_2016-->