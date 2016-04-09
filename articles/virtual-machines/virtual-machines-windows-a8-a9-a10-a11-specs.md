<properties
 pageTitle="關於 A8 - A11 執行個體和 Windows | Microsoft Azure"
 description="取得使用 Windows VM 和雲端服務適用的 Azure A8、A9、A10 和 A11 密集計算大小的背景資訊和考量"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# 關於使用 A8、A9、A10 和 A11 計算密集型執行個體搭配 Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## 存取 RDMA 網路

在單一雲端服務或可用性設定組中的 A8 和 A9 執行個體可以存取 Azure 中的 RDMA 網路，來執行會使用 Microsoft Network Direct 介面在執行個體之間通訊的 Windows MPI 應用程式。

請參閱下表取得 MPI 應用程式的必要條件，以存取虛擬機器 (IaaS) 中的 RDMA 網路和 A8 或 A9 執行個體的雲端服務 (PaaS) 部署。如需一般的部署案例，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。


必要條件 | 虛擬機器 (IaaS) | 雲端服務 (PaaS)
---------- | ------------ | -------------
作業系統 | Windows Server 2012 R2 或 Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 客體 OS 系統系列
MPI | MS-MPI 2012 R2 或更新版本為獨立安裝或透過 HPC Pack 2012 R2 或更新版本安裝<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 或更新版本會透過 HPC Pack 2012 R2 或更新版本安裝<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]針對 IaaS 案例，必須將 HpcVmDrivers 延伸模組加入 VM 中，才能安裝 RDMA 連線所需的 Windows 網路裝置驅動程式。取決於您的部署方法，HpcVmDrivers 延伸模組可能會自動加入到 A8 或 A9 VM 中，或者您可能需要自行予以加入。若要加入延伸模組，請參閱 [管理 VM 延伸模組](virtual-machines-windows-classic-manage-extensions.md)。

## HPC Pack 和 Windows 的考量

將 A8、A9、A10 和 A11 執行個體與 Windows Server 搭配使用時，並不需要 HPC Pack，但它是在 Azure 中執行會存取 RDMA 網路之 Windows MPI 應用程式的建議工具。HPC Pack 包含 Windows 訊息傳遞介面 (MS-MPI) 之 Microsoft 實作的執行階段環境。

如需在 Windows 伺服器上使用大量運算執行個體和 HPC Pack 的詳細資訊和檢查清單，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。

## 後續步驟

* 如需 A8、A9、A10 和 A11 執行個體的可用性和價格詳細資訊，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)和[雲端服務定價](https://azure.microsoft.com/pricing/details/cloud-services/)。

* 若要開始在 Windows 上部署和使用 A8 和 A9 執行個體及 HPC Pack，請參閱[使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。

* 如需以 Azure Batch 使用 A8 和 A9 執行個體執行 MPI 應用程式的資訊，請參閱[在 Azure Batch 中使用多重執行個體工作來執行訊息傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。

<!---HONumber=AcomDC_0323_2016-->