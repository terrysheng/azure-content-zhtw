<properties
 pageTitle="關於 A8、A9、A10 和 A11 執行個體 | Microsoft Azure"
 description="取得使用虛擬機器和雲端服務適用的 Azure A8、A9、A10 和 A11 密集運算大小的背景資訊和考量。"
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/29/2015"
 ms.author="danlep"/>

# 關於 A8、A9、A10 和 A11 密集運算執行個體

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]本文探討以資源管理員部署模型或傳統部署模型建立資源。

這篇文章提供使用 Azure A8、A9、A10 和 A11 執行個體，也稱為*密集運算*執行個體的背景資訊和考量。這些執行個體的主要功能包括：

* **高效能硬體** - 執行這些執行個體的 Azure 資料中心硬體是為了密集運算和密集網路應用程式而設計及最佳化的，包括高效能運算 (HPC) 叢集應用程式、模型和模擬。

* **適用於 MPI 應用程式的 RDMA 網路連線** - 使用所需的網路驅動程式進行設定時，A8 和 A9 執行個體可以透過 Azure 中低延遲、高輸送量的網路來與其他的 A8 和 A9 執行個體進行通訊，該網路是以遠端直接記憶體存取 (RDMA) 技術為基礎。此功能可以提升使用支援的 Linux 或 Windows 訊息傳遞介面 (MPI) 實作的應用程式效能。

* **支援 Linux 和 Windows HPC 叢集** - 在 Azure 中的 A8、A9、A10 和 A11 執行個體上部署叢集管理和工作排程軟體，來建立獨立的 HPC 叢集，或在內部部署叢集中新增功能。如同其他的 Azure VM 大小，A8、A9、A10 和 A11 執行個體在 Azure VM (IaaS) 中支援標準或自訂的 Windows Server 和 Linux 作業系統映像或 Azure 資源管理員範本，或在雲端服務 (PaaS，僅適用於 Windows Server) 中支援 Azure 客體 OS 版本。

>[AZURE.NOTE]A10 和 A11 執行個體具有和 A8 和 A9 執行個體相同的效能最佳化和規格化。不過，不包括 Azure 中 RDMA 網路的存取權。它們是專為節點之間不需要常數和低延遲通訊的 HPC 應用程式設計，也就是參數式或窘迫平行應用程式。此外，為了執行 MPI 應用程式以外的工作負載，A8 和 A9 執行個體不會存取 RDMA 網路，且其功能相當於 A10 和 A11 執行個體。


## 規格

### CPU 和記憶體

Azure A8、A9、A10 和 A11 密集運算執行個體的功能為高速、多核心 CPU 以及大量的記憶體，如下表所示。

大小 | CPU | 記憶體
------------- | ----------- | ----------------
A8 和 A10 | Intel Xeon E5-2670<br/>8 核心 @ 2.6 GHz | DDR3-1600 MHz<br/>56 GB
A9 和 A11 | Intel Xeon E5-2670<br/>16 核心 @ 2.6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]其他處理器詳細資料 (包括受支援的指令集延伸模組) 位於 Intel.com 網站。如需 VM 儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](virtual-machines-size-specs.md)。

### 網路介面卡

A8 和 A9 執行個體具有兩張網路介面卡，可連接到下列兩個後端 Azure 網路。


網路 | 說明
-------- | -----------
10-Gbps 乙太網路 | 連線至 Azure 服務 (例如 Azure 儲存體和 Azure 虛擬網路) 和網際網路。
32-Gbps 後端，具有 RDMA 功能 | 在單一雲端服務或可用性集合內的執行個體之間啟用低延遲、高輸送量的應用程式通訊。只保留給 MPI 流量。


