<properties
 pageTitle="關於 A8 - A11 執行個體和 Linux | Microsoft Azure"
 description="取得針對 Linux VM 使用 Azure A8、A9、A10 和 A11 密集計算大小的背景資訊和考量。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# 關於使用 A8、A9、A10 和 A11 計算密集型執行個體搭配 Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## 存取 RDMA 網路

在單一雲端服務或可用性設定組中的 A8 和 A9 執行個體，可以存取 Azure 中的 RDMA 網路來執行 Linux MPI 應用程式。此時，Azure Linux RDMA 僅支援 [Intel MPI Library 5](https://software.intel.com/zh-TW/intel-mpi-library/)。

>[AZURE.NOTE] 目前無法透過驅動程式延伸模組來安裝 Azure Linux RDMA 驅動程式。只能透過使用來自 Azure Marketplace 之已啟用 RDMA 的 SLES 12 映像來使用它們。

下表概述要讓 Linux MPI 應用程式存取計算節點 (IaaS) 叢集中 RDMA 網路的必要條件。如需部署選項和組態步驟，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

必要條件 | 虛擬機器 (IaaS)
------------ | -------------
作業系統 | 來自 Azure Marketplace 的 SLES 12 HPC 映像
MPI | Intel MPI Library 5

## HPC Pack 和 Linux 的考量

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是 Microsoft 針對 Windows 所提供的免費 HPC 叢集和工作管理解決方案。HPC Pack 2012 R2 的最新版本支援讓數個 Linux 發行版在部署於 Azure VM 中，且由 Windows Server 前端節點管理的計算節點上執行。部署在 A8 或 A9 VM 上並執行支援 MPI 實作的 Linux 計算節點可以執行存取 RDMA 網路的 MPI 應用程式。如需詳細資訊，請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](virtual-machines-linux-classic-hpcpack-cluster.md)。

## 後續步驟

* 如需 A8、A9、A10 和 A11 執行個體的可用性和價格詳細資訊，請參閱[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/)。

* 若要開始在 Linux 上部署和使用 A8 和 A9 執行個體及 RDMA，請參閱[設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。

<!---HONumber=AcomDC_0323_2016-->