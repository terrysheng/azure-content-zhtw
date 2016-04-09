 <properties
   pageTitle="Azure 和 Linux | Microsoft Azure"
   description="描述 Azure 計算、儲存體和網路服務與 Linux 虛擬機器。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/01/2016"
   ms.author="rclaus"/>

# Azure 和 Linux
Microsoft Azure 集結了各種整合式公用雲端服務且數量不斷增加，包括分析、虛擬機器、資料庫、行動、網路、儲存體和 Web，因此很適合用來裝載您的方案。Microsoft Azure 提供可調整的運算平台，可讓您用多少就付多少且隨時都能使用，完全不必投資內部部署的硬體。Azure 可供您將您的方案相應增加和放大至任何您需要的規模，以滿足您所服務之客戶的需求。
 
## Azure 虛擬機器
Azure 虛擬機器可供您靈活部署各種運算方案。若您要部署 Windows 或 Linux 虛擬機器，可透過映像庫或我們不斷增加的夥伴清單內的任何成員所建立的自訂映像來進行。您幾乎可以在任何作業系統上以幾乎任何語言部署幾乎任何工作負載。還是沒找到您在尋找的映像嗎？ 別擔心，您也可以使用您在內部部署中擁有的映像。
 
## 在 Microsoft Azure 中開始使用 Linux

同時使用 Microsoft Azure 虛擬機器、儲存體和網路可提供網際網路等級的「基礎結構即服務」以因應您的 Linux 運算需求。若要在 Azure 上開始使用 Linux，你將需要︰

1. 免費試用帳戶。[前往取得](https://azure.microsoft.com/pricing/free-trial/)。
2. 適用於 Linux、Mac 和 Windows 的 Azure 命令列介面 (Azure CLI)。[加以安裝](../xplat-cli-install.md)。
3. Linux VM。[加以建立](virtual-machines-linux-quick-create-cli.md)。
4. 其他關於 Linux 和 Azure 的資訊，包括如何符合服務等級協定 (SLA) 的資格。**即使您不喜歡法律文件也請閱讀本文件**。

## 物流︰區域、散發套件、可用性、VM 大小和配額
### 區域
Microsoft Azure 資源分散在世界各地的多個地理區域。「區域」代表單一地理區域中的多個資料中心。自 2016 年 1 月 1 日起，這包括︰美洲 8 個、歐洲 2 個、亞太地區 6 個、中國大陸 2 個，以及印度 3 個。如果您需要所有 Azure 區域的完整清單，我們在**[這裡](https://azure.microsoft.com/regions/)**維護了一份現有和新宣布之區域的清單。

### 散發套件
Microsoft Azure 支援執行由多家合作夥伴提供和維護的眾多熱門 Linux 散發套件。您可以在 Azure Marketplace 中找到 CentOS、Debian、Red Hat Enterprise、Ubuntu 和 FreeBSD 等散發套件。我們積極與各大 Linux 社群合作以便為獲得背書的散發套件清單新增更多成員。請**[查看目前的散發套件](virtual-machines-linux-endorsed-distros.md)**。如果您所選擇的慣用 Linux 散發套件目前未出現在映像庫中，您可以遵循**[此頁面](virtual-machines-linux-create-upload-generic.md)**上提供的指導方針來「使用您自己的 Linux」VM。

## 可用性和 Microsoft Azure SLA
為了讓您的部署符合 99.95 的 VM 服務等級協定資格，您必須部署兩個或更多在可用性設定組內執行工作負載的 VM。這可確保您的 VM 會分散在我們資料中心內的多個容錯網域，以及部署至具有不同維護期間的主機。如需 SLA 的完整詳細資料，您可以**[在此進行線上檢視](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**。

## VM 大小和配額
當您在 Azure 中部署 VM 時，將會從我們一系列的大小中選取一個適合您工作負載的 VM 大小。大小也會影響虛擬機器的處理能力、記憶體和儲存體容量。收費的依據則是 VM 執行和使用其配置資源的時間長短。如需更完整的清單，請參閱下列關於[虛擬機器大小](virtual-machines-linux-sizes.md)的文章。

以下是一些從我們提供的系列 (A、D、DS、G 和 GS) 中選取一個 VM 大小的基本指導方針。

* A 系列 VM 是物超所值的入門級 VM，適用於輕度工作負載和開發/測試案例。所有區域皆廣泛提供此系列 VM，其可用來連接並使用虛擬機器可用的所有標準資源。
* A 系列大小 (A8 - A11) 則是特殊的可進行大量運算的組態，適用於高效能的運算叢集應用程式。
* D 系列 VM 是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。 
* Dv2 系列是 D 系列的最新版本，其配備有功能更強大的 CPU。Dv2 系列 CPU 比 D 系列 CPU 快約 35%。它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。Dv2 系列的記憶體和磁碟組態和 D 系列一樣。
* G 系列 VM 提供最大的記憶體，並且是在具有 Intel Xeon E5 V3 系列處理器的主機上執行。

注意：DS 系列和 GS 系列 VM 可存取進階儲存體，這個搭載 SSD 的高效能、低延遲儲存體適合需要進行密集 I/O 工作的工作負載。僅特定地區可用進階儲存體。如需詳細資訊，請參閱**[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage-preview-portal.md)**。

每個 Azure 訂用帳戶都有預設配額限制，而此限制會在您要部署大量 VM 以供專案使用時造成影響。每一訂用帳戶目前的限制是每一區域 20 個 VM。只要提出支援票證來要求增加限制即可提高此配額限制。如需配額限制的詳細資訊，請參閱 **[Azure 訂用帳戶服務限制](../azure-subscription-service-limits.md)**

## 後續步驟

免費試用帳戶。**[前往取得](https://azure.microsoft.com/pricing/free-trial/)**。 如果您已有帳戶而想要試用看看，請**[安裝 Azure CLI](../xplat-cli-install.md)**。如果您已試用完畢，則請[立即建立 Linux VM](virtual-machines-linux-quick-create-cli.md)。

<!---HONumber=AcomDC_0323_2016-->