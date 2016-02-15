<properties
   pageTitle="雲端中的 Batch 和 HPC 工作負載的資源 | Microsoft Azure"
   description="列出技術資源，用以協助您在 Azure 中執行您的大規模平行、批次和高效能運算 (HPC) 工作負載。"
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/26/2016"
   ms.author="danlep"/>

# Azure 中的大量運算：批次和高效能運算 (HPC) 的技術資源
這是技術資源的指南，將幫助您在 Azure 中執行您的大規模平行、批次和 HPC 工作負載。使用各種 Azure 服務擴充您現有的批次或 HPC 工作負載至 Azure 雲端，或在 Azure 中建置新的大量計算解決方案。

## 方案選項

了解在 Azure 中的大量計算選項，並為您的工作負載和商務需求選擇正確的方法。

* [批次和 HPC 解決方案](batch-hpc-solutions.md)

* [影片：Azure 和 HPC 在雲端的大量運算](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure 批次

[批次](https://azure.microsoft.com/services/batch/)是一種平台服務，可以輕鬆地啟用您應用程式的雲端功能，並無需設定和管理叢集及工作排程器即可執行工作。使用 SDK 透過各種不同語言、接移資料至 Azure和建立工作執行管線，將用戶端應用程式與 Azure 批次整合。

* [說明文件](https://azure.microsoft.com/documentation/services/batch/)

* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 和 [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 參考

* [教學課程：開始使用適用於 .NET 的 Azure 批次程式庫](batch-dotnet-get-started.md)

* [批次論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)

* [批次影片](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## HPC 叢集解決方案

部署或擴充您現有的 Windows 或 Linux HPC 叢集至 Azure，以執行您的計算密集工作負載。

### Microsoft HPC Pack

HPC Pack 是建置在 Microsoft Azure 和 Windows Server 技術上的 Microsoft 免費 HPC 解決方案，可執行 Windows 和 Linux HPC 工作負載。

* [下載 HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)

* [說明文件](https://technet.microsoft.com/library/jj899572.aspx)


* [在 Azure 中使用 Microsoft HPC Pack 的 HPC 叢集選項](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [使用 HPC Pack 將量擴大到 Azure 工作執行個體](https://technet.microsoft.com/library/gg481749.aspx)

* [使用 HPC Pack 將量擴大到 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)


* [Windows HPC 論壇](https://social.microsoft.com/Forums/en-us/home?category=windowshpc)

### Linux 與 OSS 叢集解決方案

透過開放原始碼工具使用 Azure 快速入門範本部署 Linux HPC 叢集。

* [加速 SLURM 叢集](https://azure.microsoft.com/documentation/templates/slurm/)和[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [加速扭力叢集](https://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) 是為了在 Windows 平台上開發及執行平行應用程式，Microsoft 實作的訊息傳遞介面標準。


* [下載 MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [MS-MPI 參考資料](https://msdn.microsoft.com/library/dn473458.aspx)

* [MPI 論壇](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)


## 計算密集型執行個體

Azure 提供[各種大小](../virtual-machines/virtual-machines-size-specs.md)，包括計算密集的 [A8 和 A9](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) 執行個體，能夠連接到後端 RDMA 網路，以執行您的 Linux 和 Windows HPC 工作負載。

* [設定 Linux RDMA 叢集以執行 MPI 應用程式](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [使用 Microsoft HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## 架構藍圖

* [使用 Azure Batch 和 Data Factory 的 HPC 和資料協調](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) 和[文章](../data-factory/data-factory-data-processing-using-batch.md)

## 範例和示範

* [Azure 的批次程式碼範例](https://github.com/Azure/azure-batch-samples)

## 相關的 Azure 服務

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

* [機器學習服務](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [雲端服務](https://azure.microsoft.com/documentation/services/cloud-services/)

* [媒體服務](https://azure.microsoft.com/documentation/services/media-services/)



## 後續步驟

* 最新宣告請參閱 [Microsoft HPC 和批次的小組部落格](http://blogs.technet.com/b/windowshpc/)以及[Azure 部落格](https://azure.microsoft.com/blog/tag/hpc/)。
* 另請參閱[批次的新功能](https://azure.microsoft.com/updates/?service=batch)或訂閱 [RSS 摘要](https://azure.microsoft.com/updates/feed/?service=batch)。

<!---HONumber=AcomDC_0204_2016-->