>[AZURE.IMPORTANT]在執行 Linux 的 A8 和 A9 VM 上，目前只能透過在 SUSE Linux Enterprise Server 12 上使用 Azure Linux RDMA 和 Intel MPI Library 5 (SLES 12) 的應用程式來啟用對 RDMA 網路的存取權。在執行 Windows Server 的 A8 和 A9 執行個體上，目前只能經由使用 Microsoft Network Direct 介面的應用程式來啟用對 RDMA 網路的存取權。如需其他需求，請參閱本文中的[存取 RDMA 網路](#access-the-rdma-network)。

A10 和 A11 執行個體具有可連線至 Azure 服務和網際網路的單一 10-Gbps 乙太網路介面卡。

## Azure 訂用帳戶的考量

* **Azure 帳戶** – 如果您想要部署的不只是少數的密集運算執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。您也可以使用您的 MSDN 訂用帳戶。請參閱 [MSDN 訂用帳戶的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。如果您使用 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)，您只能使用有限數目的 Azure 運算核心。

* **核心配額** – 您可能需要從您的 Azure 訂用帳戶中增加核心配額，預設的 20 個核心不足以處理許多具有 8 核心或 16 核心執行個體的案例。若為初始測試，您可能要考慮要求將配額增加到 100 個核心。若要這樣做，請開啟[了解 Azure 限制及增加](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)中所示的免費支援票證 。

    >[AZURE.NOTE]Azure 配額為信用額度，而不是容量保證。您只需支付您使用的核心。

* **同質群組** – 針對大多數的新部署，目前不建議使用同質群組。不過，請注意，如果您正在使用的同質群組包含大小不同於 A8–A11 的執行個體，則您無法針對 A8–A11 執行個體使用它，反之亦然。

* **虛擬網路** – 使用運算密集型執行個體時並不需要 Azure 虛擬網路。不過，您可能需要至少一個雲端型 Azure 虛擬網路來處理許多 IaaS 案例，或者如果您需要存取內部部署資源 (例如，應用程式授權伺服器)，則需要站對站連線。您必須先建立新的 (區域) 虛擬網路，再部署執行個體。不支援將 A8、A9、A10 或 A11 VM 新增至同質群組中的虛擬網路。如需詳細資訊，請參閱[如何建立虛擬網路 (VNet)](../virtual-network/virtual-networks-create-vnet.md) 和[使用站對站 VPN 連線來設定虛擬網路](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

* **雲端服務或可用性集合** – 若要透過 RDMA 網路進行連線，就必須將 A8 和 A9 執行個體部署在相同的雲端服務 (適用於在 Azure 服務管理中含有 Linux VM 或 Windows VM 的 IaaS 案例，或是含有 Windows Server 的 PaaS 案例) 或相同的可用性集合 (適用於 Azure 資源管理員中的 Linux 或 Windows VM) 中。

## 使用 HPC Pack 的考量

### HPC Pack 和 Linux 的考量

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 針對 Windows 所提供的免費 HPC 叢集和工作管理解決方案。從 HPC Pack 2012 R2 Update 2 開始，HPC Pack 就支援在部署於 Azure VM 中的計算節點上執行數個 Linux 散發套件，其是透過 Windows Server 前端節點來管理。使用最新版本的 HPC Pack，您可以部署 Linux 架構的叢集，能夠執行 MPI 應用程式來存取 Azure 中的 RDMA 網路。如需詳細資訊，請參閱 [開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點](virtual-machines-linux-cluster-hpcpack.md)。

### HPC Pack 和 Windows 的考量

將 A8、A9、A10 和 A11 執行個體與 Windows Server 搭配使用時，並不需要 HPC Pack，但它是在 Azure 中建立 Windows HPC Server 叢集的建議工具。如果是 A8 和 A9 執行個體，HPC Pack 會是執行可存取 Azure 中 RDMA 網路之 Windows MPI 應用程式最有效的方式。HPC Pack 包含 Windows 訊息傳遞介面之 Microsoft 實作的執行階段環境。

如需在 Windows 伺服器上使用大量運算執行個體和 HPC Pack 的詳細資訊和檢查清單，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-hpcpack-cluster-rdma.md)。

## 存取 RDMA 網路

### 從 Linux A8 和 A9 VM 進行存取

在單一雲端服務或可用性設定組中，A8 和 A9 執行個體可以為了執行 MPI 應用程式 (使用 Linux RDMA 驅動程式在執行個體之間進行通訊)，存取 Azure 中的 RDMA 網路。此時，僅 [Intel MPI Library 5](https://software.intel.com/zh-TW/intel-mpi-library/) 支援 Azure Linux RDMA。

>[AZURE.NOTE]目前無法透過驅動程式延伸模組來安裝 Azure Linux RDMA 驅動程式。只能透過使用來自 Azure Marketplace 之已啟用 RDMA 的 SLES 12 映像來使用它們。

請參閱下表，以了解要在計算節點 (IaaS) 叢集中存取 RDMA 網路之 Linux MPI 應用程式的必要條件。如需部署選項和組態步驟，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)。

必要條件 | 虛擬機器 (IaaS)
------------ | -------------
作業系統 | 來自 Azure Marketplace 的 SLES 12 HPC 映像
MPI | Intel MPI Library 5

### 從 Windows A8 和 A9 執行個體進行存取

在單一雲端服務或可用性集合中，A8 和 A9 執行個體可為了執行 MPI 應用程式 (使用 Microsoft Network Direct 介面在執行個體之間進行通訊)，存取 Azure 中的 RDMA 網路。A10 和 A11 執行個體不包含 RDMA 網路的存取權。

請參閱下表取得 MPI 應用程式的必要條件，以存取虛擬機器 (IaaS) 中的 RDMA 網路和 A8 或 A9 執行個體的雲端服務 (PaaS) 部署。如需一般的部署案例，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-hpcpack-cluster-rdma.md)。


