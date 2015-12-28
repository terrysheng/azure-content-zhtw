<properties
	pageTitle="在 Azure 中規劃 VM 備份基礎結構 | Microsoft Azure"
	description="在 Azure 中規劃 VM 備份基礎結構的重要考量"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# 在 Azure 中規劃 VM 備份基礎結構
本文涵蓋在規劃虛擬機器 (VM) 備份基礎結構時，您應該特別注意的重要考量。若您已經[準備好您的環境](backup-azure-vms-prepare.md)，這是在開始[備份您的 VM](backup-azure-vms.md)之前的下一步。如果您需要 Azure 虛擬機器的詳細資訊，請參閱[虛擬機器文件](https://azure.microsoft.com/documentation/services/virtual-machines/)。

## Azure 如何備份虛擬機器？
Azure 備份服務在排定的時間開始備份工作時，會觸發備份擴充功能以建立時間點快照集。此快照是與磁碟區陰影複製服務 (VSS) 搭配擷取，以在而無需將虛擬機器中磁碟關閉的狀況下，取得所有磁碟一致的快照集。

擷取快照集之後，Azure 備份服務會將資料傳輸至備份保存庫。為了讓備份程序更有效率，服務會識別上次備份之後變更的資料區塊，並只傳輸這些區塊。

![Azure 虛擬機器備份架構](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

資料傳輸完畢後，系統會移除快照集並建立復原點。

### 資料一致性
備份與還原業務重要資料之所以複雜，原因在於資料必須在產生資料的應用程式執行時進行備份。為因應這種情形，Azure Backup 使用 VSS 提供Microsoft 工作負載的應用程式一致備份，以確保資料正確地寫入儲存體。

>[AZURE.NOTE]Linux 虛擬機器則只能進行檔案一致的備份，因為 Linux 沒有相當於 VSS 的平台。

Azure 備份會在 Windows VM 上執行 VSS 完整備份 (深入了解 [VSS 完整備份](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx))。若要啟用 VSS 複製備份，需要在 VM 上設定下列登錄機碼。

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


此表說明一致性的類型，以及它們在 Azure VM 備份和還原程序期間發生的條件。

| 一致性 | 以 VSS 基礎 | 說明和詳細資料 |
|-------------|-----------|---------|
| 應用程式一致性 | 是 | 這是處理 Microsoft 工作負載的理想一致性類型，因其可確保：<ol><li>VM 能夠*開機*。<li>*不會毀損*。<li>*不會遺失資料*。<li> 資料會與使用資料的應用程式保持一致，方法是在備份期間使用 VSS 將應用程式納入。</ol> 大部分 Microsoft 工作負載都有 VSS 寫入器，負責執行與資料一致性有關的工作負載特定動作。例如，Microsoft SQL Server 的 VSS 寫入器可確保正確寫入交易記錄檔和資料庫。<br><br> 對於 Azure VM 備份，取得應用程式一致復原點表示備份擴充功能可以叫用 VSS 工作流程，並在取得 VM 快照之前*正確*完成。當然，這表示也都已叫用 Azure VM 中所有應用程式的 VSS 寫入器。(<br><br>了解 [VSS 的基本知識](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx)深入[運作方式](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)的詳細資料。) |
| 檔案系統一致性 | 是 - 適用於 Windows 電腦 | 有兩種情況，復原點具有*檔案系統一致性*：<ul><li>在 Azure 中備份 Linux VM，因為 Linux 沒有相當於 VSS 的平台。<li>在 Azure 中備份 Windows VM 期間 VSS 失敗。</li></ul> 在這兩種情況中，可採取的最佳作法是確保：<ol><li> VM *開機*。<li>*不會損毀*。<li>*不會遺失資料*。</ol> 應用程式需要在還原的資料上實作自己的「修正」機制。|
| 損毀一致性 | 否 | 此狀況相當於虛擬機器「當機」(經由軟體重設或硬體重設)。這通常發生於 Azure 虛擬機器在備份期間關閉時。對於 Azure 虛擬機器備份，取得損毀一致復原點表示 Azure 備份不保證儲存媒體上的資料一致性 -- 無論從作業系統還是應用程式的觀點來說都一樣。只有備份時已存在磁碟上的資料才會擷取並備份。<br/> <br/> 在大部分情況下作業系統會開機，但並不保證必然如此。隨後通常是執行磁碟檢查程序 (如 chkdsk) 以修正任何損毀錯誤。記憶體中任何未完全排清到磁碟的資料或寫入將會遺失。如果需要進行資料復原，應用程式通常會接著進行其本身的驗證機制。<br><br>例如，如果交易記錄中有項目不存在資料庫中，則資料庫軟體會執行復原，直到資料變成一致為止。當資料跨多個虛擬磁碟時 (例如跨距磁碟區)，損毀一致復原點不保證資料的正確性。|


## 效能與資源使用率
如同內部部署的備份軟體一樣，在 Azure 中備份 VM 也需要規劃容量和資源使用率。[Azure 儲存體限制](azure-subscription-service-limits.md#storage-limits)會定義如何建構 VM 部署，以發揮最大效能並盡可能不影響執行中的工作負載。

有兩個主要的 Azure 儲存體限制會影響備份效能：

- 每一儲存體帳戶的輸出上限
- 每一儲存體帳戶的總要求率

### 儲存體帳戶限制
從客戶儲存體帳戶中複製備份資料時，這些資料會計入儲存體帳戶的每秒輸入/輸出作業 (IOPS) 和輸出 (儲存體輸送量) 度量內。同時，虛擬機器也會執行並耗用 IOPS 和輸送量。目標是要確保總流量 (備份與虛擬機器) 不超過儲存體帳戶限制。

### 磁碟數量
備份程序會竭盡所能耗用資源，目標是盡速完成備份。不過，所有 I/O 作業都受限於*單一 Blob 的目標輸送量*，上限為每秒 60MB。為了加速備份程序，系統會嘗試*平行*備份 VM 的每個磁碟。所以如果 VM 有 4 個磁碟，Azure 備份便會嘗試平行備份全部 4 個磁碟。因此，判斷從客戶儲存體帳戶輸出的備份流量時，唯一最重要的因素就是從儲存體帳戶進行備份的**磁碟數量**。

### 備份排程
影響效能的另一因素是**備份排程**。如果您將所有 VM 設定為同時備份，則*平行*備份的磁碟數目會增加，因為 Azure 備份會嘗試盡可能備份更多磁碟。因此，為了減少從儲存體帳戶輸出的備份流量，一種作法是確保在不同的時段備份 VM，時間不要重疊。

## 容量規劃
綜合考量所有這些因素，即可知需要妥善規劃儲存體帳戶的使用方式。下載 [VM 備份容量規劃 Excel 試算表](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33)，以了解磁碟和備份排程選項所產生的影響。

### 備份輸送量
針對每個要備份的磁碟，Azure 備份會讀取磁碟上的區塊，只儲存已變更的資料 (增量備份)。此表能顯示您可以預期從 Azure 備份輸出的平均輸送量值。您可利用此值預估備份特定大小的磁碟將花費的時間。

| 備份作業 | 最佳輸送量 |
| ---------------- | ---------- |
| 初始備份 | 160 Mbps |
| 增量備份 (DR) | 640 Mbps <br><br> 如果磁碟上有很多分散的變換需要備份，這個輸送量會大幅下降。 |

### VM 備份時間總計
雖然大部分的備份時間都花費在讀取和複製資料，但備份 VM 所需的全部時間還包含其他作業：

- [安裝或更新備份擴充功能](backup-azure-vms.md#offline-vms)所需要的時間。
- 快照時間，即觸發快照所需的時間。快照會在接近排定的備份時間觸發。
- 佇列等候時間。因為備份服務正在處理多個客戶的備份，所以從快照集複製備份資料到 Azure 備份保存庫可能不會立即啟動。在尖峰負載時，等候時間會因為正在處理的備份數目而長達 8 小時。不過，針對每日備份原則，總計 VM 備份時間將會小於 24 小時。

## 資料加密

Azure 備份不會在備份過程中加密資料。不過，您可以在 VM 中加密資料，並順暢地備份受保護的資料 (深入了解[加密資料的備份](backup-azure-vms-encryption.md))。


## 受保護的執行個體如何計算？
透過 Azure 備份進行備份的 Azure 虛擬機器受限於 [Azure 備份價格](http://azure.microsoft.com/pricing/details/backup/)。受保護的執行個體是根據虛擬機器的*實際*大小計算，也就是不包含「資源磁碟」，虛擬機器裡所有資料的總和。

我們*不會*以每個連接到虛擬機器可支援的資料磁碟之大小上限計費，而是根據實際儲存在資料磁碟的資料。同樣地，備份儲存體帳單也是根據 Azure 備份所儲存的資料計費，也就是每個復原點所儲存的實際資料總和。

例如，A2 標準大小的虛擬機器擁有兩個額外資料磁碟，每個大小上限為 1 TB。下表提供上述每個磁碟實際儲存的資料：

|磁碟類型|大小上限|實際儲存的資料|
|---------|--------|------|
| 作業系統磁碟 | 1023 GB | 17 GB |
| 本機磁碟/資源磁碟 | 135 GB | 5 GB (未包含備份) |
| 資料磁碟 1 |	1023 GB | 30 GB |
| 資料磁碟 2 | 1023 GB | 0 GB |

在此案例中，虛擬機器的*實際*容量為 17 GB + 30 GB + 0 GB = 47 GB。這將是受保護的執行個體大小，每月帳單的計費基礎。當虛擬機器的資料量增加時，用於計費之受保護的執行個體大小也會隨之變更。

第一次成功備份完成後才會開始計費。儲存體和受保護的執行個體也會在此同時開始計費。只要虛擬機器使用 Azure 備份儲存備份資料，就會繼續計費。如果保留備份資料，執行 [停止保護] 作業將不會停止計費。

指定的虛擬機器只有在停止保護*和*刪除全部備份資料後才會停止計費。無使用中之備份工作時 (已停止保護)﹐受保護的執行個體大小將根據上一次成功備份時的虛擬機器大小，進行每月計費。

## 有疑問嗎？
如果您有問題，或希望我們加入任何功能，請[傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## 後續步驟

- [備份虛擬機器](backup-azure-vms.md)
- [管理虛擬機器備份](backup-azure-manage-vms.md)
- [還原虛擬機器](backup-azure-restore-vms.md)
- [疑難排解 VM 備份問題](backup-azure-vms-troubleshoot.md)

<!---HONumber=AcomDC_1217_2015-->