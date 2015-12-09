<properties
	pageTitle="什麼是 Site Recovery？" 
	description="Azure Site Recovery 可將內部部署上虛擬機器和實體伺服器的複寫、容錯移轉及復原協調至 Azure 或次要內部部署站台。" 
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
	ms.date="11/29/2015" 
	ms.author="raynew"/>

#  什麼是 Site Recovery？

Site Recovery 是一項 Azure 服務，可藉由協調次要內部部署資料中心或 Azure 的內部部署伺服器和虛擬機器複寫，協助您制訂商務持續性和災害復原 (BCDR) 策略。Site Recovery 可處理複寫，您只要按一下滑鼠，就可以開始進行容錯移轉和復原。請閱讀[常見問題集](site-recovery-faq.md)中常見問題的清單


## 為什麼要使用 Site Recovery？ 
- **Simpler BCDR story**-Site Recovery 能夠讓您很輕鬆地處理複寫、容錯移轉及復原您的內部工作負載和應用程式。
- **彈性複寫**-您可以複寫內部部署伺服器、Hyper-V 虛擬機器和 VMware 虛擬機器。Site Recovery 使用智慧型複寫，只會複寫資料區塊 (而非整個 VHD) 來進行初始複寫。對於進行中的複寫，則只會複寫差異變更。Site Recovery 支援離線資料傳輸，並搭配 WAN 最佳工具使用。 
- **不需要次要資料中心**-Site Recovery 可以自動化資料中心之間的複寫，但它也藉由複寫至 Azure 來讓您能夠不需要建置次要網站位置。複寫的資料會儲存在 Azure 儲存體中，並具備所有提供的彈性。


## 部署案例

此資料表摘要說明 Site Recovery 支援的複寫案例。

**REPLICATE** | **複寫來源** | **複寫目標** | **文章**
---|---|---|---
VMware 虛擬機器 | 內部部署 VMware 伺服器 | Azure 儲存體 | [部署](site-recovery-vmware-to-azure.md)
實體 Windows/Linux 伺服器 | 內部部署實體伺服器 | Azure 儲存體 | [部署](site-recovery-vmware-to-azure.md)
Hyper-V 虛擬機器 | VMM 雲端中的內部部署 Hyper-V 主機伺服器 | Azure 儲存體 | [部署](site-recovery-vmm-to-azure.md)
Hyper-V 虛擬機器 | 內部部署 Hyper-V 網站 (一或多個 Hyper-V 主機伺服器) | Azure 儲存體 | [部署](site-recovery-hyper-v-site-to-azure.md)
內部部署 Hyper-V 虛擬機器| VMM 雲端中的內部部署 Hyper-V 主機伺服器 | 在次要資料中心之 VMM 雲端中的內部部署 Hyper-V 主機伺服器 | [部署](site-recovery-vmm-to-vmm)
Hyper-V 虛擬機器 | VMM 雲端中使用 SAN 存放裝置的內部部署 Hyper-V 主機伺服器| 在次要資料中心的 VMM 雲端中使用 SAN 存放裝置的內部部署 Hyper-V 主機伺服器 | [部署](site-recovery-vmm-san.md)
VMware 虛擬機器 | 內部部署 VMware 伺服器 | 執行 VMware 的次要資料中心 | [部署](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 
實體 Windows/Linux 伺服器 | 內部部署實體伺服器 | 次要資料中心 | [部署](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 

下列圖表中會摘要說明。

![內部部署至內部部署](./media/site-recovery-overview/asr-overview-graphic.png)

## 可以保護哪些工作負載？

Site Recovery 可協助您的應用裝置感知業務持續性。您可以使用 Site Recovery 來為 Windows 和協力廠商應用程式協調災害復原。此應用程式感知保護可提供：


- 針對 Hyper-V 僅需 30 秒即可複寫 RPO 之接近同步的複寫功能，及針對 VMware 的連續複寫功能，以符合最重要的應用程式需求。
- 適用於單一或多層式架構應用程式的應用程式一致性快照
- 整合 SQL Server AlwaysOn，並與其他應用程式層級的複寫技術合作，包括 Active Directory 複寫、Exchange DAG 和 Oracle 資料保護。
- 彈性修復計劃，只要按一下就能讓您復原整個應用程式堆疊，並包含外部指令碼或手動動作。 
- Site Recovery 和 Azure 中的進階網路管理可簡化應用程式的網路需求，包括保留 IP 位址，設定負載平衡器或低 RTO 網路轉換的 Azure 流量管理員整合。
- 豐富的自動化程式庫，提供已可用於生產環境，且可下載並已經與 Site Recovery 整合的應用程式特定指令碼。  


如需詳細資料，請閱讀 [Site Recovery 可以保護哪些工作負載？](site-recovery-workload.md)。


## 後續步驟

了解此概觀之後，請[深入了解](site-recovery-components.md) Site Recovery 架構的相關資訊。
 

<!---HONumber=AcomDC_1203_2015-->