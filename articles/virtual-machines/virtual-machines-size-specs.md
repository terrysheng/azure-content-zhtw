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
 ms.date="09/02/2015"
 ms.author="cynthn"/>

# 虛擬機器的大小

## 概觀

本文說明以虛擬機器為基礎之計算資源的可用大小和選項，而您可以使用這類資源來執行應用程式和工作負載。同時也提供當您規劃使用這些資源時所需注意的部署考量。

Azure 虛擬機器和雲端服務是 Azure 所提供數種計算資源類型的其中兩種。如需說明，請參閱 [Azure 所提供的運算裝載選項](http://go.microsoft.com/fwlink/p/?LinkID=311926)。

>[AZURE.NOTE]若要查看相關的 Azure 限制，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。

## 規劃考量

虛擬機器可用於這兩個層級：基本和標準。這兩種類型提供各種大小的選擇，但是基本層級不會提供一些可用於標準層級的功能，例如負載平衡和自動調整。標準層級的大小是不同系列所組成︰A、D、DS、G 和 GS。這些大小的一些考量事項包括：

*   D 系列 VM 是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。

*   Dv2 系列是原始 D 系列的延續，擁有更強大的 CPU。Dv2 系列 CPU 比 D 系列 CPU 快約 35%。它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。Dv2 系列的記憶體和磁碟組態和 D 系列一樣。如需詳細資訊，請參閱 Azure 部落格公告：[新 Dv2 系列的虛擬機器大小](http://azure.microsoft.com/blog/2015/10/01/new-dv2-series-virtual-machine-sizes/)。

*   G 系列 VM 提供最大的大小和最佳效能，並且可在具有 Intel Xeon E5 V3 系列處理器的主機上執行。

*   DS 系列和 GS 系列 VM 可以使用進階儲存體，針對使用大量 I/O 的工作負載提供高效能、低延遲的儲存體。這些 VM 會使用固態硬碟 (SSD) 來裝載虛擬機器的磁碟，還可提供本機 SSD 磁碟快取。僅特定地區可用進階儲存體。如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage-premium-storage-preview-portal.md)。

虛擬機器的大小會影響定價。大小也會影響虛擬機器的處理、記憶體和儲存體容量。儲存體成本會分別根據儲存體帳戶中使用的頁面來計算。如需詳細資訊，請參閱[虛擬機器定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/)和 [Azure 儲存體定價](http://azure.microsoft.com/pricing/details/storage/)。如需有關 VM 儲存體的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](virtual-machines-disks-vhds.md)。

下列考量可協助您決定大小：

*   只有使用 Azure SDK 1.3 版或更新版本，才能使用 A0\\Basic\_A0 大小。  

*   A1\\Basic\_A1 是建議針對生產工作負載採用的最小大小。

*   使用 SQL Server Enterprise Edition 時，請選取具有 4 個或 8 個 CPU 核心的虛擬機器。

*   Azure 資料中心的某些實體主機可能不支援較大的虛擬機器大小，例如 A5-A11。因此，您可能會在將現有的虛擬機器調整為新的大小、在 2013 年 4 月 16 日之前建立的虛擬網路中建立新的虛擬機器，或將新的虛擬機器加入現有雲端服務時，看到錯誤訊息：「無法設定虛擬機器 <machine name>」或「無法建立虛擬機器 <machine name>」 。請參閱支援論壇上的主題[錯誤：「無法設定虛擬機器」](https://social.msdn.microsoft.com/Forums/zh-TW/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows)，以查看每個部署案例的因應措施。

*   A8/A10 和 A9/A11 虛擬機器大小具有相同的容量。A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，其會連線到遠端直接記憶體存取 (RDMA) 網路，以利在虛擬機器之間進行快速通訊。A8 和 A9 執行個體是專為執行期間，節點之間需要常數和低延遲通訊的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

*   Dv2 系列和 D 系列是要求更快速的 CPU、更好的本機磁碟效能，或有更高記憶體需求之應用程式的最佳選擇。它們為許多企業級應用程式提供了強大的組合。

## 一般限制

下表顯示的限制適用於使用服務管理部署模型建立的虛擬機器 (不管虛擬機器的大小)。

[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

下表顯示的限制適用於使用資源管理員部署模型建立的虛擬機器 (不管虛擬機器的大小)。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## 大小資料表

下表顯示其所提供的大小和容量。

>[AZURE.NOTE]儲存體容量是使用 1024^3 位元組當作 GB 的度量單位來表示。這有時稱為 gibibyte 或基底 2 定義。比較使用不同基底系統的大小時，請記住，基底 2 的大小可能會顯示為小於基底 10，但是對於任何特定的大小 (例如 1 GB)，基底 2 的系統可提供比基底 10 系統更大的容量，因為 1024^3 大於 1000^3。

### 基本層

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 300)|
|---|---|---|---|---|---|
|A0\\Basic\_A0|1|768 MB|<p>作業系統 = 1023 GB</p><p>暫存 = 20 GB</p>|1|1x300|
|A1\\Basic\_A1|1|1\.75 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 40 GB</p>|2|2x300|
|A2\\Basic\_A2|2|3\.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 60 GB</p>|4|4x300|
|A3\\Basic\_A3|4|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 120 GB</p>|8|8x300|
|A4\\Basic\_A4|8|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 240 GB</p>|16|16x300|

### 標準層級：A 系列

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|
|A0\\超小型|1|768 MB|<p>作業系統 = 1023 GB</p><p>暫存 = 20 GB</p>|1|1x500|
|A1\\小型|1|1\.75 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 70 GB</p>|2|2x500|
|A2\\中型|2|3\.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 135 GB</p>|4|4x500|
|A3\\大型|4|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 285 GB</p>|8|8x500|
|A4\\超大型|8|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 605 GB</p>|16|16x500|
|A5\\相同|2|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 135 GB</p>|4|4X500|
|A6\\相同|4|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 285 GB</p>|8|8x500|
|A7\\相同|8|56 GB|<p>作業系統 = 1023 GB</p><p>暫存 = 605 GB</p>|16|16x500|
|A8\\相同|8|56 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 運算密集型執行個體</a>。</p></blockquote>|16|16x500|
|A9\\相同|16|112 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 運算密集型執行個體</a>。</p></blockquote>|16|16x500|
|A10\\相同|8|56 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 運算密集型執行個體</a>。</p></blockquote>|16|16x500|
|A11\\相同|16|112 GB|<p><p>OS = 1023 GB</p><p>暫存 = 382 GB</p><blockquote><p>注意：如需使用此大小的資訊與考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 與 A11 運算密集型執行個體</a>。</p></blockquote>|16|16x500|

### 標準層級：D 系列

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|
|Standard\_D1\\相同|1|3\.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 50 GB</p>|2|2x500|
|Standard\_D2\\相同|2|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 100 GB</p>|4|4x500|
|Standard\_D3\\相同|4|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 200 GB</p>|8|8x500|
|Standard\_D4\\相同|8|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 400 GB</p>|16|16x500|
|Standard\_D11\\相同|2|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 100 GB</p>|4|4x500|
|Standard\_D12\\相同|4|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 200 GB</p>|8|8x500|
|Standard\_D13\\相同|8|56 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 400 GB</p>|16|16x500|
|Standard\_D14\\相同|16|112 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 800 GB</p>|32|32x500|

### 標準層級：Dv2 系列

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|
|Standard\_D1\_v2\\相同|1|3\.5 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 50 GB</p>|2|2x500|
|Standard\_D2\_v2\\相同|2|7 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 100 GB</p>|4|4x500|
|Standard\_D3\_v2\\相同|4|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 200 GB</p>|8|8x500|
|Standard\_D4\_v2\\相同|8|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 400 GB</p>|16|16x500|
|Standard\_D5\_v2\\相同|16|56 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 800 GB</p>|32|32x500|
|Standard\_D11\_v2\\相同|2|14 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 100 GB</p>|4|4x500|
|Standard\_D12\_v2\\相同|4|28 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 200 GB</p>|8|8x500|
|Standard\_D13\_v2\\相同|8|56 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 400 GB</p>|16|16x500|
|Standard\_D14\_v2\\相同|16|112 GB|<p>作業系統 = 1023 GB</p><p>暫存 (SSD) = 800 GB</p>|32|32x500|

### 標準層級：DS 系列*

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|快取大小 (GB)|最大的磁碟 IOPS 和頻寬|
|---|---|---|---|---|---|---|
|Standard\_DS1\\相同|1|3\.5|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 7 GB</p>|2|43|<p>3,200</p><p>每秒 32 MB</p>|
|Standard\_DS2\\相同|2|7|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 14 GB</p>|4|86|<p>6,400</p><p>每秒 64 MB</p>|
|Standard\_DS3\\相同|4|14|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 28 GB</p>|8|172|<p>12,800</p><p>每秒 128 MB</p>|
|Standard\_DS4\\相同|8|28|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 56 GB</p>|16|344|<p>25,600</p><p>每秒 256 MB</p>|
|Standard\_DS11\\相同|2|14|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 28 GB</p>|4|72|<p>6,400</p><p>每秒 64 MB</p>|
|Standard\_DS12\\相同|4|28|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 56 GB</p>|8|144|<p>12,800</p><p>每秒 128 MB</p>|
|Standard\_DS13\\相同|8|56|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 112 GB</p>|16|288|<p>25,600</p><p>每秒 256 MB</p>|
|Standard\_DS14\\相同|16|112|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 224 GB</p>|32|576|<p>50,000</p><p>每秒 512 MB</p>|

**磁碟大小會影響 DS 系列 VM 每秒 (IOPS) 輸入/輸出作業量最大值，和可以達到最大輸送量 (頻寬)。如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage-premium-storage-preview-portal.md)。

### 標準層級：G 系列

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|最大IOPS (每個磁碟 500)|
|---|---|---|---|---|---|
|Standard\_G1\\相同|2|28 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 384 GB</p>|4|4 x 500|
|Standard\_G2\\相同|4|56 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 768 GB</p>|8|8 x 500|
|Standard\_G3\\相同|8|112 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 1,536 GB</p>|16|16 x 500|
|Standard\_G4\\相同|16|224 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 3,072 GB</p>|32|32 x 500|
|Standard\_G5\\相同|32|448 GB|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 6,144 GB</p>|64|<p>64 x 500</p>|

### 標準層級：GS 系列

|大小 – Azure 入口網站\\Cmdlet 和 API|CPU 核心|記憶體|最大的磁碟大小 - 虛擬機器|最大的資料磁碟 (每個 1023 GB)|快取大小 (GB)|最大的磁碟 IOPS 和頻寬|
|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 56 GB</p>|4|264|<p>5,000</p><p>每秒 125 MB</p>|
|Standard\_GS2|4|56|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 112 GB</p>|8|528|<p>10,000</p><p>每秒 250 MB</p>|
|Standard\_GS3|8|112|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 224 GB</p>|16|1056|<p>20,000</p><p>每秒 500 MB</p>|
|Standard\_GS4|16|224|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 448 GB</p>|32|2112|<p>40,000</p><p>每秒 1,000 MB</p>|
|Standard\_GS5|32|448|<p>作業系統 = 1023 GB</p><p>本機 SSD 磁碟 = 896 GB</p>|64|4224|<p>80,000</p><p>每秒 2,000 MB</p>|


### 另請參閱

[Azure 訂用帳戶和服務限制、配額與限制](../azure-subscription-service-limits.md)

[關於 A8、A9、A10 和 A11 運算密集型執行個體](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=Oct15_HO2-->