<properties
 pageTitle="將高載節點新增至 HPC Pack 叢集 | Microsoft Azure"
 description="了解如何將在雲端服務中隨選做為計算資源來執行的背景工作角色執行個體，新增至 Azure 中的 HPC Pack 前端節點。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 將做為運算資源的隨選「高載」節點 (背景工作角色執行個體) 新增至 Azure 中的 HPC Pack 叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


本文將說明如何將隨選做為運算資源的 Azure「高載」節點 (在雲端服務中執行的背景工作角色執行個體) 新增至 Azure 中現有的 HPC Pack 前端節點。這可讓您相應增加 Azure 中的 HPC 叢集的運算能力，且無需維護一組預先設定的運算節點 VM。

![高載節點][burst]

>[AZURE.TIP]如果您使用 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)在 Azure 中建立叢集，則可以在自動化部署中包含 Azure 高載節點。

本文中的步驟可協助您將 Azure 節點快速新增至雲端架構 HPC Pack 前端節點 VM，以測試或證明部署概念。此程序基本上相當於將雲端運算能力新增至內部部署 HPC Pack 叢集的「將量擴大到 Azure」程序。如需教學課程，請參閱[使用 Microsoft HPC Pack 設定混合式計算叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。如需生產部署的詳細指引和考量，請參閱[使用 Microsoft HPC Pack 將量擴大到 Azure](https://technet.microsoft.com/library/gg481749.aspx)。

如果您要使用 A8 或 A9 計算密集型執行個體大小，請參閱[關於 A8、A9、A10 和 A11 計算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)。

## 必要條件

* **在 Azure VM 中部署的 HPC Pack 前端節點** - 請參閱[在 Azure VM 中部署 HPC Pack 前端節點](virtual-machines-hpcpack-cluster-headnode.md)，以了解在傳統 (服務管理) 部署模型中建立叢集前端節點的步驟。

* **Azure 訂用帳戶** - 若要新增 Azure 節點，您可以選擇用來部署前端節點 VM 的同一個訂用帳戶，或選擇一或多個不同的訂用帳戶。

* **核心配額** - 您可能需要增加核心的配額，特別是當您選擇部署數個具有多核心大小的 Azure 節點時。若要增加配額，請[開立線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (免費)。

## 步驟 1：建立雲端服務和儲存體帳戶以新增 Azure 節點。

使用 Azure 傳統入口網站或對等工具，設定下列您在部署 Azure 節點時所需的項目：

* 新的 Azure 雲端服務
* 新的 Azure 儲存體帳戶

>[AZURE.NOTE]請勿重複使用您的訂用帳戶中現有的雲端服務。也不要將個別的自訂雲端服務套件部署至此雲端服務。HPC Pack 會在您啟動 (佈建) Azure 節點時自動部署雲端服務套件。

**考量**

* 您打算建立的每個 Azure 節點範本設定個別的雲端服務。不過，您可以將同一個儲存體帳戶用於多個節點範本。

* 您通常應將部署的雲端服務與儲存體帳戶放置在相同區域中。




## 步驟 2：設定 Azure 管理憑證

若要將 Azure 節點新增運算資源，您在前端節點上必須要有管理憑證，且必須將對應的憑證上傳至用於部署的 Azure 訂用帳戶。

針對這個案例，您可以選擇 HPC Pack 在前端節點上自動安裝及設定的**預設 HPC Azure 管理憑證**。此憑證可用於測試及概念證明部署。若要使用此憑證，請直接將檔案 C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer 從前端節點 VM 上傳至訂用帳戶。

如需設定管理憑證的其他選項，請參閱[為 Azure 高載部署設定 Azure 管理憑證的案例](http://technet.microsoft.com/library/gg481759.aspx)。

## 步驟 3：將 Azure 節點部署至叢集



在此案例中新增及啟動 Azure 節點的步驟，通常與用於內部部署前端節點的步驟相同。如需詳細資訊，請參閱 [使用 Microsoft HPC Pack 部署 Azure 節點的步驟](https://technet.microsoft.com/library/gg481758(v=ws.10).aspx) 中的下列小節：

* 建立 Azure 節點範本

* 將 Azure 節點新增至 Windows HPC 叢集

* 啟動 (佈建) Azure 節點

節點新增並啟動之後，即可供您用來執行叢集工作。

如果您在部署 Azure 節點時遇到問題，請參閱 [使用 Microsoft HPC Pack 部署 Azure 節點時的疑難排解](http://technet.microsoft.com/library/jj159097(v=ws.10).aspx)。

## 後續步驟

* 如果您需要能根據叢集上目前的工作和任務的工作負載自動增加和縮減 Azure 運算資源的方法，請參閱[在 HPC Pack 叢集中增加和縮減 Azure 運算資源](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)。

<!--Image references-->
[burst]: ./media/virtual-machines-hpcpack-cluster-node-burst/burst.png

<!---HONumber=AcomDC_1210_2015-->