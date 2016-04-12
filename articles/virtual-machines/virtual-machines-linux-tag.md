<properties
   pageTitle="如何標記 Linux 虛擬機器 | Microsoft Azure"
   description="了解如何標記以資源管理員部署模型於 Azure 中所建立的 Linux 虛擬機器。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# 如何在 Azure 中標記 Linux 虛擬機器

本文說明在 Azure 中透過 Azure Resource Manager 標記 Linux 虛擬機器的各種不同方式。標記是使用者定義的成對「索引鍵/值」，可直接置於資源或資源群組。Azure 目前對每一個資源和資源群組最多支援 15 個標記。標記可在建立或加入至現有資源時置於資源上。請注意，標籤只支援透過 Azure 資源管理員建立的資源。若要標記 Windows 虛擬機器，請參閱 [如何在 Azure 中標記 Windows 虛擬機器](virtual-machines-windows-tag.md)。

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## 透過 Azure CLI 進行標記

已透過 Azure CLI 建立的資源也支援標記。首先，請設定 [Azure CLI 環境][]。透過 Azure CLI 登入您的訂用帳戶並切換至 ARM 模式。您可以使用這個命令來檢視指定之虛擬機器的所有屬性，包括標記：

        azure vm show -g MyResourceGroup -n MyVM

不同於 PowerShell 的是，如果您要將標記新增至已含有標記的資源，不需先指定所有標記 (無論新舊)，即可使用 `azure vm set` 命令。相反地，此命令可讓您將標記附加至您的資源。若要透過 Azure CLI 新增 VM 標記，您可以搭配使用 `azure vm set` 命令搭配標記參數 **-t**：

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

若要移除所有標記，您可以在 `azure vm set` 命令中使用 **–T** 參數。

        azure vm set – g MyResourceGroup –n MyVM -T


既然我們已透過 PowerShell、Azure CLI 和入口網站，將標記套用至我們的資源，就讓我們來看一下使用量詳細資料，以在計費入口網站中查看標記。




## 後續步驟

* 若要深入了解如何標記您的 Azure 資源，請參閱 [Azure Resource Manager 概觀][]與[使用標記來組織您的 Azure 資源][]。
* 若要查看標記如何協助您管理 Azure 資源的使用，請參閱[了解 Azure 帳單][]與[深入了解 Microsoft Azure 資源耗用量][]。





[Azure CLI 環境]: ./xplat-cli-azure-resource-manager.md
[Azure Resource Manager 概觀]: ../resource-group-overview.md
[使用標記來組織您的 Azure 資源]: ../resource-group-using-tags.md
[了解 Azure 帳單]: ../billing-understand-your-bill.md
[深入了解 Microsoft Azure 資源耗用量]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0330_2016-->