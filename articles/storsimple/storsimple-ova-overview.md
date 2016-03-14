<properties
   pageTitle="StorSimple Virtual Array 概觀 | Microsoft Azure"
   description="說明 StorSimple Virtual Array，這是一個整合式儲存體解決方案，可管理內部部署虛擬裝置與 Microsoft Azure 雲端儲存體之間的儲存體工作。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/01/2016"
   ms.author="alkohli" />

# StorSimple Virtual Array 簡介

## 概觀

歡迎使用 Microsoft Azure StorSimple Virtual Array，這是一個整合式儲存體解決方案，可管理 Hypervisor 中執行之內部部署虛擬裝置與 Microsoft Azure 雲端儲存體之間的儲存體工作。Virtual Array (也稱為 StorSimple 內部部署虛擬裝置) 是一個有效率、符合成本效益且易於管理的檔案伺服器或 iSCSI 伺服器解決方案，可減少許多與企業儲存體和資料保護相關聯的問題和支出。Virtual Array 特別適用於遠端辦公室/分公司案例。

本概觀著重於 Virtual Array。

- 如需 StorSimple 8000 系列的概觀，請移至 [StorSimple 8000 系列：混合式雲端解決方案](storsimple-overview.md)。 

- 如需 StorSimple 5000/7000 系列裝置的相關資訊，請移至 [StorSimple 線上說明](http://onlinehelp.storsimple.com/)。

Virtual Array 支援 iSCSI 或伺服器訊息區 (SMB) 通訊協定。它會在現有 Hypervisor 基礎結構上執行，並提供雲端、雲端備份、快速還原、項目層級復原和災害復原功能的分層。

下表總結 Virtual Array 的重要功能。

| 功能 | Virtual Array |
| ------- | ------------- |
|安裝需求 | 使用虛擬化基礎結構 (Hyper-V 或 VMware)|
| Availability | 單一節點 |
| 總容量 (包括雲端) |每個虛擬裝置最多 64 TB 可用容量 |
| 本機容量 | 每個虛擬裝置有 390 GB 到 6.4 TB 可用容量 (需要佈建 500 GB 到 8 TB 的磁碟空間)|
| 原生通訊協定 | iSCSI 或 SMB |
| 復原時間目標 (RTO) | iSCSI：少於 2 分鐘 (不論大小為何) |
| 復原點目標 (RPO) | 每日備份和隨選備份 |
| 儲存體分層 | 使用熱度對應來判斷應該分層輸入或分層輸出資料 |
| 支援 | 供應商所支援的虛擬化基礎結構 |
| 效能 | 會視基礎結構而異 |
| 資料行動性 | 可以還原至相同裝置，或執行項目層級復原 (檔案伺服器) |
| 儲存層 | 本機 Hypervisor 儲存體和雲端 |
| 共用或磁碟區大小 |階層式：最多 20 TB；固定在本機：最多 2 TB |
| 快照集 | 當機時保持一致 |
| 項目層級復原 | 是；使用者可以從共用還原 |

## 為何要使用 StorSimple？

StorSimple 會在數分鐘內將使用者和伺服器連接到 Azure 儲存體，而不需要進行任何應用程式修改。

下表說明一些 Virtual Array 解決方案所提供的主要優點。

| 功能 | 優點 |
|---------|---------|
| 透明整合 | Virtual Array 支援 iSCSI 或 SMB 通訊協定。這可確保儲存在雲端、在資料中心或在遠端伺服器上的資料會看似儲存在單一位置。|
| 降低儲存成本 | 您可以使用 StorSimple 佈建足夠的本機儲存體，以符合目前需求。隨著儲存體需求的成長，StorSimple 會將資料分層至符合成本效益的雲端儲存體。在雲端中，它會使用重複資料刪除和資料壓縮進一步降低儲存體需求和費用。|
| 簡化儲存管理 | StorSimple 提供雲端中的集中式管理。您可以使用網路工具來設定和管理在 Virtual Array 上、在遠端伺服器上和在雲端中儲存的資料。| 
| 改進災害復原和提高合規性 | StorSimple 不需要延長的回復時間。相反地，它會立即還原中繼資料，並視需要還原儲存的資料。這表示正常作業可以在最少干擾的情況下繼續執行。|
| 資料行動性 | 您可以從其他站台存取分層至雲端的資料，以供復原和移轉使用。請注意，您只可以將資料還原到原始 Virtual Array。不過，您可以使用災害復原功能將整個 Virtual Array 還原到另一個 Virtual Array。|

## 工作流程

StorSimple Virtual Array 特別適用於下列工作流程：

- [雲端儲存體管理](#cloud-based-storage-management)
- [與位置無關的備份](#location-independent-backup)
- [資料保護和災害復原](#data-protection-and-disaster-recovery)

### 雲端儲存體管理

您可以使用 Azure 傳統入口網站中執行的 StorSimple Manager 服務，來管理多個裝置上和多個位置中所儲存的資料。這在分散式分公司案例中特別有用。請注意，您必須建立個別的 StorSimple Manager 服務執行個體來管理 Virtual Array 和實體 StorSimple 裝置。

![雲端儲存體管理](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### 與位置無關的備份

運用 Virtual Array，雲端快照集可針對磁碟區或共用提供與位置無關的時間點複本。預設會啟用雲端快照集，並且無法停用。所有磁碟區和共用都是透過單一每日備份原則同時備份，需要時，也可以採用其他臨機操作備份。

### 資料保護和災害復原

Virtual Array 支援下列資料保護和災害復原案例：

- **磁碟區或共用還原** – 使用還原作為新的工作流程來復原磁碟區或共用。這種方法可用來復原整個磁碟區或共用。
- **項目層級復原** – 共用允許對最新備份進行簡化存取。您可以輕鬆地從雲端中可用的特殊 .backup 資料夾復原個別檔案。這個還原功能是由使用者所驅動，而且不需要系統管理介入。
- **災害復原** – 使用容錯移轉功能，將所有磁碟區或共用復原到新的 Virtual Array。您可以建立新的 Virtual Array，並向 StorSimple Manager 服務進行註冊，然後容錯移轉原始 Virtual Array。新的 Virtual Array 之後將採用佈建的資源。 

## Virtual Array 元件

Virtual Array 包括下列元件：

- [Virtual Array](#virtual-array) – 根據虛擬化環境或 Hypervisor 中所佈建之虛擬機器的混合式雲端存放裝置。  
- [StorSimple Manager 服務](#storsimple-manager-service) – Azure 傳統入口網站的延伸模組，可讓您透過單一 Web 介面管理可從不同地理位置存取的一個或多個 StorSimple 裝置。您可以使用 StorSimple Manager 服務來建立和管理服務、檢視和管理裝置及警示，以及管理磁碟區、共用和現有快照集。
- [本機 Web 使用者介面](#local-web-user-interface) – 一種 Web UI，用來設定裝置，使其可以連接到區域網路，然後向 StorSimple Manager 服務註冊裝置。 
- [命令列介面](#command-line-interface) – 一個 Windows PowerShell 介面，可用來在 Virtual Array 上啟動支援工作階段。下列各節將更詳細描述每個元件，並說明解決方案如何排列資料、配置儲存體、加快儲存體管理速度以及資料保護。

### Virtual Array

Virtual Array 是一種單一節點儲存體解決方案，可提供主要儲存體、管理與雲端儲存體的通訊，並協助確保所有儲存在裝置之資料的安全性和機密性。

在可供下載的模型中提供 Virtual Array。裝置上的存放裝置陣列最大容量是 6.4 TB (基礎儲存體需求為 8 TB) 和 64 TB (包括雲端儲存體)。

Virtual Array 具有下列功能：

- 符合成本效益。使用現有的虛擬化基礎結構，而且可以部署於現有 Hyper-V 或 VMware Hypervisor 上。
- 位於資料中心，而且可以設定為 iSCSI 伺服器或檔案伺服器。 
- 與雲端整合。
- 備份儲存在雲端中，可以加速災害復原並簡化項目層級復原 (ILR)。 
- 您可以將更新套用至 Virtual Array，就像將它們套用至實體裝置一樣。

>[AZURE.NOTE] 無法展開 Virtual Array。因此，建立虛擬裝置時，請一定要佈建足夠的儲存體。

### StorSimple Manager 服務

Microsoft Azure StorSimple 提供可讓您集中管理資料中心和雲端儲存體的 Web 架構使用者介面 (StorSimple Manager 服務)。您可以使用 StorSimple Manager 服務來執行下列工作：

- 透過單一服務管理多個 StorSimple Virtual Array。 
- 進行和管理 StorSimple 裝置的安全性設定。(雲端中的加密與 Microsoft Azure API 相依。)
- 設定儲存體帳戶認證和屬性。
- 設定和管理磁碟區或共用。
- 備份和還原磁碟區或共用上的資料。
- 監視效能。
- 檢閱系統設定，並識別可能發生的問題。

您可以使用 StorSimple Manager 服務來執行 Virtual Array 的每日管理。

如需詳細資訊，請移至[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

### 本機 Web 使用者介面

Virtual Array 包括一個 Web UI，用於單次設定裝置，並向 StorSimple Manager 服務註冊裝置。您可以使用它來關閉和重新啟動 Virtual Array、執行診斷測試、更新軟體、變更裝置系統管理員密碼、檢視系統記錄檔，以及連絡 Microsoft 支援服務發出服務要求。

如需使用 Web UI 的相關資訊，請移至[使用 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

### 命令列介面

包括的 Windows PowerShell 介面可讓您起始與 Microsoft 支援服務的支援工作階段，讓他們協助您疑難排解並解決虛擬裝置上可能發生的問題。

## 儲存體管理技術

除了 Virtual Array 和其他元件以外，StorSimple 解決方案還會使用下列軟體技術來提供重要資料的快速存取、減少儲存體使用，以及保護 Virtual Array 上所儲存的資料：

- [自動儲存體分層](#automatic-storage-tiering) 
- [固定在本機的共用和磁碟區](#locally-pinned-shares-and-volumes)
- [分層或備份至雲端之資料的重複資料刪除和壓縮](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [排程和隨選備份](#scheduled-and-on-demand-backups)

### 自動儲存體分層

Virtual Array 使用新的分層機制來管理跨 Virtual Array 和雲端儲存的資料。只有兩層：本機 Virtual Array 和 Azure 雲端儲存體。StorSimple Virtual Array 會根據熱度圖自動排列分層中的資料，以追蹤目前使用量、使用年限以及與其他資料的關聯性。最常使用 (最熱) 的資料會儲存在本機，而不常使用和非使用中資料會自動移轉至雲端 (所有備份都是儲存在雲端中)。 StorSimple 會隨著使用模式變更而調整並重新排列資料和儲存體指派。例如，某些資訊在經過一段時間之後可能會變成不常使用。資訊逐漸變成不常使用時，會分層輸出至雲端。如果相同的資料再次變成使用中，則會分層輸入至存放裝置陣列。

保證特定階層式共用或磁碟區資料的專屬本機層空間 (該共用或磁碟區大約有 10% 的總佈建空間)。雖然這會降低虛擬裝置上該共用或磁碟區的可用儲存體，但是可確保某個共用或磁碟區的分層不受其他共用或磁碟區的分層需求的影響。因此，某個共用或磁碟區上的極忙碌工作負載無法將所有其他工作負載強制到雲端。

![自動儲存體分層](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] 您可以指定本機固定的磁碟區，在此情況下，資料會保留在 Virtual Array 上，而且絕不會分層至雲端。如需詳細資訊，請移至[固定在本機的共用和磁碟區](#locally-pinned-shares-and-volumes)。

### 固定在本機的共用和磁碟區

您可以建立適當的固定在本機共用和磁碟區。這項功能可確保重要應用程式所需的資料保留在 Virtual Array 中，而且絕不會分層至雲端。固定在本機的共用和磁碟區具有下列功能：

- 不受限於雲端延遲或連線問題。
- 依然受益於 StorSimple 雲端備份和災害復原功能。

您可以將固定在本機的共用或磁碟區還原為階層式，或將階層式共用或磁碟區還原為固定在本機。

如需固定在本機的磁碟區的詳細資訊，請移至[使用 StorSimple Manager 服務來管理磁碟區](storsimple-manage-volumes-u2.md)。

### 分層或備份至雲端之資料的重複資料刪除和壓縮

StorSimple 會使用重複資料刪除和資料壓縮，來進一步降低雲端中的儲存體需求。重複資料刪除減少整體儲存資料量的方式是刪除儲存資料集中的重複資料。當資訊變更時，StorSimple 會忽略未變更的資料，而只擷取變更。此外，StorSimple 還會透過識別並移除重複資訊來減少儲存的資料量。

>[AZURE.NOTE] Virtual Array 上所儲存的資料不會進行重複資料刪除或壓縮。所有重複資料刪除和壓縮只會在將資料傳送至雲端之前發生。

### 排程和隨選備份

StorSimple 資料保護功能可讓您建立隨選備份。此外，預設備份排程可確保每日備份資料。備份採用儲存在雲端的累加快照形式。快照集 (僅記錄自上次備份後的變更) 可以快速進行建立和還原。這些快照集在災害復原案例中至關重要，因為這些快照集會取代次要儲存體系統 (例如磁帶備份)，並讓您將資料還原到資料中心或在必要時還原至其他網站。

## 後續步驟

了解如何[準備 Virtual Array 入口網站](storsimple-ova-deploy1-portal-prep.md)。

<!---HONumber=AcomDC_0302_2016-------->