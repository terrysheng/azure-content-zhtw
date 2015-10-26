<properties
 pageTitle="雲端 HPC Pack 叢集選項 |Microsoft Azure"
 description="了解在 Azure 雲端使用 Microsoft HPC Pack 建立及管理高效能運算 (HPC) 叢集的選項。"
 services="virtual-machines,cloud-services"
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
 ms.workload="big-compute"
 ms.date="10/08/2015"
 ms.author="danlep"/>

# 使用 Microsoft HPC Pack 在 Azure 中建立及管理高效能運算 (HPC) 叢集的選項

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


利用 Microsoft HPC Pack 與 Azure 的運算和基礎結構服務，建立及管理雲端高效能運算 (HPC) 叢集。[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) 是建置在 Microsoft Azure 和 Windows Server 技術上的 Microsoft 免費 HPC 解決方案，並支援 Windows 和 Linux HPC 工作負載。雲端 HPC Pack 叢集為叢集系統管理員或獨立軟體廠商 (ISV) 提供富彈性的可擴充平台，能在執行大量運算應用程式的同時，降低內部部署運算叢集基礎結構的投資。

<!-- Take advantage of automated tools for HPC Pack cluster deployment in Azure VMs, including an HPC Pack image that can be used with either Azure quickstart templates or Azure PowerShell scripts, or deploy your cluster manually in the Azure portal.-->


## 在 Azure VM 中執行 HPC Pack 叢集


### Azure VM 映像

* [Windows Server 2012 R2 上的 HPC Pack](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Windows Server 2012 R2 上的 HPC Pack 運算節點](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Windows Server 2012 R2 上含 Excel 的 HPC Pack 運算節點](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### Azure 快速入門範本

* [建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* [建立 Linux 計算節點的 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [使用自訂的運算節點映像建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### PowerShell 部署指令碼

* [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)

### 教學課程

* [教學課程：開始在 Azure 中的 HPC Pack 叢集使用 Linux 運算節點](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

* [教學課程：在 Azure 中的 Linux 計算節點以 Microsoft HPC Pack 執行 NAMD](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

* [教學課程：開始使用 Azure 中的 HPC Pack 叢集執行 Excel 和 SOA 工作負載](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)



### 使用 Azure 入口網站手動部署



* [在 Azure VM 中設定 HPC Pack 叢集的前端節點](virtual-machines-hpcpack-cluster-headnode.md)

### 叢集管理

* [在 Azure 中管理 HPC Pack 叢集的運算節點](virtual-machines-hpcpack-cluster-node-manage.md)

* [將 Azure「高載」節點加入 Azure 中的 HPC Pack 前端節點](virtual-machines-hpcpack-cluster-node-burst.md)

* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [將工作送出至 Azure 的 HPC Pack 叢集](virtual-machines-hpcpack-cluster-submit-jobs.md)



## 將背景工作角色節點加入 HPC Pack 叢集


* [使用 HPC Pack 將量擴大到 Azure](https://technet.microsoft.com/library/gg481749.aspx)

* [教學課程：在 Azure 中使用 HPC Pack 設定混合式叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [將 Azure「高載」節點加入 Azure 中的 HPC Pack 前端節點](virtual-machines-hpcpack-cluster-node-burst.md)

* [增加及縮減 HPC Pack 叢集中的 Azure 運算資源](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)


## 建立 MPI 工作負載的 RDMA 叢集

* [使用 HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](virtual-machines-windows-hpcpack-cluster-rdma.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=Oct15_HO3-->