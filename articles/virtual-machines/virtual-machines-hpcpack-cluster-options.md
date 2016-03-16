<properties
 pageTitle="雲端 HPC Pack 叢集選項 |Microsoft Azure"
 description="了解在 Azure 雲端使用 Microsoft HPC Pack 建立及管理高效能運算 (HPC) 叢集的選項。"
 services="virtual-machines,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# 使用 Microsoft HPC Pack 在 Azure 中建立及管理高效能運算 (HPC) 叢集的選項

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


利用 Microsoft HPC Pack 與 Azure 的運算和基礎結構服務，建立及管理雲端高效能運算 (HPC) 叢集。[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是建置在 Microsoft Azure 和 Windows Server 技術上的 Microsoft 免費 HPC 解決方案，並支援 Windows 和 Linux HPC 工作負載。雲端 HPC Pack 叢集為叢集系統管理員或獨立軟體廠商 (ISV) 提供富彈性的可擴充平台，能在執行大量運算應用程式的同時，降低內部部署運算叢集基礎結構的投資。


## 在 Azure VM 中執行 HPC Pack 叢集

### Azure 範本

* (Marketplace) [HPC Pack cluster for Windows workloads (適用於 Windows 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [HPC Pack cluster for Excel workloads (適用於 Excel 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Marketplace) [HPC Pack cluster for Linux workloads (適用於 Linux 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (快速入門) [Create an HPC cluster (建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* (快速入門) [Create an HPC cluster with Linux compute nodes (使用 Linux 計算節點建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* (快速入門) [Create an HPC cluster with custom compute node image (使用自訂的計算節點映像建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Azure VM 映像

* [Windows Server 2012 R2 上的 HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Windows Server 2012 R2 上的 HPC Pack 運算節點](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Windows Server 2012 R2 上含 Excel 的 HPC Pack 運算節點](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### PowerShell 部署指令碼

* [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)

### 教學課程

* [教學課程：開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點](virtual-machines-linux-cluster-hpcpack.md)

* [教學課程：在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](virtual-machines-linux-cluster-hpcpack-namd.md)

* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [教學課程：開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載](virtual-machines-excel-cluster-hpcpack.md)



### 使用 Azure 入口網站手動部署

* [在 Azure VM 中設定 HPC Pack 叢集的前端節點](virtual-machines-hpcpack-cluster-headnode.md)

### 叢集管理

* [在 Azure 中管理 HPC Pack 叢集的運算節點](virtual-machines-hpcpack-cluster-node-manage.md)


* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [將工作送出至 Azure 的 HPC Pack 叢集](virtual-machines-hpcpack-cluster-submit-jobs.md)


## 將背景工作角色節點加入 HPC Pack 叢集


* [使用 HPC Pack 將量擴大到 Azure 背景工作執行個體](https://technet.microsoft.com/library/gg481749.aspx)

* [教學課程：在 Azure 中使用 HPC Pack 設定混合式叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [將 Azure「高載」節點加入 Azure 中的 HPC Pack 前端節點](virtual-machines-hpcpack-cluster-node-burst.md)

* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

## 與 Azure Batch 整合 

* [使用 HPC Pack 將量擴大到 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)

## 建立 MPI 工作負載的 RDMA 叢集

* [使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-hpcpack-cluster-rdma.md)

* [教學課程：在 Azure 中的 Linux RDMA 叢集以 Microsoft HPC Pack 執行 OpenFOAM](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)

<!---HONumber=AcomDC_0211_2016-->