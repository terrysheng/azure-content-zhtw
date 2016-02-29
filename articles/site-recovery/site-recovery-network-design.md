<properties
	pageTitle="VMM 和 Site Recovery 的部署考量 | Microsoft Azure"
	description="這篇文章討論整合 VMM 與 Azure Site Recovery 的實用設計考量"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  VMM 和 Site Recovery 的部署考量

Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。機器可以複寫至 Azure，或次要的內部部署資料中心。如需快速概觀，請參閱[什麼是 Azure Site Recovery？](site-recovery-overview.md)。


## 概觀

這篇文章協助您設計和實作一套包含 System Center VMM 和 Azure Site Recovery 的 BCDR 方案和基礎結構。

BCDR 策略的目的是讓您的商務應用程式繼續執行，並還原失敗的工作負載與服務，以便組織可以快速地恢復正常運作。開發災害復原策略有相當的挑戰性，因為難以預測無法預料的事件，以及實作足夠的保護以避免發生大規模的失敗，需要很高的成本。Site Recovery 可協助您保護，並從您的主要資料中心容錯移轉至次要資料中心 (或 Azure)，方法是一開始複製 (複寫) 主要資料，然後定期更新複本。

作為 BCDR 規劃的重要部分，您需要定義您的復原時間目標 (RTO) 和復原點目標 (RPO)，讓您可以使您的組織資料盡快恢復上線 (具有低 RTO) 與最少的資料遺失 (低 RPO)。您組織中設計的網路是 RTO 和 RPO 目標的潛在瓶頸，因此規劃穩定的設計可協助避免發生此瓶頸。


如果您要部署 Site Recovery 來複寫 VMM 雲端中的 Hyper-V VM，您必須考慮 VMM 如何整合到 Site Recovery 複寫及容錯移轉策略。

## 整合獨立的 VMM 伺服器

在此拓撲中，您將在主要站台中的虛擬機器上部署 VMM 伺服器，然後使用站台復原和 Hyper-V 複本，將此虛擬機器複寫至次要站台。您可能考量在相同的虛擬機器上安裝 VMM 伺服器和其支援的 SQL Server，可以縮短停機時間，因為只有一個 VM 必須具現化。當 VMM 服務使用的是遠端 SQL Server，您必須先復原 SQL Server 執行個體，然後再復原 VMM 伺服器。

若要使用 HYPER-V 複本，在 VM 上部署單一 VMM：

1. 在已安裝 SQL Server 的 VM 上設定 VMM。
2. 將要管理的主機新增到此 VMM 伺服器上的雲端。
3. 登入 Azure 入口網站，然後設定雲端進行保護。
4. 對需要受到 VMM 伺服器保護的所有 VM 啟用複寫。
5. 移至 [HYPER-V 管理員] 主控台，選擇 HYPER-V 複本，然後啟用 VMM VM 上的複寫。
6. 確定 VMM VM 不會新增至受到 ASR 服務保護的雲端，以便 ASR 不會覆寫 HYPER-V 複寫設定。

發生災害時，可依下列方式復原工作負載：

1. 使用 HYPER-V 管理員，將複本 VMM VM 容錯移轉至復原站台。
2. 在復原了 VMM VM 之後，使用者可以從次要站台登入 HYPER-V 復原管理員。
3. 在非計劃性的容錯移轉完成之後，使用者可在主要站台上存取所有資源。
4. 請注意，VMM VM 將必須手動容錯移轉至次要站台，然後工作負載才能進行容錯移轉。


### 整合 VMM 叢集


[在叢集中部署 VMM](https://technet.microsoft.com/library/gg610675.aspx) 提供高可用性和保護，以防範硬體容錯移轉。如果您使用站台復原，部署您的 VMM 叢集，請注意：

- 應該在延伸的叢集中跨地理上不同位置的站台部署 VMM 伺服器。
- VMM 所使用的 SQL Server 資料庫應該使用複本位於次要站台的 SQL Server AlwaysOn 可用性群組保護。
- 如果發生災害，VMM 伺服器和其對應的 SQL Server 將自動容錯移轉至復原站台。然後，您可以使用站台復原，容錯移轉工作負載。

	![延伸的 VMM 叢集](./media/site-recovery-network-design/network-design1.png)


## 後續步驟

[了解](site-recovery-network-mapping.md)站台復原如何對應來源及目標網路。

<!---HONumber=AcomDC_0218_2016-->