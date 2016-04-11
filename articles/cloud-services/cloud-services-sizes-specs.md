<properties
 pageTitle="雲端服務的大小"
 description="列出 Azure 雲端服務 Web 和背景工作角色不同的大小。"
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="03/25/2016"
 ms.author="adegeo"/>

# 雲端服務的大小

本主題描述雲端服務角色執行個體 (Web 角色和背景工作角色)的可用大小和選項。同時也提供計劃使用這些資源時，需注意的部署考量。

Azure 虛擬機器和雲端服務是 Azure 所提供數種計算資源類型的其中兩種。如需說明，請參閱＜[計算 Azure 所提供的裝載選項](fundamentals-application-models.md)＞。

> [AZURE.NOTE]若要查看相關的 Azure 限制，請參閱 ＜[Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)＞

## 用於 Web 和背景工作角色執行個體的大小

下列考量可協助您決定大小：

* D 系列 VM 執行個體是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。  

* Dv2 系列是原始 D 系列的延續，擁有更強大的 CPU。Dv2 系列 CPU 比 D 系列 CPU 快約 35%。它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

* 因系統需求，Web 角色和背景工作角色比 Azure 虛擬機器需要更多的暫存磁碟空間。系統檔案保留 4 GB 的空間供 Windows 分頁檔和 2 GB 的空間供 Windows 傾印檔案。

* 作業系統磁碟包含 Windows 客體 OS，並且包含 Program Files 資料夾 (包括透過啟動工作完成的安裝，除非您指定另一個磁碟)、登錄變更、System32 資料夾和 .NET Framework。

* **暫存磁碟**包含 Azure 記錄檔和設定檔、Azure 診斷 (其中包括 IIS 記錄檔)，以及您所定義的任何本機儲存體資源。

* **應用程式磁碟**是您解壓縮 .cspkg 的地方，並包含您的網站、二進位檔、角色主機處理序、啟動工作、web.config 等等。

* A8/A10 和 A9/A11 虛擬機器大小有相同的容量。A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，其會連線到遠端直接記憶體存取 (RDMA) 網路，以利在虛擬機器之間進行快速通訊。A8 和 A9 執行個體是專為執行期間，節點之間需要常數和低延遲通訊的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

    >[AZURE.NOTE] 如果您正在考慮調整 A8 到 A11 的大小，請閱讀[此](..\virtual-machines\virtual-machines-a8-a9-a10-a11-specs.md)資訊。

>[AZURE.NOTE] 所有機器大小會提供**應用程式磁碟**，它會儲存您的雲端服務封裝的所有檔案，大小大約 1.5 GB。

請確定您檢閱每個雲端服務大小的[價格](pricing/details/cloud-services/)。

## 一般用途

適用於網路、中小型資料庫及其他日常應用程式。

>[AZURE.NOTE] 儲存體容量是使用 1024^3 位元組當作 GB 的度量單位來表示。這有時稱為 gibibyte 或基底 2 定義。比較使用不同基底系統的大小時，請記住，基底 2 的大小可能會顯示為小於基底 10，但是對於任何特定的大小 (例如 1 GB)，基底 2 的系統可提供比基底 10 系統更大的容量，因為 1024^3 大於 1000^3。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| 特小型 | 1 | 0\.75 GB | 19 GB |
| 小型 | 1 | 1\.75 GB | 224 GB |
| 中型 | 2 | 3\.5 GB | 489 GB |
| 大型 | 4 | 7 GB | 999 GB |
| 特大型 | 8 | 14 GB | 2,039 GB |

>[AZURE.NOTE] **ExtraSmall** 到 **ExtraLarge** 也可以分別命名為 **A0-A4**。

## 記憶體高用量

適用於大型資料庫、SharePoint 伺服器陣列及高輸送量應用程式。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| A5 | 2 | 14 GB | 489 GB |
| A6 | 4 | 28 GB | 999 GB |
| A7 | 8 | 56 GB | 2,039 GB |

## 經過優化處理的網路，支援 InfiniBand