必要條件 | 虛擬機器 (IaaS) | 雲端服務 (PaaS)
---------- | ------------ | -------------
作業系統 | Windows Server 2012 R2 或 Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 客體 OS 系統系列
MPI | MS-MPI 2012 R2 或更新版本為獨立安裝或透過 HPC Pack 2012 R2 或更新版本安裝<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 或更新版本會透過 HPC Pack 2012 R2 或更新版本安裝<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]針對 IaaS 案例，必須將 HpcVmDrivers 延伸模組加入 VM 中，才能安裝 RDMA 連線所需的 Windows 網路裝置驅動程式。取決於您的部署方法，HpcVmDrivers 延伸模組可能會自動加入到 A8 或 A9 VM 中，或者您可能需要自行予以加入。若要加入延伸模組，請參閱 [管理 VM 延伸模組](virtual-machines-extensions-install.md)。


## 其他應該知道的事項

* A8–A11 VM 大小僅適用於標準價格層級。

* 您無法將現有 Azure VM 的大小調整為 A8、A9、A10 或 A11 的大小。

* 目前無法使用屬於現有同質群組的雲端服務來部署 A8、A9、A10 和 A11 執行個體。同樣地，具有包含 A8、A9、A10 和 A11 執行個體之雲端服務的同質群組不能用來部署其他執行個體大小。如果您嘗試這些部署，您會看到類似 `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.` 的錯誤訊息。

* Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/12。如果您打算在 Azure 虛擬網路中已部署的 A8 和 A9 執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。

## 後續步驟

* 如需有關 A8、A9、A10 和 A11 執行個體的可用性和價格等詳細資料，請參閱[虛擬機器價格](http://azure.microsoft.com/pricing/details/virtual-machines/)和[雲端服務價格](http://azure.microsoft.com/pricing/details/cloud-services/)。
* 若要使用 A8 和 A9 執行個體來部署和設定 Linux 叢集以存取 Azure RDMA 網路，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)。
* 若要開始在 Windows 上部署和使用 A8 和 A9 執行個體及 HPC Pack，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-hpcpack-cluster-rdma.md)。

<!---HONumber=Oct15_HO1-->