<properties
	pageTitle="Azure Site Recovery 中用於在內部部署資料中心之間複寫 Hyper-V 虛擬機器的對應儲存體 | Microsoft Azure"
	description="準備儲存體對應以使用 Azure Site Recovery 在兩個內部部署資料中心之間複寫 Hyper-V 虛擬機器。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/14/2015"
	ms.author="raynew"/>


# 準備儲存體對應以使用 Azure Site Recovery 在兩個內部部署資料中心之間複寫 Hyper-V 虛擬機器


Azure Site Recovery 可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原，為您的商務持續性與嚴重損壞修復 (BCDR) 策略做出貢獻。本文說明儲存體對應，它可以協助您在使用 Site Recovery 於兩個內部部署資料中心之間複寫 Hyper-V 虛擬機器時，將儲存體做最佳利用。

如果您在閱讀本文後有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (英文)。

## 概觀

當您使用 Hyper-V 複本或 SAN 複寫將位於 VMM 雲端的 Hyper-V 虛擬機器從主要資料中心複寫到次要資料中心時，儲存體對應才會有關係，如下所示：


- **使用 Hyper-V 複本進行內部部署至內部部署複寫)**—您可以透過對應來源和目標 VMM 伺服器上的儲存體分類來設定儲存體對應，以執行下列操作：

	- **識別複本虛擬機器的目標儲存體** — 虛擬機器將會複寫到您選擇的儲存體目標 (SMB 共用或叢集共用磁碟區 (CSV))。
	- **複本虛擬機器放置** — 儲存體對應用於以最佳方式將複本虛擬機器放在 Hyper-V 主機伺服器上。複本虛擬機器將會放在可以存取對應的儲存體分類的主機上。
	- **無儲存體對應** — 如果您沒有設定儲存體對應，虛擬機器將會複寫至與複本虛擬機器相關聯之 Hyper-V 主機伺服器上指定的預設儲存體位置。

- **使用 SAN 進行內部部署至內部部署複寫**—您可以透過在來源和目標 VMM 伺服器上對應儲存體陣列集區以設定儲存體對應。
	- **指定存放集區**—指定要從主要集區接收複寫資料的次要存放集區。
	- **識別目標存放集區**—可確保來源複寫群組中的 LUN 會複寫到您所選擇的對應目標存放集區。

## 設定 Hyper-V 複寫的儲存體分類

當您使用 Hyper-V 複本進行 Site Recovery 複寫時，您會在來源與目標 VMM 伺服器，或是在單一 VMM 伺服器 (如果兩個網站受到相同的 VMM 伺服器管理) 上的儲存體分類之間對應。請注意：

- 正確設定對應並啟用複寫時，主要位置的虛擬機器虛擬硬碟將會複寫至對應目標位置的儲存體中。
- 儲存體分類必須可用於來源與目標雲端中的主機群組。
- 分類不需要具有相同類型的儲存體。例如，您可以將包含 SMB 共用的來源分類對應至包含 CSV 的目標分類。
- 若要深入了解，請參閱[如何在 VMM 中建立儲存體分類](https://technet.microsoft.com/library/gg610685.aspx)。

## 範例

如果在 VMM 中正確設定分類，當您在儲存體對應期間選取來源和目標 VMM 伺服器時，將會顯示來源與目標分類。以下是儲存體檔案共用和分類的範例，以供擁有兩個位置 (紐約和芝加哥) 的組織使用。

**位置** | **VMM 伺服器** | **檔案共用 (來源)** | **分類 (來源)** | **對應至** | **檔案共用 (目標)**
---|---|--- |---|---|---
紐約 | VMM\_Source| SourceShare1 | GOLD | GOLD\_TARGET | TargetShare1
 | | SourceShare2 | SILVER | SILVER\_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE\_TARGET | TargetShare3
芝加哥 | VMM\_Target | | GOLD\_TARGET | 未對應 |
| | | SILVER\_TARGET | 未對應 |
 | | | BRONZE\_TARGET | 未對應

您要在 Site Recovery 入口網站的 [資源] 頁面中的 [伺服器儲存體] 索引標籤上進行設定。

![設定儲存體對應](./media/site-recovery-storage-mapping/storage-mapping1.png)

在此範例中：- 為 GOLD 儲存體 (SourceShare1) 上的任何虛擬機器建立複本虛擬機器時，它將會複寫至 GOLD\_TARGET 儲存體 (TargetShare1)。- 為 SILVER 儲存體 (SourceShare2) 上的任何虛擬機器建立複本虛擬機器時，它將會複寫至 SILVER\_TARGET (TargetShare2) 儲存體，依此類推。

VMM 中的實際檔案共用及其獲指派的分類會顯示在下一個螢幕擷取畫面中。

![VMM 中的儲存體分類](./media/site-recovery-storage-mapping/storage-mapping2.png)

## 多個儲存體位置

如果將目標分類指派給多個 SMB 共用或 CSV，將會在虛擬機器受到保護時，自動選取最佳的儲存體位置。如果沒有適合的目標儲存體可搭配指定的分類使用，則會使用 Hyper-V 主機上指定的預設儲存體位置來放置複本虛擬硬碟。

下表顯示儲存體分類和叢集共用磁碟區在我們的範例中設定的方式。

**位置** | **分類** | **相關聯的儲存體**
---|---|---
紐約 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p>
 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
芝加哥 | GOLD\_TARGET | <p>C:\\ClusterStorage\\TargetVolume1</p><p>\\FileServer\\TargetShare1</p>
 | SILVER\_TARGET| <p>C:\\ClusterStorage\\TargetVolume2</p><p>\\FileServer\\TargetShare2</p>

下表摘要說明您在此範例環境中為虛擬機器 (VM1 - VM5) 啟用保護時的行為。

**虛擬機器** | **來源儲存體** | **來源分類** | **對應的目標儲存體**
---|---|---|---
VM1 | C:\\ClusterStorage\\SourceVolume1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\\FileServer\\SourceShare1</p><p>Both GOLD\_TARGET</p>
VM2 | \\FileServer\\SourceShare1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p> <p>Both GOLD\_TARGET</p>
VM3 | C:\\ClusterStorage\\SourceVolume2 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
VM4 | \\FileServer\\SourceShare2 | SILVER |<p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p><p>Both SILVER\_TARGET</p>
VM5 | C:\\ClusterStorage\\SourceVolume3 | N/A | 沒有對應，因此會使用 Hyper-V 主機的預設儲存體位置

## 後續步驟

既然您已經更了解儲存體對應，[請準備開始部署 Azure Site Recovery](site-recovery-best-practices.md)。

<!---HONumber=AcomDC_1217_2015-->