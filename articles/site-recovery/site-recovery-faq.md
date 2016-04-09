<properties 
	pageTitle="Site Recovery：常見問題集 | Microsoft Azure" 
	description="本文回答 Azure Site Recovery 的相關常見問題。"
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
	ms.date="03/27/2016"
	ms.author="raynew"/>


# Azure Site Recovery：常見問題集 (FAQ)

閱讀本文，以檢閱有關 Azure Site Recovery 服務的常見問題集。

如果您在閱讀本文後仍有問題，可以將問題張貼在本文底部或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。

## 一般

### Site Recovery 的功能是什麼？

Site Recovery 可藉由協調及自動執行內部部署虛擬機器與實體伺服器對 Azure 或次要資料中心的複寫，為您的商務持續性與災害復原 (BCDR) 做出貢獻。[深入了解](site-recovery-overview.md)。

### Site Recovery 可以複寫什麼？

- **Hyper-V 虛擬機器**：Site Recovery 可以複寫在支援的 Hyper-V VM 上執行的所有工作負載。 
- **實體伺服器**：Site Recovery 可以複寫在支援的 Windows/Linux 實體伺服器上執行的所有工作負載。
- **VMware 虛擬機器**：Site Recovery 可以複寫在支援的 VMware VM 上執行的所有工作負載。


### 我在 Hyper-V 中需要什麼？

對於 Hyper-V 主機伺服器，您的需求視部署案例而定。請查看下列主題中的 Hyper-V 先決條件：

