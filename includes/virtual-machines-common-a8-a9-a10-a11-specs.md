

這篇文章提供使用 Azure A8、A9、A10 和 A11 執行個體，也稱為*密集運算*執行個體的背景資訊和考量。這些執行個體的主要功能包括：

* **高效能硬體** - 執行這些執行個體的 Azure 資料中心硬體是為了密集運算和密集網路應用程式而設計及最佳化的，包括高效能運算 (HPC) 叢集應用程式、模型和模擬。

* **適用於 MPI 應用程式的 RDMA 網路連線** - 您可以設定 A8 和 A9 執行個體，來透過 Azure 中低延遲、高輸送量的網路來與其他 A8 和 A9 執行個體通訊，而該網路是以遠端直接記憶體存取 (RDMA) 技術為基礎。此功能可以提升某些 Linux 或 Windows 訊息傳遞介面 (MPI) 應用程式的效能。

* **支援 HPC 叢集** - 在 Azure 中的 A8、A9、A10 和 A11 執行個體上部署叢集管理和工作排程軟體，來建立獨立的 HPC 叢集，或在內部部署叢集中新增功能。

>[AZURE.NOTE]A10 和 A11 執行個體具有和 A8 和 A9 執行個體相同的效能最佳化和規格化。不過，不包括 Azure 中 RDMA 網路的存取權。它們是專為節點之間不需要常數和低延遲通訊的 HPC 應用程式設計，也就是參數式或窘迫平行應用程式。


## 規格

### CPU 和記憶體

Azure A8、A9、A10 和 A11 密集運算執行個體的功能為高速、多核心 CPU 以及大量的記憶體，如下表所示。

大小 | CPU | 記憶體
------------- | ----------- | ----------------
A8 和 A10 | Intel Xeon E5-2670<br/>8 核心 @ 2.6 GHz | DDR3-1600 MHz<br/>56 GB
A9 和 A11 | Intel Xeon E5-2670<br/>16 核心 @ 2.6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]其他處理器詳細資料 (包括受支援的指令集延伸模組) 位於 Intel.com 網站。如需 VM 儲存體容量與磁碟詳細資訊，請參閱[虛擬機器的大小](../articles/virtual-machines/virtual-machines-linux-sizes.md)。

### 網路介面卡

A8 和 A9 執行個體具有兩張網路介面卡，可連接到下列兩個後端 Azure 網路。


網路 | 說明
-------- | -----------
10-Gbps 乙太網路 | 連線至 Azure 服務 (例如 Azure 儲存體和 Azure 虛擬網路) 和網際網路。
32-Gbps 後端，具有 RDMA 功能 | 在單一雲端服務或可用性集合內的執行個體之間啟用低延遲、高輸送量的應用程式通訊。只保留給 MPI 流量。


>[AZURE.IMPORTANT]如需 MPI 應用程式用來存取 RDMA 網路的其他需求，請參閱本文的[存取 RDMA 網路](#access-the-rdma-network)一節。

A10 和 A11 執行個體具有可連線至 Azure 服務和網際網路的單一 10-Gbps 乙太網路介面卡。

## 部署考量

* **Azure 帳戶** – 如果您想要部署的不只是少數的密集運算執行個體，請考慮隨用隨付訂用帳戶或其他購買選項。您也可以使用您的 MSDN 訂用帳戶。請參閱 [MSDN 訂用帳戶的 Azure 權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/pricing/free-trial/)，您只能使用有限數目的 Azure 計算核心。

* **核心配額** – 您可能需要增加您 Azure 訂用帳戶的核心配額，預設配額是每個訂用帳戶 20 個核心 (如果您使用傳統部署模型)，或是每個區域 20 個核心 (如果您使用 Azure Resource Manager 部署模型)。如要要求增加配額，請遵循 [Understanding Azure Limits and Increases (了解 Azure 的限制及增加)](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 中所示的步驟來免費開啟支援票證。

    >[AZURE.NOTE]Azure 配額為信用額度，而不是容量保證。您只需支付您使用的核心。

* **虛擬網路** – 使用計算密集型執行個體時，並不需要 Azure [虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)。不過，您可能需要至少一個雲端型 Azure 虛擬網路來處理許多 IaaS 案例，或者如果您需要存取內部部署資源 (例如，應用程式授權伺服器)，則需要站對站連線。您必須建立新的虛擬網路，才能部署執行個體。不支援將 A8、A9、A10 或 A11 VM 新增至同質群組中的虛擬網路。

* **雲端服務或可用性設定組** – 如要透過 RDMA 網路連線，A8 和 A9 執行個體必須部署在相同的雲端服務中 (如果您使用傳統部署模型)，或是相同的可用性設定組中 (如果您使用 Azure Resource Manager 部署模型)。

* **價格** - A8–A11 VM 大小僅適用於標準定價層級。

* **調整大小** - 您無法將大小為 A8–A11 以外的執行個體調整為其中一個密集運算執行個體大小 (A8-11)，而且無法將密集運算執行個體調整為非密集運算的大小。這是因為特殊化硬體和效能最佳化是密集運算執行個體特有的。

* **RDMA 網路位址空間** - Azure 中的 RDMA 網路會保留位址空間 172.16.0.0/12。如果您打算在 Azure 虛擬網路中的 A8 和 A9 執行個體上執行 MPI 應用程式，請確定虛擬網路位址空間不會與 RDMA 網路重疊。

<!---HONumber=AcomDC_0330_2016-->