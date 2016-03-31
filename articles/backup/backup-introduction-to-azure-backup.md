<properties
	pageTitle="何謂 Azure 備份中心？ | Microsoft Azure"
	description="您可以使用 Azure 備份和復原服務，從 Windows Server、Windows 用戶端電腦、System Center DPM 伺服器和 Azure 虛擬機器備份和還原資料與應用程式。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="備份與還原；復原服務；備份解決方案"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="jimpark; trinadhk"/>

# 何謂 Azure 備份？
Azure 備份是您用來備份和還原 Microsoft 雲端資料的服務。它將以一個可靠、安全及具成本競爭力的雲端架構解決方案，取代您現有的內部部署或異地備份解決方案。它也有助於保護在雲端中執行的資產。Azure 備份提供的復原服務是以可調整、持久和高可用性的世界級基礎結構為建置基礎。

[觀看 Azure 備份的影片概觀](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## 為何使用 Azure 備份？
傳統的備份解決方案已演變成將雲端視為類似於磁碟或磁帶的端點。雖然此方法很簡單，但是也有限制。它並不會完整運用基礎雲端平台，可謂是效率不佳又昂貴的解決方案。相反地，Azure 備份可提供功能強大且價格合理的雲端備份解決方案的所有優點。以下是一些 Azure 備份提供的主要優點。

| 功能 | 優點 |
| ------- | ------- |
| 自動儲存體管理 | 內部部署儲存裝置不需要資本支出。Azure 備份會自動配置和管理備份儲存體，且採用使用時付費制。 |
| 無限制的調整 | 利用高可用性保證不會造成額外的維護與監控負荷。Azure 備份會透過非干擾式的自動調整功能，使用 Azure 雲端的基礎能力和調整。 |
| 多個儲存體選項 | 根據需求選擇備份儲存體：<li>本地備援儲存體區塊 Blob 非常適合於注重價格的客戶，同時為資料提供本機硬體故障的防護。<li>異地複寫存體區塊 Blob 在配對的資料中心提供額外三個複本。這些額外複本有助於確保備份資料具高可用性，即使發生 Azure 網站層級損毀亦然。 |
| 無限制的資料傳輸 | 不會收取從備份保存庫進行還原作業期間的任何出口流量 (輸出) 資料傳送費用。資料輸入至 Azure 也是免費的。適用於可用的匯入服務。 |
| 資料加密 | 資料加密可讓公用雲端中的客戶資料安全傳輸與儲存。加密複雜密碼會儲存在來源，並且永遠不會傳送至或儲存在 Azure 中。還原任何資料皆需要加密金鑰，而只有客戶在服務中擁有資料的完整存取權。 |  
| 應用程式一致備份 | Windows 上的應用程式一致備份有助於確保在還原時不需要修正，以減少復原時間目標並可讓客戶更快地回到執行中的狀態。 |
| 長期保留 | 與其為異地磁帶備份解決方案付費，客戶可以備份至以低成本提供令人信服且近似磁帶解決方案的 Azure。 |

## Azure 備份元件
因為備份是一個混合式備份解決方案，所以是由多個一起運作以進行端對端備份及還原工作流程的元件所組成。

![Azure 備份元件](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

### 部署案例

| 元件 | 可以在 Azure 中部署嗎？ | 可以在內部部署嗎？ | 支援的目標儲存體|
| --- | --- | --- | --- |
| Azure 備份代理程式 | <p>**是**</p> <p>您可以在 Azure 中執行的任何 Windows Server VM 上部署 Azure 備份代理程式。</p> | <p>**是**</p> <p>您可以在 Windows Server VM 或實體機器上部署備份代理程式。</p> | <p>Azure 備份保存庫</p> |
| System Center Data Protection Manager (DPM) | <p>**是**</p> <p>深入了解[如何使用 System Center DPM 保護 Azure 中的工作負載](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx)。</p> | <p>**是**</p> <p>深入了解[如何保護工作負載和資料中心內的 VM](https://technet.microsoft.com/library/hh758173.aspx)。</p> | <p>本機連接的磁碟、</p> <p>Azure 備份保存庫、</p> <p>磁帶 (僅限內部部署)</p> |
| Azure 備份伺服器 | <p>**是**</p> <p>深入了解[如何使用 Azure 備份伺服器保護 Azure 中的工作負載](backup-azure-microsoft-azure-backup.md)。</p> | <p>**是**</p> <p>深入了解[如何使用 Azure 備份伺服器保護 Azure 中的工作負載](backup-azure-microsoft-azure-backup.md)。</p> | <p>本機連接的磁碟、</p> <p>Azure 備份保存庫</p> |
| Azure 備份 (VM 延伸模組) | <p>**是**</p><p>Azure 網狀架構的一部分</p><p>適用於[備份 Azure 基礎結構即服務 (IaaS) 虛擬機器](backup-azure-vms-introduction.md)。</p> | <p>**否**</p> <p>請使用 System Center DPM 備份資料中心內的虛擬機器。</p> | <p>Azure 備份保存庫</p> |

### 元件層級的優點與限制

| 元件 | 優點 | 限制 | 復原細微度 |
| --- | --- | --- | --- |
| Azure 備份 (MARS) 代理程式 | <li>可以在 Windows OS 電腦上備份檔案和資料夾 (不論是實體或虛擬，VM 可位於內部部署或 Azure 上的任意處)<li>不需要個別的備份伺服器<li>使用 Azure 備份保存庫 | <li>一天備份三次/檔案層級還原<li>僅限檔案/資料夾/磁碟區層級還原，非應用程式感知<li>不支援 Linux | 檔案/資料夾/磁碟區 |
| System Center Data Protection Manager | <li>應用程式感知快照 (VSS)<li>具有充分彈性的備份時間<li>復原細微度 (全部)<li>可以使用 Azure 備份保存庫<li>Linux 支援 (如果裝載於 Hyper-V) | <li>缺乏異質支援 (VMware VM 備份、Oracle 工作負載備份)。 | 檔案/資料夾/磁碟區<br>/VM/應用程式 |
| Microsoft Azure 備份伺服器 | <li>應用程式感知快照 (VSS)<li>具有充分彈性的備份時間<li>復原細微度 (全部)<li>可以使用 Azure 備份保存庫<li>Linux 支援 (如果裝載於 Hyper-V)<li>不需要 System Center 授權 | <li>缺乏異質支援 (VMware VM 備份、Oracle 工作負載備份)。<li>永遠需要即時 Azure 訂用帳戶<li>不支援磁帶備份 | 檔案/資料夾/磁碟區<br>/VM/應用程式 |
| Azure IaaS VM 備份 | <li>適用於 Windows/Linux 的原生備份<li>不需要安裝特定代理程式<li>不需要備份基礎結構的網狀架構層級備份<li>使用 Azure 備份保存庫 | <li>一天備份一次/磁碟層級還原<li>無法備份內部部署 | VM<br>個別磁碟 |

## 可以備份哪些應用程式和工作負載？

| 工作負載 | 來源機器 | Azure 備份方案 |
| --- | --- |---|
| 檔案和資料夾 | Windows Server | <p>[Azure 備份代理程式](backup-configure-vault.md)、</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| 檔案和資料夾 | Windows 用戶端 | <p>[Azure 備份代理程式](backup-configure-vault.md)、</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| Hyper-V 虛擬機器 (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| Hyper-V 虛擬機器 (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure 備份代理程式)、</p> <p>[Azure 備份伺服器](backup-azure-microsoft-azure-backup.md) (包括 Azure 備份代理程式)</p> |
| Azure IaaS VM (Windows) | - | [Azure 備份 (VM 延伸模組)](backup-azure-vms-introduction.md) |
| Azure IaaS VM (Linux) | - | [Azure 備份 (VM 延伸模組)](backup-azure-vms-introduction.md) |

## ARM 和 Linux 支援

| 元件 | ARM 支援 | Linux (Azure 背書) 支援 |
| --- | --- | --- |
| Azure 備份 (MARS) 代理程式 | 是 | 否 (僅限 Windows 代理程式) |
| System Center Data Protection Manager | 是 (客體中的代理程式) | 僅限 Hyper-V (不是 Azure VM)，只能進行檔案一致的備份 |
| Azure 備份伺服器 (MABS) | 是 (客體中的代理程式) | 僅限 Hyper-V (不是 Azure VM)，只能進行檔案一致的備份 (與 DPM 相同) |
| Azure IaaS VM 備份 | 敬請期待 | 敬請期待 - V2 Linux VM <br><br>(檔案系統層級的一致性) |

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

## 功能
以下五個資料表概述備份功能如何處理每個元件：

### 儲存體

| 功能 | Azure 備份代理程式 | System Center DPM | Azure 備份伺服器 | Azure 備份 (VM 延伸模組) |
| ------- | --- | --- | --- | ---- |
| Azure 備份保存庫 | ![是][green] | ![是][green] | ![是][green] | ![是][green] |
| 磁碟儲存體 | | ![是][green] | ![是][green] | |
| 磁帶儲存體 | | ![是][green] | | |
| 壓縮 (在備份保存庫中) | ![是][green] | ![是][green]| ![是][green] | |
| 增量備份 | ![是][green] | ![是][green] | ![是][green] | ![是][green] |
| 磁碟重複資料刪除 | | ![部分][yellow] | ![部分][yellow]| | |

![資料表索引鍵](./media/backup-introduction-to-azure-backup/table-key.png)

備份保存庫是所有元件慣用的儲存體目標。System Center DPM 和備份伺服器也提供產生本機磁碟複本的選項。不過，System Center DPM 會提供將資料寫入磁帶儲存裝置的選項。

#### 增量備份
不論目標儲存體 (磁碟、磁帶、備份保存庫) 為何，每個元件都支援增量備份。增量備份可協助確保有效利用備份的儲存體和時間。做法是只傳輸上次備份到目標儲存體後所做的變更。

#### 壓縮
備份會經過壓縮以減少所需的儲存空間。唯一不進行壓縮的元件為 VM 延伸模組。透過 VM 延伸模組，所有備份資料會從客戶儲存體帳戶複製到相同區域中的備份保存庫，且不加以壓縮。雖然未經壓縮會稍微擴大使用的儲存體，但儲存未經壓縮的資料可讓還原時間加快。

#### 重複資料刪除
[部署在 Hyper-V 虛擬機器中](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)的 System Center DPM 和備份伺服器可支援重複資料刪除。使用 Windows Server 重複資料刪除可在主機層級執行重複資料刪除，在當作備份儲存體連接至虛擬機器的虛擬硬碟 (VHD) 上。

>[AZURE.WARNING] 重複資料刪除不適用於 Azure 中任何的備份元件。當 System Center DPM 和備份伺服器部署在 Azure 中，連接至 VM 的儲存體磁碟不能進行重複資料刪除。

### 安全性

| 功能 | Azure 備份代理程式 | System Center DPM | Azure 備份伺服器 | Azure 備份 (VM 延伸模組) |
| ------- | --- | --- | --- | ---- |
| 網路安全性 (至 Azure) | ![是][green] |![是][green] | ![是][green] | ![部分][yellow]|
| 資料安全性 (在 Azure 中) | ![是][green] |![是][green] | ![是][green] | ![部分][yellow]|

![資料表索引鍵](./media/backup-introduction-to-azure-backup/table-key.png)

從您的伺服器到備份保存庫的所有備份流量都會使用進階加密標準 256 加密。資料會透過安全的 HTTPS 連結來傳送。備份資料也會在備份保存庫中以加密格式儲存。只有持有複雜密碼的客戶可以解除鎖定此資料。Microsoft 無法解密在任何時間點所備份的資料。

>[AZURE.WARNING] 只有客戶會看見用來加密備份資料的金鑰。Microsoft 不會維護 Azure 中的複本，且無法存取金鑰。如果錯置金鑰，則 Microsoft 無法復原備份資料。

備份 Azure VM 時，需要在虛擬機器內設定加密。在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 dm-crypt。Azure 備份不會自動加密來自此路徑的備份資料。

### 支援的工作負載

| 功能 | Azure 備份代理程式 | System Center DPM | Azure 備份伺服器 | Azure 備份 (VM 延伸模組) |
| ------- | --- | --- | --- | ---- |
| Windows Server 機器 - 檔案和資料夾 | ![是][green] | ![是][green] | ![是][green] | |
| Windows 用戶端機器 - 檔案和資料夾 | ![是][green] | ![是][green] | ![是][green] | |
| Hyper-V 虛擬機器 (Windows) | | ![是][green] | ![是][green] | |
| Hyper-V 虛擬機器 (Linux) | | ![是][green] | ![是][green] | |
| Microsoft SQL Server | | ![是][green] | ![是][green] | |
| Microsoft SharePoint | | ![是][green] | ![是][green] | |
| Microsoft Exchange | | ![是][green] | ![是][green] | |
| Azure 虛擬機器 (Windows) | | | | ![是][green] |
| Azure 虛擬機器 (Linux) | | | | ![是][green] |

![資料表索引鍵](./media/backup-introduction-to-azure-backup/table-key-2.png)

### 網路

| 功能 | Azure 備份代理程式 | System Center DPM | Azure 備份伺服器 | Azure 備份 (VM 延伸模組) |
| ------- | --- | --- | --- | ---- |
| 網路壓縮 (至備份伺服器) | | ![是][green] | ![是][green] | |
| 網路壓縮 (至備份保存庫) | ![是][green] | ![是][green] | ![是][green] | |
| 網路通訊協定 (至備份伺服器) | | TCP | TCP | |
| 網路通訊協定 (至備份保存庫) | HTTPS | HTTPS | HTTPS | HTTPS |

![資料表索引鍵](./media/backup-introduction-to-azure-backup/table-key-2.png)

因為 VM 延伸模組會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料，所以不需要最佳化此流量。流量會透過 Azure 資料中心中的本機儲存體網路傳送，因此不太需要因為頻寬考量而進行壓縮。

將資料送到備份伺服器 (DPM 或備份伺服器) 保護的客戶，可以壓縮從主要伺服器到備份伺服器的流量以節省頻寬。

#### 網路節流
Azure 備份代理程式提供的節流功能可讓您控制在資料傳輸期間使用網路頻寬的方式。如果您需要在上班時間內備份資料，但不希望備份程序干擾其他網際網路流量，這會很有幫助。資料傳輸的節流適用於備份和還原活動。

### 備份和保留

| | Azure 備份代理程式 | System Center DPM | Azure 備份伺服器 | Azure 備份 (VM 延伸模組) |
| --- | --- | --- | --- | --- |
| 備份頻率 (至備份保存庫) | 每天備份 3 次 | 每天備份 2 次 |每天備份 2 次 | 每天備份 1 次 |
| 備份頻率 (至磁碟) | 不適用 | <p>每隔 15 分鐘 (SQL Server)</p> <p>每隔 1 小時 (其他工作負載)</p> | <p>每隔 15 分鐘 (SQL Server)</p> <p>每隔 1 小時 (其他工作負載)</p> |不適用 |
| 保留選項 | 每日、每週、每月、每年 | 每日、每週、每月、每年 | 每日、每週、每月、每年 |每日、每週、每月、每年 |
| 保留期限 | 最多 99 年 | 最多 99 年 | 最多 99 年 | 最多 99 年 |
| 備份保存庫中的復原點 | 無限 | 無限 | 無限 | 無限 |
| 本機磁碟上的復原點 | 不適用 | 64 (檔案伺服器)、<br><br>448 (應用程式伺服器) | 64 (檔案伺服器)、<br><br>448 (應用程式伺服器) |不適用 |
| 磁帶上的復原點 | 不適用 | 無限 | 不適用 | 不適用 |

## 什麼是保存庫認證檔？

保存庫認證檔是入口網站針對每個備份保存庫所產生的憑證。入口網站接著會將公開金鑰上傳至「存取控制服務」(ACS)。當使用者下載認證，而後在電腦註冊期間輸入認證時，會提供私密金鑰給使用者。私密金鑰會對電腦進行驗證，以便將備份資料傳送至 Azure 備份服務中的已識別保存庫。

保存庫認證僅在註冊工作流程期間使用。您應自行負責，並確保保存庫認證檔不會遭到破解。保存庫認證檔落入任何惡意使用者的手中，則可用來針對相同的保存庫註冊其他電腦。不過，因為備份資料是使用客戶專屬的複雜密碼進行加密，所以現有的備份資料不會遭到洩漏。若要減輕這個問題，保存庫認證設在 48 小時後過期。您可以下載備份保存庫的保存庫認證，次數不受限制，但僅最新的檔案可在註冊工作流程期間提供使用。

## Azure 備份與 Azure Site Recovery 有何不同？
許多客戶會混淆備份復原和災害復原。兩者都可擷取資料和提供還原語意，但兩者的核心價值主張不同。

Azure 備份會在內部部署和雲端備份資料。Azure Site Recovery 可協調虛擬機器和實體伺服器的複寫、容錯移轉及容錯回復。這兩項服務都很重要，因為您的災害復原解決方案必須讓資料保持安全且可復原 (備份)，並且在服務中斷時讓您的工作負載可供使用和存取 (Site Recovery)。

下列概念可協助您做出有關備份和災害復原的重要決策。

| 概念 | 詳細資料 | 備份 | 災害復原 (DR) |
| ------- | ------- | ------ | ----------------- |
| 復原點目標 (RPO) | 在需要進行復原的情況下可接受的資料遺失數量。 | 備份解決方案在其可接受的 RPO 有寬廣的變化性。虛擬機器備份的 RPO 通常為 1 天，而資料庫備份的 RPO 只需 15 分鐘。 | 災害復原解決方案的 RPO 很低。DR 複本可以落後幾秒鐘或幾分鐘的時間。 |
| 復原時間目標 (RTO) | 完成復原或還原所需的時間量。 | 由於 RPO 較大，備份解決方案需要處理的資料量通常更多，這會導致 RTO 較長。例如，根據從異地傳輸磁帶所需的時間，從磁帶還原資料可能需要數天的時間。 | 因為災害復原解決方案與來源比較能保持同步，所以其 RTO 比較小。需要處理的變更也比較少。 |
| 保留 | 資料需要儲存多久？ | 在需要作業復原的情況下 (資料損毀、不小心刪除檔案、作業系統失敗)，備份資料通常會保留 30 天或更短。<br>從法規遵循觀點來看，資料可能需要儲存達數個月或甚至數年。在這類情況下，最理想的方式是將備份資料封存。 | 災害復原只需要作業復原資料，通常是幾個小時或最多一天。因為 DR 解決方案中使用的細部資料擷取，所以不建議使用 DR 資料進行長期保留。 |

## 後續步驟

嘗試簡單的 Azure 備份。如需指示，請參閱下列教學課程︰

- [試用 Azure 備份](backup-try-azure-backup-in-10-mins.md)
- [試用 Azure VM 備份](backup-azure-vms-first-look.md)

這些教學課程可協助您快速備份，因此只會顯示備份資料的最直接路徑。如需您要執行之備份類型的詳細資訊，請參閱︰

- [備份 Windows 電腦](backup-configure-vault.md)
- [備份應用程式工作負載](backup-azure-microsoft-azure-backup.md)
- [備份 Azure IaaS VM](backup-azure-vms-prepare.md)



[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!----HONumber=AcomDC_0323_2016-->