<properties
 pageTitle="雲端 Windows HPC Pack 叢集選項 |Microsoft Azure"
 description="了解在 Azure 雲端使用 Microsoft HPC Pack 建立及管理 Windows 高效能運算 (HPC) 叢集的選項"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# 使用 Microsoft HPC Pack 在 Azure 中建立及管理 Windows 高效能運算 (HPC) 叢集的選項

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

## 在 Azure VM 中執行 HPC Pack 叢集

### Azure 範本

* (Marketplace) [HPC Pack cluster for Windows workloads (適用於 Windows 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [HPC Pack cluster for Excel workloads (適用於 Excel 工作負載的 HPC Pack 叢集)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (快速入門) [Create an HPC cluster (建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* (快速入門) [Create an HPC cluster with custom compute node image (使用自訂的計算節點映像建立 HPC 叢集)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Azure VM 映像

* [Windows Server 2012 R2 上的 HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Windows Server 2012 R2 上的 HPC Pack 運算節點](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Windows Server 2012 R2 上含 Excel 的 HPC Pack 運算節點](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### PowerShell 部署指令碼

* [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### 教學課程

* [教學課程：開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載](virtual-machines-windows-excel-cluster-hpcpack.md)



### 使用 Azure 入口網站手動部署

* [在 Azure VM 中設定 HPC Pack 叢集的前端節點](virtual-machines-windows-hpcpack-cluster-headnode.md)

### 叢集管理

* [在 Azure 中管理 HPC Pack 叢集的運算節點](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)


* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [將工作送出至 Azure 的 HPC Pack 叢集](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## 將背景工作角色節點加入 HPC Pack 叢集


* [使用 HPC Pack 將量擴大到 Azure 背景工作執行個體](https://technet.microsoft.com/library/gg481749.aspx)

* [教學課程：在 Azure 中使用 HPC Pack 設定混合式叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [將 Azure「高載」節點加入 Azure 中的 HPC Pack 前端節點](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

## 與 Azure Batch 整合 

* [使用 HPC Pack 將量擴大到 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)

## 建立 MPI 工作負載的 RDMA 叢集

* [使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0323_2016-->