可在選取的資料中心使用。A8 和 A9 虛擬機器搭載 [Intel® Xeon® E5 處理器](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html)。新增搭配遠端直接記憶體存取 (RDMA) 技術的 32Gbit/秒 **InfiniBand** 網路。適用於訊息傳遞介面 (MPI) 應用程式、高效能叢集、模型製作與模擬、視訊編碼及其他大量計算和網路高用量等案例。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| A8 | 8 | 56 GB | 382 GB |
| A9 | 16 | 112 GB | 382 GB |

## 大量計算

可在選取的資料中心使用。A10 和 A11 虛擬機器搭載 [Intel® Xeon® E5 處理器](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html)。適用於高效能叢集、模型製作與模擬、視訊編碼，以及其他需要大量計算及大量使用網路等案例。與 A8 和 A9 執行個體設定相似，沒有 InfiniBand 網路和 RDMA 技術。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| A10 | 8 | 56 GB | 382 GB |
| A11 | 16 | 112 GB | 382 GB |

## D 系列︰最佳化計算

D 系列虛擬機器的特色包括固態磁碟機 (SSD) 和比 A 系列還要快的處理器 (快上 60%)，而且也適用於 Azure 雲端服務中的 Web 角色或背景工作角色。應用程式若需要更快的 CPU、較佳的本機磁碟效能或更高的記憶體，則此系列會是理想的選擇。

## 一般用途 (D)

適用於網路、中小型資料庫及其他日常應用程式。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| 標準\_D1 | 1 | 3\.5 GB | 50 GB |
| 標準\_D2 | 2 | 7 GB | 100 GB |
| 標準\_D3 | 4 | 14 GB | 200 GB |
| 標準\_D4 | 8 | 28 GB | 400 GB |

## 記憶體高用量 (D)

適用於大型資料庫、SharePoint 伺服器陣列及高輸送量應用程式。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| 標準\_D11 | 2 | 14 GB | 100 GB |
| 標準\_D12 | 4 | 28 GB | 200 GB |
| 標準\_D13 | 8 | 56 GB | 400 GB |
| 標準\_D14 | 16 | 112 GB | 800 GB |

## Dv2 系列︰最佳化計算

Dv2 系列執行個體是第二代 D 系列執行個體，可做為虛擬機器或雲端服務使用。Dv2 系列執行個體會搭載更強大的 CPU，平均比 D 系列執行個體快上 35%，並與 D 系列搭載相同的記憶體及磁碟組態。Dv2 系列執行個體是以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，且搭配 Intel 渦輪加速技術 2.0 可達 3.2 GHz。Dv2 系列及 D 系列適用於需要更快的 CPU、更佳的本機磁碟效能、或是更高記憶體的應用程式，並替許多企業級應用程式帶來強大的組合。

## 一般用途 (Dv2)

適用於網路、中小型資料庫及其他日常應用程式。

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| Standard\_D1\_v2 | 1 | 3\.5 GB | 50 GB |
| Standard\_D2\_v2 | 2 | 7 GB | 100 GB |
| Standard\_D3\_v2 | 4 | 14 GB | 200 GB |
| Standard\_D4\_v2 | 8 | 28 GB | 400 GB |
| Standard\_D5\_v2 | 16 | 56 GB | 800 GB |

## 記憶體高用量 (Dv2)

適用於大型資料庫、SharePoint 伺服器陣列及高輸送量應用程式

| 大小 (id) | 核心 | RAM | 磁碟大小總計 |
| --------------- | :-------: | ------: | ------: |
| Standard\_D11\_v2 | 2 | 14 GB | 100 GB |
| Standard\_D12\_v2 | 4 | 28 GB | 200 GB |
| Standard\_D13\_v2 | 8 | 56 GB | 400 GB |
| Standard\_D14\_v2 | 16 | 112 GB | 800 GB |

## 設定雲端服務大小

您可以指定角色執行個體的虛擬機器大小，作為[服務定義檔](cloud-services-model-and-package.md#csdef)所描述之服務模型的一部分。角色大小決定 CPU 核心數目、記憶體容量，以及配置給執行中執行個體的本機檔案系統大小。根據您應用程式的資源需求選擇角色大小。

以下是將 Web 角色執行個體的角色大小設定為 [Standard\_D2](一般用途 (D)) 的範例：

```xml
<WebRole name="WebRole1" vmsize="<mark>Standard_D2</mark>">
...
</WebRole>
```

<!---HONumber=AcomDC_0330_2016-->