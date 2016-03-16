<properties
	pageTitle="建立 Linux VM 的不同方式 | Microsoft Azure"
	description="列出在 Azure 建立 Linux 虛擬機器的不同方式，並提供進一步指示的連結。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# 建立 Linux 虛擬機器的不同方式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure 提供建立 VM 的不同方式，以供不同的使用者和用途使用。本文章將摘要說明這些差異，以及您建立 Linux 虛擬機器時可做的選擇。

Azure 資源管理員範本也可用於將虛擬機器與其不同的資源當成一個邏輯部署單位來建立並管理。情況適用時會包含此方法的指示。若要深入了解 Azure 資源管理員，以及如何將資源當成一個單位來管理，請參閱[概觀][]。

## 工具選項

### GUI：Azure 傳統入口網站或 Azure 入口網站

入口網站的圖形化使用者介面是用來嘗試設定虛擬機器的簡單方法，特別是在您剛開始使用 Azure 時。使用 [Azure 傳統入口網站](https://manage.windowsazure.com)或 [Azure 入口網站](https://portal.azure.com)來建立 VM。如需一般指示，請參閱[建立自訂虛擬機器][]，並從**資源庫**選取任何 Linux 映像。請注意 [Azure 傳統入口網站](https://manage.windowsazure.com)僅使用傳統部署模型建立虛擬機器。

### 命令殼層：Azure CLI 或 Azure PowerShell

如果您偏好在命令殼層中工作，可選擇適用於 Mac、Linux 和 Windows 使用者的 Azure 命令列介面 (CLI)，或是 Azure PowerShell (其中具有適用於 Azure 和自訂主控台的 Windows PowerShell Cmdlet)。

若要使用 Azure CLI 在資源管理員部署模型中建立虛擬機器，請參閱[建立執行 Linux 的虛擬機器][]。遵循此文章上的索引標籤或文章選擇器，閱讀使用 Azure PowerShell 和範本的指示。

若為傳統部署模型，請參閱[使用 Azure CLI 建立自訂 Linux 虛擬機器](virtual-machines-linux-create-custom.md)和[使用 Azure PowerShell 建立並預先設定 Linux 虛擬機器][]。


### 開發環境：Visual Studio

Visual Studio 也支援建立 Azure 虛擬機器。若為傳統部署模型，請參閱[使用 Visual Studio 針對網站建立虛擬機器][]。若為資源管理員部署模型，請參閱[使用 Compute、Network 和 Storage .NET Library 部署 Azure 資源][]。


## 作業系統和映像選項

根據您想要執行的作業系統來選擇映像。Azure 與其合作夥伴提供許多映像，其中有些包括應用程式和工具。或者，使用您自己的其中一個映像。


### Azure 映像

在上述的所有文章中，您可以輕鬆地使用現有的 Azure 映像來建立虛擬機器，並針對網路、負載平衡及更多功能來自訂。入口網站為 Azure 提供的映像提供資源庫或映像清單。您可以使用命令列取得類似的清單。例如，在 Azure CLI 中執行 `azure vm image list` 來取得所有可用映像的清單 (根據位置和發行者提供)。


### 使用您自己的映像

「擷取」現有的 Azure 虛擬機器，根據該 VM 來使用映像，或者，上傳您自己的映像 (儲存於虛擬硬碟 (VHD) 中)。若為傳統部署模型，請參閱[如何透過 CLI 將 Linux 虛擬機器擷取為範本使用][]和[建立及上傳包含 Linux 作業系統的虛擬硬碟][]。若為資源管理員部署模型，請參閱[如何擷取 Linux 虛擬機器作為資源管理員範本](virtual-machines-linux-capture-image-resource-manager.md)。

## 後續步驟

[登入虛擬機器][]

[連接資料磁碟][]

## 其他資源

[基本組態測試環境][]

[Azure 混合式雲端測試環境][]

[在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 操作中的對等資源管理員和服務管理命令][]

<!-- LINKS -->
[概觀]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的 VM 操作中的對等資源管理員和服務管理命令]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[使用 Azure PowerShell 建立並預先設定 Linux 虛擬機器]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[如何透過 CLI 將 Linux 虛擬機器擷取為範本使用]: virtual-machines-linux-capture-image.md

[建立及上傳包含 Linux 作業系統的虛擬硬碟]: virtual-machines-linux-create-upload-vhd.md

[使用 Visual Studio 針對網站建立虛擬機器]: virtual-machines-dotnet-create-visual-studio-powershell.md
[使用 Compute、Network 和 Storage .NET Library 部署 Azure 資源]: virtual-machines-arm-deployment.md

[登入虛擬機器]: virtual-machines-linux-how-to-log-on.md

[連接資料磁碟]: virtual-machines-linux-how-to-attach-disk.md

[基本組態測試環境]: virtual-machines-base-configuration-test-environment.md
[Azure 混合式雲端測試環境]: virtual-machines-hybrid-cloud-test-environments.md

[建立執行 Linux 的虛擬機器]: virtual-machines-linux-tutorial.md
[建立自訂虛擬機器]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->