<properties
	pageTitle="什麼是 Site Recovery？ | Microsoft Azure" 
	description="提供 Azure Site Recovery 服務的概觀，並說明可以如何部署服務。" 
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
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  什麼是 Site Recovery？

歡迎使用 Azure Site Recovery！ 請以此文章做為開端，快速地概略了解 Site Recovery 服務以及其如何有助於商務持續性和災害復原 (BCDR) 策略。

Azure 建立和處理資源的部署模型有二種：[資源管理員和傳統](../resource-manager-deployment-model.md)。本文同時適用於這兩種模型。Microsoft 建議讓大部分的新部署使用資源管理員模式。

## 概觀

組織的 BCDR 策略的其中一個重要部分是，找出在計劃中和非計劃中的中斷發生時讓企業工作負載和應用程式保持啟動並執行的方法。

Site Recovery 可協助做到這點，因為它能協調工作負載和應用程式的複寫、容錯移轉及復原，因此能夠在主要位置發生故障時透過次要位置來提供工作負載和應用程式。

## 為什麼要使用 Site Recovery？ 

以下是 Site Recovery 可以為企業提供的協助︰

- 簡化 BCDR 策略 - Site Recovery 可讓您從單一位置輕鬆處理多個商務工作負載和應用程式的複寫、容錯移轉及復原。Site Recovery 會協調複寫和容錯移轉，但不會攔截應用程式資料或擁有任何相關資訊。
- 提供彈性的複寫 - 使用 Site Recovery，您就可以複寫 Hyper-V 虛擬機器、VMware 虛擬機器和 Windows/Linux 實體伺服器上執行的工作負載。 
- 簡單容錯移轉和復原 - Site Recovery 可提供測試用容錯移轉，既能支援災害復原演練，又不會影響生產環境。您也可以執行計劃性容錯移轉，因為是預期中的中斷，所以不會遺失任何資料；或是執行非計劃性容錯移轉，以在發生非未預期的災害時將資料損失減到最少 (取決於複寫頻率)。在容錯移轉之後，您可以容錯回復到主要站台。Site Recovery 提供了包含指令碼和 Azure 自動化作業手冊的復原計畫，以供您自訂多層式應用程式的容錯移轉和復原。 
- 消除次要資料中心 - 您可以複寫至次要內部部署站台或 Azure。使用 Azure 做為災害復原目的地可免除次要站台的維護成本與複雜度，而且複寫的資料會儲存在 Azure 儲存體，能夠具備其提供的所有恢復功能。
- 與現有 BCDR 技術整合 - Site Recovery 能夠與其他應用程式的 BCDR 功能搭配使用。例如，您可以使用 Site Recovery 保護公司工作負載的 SQL Server 後端，包括原生支援 SQL Server AlwaysOn 以便管理可用性群組的容錯移轉。 

## 我可以複寫哪些項目？

以下是 Site Recovery 可以複寫的項目摘要。

![內部部署至內部部署](./media/site-recovery-overview/asr-overview-graphic.png)

REPLICATE | 複寫來源 | 複寫目標 | 文章
---|---|---|---
VMware VM 上執行的工作負載 | 內部部署 VMware 伺服器 | Azure 儲存體 | [部署](site-recovery-vmware-to-azure-classic.md)
VMware VM 上執行的工作負載 | 內部部署 VMware 伺服器 | 次要 VMware 站台 | [部署](site-recovery-vmware-to-vmware.md) 
Hyper-V VM 上執行的工作負載 | VMM 雲端中的內部部署 Hyper-V 主機伺服器 | Azure 儲存體 | [部署](site-recovery-vmm-to-azure.md)
Hyper-V VM 上執行的工作負載 | VMM 雲端中的內部部署 Hyper-V 主機伺服器 | 次要 VMM 站台 | [部署](site-recovery-vmm-to-vmm.md)
Hyper-V VM 上執行的工作負載 | VMM 雲端中使用 SAN 存放裝置的內部部署 Hyper-V 主機伺服器| 具有 SAN 儲存體的次要 VMM 站台 | [部署](site-recovery-vmm-san.md)
Hyper-V VM 上執行的工作負載 | 內部部署 Hyper-V 站台 (無 VMM) | Azure 儲存體 | [部署](site-recovery-hyper-v-site-to-azure.md)
實體 Windows/Linux 伺服器上執行的工作負載 | 內部部署實體伺服器 | Azure 儲存體 | [部署](site-recovery-vmware-to-azure-classic.md)
實體 Windows/Linux 伺服器上執行的工作負載 | 內部部署實體伺服器 | 次要資料中心 | [部署](site-recovery-vmware-to-vmware.md) 


## 可以保護哪些工作負載？

Site Recovery 有助於應用程式感知 BCDR，讓工作負載和應用程式在中斷發生時繼續以一致的方式執行。Site Recovery 提供：

- 應用程式一致快照 - 使用單一或多層式架構應用程式的應用程式一致快照進行複寫。近乎同步複寫 - Hyper-V 的複寫頻率最低可為 30 秒，VMware 則可連續複寫。與 SQL Server AlwaysOn 整合 - 您可以在 Site Recovery 復原計畫中管理可用性群組的容錯移轉。 
- 彈性復原計畫 - 您可以使用外部指令碼、手動動作和 Azure 自動化 Runbook 建立並自訂復原計畫，讓您只要按一下就能復原整個應用程式堆疊。
- 自動化程式庫 - 豐富的 Azure 自動化程式庫提供了已可供生產環境使用的應用程式特定指令碼供您下載，並可整合到 Site Recovery。-**簡易網路管理**- Site Recovery 和 Azure 中的進階網路管理簡化了應用程式網路需求，包括保留 IP 位址、設定負載平衡器，以及整合 Azure 流量管理員以進行有效率的網路交換動作。


## 後續步驟

- 如需詳細資料，請閱讀 [Site Recovery 可以保護哪些工作負載？](site-recovery-workload.md)
- 若要深入了解 Site Recovery 架構，請閱讀 [Site Recovery 如何運作？](site-recovery-components.md)
 

<!---HONumber=AcomDC_0316_2016-->