<properties
 pageTitle="虛擬機器大小 | Microsoft Azure"
 description="列出虛擬機器的不同大小及其容量。"
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="01/05/2016"
 ms.author="cynthn"/>

# 虛擬機器的大小

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 概觀

本文說明以虛擬機器為基礎之計算資源的可用大小和選項，而您可以使用這類資源來執行應用程式和工作負載。同時也提供當您規劃使用這些資源時所需注意的部署考量。如需各式大小的定價，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)。

若要查看 Azure VM 的一般限制，請參閱 ＜[Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)＞。

標準大小包含幾個系列：A、D、DS、G 與 GS。這些大小的一些考量事項包括：

*   D 系列 VM 是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。

*   Dv2 系列是原始 D 系列的延續，擁有更強大的 CPU。Dv2 系列 CPU 比 D 系列 CPU 快約 35%。它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

*   G 系列 VM 提供最大的記憶體，並且是在具有 Intel Xeon E5 V3 系列處理器的主機上執行。

*   DS 系列和 GS 系列 VM 可以使用進階儲存體，針對使用大量 I/O 的工作負載提供高效能、低延遲的儲存體。這些 VM 會使用固態硬碟 (SSD) 來裝載虛擬機器的磁碟，還可提供本機 SSD 磁碟快取。僅特定地區可用進階儲存體。如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md)。

*   A 系列 VM 可以部署在各種不同的硬體類型和處理器上。根據硬體節流大小，為執行中的執行個體提供一致的處理器效能，不論硬體部署的位置。若要判斷此大小部署所在的實體硬體，請從虛擬機器內查詢虛擬硬體。

*   A0 大小是在實體硬體上過度訂閱。僅針對這個特定大小，其他客戶部署可能會影響您正在執行的工作負載的效能。以下概述的相對效能為預期的基準，受限於近似變化性的 15%。

