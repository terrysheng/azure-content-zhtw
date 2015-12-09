<properties
	pageTitle="Azure Site Recovery 如何運作？"
	description="本文提供 Site Recovery 架構的概觀"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/29/2015"
	ms.author="raynew"/>

# Azure Site Recovery 如何運作？

## 本文內容

本文說明 Site Recovery 的基礎架構和讓它運作的元件。閱讀本文之後，若有任何問題，請造訪 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## 概觀

組織需要商務持續性和災害復原 (BCDR) 策略，決定應用程式、工作負載和資料如何在規劃與未規劃停機期間保持可用，並儘速復原到正常運作的情況。大部分的 BCDR 策略重點集中在保護商務資料安全且可復原，以及當發生災害時工作負載持續可用的解決方案。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。當您的主要位置發生故障時，您容錯移轉至次要網站，讓應用程式和工作負載保持可用。當它恢復正常作業時，容錯回復至您的主要位置。

Site Recovery 可以用在許多案例中，並可保護許多工作負載。

- **保護 VMware 虛擬機器**：您可以藉由將內部部署 VMware 虛擬機器複寫至 Azure 或次要資料中心，來保護它們。- **保護 Hyper-V VM**：您可以藉由將內部部署 Hyper-V 虛擬機器複寫至雲端 (Azure) 或次要資料中心，來保護它們。  
- **保護實體伺服器**：您可以藉由將執行 Windows 或 Linux 的實體機器複寫至 Azure 或次要資料中心，來保護它們。
- **移轉 VM**：您可以使用 Site Recovery 在區域之間移轉 Azure IaaS VM，或者將 AWS Windows 執行個體移轉至 Azure IaaS VM。

您可以在[什麼是 Azure Site Recovery？](site-recovery-overview.md)和 [Azure Site Recovery 可以保護哪些工作負載？](site-recovery-workload.md)中取得支援部署的完整摘要

## 在內部部署實體伺服器或 VMware 虛擬機器和 Azure 之間複寫

如果您想要藉由將 VMware VM 或 Windows/Linux 實體機器複寫至 Azure 來保護它們，以下是您需要的項目。