- [將 Hyper-V VM (不使用 VMM) 複寫至 Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [將 Hyper-V VM (使用 VMM) 複寫至 Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [將 Hyper-V VM 複寫至次要資料中心](site-recovery-vmm-to-vmm.md#before-you-start)

若為在 Hyper-V 主機伺服器上執行的客體︰

- 如果您要複寫至次要網站，請參閱 [Hyper-V VM 支援的客體作業系統](https://technet.microsoft.com/library/mt126277.aspx)。
- 如果您要複寫至 Azure，則 Site Recovery 支援 [Azure 支援的](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)相同客體作業系統。


### 如果 Hyper-V 在用戶端作業系統上執行，我可以保護 VM 嗎？

不能，VM 所在的 Hyper-V 主機伺服器必須在支援的 Windows 伺服器機器上執行。如果您需要保護用戶端電腦，可以用實體機器的形式將其複寫至 [Azure](site-recovery-vmware-to-azure-classic.md) 或[次要資料中心](site-recovery-vmware-to-vmware.md)。


### 我可以使用 Site Recovery 來保護哪些工作負載？

Site Recovery 可複寫在的支援 VM 或實體伺服器上執行的所有工作負載。Site Recovery 支援應用程式感知複寫，可讓應用程式復原為智慧型狀態。除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，它還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。[深入了解](site-recovery-workload.md)工作負載保護。


### 我的 Hyper-V 主機必須位於 System Center VMM 雲端，才能使用 Site Recovery 進行複寫嗎？ 

如果您想要複寫至次要資料中心，Hyper-V VM 就必須位於 VMM 雲端的 Hyper-V 主機伺服器中。如果您想要複寫至 Azure，則可以複寫 Hyper-V 主機伺服器上的 VM，不論您是否使用 VMM 雲端。[深入了解](site-recovery-hyper-v-site-to-azure.md)。

### 如果我只有一部 VMM 伺服器，可以部署 Site Recovery 搭配 VMM 嗎？ 

是。您可以將 VMM 雲端 Hyper-V 伺服器中的 VM 複寫至 Azure，或是在同一部伺服器上的 VMM 雲端之間進行複寫。若要在內部部署的單位間進行複寫，建議您同時在主要與次要網站中具有 VMM 伺服器。[閱讀更多資訊](site-recovery-single-vmm.md)

### 我可以保護哪些實體伺服器？

您可以將執行支援的 Windows 或 Linux 作業系統的實體伺服器複寫至 Azure 或次要網站來進行保護。[了解](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) Azure 或次要網站複寫的作業系統需求。請注意，發生容錯移轉時，複寫到 Azure 儲存體的實體伺服器會在 Azure 中以 VM 的方式執行。當您從 Azure 容錯回復至內部部署網站時，目前不支援容錯回復至實體伺服器。您只可以容錯回復至在 VMware 上執行的虛擬機器。

### 我可以保護哪些 VMware VM？

若要保護 VMware VM，您需要一個 vSphere Hypervisor 以及幾個執行 VMware 工具的虛擬機器。此外，建議您擁有 VMware vCenter 伺服器以便管理 Hypervisor。[深入了解](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)將 VMware 伺服器和 VM 複寫至 Azure 或次要網站的確切需求。

### 我可以使用 Site Recovery 來管理分公司的災害復原嗎？

是。當您使用 Site Recovery 來協調分公司中的複寫與容錯移轉時，您可以在一個位置集中協調與檢視所有分公司的工作負載。您不需要造訪分公司，就可以從總公司輕鬆執行所有分公司的容錯移轉及管理災害復原。

## 安全性


### 複寫資料會傳送到 Site Recovery 服務嗎？

否，Site Recovery 不會攔截複寫的資料，也不會擁有您虛擬機器或實體伺服器上執行哪些項目的任何相關資訊。

內部部署 Hyper-V 主機、VMware Hypervisor 或實體伺服器，會與 Azure 儲存體或次要站台交換複寫資料。Site Recovery 並不具有攔截該資料的能力。只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。

Site Recovery 已經過 ISO 27001:2005 認證，並且正在完成其 HIPAA、DPA 及 FedRAMP JAB 評定程序。

### 為了遵循法規，我們甚至連內部部署環境中的中繼資料也必須保留在相同的地理區域內。Site Recovery 能做到這點嗎？

是。當您在某個區域中建立 Site Recovery 保存庫時，我們會確保我們啟用及協調複寫與容錯移轉時所需的一切中繼資料都會保留在該區域地理界限內。

### Site Recovery 會將複寫加密嗎？
在內部部署站台之間複寫虛擬機器和實體伺服器時，支援傳輸中加密。在將虛擬機器和實體伺服器複寫至 Azure 時，則同時支援傳輸中加密和靜態加密 (在 Azure 中)。



## 複寫

### 將虛擬機器複寫至 Azure 有任何先決條件嗎？

您想要複寫至 Azure 的虛擬機器應該要符合 [Azure 需求](site-recovery-best-practices.md#azure-virtual-machine-requirements)。

### 我可以將 Hyper-V 第 2 代虛擬機器複寫至 Azure 嗎？

是。Site Recovery 會在容錯移轉時將 VM 從第 2 代轉換成第 1 代。在容錯回復時，VM 會轉換回第 2 代。[閱讀更多資訊](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。

### 如果複寫至 Azure，我要如何支付 Azure VM 費用？ 

在一般複寫期間，就會將資料複寫至異地備援的 Azure 儲存體，您不需要支付任何 Azure IaaS 虛擬機器費用。當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器，之後就會針對您在 Azure 中取用的運算資源進行計費。

### 有可以用來將 Site Recovery 工作流程自動化的 SDK 嗎？

是。您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。深入了解[使用 PowerShell 和 Azure Resource Manager 來部署 Site Recovery](site-recovery-deploy-with-powershell-resource-manager.md)。

### 如果要複寫至 Azure，我需要哪一種儲存體帳戶？

您將需要一個搭配[標準異地備援儲存體](../storage/storage-introduction.md#replication-for-durability-and-high-availability)的儲存體帳戶。目前不支援進階儲存體。

### 我可以多久複寫一次資料？

- Hyper-V：在 Windows Server 2012 R2 上執行的 Hyper-V VM 可以每隔 30 秒、5 分鐘或 15 分鐘複寫一次。如果您已設定 SAN 複寫，則複寫將會是同步的。
- VMware 與實體伺服器：複寫頻率在此處並不相關。複寫將會是連續的。 

### 我可以將複寫從現有的復原網站延伸到另一個復原網站嗎？

不支援延伸的或鏈結的複寫。請[傳送有關這項功能的意見反應給我們](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/)。


### 我可以在第一次複寫至 Azure 時進行離線複寫嗎？ 

不支援此做法。請[傳送有關這項功能的意見反應給我們](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/)。

### 我可以從複寫中排除特定的磁碟嗎？

不支援此做法。請[傳送有關這項功能的意見反應給我們](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/)。

### 我可以使用動態磁碟來複寫虛擬機器嗎？

複寫 Hyper-V 虛擬機器時，支援使用動態磁碟。如果您是使用[增強部署](site-recovery-vmware-to-azure-classic.md)，則複寫 VMware VM 和實體機器時同樣支援。請注意，作業系統磁碟必須是基本磁碟。

### 我可以調節為 Hyper-V 複寫流量配置的頻寬嗎？
- 如果您是在兩個內部部署站台上的 Hyper-V VM 之間進行複寫，則您可以使用 Windows QoS。以下是一個簡單的指令碼： 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- 如果您是將 Hyper-V VM 複寫至 Azure，您可以使用以下的範例 PowerShell Cmdlet 來設定節流：

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [深入了解](https://support.microsoft.com/zh-TW/kb/3056159) Hyper-V 流量節流。
- [深入了解](site-recovery-vmware-to-azure-classic.md#capacity-planning) VMware 至 Azure 複寫的節流。


## 容錯移轉

### 如何在容錯移轉至 Azure 之後存取 Azure 虛擬機器？ 

您可以透過安全的網際網路連線、透過站台對站台 VPN 或透過 Azure ExpressRoute 存取 Azure VM。透過 VPN 連線的通訊會通向 VM 所在的 Azure 網路上的內部連接埠。透過網際網路的通訊會對應到 Azure 雲端服務上 VM 的公用端點。

### 在容錯移轉到 Azure 時，Azure 如何確定我的資料具有復原能力？

Azure 是針對復原能力而設計的。Site Recovery 已經設計成可在需要時，容錯移轉到符合 Azure SLA 的次要 Azure 資料中心。當此情況發生時，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。

### 如果我在兩個站台之間進行複寫，當我的主要站台發生意外中斷時，會發生什麼情況？

您可以從次要站台觸發非計劃性容錯移轉。Site Recovery 不需要來自主要站台的連線即可執行容錯移轉。

### 容錯移轉是自動發生的嗎？

容錯移轉並非自動發生。您只要在入口網站中按一下滑鼠即可起始容錯移轉，或是使用 [Site Recovery PowerShell Cmdlet](https://msdn.microsoft.com/library/dn850420.aspx) 來觸發容錯移轉。容錯回復是 Site Recovery 入口網站中的一系列動作。

若要自動執行容錯移轉，您可以使用內部部署 Orchestrator 或 Operations Manager 來偵測虛擬機器失敗，然後使用 SDK 來觸發容錯移轉。


##
### 我是服務提供者。Site Recovery 適用於專用或共用的基礎結構模型嗎？
是，Site Recovery 同時支援專用與共用的基礎結構模型。

### 對於服務提供者而言，租用戶的身分識別是否會與 Site Recovery 服務共用？
否。您的租用戶不需要存取 Site Recovery 入口網站。只有服務提供者系統管理員會與入口網站互動。


### 我租用戶的應用程式資料會傳送到 Azure 嗎？

在服務提供者擁有的站台之間進行複寫時，永遠不會將應用程式資料傳送到 Azure。資料會在傳輸中加密，並且會在服務提供者站台之間直接進行複寫。

如果您是複寫至 Azure，應用程式資料就會傳送到 Azure 儲存體而不是 Site Recovery 服務。資料會在傳輸中加密並在 Azure 中繼續維持加密狀態。

### 我的租用戶會收到來自 Azure 服務的帳單嗎？

否。Azure 的計費關係是直接與服務提供者相關。服務提供者負責為其租用戶產生特定的帳單。

### 如果我複寫至 Azure，我們需要在 Azure 中隨時執行虛擬機器嗎？

否。資料會複寫至您訂用帳戶中的異地備援 Azure 儲存體帳戶。當您執行測試容錯移轉 (DR 演練) 或實際容錯移轉時，Site Recovery 會在您的訂用帳戶中自動建立虛擬機器。

### 當我複寫至 Azure 時，您會確保提供租用戶層級的隔離嗎？

是。

### 目前支援哪些平台？

我們支援 Azure 套件、雲端平台系統及 System Center (2012 及更新版本) 的部署。若要深入了解 Azure 套件整合，請參閱[設定虛擬機器的保護](https://technet.microsoft.com/library/dn850370.aspx)。

### 您支援單一 Azure 套件與單一 VMM 伺服器部署嗎？

是，您可以複寫 Hyper-V 虛擬機器與 Azure，或是在服務提供者站台之間進行複寫。請注意，如果您是在服務提供者站台之間進行複寫，將無法使用 Azure Runbook 整合。


## 後續步驟

- 參閱 [Site Recovery 概觀](site-recovery-overview.md)
- 了解 [Site Recovery 架構](site-recovery-components.md)  

<!---HONumber=AcomDC_0330_2016-->