虛擬機器的大小會影響定價。大小也會影響虛擬機器的處理、記憶體和儲存體容量。儲存體成本會分別根據儲存體帳戶中使用的頁面來計算。如需詳細資訊，請參閱[虛擬機器定價詳細資料](https://azure.microsoft.com/pricing/details/virtual-machines/)和 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。如需有關 VM 儲存體的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](virtual-machines-disks-vhds.md)。

下列考量可協助您決定大小：


* A8-A11 大小也稱為「計算密集型執行個體」。執行這些大小的硬體是針對計算密集型和網路密集型應用程式 (包括高效能運算 (HPC) 叢集應用程式)、模型化及模擬而設計及最佳化的。如需有關使用這些大小的詳細資訊和考量，請參閱[關於 A8、A9、A10 及 A11 計算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)。


*	Dv2、D、G以及對應的 DS/GS 是要求更快速的 CPU、更好的本機磁碟效能，或有更高記憶體需求之應用程式的最佳選擇。它們為許多企業級應用程式提供了強大的組合。

*   Azure 資料中心的某些實體主機可能不支援較大的虛擬機器大小，例如 A5-A11。因此，您可能會在將現有的虛擬機器調整為新的大小、在 2013 年 4 月 16 日之前建立的虛擬網路中建立新的虛擬機器，或將新的虛擬機器加入現有雲端服務時，看到錯誤訊息：「無法設定虛擬機器 <machine name>」或「無法建立虛擬機器 <machine name>」 。請參閱支援論壇上[錯誤：「無法設定虛擬機器」](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows)，以查看每個部署案例的因應措施。


## 效能考量

我們已經建立「Azure 運算單位」(ACU) 概念來提供一種比較各個 Azure SKU 之運算 (CPU) 效能的方法。這將可協助您輕鬆識別哪個 SKU 最可能符合您的效能需求。ACU 目前是以「小型 (Standard\_A1)」VM 為標準 (數值為 100)，而所有其他 SKU 則大致上代表該 SKU 在執行標準基準測試上可以快多少。

>[AZURE.IMPORTANT] ACU 只是一個指導方針。您工作負載的結果可能會有所不同。

<br>

|SKU 系列 |ACU/核心 |
|---|---|
|[Standard\_A0 (超小型)](#standard-tier-a-series) |50 |
|[Standard\_A1-4 (小型 – 大型)](#standard-tier-a-series) |100 |
|[Standard\_A5-7](#standard-tier-a-series) |100 |
|[A8-A11](#standard-tier-a-series) |225 *|
|[D1-14](#standard-tier-d-series) |160 |
|[D1-14v2](#standard-tier-dv2-series) |210 - 250 *|
|[DS1-14](#standard-tier-ds-series) |160 |
|[G1-5](#standard-tier-g-series) |180 - 240 *|
|[GS1-5](#standard-tier-gs-series) |180 - 240 *|


以 * 標示的 ACU 使用了「Intel® 渦輪」技術來增加 CPU 頻率及提升效能。提升的程度會依 VM 大小、工作負載及在相同主機上執行的其他工作負載而有所不同。



## 大小資料表

下表顯示其所提供的大小和容量。

>[AZURE.NOTE] 儲存體容量是使用 1024^3 位元組當作 GB 的度量單位來表示。這有時稱為 gibibyte 或基底 2 定義。比較使用不同基底系統的大小時，請記住，基底 2 的大小可能會顯示為小於基底 10，但是對於任何特定的大小 (例如 1 GB)，基底 2 的系統可提供比基底 10 系統更大的容量，因為 1024^3 大於 1000^3。

<br>



## 標準層級：A 系列

在傳統部署模型中，部分 VM 大小會與 Powershell 和 CLI 中的稍有不同。

* Standard\_A0 是「特小型」 
* Standard\_A1 是「小型」
* Standard\_A2 是「中型」
* Standard\_A3 是「大型」
* Standard\_A4 是「特大型」

<br>

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
|Standard\_A0\\特小型 |1|768 MB|1| 暫存 = 20 GB |1|1x500|
|Standard\_A1\\小型|1|1\.75 GB|1|暫存 = 70 GB |2|2x500|
|Standard\_A2\\中型|2|3\.5 GB|1|暫存 = 135 GB |4|4x500|
|Standard\_A3\\大型|4|7 GB|2|暫存 = 285 GB |8|8x500|
|Standard\_A4\\特大型|8|14 GB|4|暫存 = 605 GB |16|16x500|
|Standard\_A5|2|14 GB|1|暫存 = 135 GB |4|4X500|
|Standard\_A6|4|28 GB|2|暫存 = 285 GB |8|8x500|
|Standard\_A7|8|56 GB|4|暫存 = 605 GB |16|16x500|


## 標準層級：A 系列 - 計算密集型執行個體

注意：如需有關使用這些大小的資訊和考量，請參閱[關於 A8、A9、A10 及 A11 計算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)。

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
|Standard\_A8|8|56 GB|2| 暫存 = 382 GB |16|16x500|
|Standard\_A9|16|112 GB|4| 暫存 = 382 GB |16|16x500|
|Standard\_A10|8|56 GB|2| 暫存 = 382 GB |16|16x500|
|Standard\_A11|16|112 GB|4| 暫存 = 382 GB |16|16x500|

## 標準層級：D 系列

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
|標準\_D1 |1|3\.5 GB|1|暫存 (SSD) =50 GB |2|2x500|
|標準\_D2 |2|7 GB|2|暫存 (SSD) = 100 GB |4|4x500|
|標準\_D3 |4|14 GB|4|暫存 (SSD) = 200 GB |8|8x500|
|標準\_D4 |8|28 GB|8|暫存 (SSD) = 400 GB |16|16x500|
|標準\_D11 |2|14 GB|2|暫存 (SSD) = 100 GB |4|4x500|
|標準\_D12 |4|28 GB|4|暫存 (SSD) = 200 GB |8|8x500|
|標準\_D13 |8|56 GB|8|暫存 (SSD) = 400 GB |16|16x500|
|標準\_D14 |16|112 GB|8|暫存 (SSD) = 800 GB |32|32x500|

## 標準層級：Dv2 系列

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3\.5 GB|1|暫存 (SSD) =50 GB |2|2x500|
|Standard\_D2\_v2 |2|7 GB|2|暫存 (SSD) = 100 GB |4|4x500|
|Standard\_D3\_v2 |4|14 GB|4|暫存 (SSD) = 200 GB |8|8x500|
|Standard\_D4\_v2 |8|28 GB|8|暫存 (SSD) = 400 GB |16|16x500|
|Standard\_D5\_v2 |16|56 GB|8|暫存 (SSD) = 800 GB |32|32x500|
|Standard\_D11\_v2 |2|14 GB|2|暫存 (SSD) = 100 GB |4|4x500|
|Standard\_D12\_v2 |4|28 GB|4|暫存 (SSD) = 200 GB |8|8x500|
|Standard\_D13\_v2 |8|56 GB|8|暫存 (SSD) = 400 GB |16|16x500|
|Standard\_D14\_v2 |16|112 GB|8|暫存 (SSD) = 800 GB |32|32x500|

## 標準層級：DS 系列*

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|快取大小 (GB)|最大的磁碟 IOPS 和頻寬|
|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3\.5|1|本機 SSD 磁碟 = 7 GB |2|43| 3,200 每秒 32 MB |
|Standard\_DS2 |2|7|2|本機 SSD 磁碟 = 14 GB |4|86| 6,400 每秒 64 MB |
|Standard\_DS3 |4|14|4|本機 SSD 磁碟 = 28 GB |8|172| 12,800 每秒 128 MB |
|Standard\_DS4 |8|28|8|本機 SSD 磁碟 = 56 GB |16|344| 25,600 每秒 256 MB |
|Standard\_DS11 |2|14|2|本機 SSD 磁碟 = 28 GB |4|72| 6,400 每秒 64 MB |
|Standard\_DS12 |4|28|4|本機 SSD 磁碟 = 56 GB |8|144| 12,800 每秒 128 MB |
|Standard\_DS13 |8|56|8|本機 SSD 磁碟 = 112 GB |16|288| 25,600 每秒 256 MB |
|Standard\_DS14 |16|112|8|本機 SSD 磁碟 = 224 GB |32|576| 50,000 每秒 512 MB |

**磁碟大小會影響 DS 系列 VM 每秒 (IOPS) 輸入/輸出作業量最大值，和可以達到最大輸送量 (頻寬)。如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage/storage-premium-storage.md)。

## 標準層級：G 系列

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|本機 SSD 磁碟 = 384 GB |4|4 x 500|
|Standard\_G2 |4|56 GB|2|本機 SSD 磁碟 = 768 GB |8|8 x 500|
|Standard\_G3 |8|112 GB|4|本機 SSD 磁碟 = 1,536 GB |16|16 x 500|
|Standard\_G4 |16|224 GB|8|本機 SSD 磁碟 = 3,072 GB |32|32 x 500|
|Standard\_G5 |32|448 GB|8|本機 SSD 磁碟 = 6,144 GB |64| 64 x 500 |

## 標準層級：GS 系列

|大小 |CPU 核心|記憶體|NIC (最大)|最大磁碟大小|最大的資料磁碟 (每個 1023 GB)|快取大小 (GB)|最大的磁碟 IOPS 和頻寬|
|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|本機 SSD 磁碟 = 56 GB |4|264| 5,000 每秒 125 MB |
|Standard\_GS2|4|56|2|本機 SSD 磁碟 = 112 GB |8|528| 10,000 秒 250 MB |
|Standard\_GS3|8|112|4|本機 SSD 磁碟 = 224 GB |16|1056| 20,000 每秒 500 MB |
|Standard\_GS4|16|224|8|本機 SSD 磁碟 = 448 GB |32|2112| 40,000 每秒 1,000 MB |
|Standard\_GS5|32|448|8|本機 SSD 磁碟 = 896 GB |64|4224| 80,000 每秒 2,000 MB |


### 另請參閱

[Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)

[關於 A8、A9、A10 和 A11 運算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_0302_2016-->