**位置** | **您需要什麼** 
--- | --- 
 內部部署 | **處理序伺服器**：此伺服器會在將資料傳送至 Azure 之前，最佳化受保護的 VMware 虛擬機器或實體 Windows/Linux 機器中的資料。它還會處理用來保護機器的行動服務元件的推入安裝，並執行 VMWare 虛擬機器的自動探索。<br/><br/> **VMware vCenter 伺服器**：如果您要保護 VMware VM，則您需要 VMwave vCenter 伺服器管理您的 vSphere hypervisors<br/><br/> **ESX 伺服器**：如果您要保護 VMware VM，則您需要伺服器執行具有最新更新的 ESX/ESXi 版本 5.1 或 5.5。<br/><br/> **機器**：如果您要保護 VMware，您應該安裝並執行 VMware VM 與 VMware 工具。如果您要保護實體機器，它們應該執行支援的 Windows 或 Linux 作業系統。請參閱[支援的項目](site-recovery-vmware-to-azure/#before-you-start)。<br/><br/> **行動服務**：在您想要保護的機器上安裝以擷取變更，並傳送給處理序伺服器。<br/><br/>協力廠商元件：這項部署取決於一些[協力廠商元件](http://download.microsoft.com/download/C/D/7/CD79E327-BF5A-4026-8FF4-9EB990F9CEE2/Third-Party_Notices.txt)。
Azure | **設定伺服器**：標準 A3 Azure VM，可在 Azure 中的受保護機器、處理序伺服器與主要目標伺服器之間協調通訊。它會設定容錯移轉發生時的複寫和協調復原。<br/><br/>**主要目標伺服器**：Azure VM 使用在您的 Azure 儲存體帳戶之 Blob 儲存體上所建立的附加 VHD，從受保護機器保留複寫的資料。容錯回復主要目標伺服器會在內部部署執行，讓您可以將 Azure VM 容錯回復至 VMware VM。<br/><br/> **Site Recovery 保存庫**：至少一個 Azure Site Recovery 保存庫 (以 Site Recovery 服務的訂用帳戶設定)<br/><br/> **虛擬網路**：Azure 網路，設定伺服器與主要目標伺服器位於該網路，在與 Site Recovery 服務相同的訂用帳戶和區域中。<br/><br/> **Azure 儲存體**：用來儲存複寫資料的 Azure 儲存體帳戶。應該是標準異地備援或位於與 Site Recovery 訂用帳戶相同區域中的高階帳戶。


在此案例中，通訊可以透過 VPN 連線傳送至 Azure 網路上的內部連接埠 (使用 Azure ExpressRoute 或站對站 VPN)，或透過安全的網際網路連線傳送至設定和主要目標伺服器 VM 的 Azure 雲端服務上的對應公用端點。

受保護機器上的行動服務會將複寫資料傳送至處理序伺服器，將複寫中繼資料傳送至設定伺服器。處理序伺服器會與設定伺服器通訊，以接收管理和控制資訊。它會將複寫資訊傳送至主要目標伺服器，並且最佳化複寫資料並傳送至主要目標伺服器。

## 將 Hyper-V VM 複寫至 Azure (具有 VMM)

如果您的 VM 位於在 System Center VMM 雲端中管理的 Hyper-V 主機上，以下是您將它們複寫至 Azure 所需要的項目。

**位置** | **您需要什麼** 
--- | --- 
內部部署 | **VMM 伺服器**：至少一個 VMM 伺服器設定至少一個 VMM 私人雲端。將在 VMM 伺服器上安裝 Azure Site Recovery 提供者<br/><br/>**Hyper-V 伺服器**：至少一個 Hyper-V 主機伺服器位於 VMM 雲端。Microsoft 復原服務代理程式將會安裝在每部 Hyper-V 伺服器上。<br/><br/> **虛擬機器**：至少一部在 Hyper-V 伺服器上執行的虛擬機器。不會在虛擬機器上安裝任何項目。
Azure | **Site Recovery 保存庫**：至少一個 Azure Site Recovery 保存庫 (以 Site Recovery 服務的訂用帳戶設定)<br/><br/>**儲存體帳戶**：Azure 儲存體帳戶，位於與 Site Recovery 服務相同的訂用帳戶底下。複寫的機器會儲存在 Azure 儲存體。 

在此案例中，在 VMM 伺服器上執行的提供者會透過網際網路協調使用 Site Recovery 服務的複寫。資料是在內部部署 Hyper-V 伺服器上執行的復原服務代理程式與透過 HTTPS 443 的 Azure 儲存體之間複寫。來自提供者和代理程式的通訊都是安全且加密的。Azure 儲存體中的複寫的資料也會加密。

![內部部署 VMM 至 Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

## 將 Hyper-V VM 複寫至 Azure (沒有 VMM)

如果您的 VM 未受 System Center VMM 伺服器管理，以下是您將它們複寫至 Azure 需要執行的動作

**位置** | **您需要什麼**
--- | --- 
 內部部署 | **Hyper-V 伺服器**：至少一部 Hyper-V 主機伺服器。Azure Site Recovery 提供者和 Microsoft 復原服務代理程式會安裝在每部 Hyper-V 伺服器上。<br/><br/>**虛擬機器**：至少一部在 Hyper-V 伺服器上執行的虛擬機器。不會在虛擬機器上安裝任何項目。
Azure | **Site Recovery 保存庫**：至少一個 Azure Site Recovery 保存庫 (以 Site Recovery 服務的訂用帳戶設定)<br/><br/>**儲存體帳戶**：Azure 儲存體帳戶，位於與 Site Recovery 服務相同的訂用帳戶底下。複寫的機器會儲存在 Azure 儲存體。

在此案例中，在 Hyper-V 伺服器上執行的提供者會透過網際網路協調使用 Site Recovery 服務的複寫。資料是在內部部署 Hyper-V 伺服器上執行的復原服務代理程式與透過 HTTPS 443 的 Azure 儲存體之間複寫。來自提供者和代理程式的通訊都是安全且加密的。Azure 儲存體中的複寫的資料也會加密。

![內部部署 VMM 至 Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)



## 將 Hyper-V VM 複寫至次要資料中心

如果您想要藉由將 Hyper-V VM 複寫至次要資料中心來保護它們，以下是您需要執行的動作。請注意，只有在您的 Hyper-V 主機伺服器是在 System Center VMM 雲端中受管理時，才可以這麼做。

**位置** | **您需要什麼** 
--- | --- 
 內部部署 | **VMM 伺服器**：一部 VMM 伺服器在主要網站、一部伺服器在次要網站。Azure Site Recovery 提供者會安裝在每部 VMM 伺服器上<br/><br/>**Hyper-V 伺服器**：至少一部位於主要和次要網站中 VMM 雲端的 Hyper-V 主機伺服器。不會在 Hyper-V 伺服器上安裝任何項目<br/><br/> **虛擬機器**：至少一部在 Hyper-V 伺服器上執行的虛擬機器。不會在虛擬機器上安裝任何項目。
Azure | **Site Recovery 保存庫**：至少一個 Azure Site Recovery 保存庫 (以 Site Recovery 服務的訂用帳戶設定)。 

在此案例中，在 VMM 伺服器上的提供者會透過網際網路協調使用 Site Recovery 服務的複寫。資料是在主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過網際網路複寫。來自提供者和 Hyper-V 主機伺服器之間的通訊都是安全且加密的。

![內部部署至內部部署](./media/site-recovery-components/arch-onprem-onprem.png)

## 使用 SAN 複寫將 Hyper-V VM 複寫至次要資料中心

如果您的 VM 位於在 System Center VMM 雲端中管理的 Hyper-V 主機上，而且您使用 SAN 儲存體，以下是您在兩個資料中心之間複寫時所需的項目。

**位置** | **您需要什麼** 
--- | --- 
 主要資料中心 | **SAN 陣列**：主要 VMM 伺服器管理的[支援的 SAN 陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。SAN 與次要網站中的另一個 SAN 陣列共用網路基礎結構<br/><br/> **VMM 伺服器**：至少一個 VMM 伺服器，已設定一或多個 VMM 雲端和複寫群組。將在每部 VMM 伺服器上安裝 Azure Site Recovery 提供者。<br/><br/> **Hyper-V 伺服器**：至少一部具有虛擬機器的 Hyper-V 主機伺服器，位於複寫群組。不會在 Hyper-V 主機伺服器上安裝任何項目。<br/><br/> **虛擬機器**：至少一部在 Hyper-V 主機伺服器上執行的虛擬機器。不會在虛擬機器上安裝任何項目。 
次要資料中心 | **SAN 陣列**：次要 VMM 伺服器管理的[支援的 SAN 陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。<br/><br/>**VMM 伺服器**：至少一部 VMM 伺服器，具有一或多個 VMM 雲端。<br/><br/> **Hyper-V 伺服器**：至少一部 Hyper-V 主機伺服器。 
Azure | **Site Recovery 保存庫**：至少一個 Azure Site Recovery 保存庫 (以 Site Recovery 服務的訂用帳戶設定)

在此案例中，在 VMM 伺服器上的提供者會透過網際網路協調使用 Site Recovery 服務的複寫。資料是在主要和次要儲存體陣列之間，使用同步 SAN 複寫進行複寫。

![內部部署至內部部署](./media/site-recovery-components/arch-onprem-onprem-san.png)



## Hyper-V 保護生命週期

此工作流程會顯示保護、複寫和容錯移轉 Hyper-V 虛擬機器的程序。

1. **啟用保護**：設定 Site Recovery 保存庫、設定 VMM 雲端或 Hyper-V 網站的複寫設定，以及啟用 VM 的保護。會起始稱為「啟用保護」的作業，並且可以在 [作業] 索引標籤中監視。作業會檢查符合必要條件的機器，然後再叫用 [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) 方法，它會使用您的設定來設定到 Azure 的複寫。「啟用保護」作業也會叫用 [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) 方法來初始化完整的 VM 複寫。
2. **初始複寫**：擷取虛擬機器快照，並且逐一複寫虛擬硬碟，直到它們全部複製到 Azure 或次要資料中心。完成的時間取決於大小和網路頻寬以及您所選擇的初始複寫方法。如果在初始複寫進行時發生磁碟變更，Hyper-V 複本複寫追蹤器會追蹤這些變更，並記錄在 Hyper-V 複寫記錄檔 (.hrl)，該檔案位於與磁碟相同的資料夾中。每個磁碟都有一個相關聯的.hrl 檔案，將會傳送至次要儲存體。請注意，當初始複寫正在進行時，快照和記錄檔會使用磁碟資源。當初始複寫完成時，會刪除 VM 快照，並且會同步處理和合併記錄檔中的差異磁碟變更。
3. **完成保護**：初始複寫完成之後，「完成保護」作業會設定網路和其他複寫後設定，虛擬機器就會受到保護。如果您要複寫至 Azure，您可能需要調整虛擬機器的設定，使其準備好進行容錯移轉。此時，您可以執行測試容錯移轉，以確認一切如預期般運作。
4. **複寫**：在初始複寫之後會根據複寫設定和方法，發生差異同步處理。 
	- **複寫失敗**：如果差異複寫失敗且完整複寫因為頻寬或時間需要大量成本，就會發生重新同步處理。例如，如果 .hrl 檔案達到磁碟大小的 50% 時，系統就會標示虛擬機器以便重新同步處理。重新同步處理會計算來源和目標虛擬機器的總和檢查碼，並只傳送差異部分，藉此有效減少傳送的資料量。重新同步處理完成之後，應會繼續進行差異複寫。根據預設，重新同步處理會排程在上班時間以外的時間自動執行，但是您可以手動重新同步處理虛擬機器。
	- **複寫錯誤**：如果發生複寫錯誤，會有內建的重試。如果它是無法復原的錯誤，例如驗證或授權錯誤，或複本機器處於無效狀態，則不會嘗試重試。如果它是可復原的錯誤，例如網路錯誤，或低磁碟空間/記憶體，則會發生重試，重試之間的間隔會遞增 (1、2、4、8、10，然後每隔 30 分鐘)。
4. **計劃性/非計劃性容錯移轉**：必要時，執行計劃性/非計劃性容錯移轉。如果您執行計劃性容錯移轉，來源 VM 會關閉以確保不會遺失資料。建立複本 VM 之後，它們即會處於認可擱置中的狀態。您必須認可它們以完成容錯移轉，除非您使用 SAN 複寫，在此情況下認可是自動的。主要網站已啟動並執行之後，會發生容錯回復。如果您已複寫到 Azure，則反向複寫是自動的。否則您應該開始反向複寫。
 

![工作流程](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## 將 VMWare 虛擬機器和實體伺服器複寫至 Azure

您可以透過 VPN 站對站連線或透過網際網路，將 VMware 虛擬機器和實體伺服器 (Windows/Linux) 複寫至 Azure。

### 透過 VPN 站對站連線 (或 ExpressRoute) 複寫至 Azure

![透過網際網路將 VMware 或實體機器連至 Azure](./media/site-recovery-components/arch-onprem-azure-vmware-vpn.png)

#### 透過網際網路複寫

![透過網際網路將 VMware 或實體機器連至 Azure](./media/site-recovery-components/arch-onprem-azure-vmware-internet.png)

## 在主要和次要資料中心的內部部署實體伺服器或 VMware 虛擬機器之間複寫

如果您想要藉由在兩個內部部署資料中心之間複寫 VMware VM 或 Windows/Linux 實體機器來保護它們，以下是您需要的項目。

**位置** | **您需要什麼** 
--- | --- 
 內部部署主要 | **處理序伺服器**：設定您的主要網站的處理序伺服器元件，以處理快取、壓縮和資料最佳化。它也會處理您想要保護的機器的整合代理程式推入安裝。<br/><br/> **VMware 保護**：如果您要保護 VMware VM，您需要 VMware EXS/ESXi hypervisor 或管理多個 hypervisor 的 VMware vCenter 伺服器<br/><br/> **實體伺服器保護**：如果您要保護實體機器，它們應該執行 Windows 或 Linux。<br/><br/> **整合代理程式**：安裝在您想要保護的電腦上與做為主要目標伺服器的電腦上。它會做為所有 InMage 元件之間的通訊提供者。
內部部署次要 | **設定伺服器**：設定伺服器是您安裝的第一個元件，它會安裝在次要網站以管理、設定和監視您的部署，使用管理網站或 vContinuum 主控台。設定伺服器也包含遠端部署整合代理程式的推入機制。在部署中只有單一設定伺服器，且必須安裝在執行 Windows Server 2012 R2 的機器上。<br/><br/> **vContinuum 伺服器**：安裝在與設定伺服器相同的位置 (次要網站)。它會提供主控台來管理及監視您的受保護的環境。在預設安裝中，vContinuum 伺服器是第一個主要目標伺服器，並且已安裝整合代理程式。<br/><br/> **主要目標伺服器**：主要目標伺服器會保留複製的資料。它會從處理序伺服器接收資料，在次要網站中建立複本機器，並且保存資料保留點。您需要的主要目標伺服器的數目取決於您要保護的機器數目。如果您想要容錯回復到主要網站，您也需要主要目標伺服器。 
Azure | **Site Recovery 保存庫**：至少一個 Azure Site Recovery 保存庫 (以 Site Recovery 服務的訂用帳戶設定)。您下載 InMage Scout 以在建立保存庫之後設定部署。您也會安裝所有 InMage 元件伺服器的最新更新。


在此案例中，差異複寫變更會從受保護電腦上執行的整合代理程式傳送到處理序伺服器。處理序伺服器會最佳化這項資料，並且將其傳輸至次要網站上的主要目標伺服器。設定伺服器會管理複寫程序。




## 後續步驟

[準備部署](site-recovery-best-practices.md)。

<!---HONumber=AcomDC_1203_2015-->