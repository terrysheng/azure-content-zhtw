<properties
	pageTitle="Site Recovery 如何運作？| Microsoft Azure"
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
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Azure Site Recovery 如何運作？

本文說明 Site Recovery 的基礎架構和讓它運作的元件。閱讀本文之後，若有任何問題，請造訪 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## 概觀

組織需要商務持續性和災害復原 (BCDR) 策略，決定應用程式、工作負載和資料如何在規劃與未規劃停機期間維持運作，並儘速復原到正常運作的情況。BCDR 策略的解決方案重點集中在保護商務資料安全且可復原，以及當發生災害時工作負載持續可用。

Site Recovery 是一項 Azure 服務，可藉由將內部部署實體伺服器和虛擬機器的複寫協調至雲端 (Azure) 或次要資料中心，協助您的 BCDR 策略。當您的主要位置發生故障時，您容錯移轉至次要網站，讓應用程式和工作負載保持可用。當它恢復正常作業時，容錯回復至您的主要位置。

Site Recovery 可以用在許多案例中，並可保護許多工作負載。

- **保護 VMware 虛擬機器**：您可以將內部部署 VMware 虛擬機器複寫至 [Azure](site-recovery-vmware-to-azure-classic.md) 或[次要資料中心](site-recovery-vmware-to-vmware.md)來保護它們。
- **保護 Hyper-V VM**：您可以將 VMM 雲端中的內部部署 Hyper-V 虛擬機器複寫至 [Azure](site-recovery-vmm-to-azure.md) 或[次要資料中心](site-recovery-vmm-to-vmm.md)來保護它們。您可以將未受 VMM 管理的 Hyper-V VM 複寫至 [Azure](site-recovery-hyper-v-site-to-azure.md)。
- **使用 Azure 來保護實體伺服器**：您可以將執行 Windows 或 Linux 的實體機器複寫至 [Azure](site-recovery-vmware-to-azure-classic.md) 或[次要資料中心](site-recovery-vmware-to-vmware.md)來保護它們。
- **移轉 VM**：您可以使用 Site Recovery 在區域之間[移轉 Azure IaaS VM](site-recovery-migrate-azure-to-azure.md)，或者[移轉 AWS Windows 執行個體](site-recovery-migrate-aws-to-azure.md)至 Azure IaaS VM。

Site Recovery 可複寫這些 VM 與實體伺服器上執行的大部分應用程式。您可以在 [Azure Site Recovery 可以保護哪些工作負載？](site-recovery-workload.md)中取得支援的應用程式的完整摘要。


## 將內部部署 VMware 虛擬機器或實體伺服器複寫至 Azure

如果您想要藉由將 VMware VM 或 Windows/Linux 實體機器複寫至 Azure 來保護它們，以下是您需要的項目。

目前有兩個不同的架構適用於此複寫案例：

- **舊版架構**：這個架構不應該用於新的部署。 
- **增強型架構**：這是最新的解決方案，應該用於所有新的部署。您也可以[移轉舊版架構](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment)至這個新的解決方案。

以下是增強型部署的架構

![增強](./media/site-recovery-components/arch-enhanced.png)

- **內部部署**：當您部署增強型架構時，您不需要在 Azure 中部署基礎結構 VM。此外，所有流量都會加密，且複寫管理通訊會透過 HTTPS 443 傳送。以下是您在內部部署基礎結構中必須備妥的項目：

	- **管理伺服器**：執行所有 Site Recovery 元件的單一管理伺服器，包括：

		- **設定伺服器**：協調內部部署環境與 Azure 之間的通訊，以及管理資料複寫和復原程序。
		- **處理序伺服器**：做為複寫閘道器。從受保護的來源機器接收資料、以快取最佳化、壓縮和加密，以及將複寫資料傳送至 Azure 儲存體。它還會處理用來保護機器的行動服務的推入安裝，並執行 VMWare VM 的自動探索。隨著部署規模擴大，您可以新增更多專用的伺服器，當作處理序伺服器執行來專責處理較大量的複寫流量。
		- **主要目標伺服器**：從 Azure 容錯回復期間處理複寫資料。 

	- **VMware ESX/ESXi 主機與 vCenter 伺服器**：VMware VM 所在的一或多個 ESX/ESXi 主機伺服器。建議您使用 vCenter 伺服器來管理這些主機。請注意，即使是在保護實體伺服器，您也需要有 VMware 環境，以便從 Azure 容錯回復至內部部署網站。
	
	- **受保護的電腦**：每個您想要複寫至 Azure 的電腦都必須安裝行動服務元件。它會擷取在電腦上寫入的資料，並將它們轉送到處理序伺服器。您可以手動安裝此元件，或在對電腦啟用保護時由處理序伺服器自動推送並安裝。

- **Azure**：以下是您在 Azure 基礎結構中必須備妥的項目：
	- **Azure 帳戶**：您需要 Microsoft Azure 帳戶。
	- **Azure 儲存體**：您需要 Azure 儲存體帳戶來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。 
	- **Azure 網路**：容錯移轉發生時，您需要有 Azure VM 將連接的 Azure 虛擬網路。您也將需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。

	![增強](./media/site-recovery-components/arch-enhanced2.png)

深入了解確切的[部署需求](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)。

### 容錯回復架構架構

- Azure 必須容錯回復到 VMware VM。您目前無法容錯回復到實體伺服器。
- 若要容錯回復，您需要有從 Azure 網路到內部部署網路的 VPN 連線 (或 Azure ExpressRoute)。
- 您在 Azure 中需要有處理序伺服器才能執行容錯回復。容錯回復完成後，您可以刪除它。
- 您在內部部署需要有主要目標伺服器。在內部部署中設定管理伺服器時，依預設會安裝主要目標伺服器。但對於更大量的流量，建議您在內部部署設定另一個主要目標伺服器，以因應容錯回復。

![增強型容錯回復](./media/site-recovery-components/enhanced-failback.png)

深入了解[容錯回復](site-recovery-failback-azure-to-vmware-classic.md)。

### 舊版架構

舊版架構需要有內部部署設定伺服器和處理序伺服器、VMware ESX/ESXi 主機和 vCenter 伺服器，而且在您想要保護的電腦上必須安裝行動服務。在 Azure 中，您為設定伺服器和處理序伺服器設定 Azure VM。您也需要有 Azure 訂用帳戶、儲存體帳戶和虛擬網路。



## 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure

如果您的 VM 位於在 System Center VMM 雲端中管理的 Hyper-V 主機上，以下是您將它們複寫至 Azure 所需要的項目。

- 內部部署： 
	- **VMM 伺服器**：至少設定一個 VMM 伺服器和至少一個 VMM 私人雲端。此伺服器應該執行 Systerm Center 2012 R2。VMM 伺服器應該具有網際網路連線。如果您想要確保 Azure VM 在容錯移轉之後會連線到網路，您必須設定網路對應。若要這樣做，您需要將來源 VM 連線到 VMM VM 網路。該網路應該連結到與雲端相關聯的邏輯網路。
	- **Hyper-V 伺服器**：VMM 雲端中至少有一部 Hyper-V 主機伺服器。Hyper-V 主機應該執行 Windows Server 2012 R2。
	- **受保護的電腦**：來源 Hyper-V 主機伺服器應該至少有一個您想要保護的 VM。
	
- Azure：
	- **Azure 帳戶**：您需要 Microsoft Azure 帳戶。
	- **Azure 儲存體**：您需要 Azure 儲存體帳戶來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。
	- **Azure 網路**：如果您想要確定 Azure VM 在容錯移轉之後會連線到網路，您必須設定網路對應。若要這樣做，您需要設定 Azure 網路。

	![VMM 至 Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

在此案例中，Azure Site Recovery 提供者在 Site Recovery 部署期間安裝在 VMM 伺服器上。它會透過網際網路與 Site Recovery 服務協調進行複寫。Azure 復原服務代理程式在 Site Recovery 部署期間安裝在 Hyper-V 主機伺服器上，它與 Azure 儲存體之間會透過 HTTPS 443 複寫資料。來自提供者和代理程式的通訊都是安全且加密的。Azure 儲存體中的複寫的資料也會加密。

深入了解確切的[部署需求](site-recovery-vmm-to-azure.md#before-you-start)。

## 將 Hyper-V VM 複寫至 Azure (不使用 VMM)

如果您的 VM 未受 System Center VMM 伺服器管理，以下是您將它們複寫至 Azure 需要執行的動作

- 內部部署： 
	- **Hyper-V 伺服器**：至少一部 Hyper-V 主機伺服器。Hyper-V 主機應該執行 Windows Server 2012 R2。
	- **受保護的電腦**：來源 Hyper-V 主機伺服器應該至少有一個您想要保護的 VM。
	
- Azure：
	- **Azure 帳戶**：您需要 Microsoft Azure 帳戶。
	- **Azure 儲存體**：您需要 Azure 儲存體帳戶來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。

	![從 Hyper-V 站台到 Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

在此案例中，Azure Site Recovery 提供者和 Azure 復原服務代理程式在 Site Recovery 部署期間安裝在 VMM 伺服器上。此提供者會透過網際網路與 Site Recovery 服務協調進行複寫。此代理程式會透過 HTTPS 443 來處理資料複寫。來自提供者和代理程式的通訊都是安全且加密的。Azure 儲存體中的複寫的資料也會加密。

深入了解確切的[部署需求](site-recovery-hyper-v-site-to-azure.md#before-you-start)

## 將 Hyper-V VM 複寫至次要資料中心

如果您想要將 Hyper-V VM 複寫至次要資料中心來保護它們，以下是您必須備妥的項目。請注意，只有在您的 Hyper-V 主機伺服器是在 System Center VMM 雲端中受管理時，才可以這麼做。

- **內部部署**： 
	- **VMM 伺服器**：建議在主要網站和次要網站上都有一個 VMM 伺服器，且各自包含至少一個 VMM 私人雲端。伺服器至少應該執行 System Center 2012 SP1 與最新的更新，並且連線至網際網路。雲端應該設定 Hyper-V 容量設定檔。
	- **Hyper-V 伺服器**：位於主要和次要 VMM 雲端中的 Hyper-V 主機伺服器。主機伺服器至少應該執行 Windows Server 2012 且安裝最新的更新，並且連接至網際網路。
	- **受保護的電腦**：來源 Hyper-V 主機伺服器應該至少有一個您想要保護的 VM。
	
- **Azure**：您將需要 Azure 訂用帳戶。

	![內部部署至內部部署](./media/site-recovery-components/arch-onprem-onprem.png)

在此案例中，Azure Site Recovery 提供者在 Site Recovery 部署期間安裝在 VMM 伺服器上。它會透過網際網路與 Site Recovery 服務協調進行複寫。主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過 LAN 或 VPN 來複寫資料。來自提供者和 Hyper-V 主機伺服器之間的通訊都是安全且加密的。

深入了解確切的[部署需求](site-recovery-vmm-to-vmm.md#before-you-start)



## 使用 SAN 複寫將 Hyper-V VM 複寫至次要資料中心

如果您的 VM 位於在 System Center VMM 雲端中管理的 Hyper-V 主機上，而且您使用 SAN 儲存體，以下是您在兩個資料中心之間複寫時所需的項目。

- **內部部署**： 
	- **SAN 陣列**：主要 VMM 伺服器管理的[支援的 SAN 陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。SAN 與次要網站中的另一個 SAN 陣列共用網路基礎結構。
	- **VMM 伺服器**：建議在主要網站和次要網站上都有一個 VMM 伺服器，且各自包含至少一個 VMM 私人雲端。伺服器至少應該執行 System Center 2012 SP1 與最新的更新，並且連線至網際網路。雲端應該設定 Hyper-V 容量設定檔。
	- **Hyper-V 伺服器**：位於主要和次要 VMM 雲端中的 Hyper-V 主機伺服器。主機伺服器至少應該執行 Windows Server 2012 且安裝最新的更新，並且連接至網際網路。
	- **受保護的電腦**：來源 Hyper-V 主機伺服器應該至少有一個您想要保護的 VM。
	
- **Azure**：您將需要 Azure 訂用帳戶。

	![SAN 複寫](./media/site-recovery-components/arch-onprem-onprem-san.png)

在此案例中，Azure Site Recovery 提供者在 Site Recovery 部署期間安裝在 VMM 伺服器上。它會透過網際網路與 Site Recovery 服務協調進行複寫。資料是在主要和次要儲存體陣列之間，使用同步 SAN 複寫進行複寫。

深入了解確切的[部署需求](site-recovery-vmm-san.md#before-you-start)


## 將 VMware 虛擬機器或實體伺服器複寫至次要網站

如果您想要藉由在兩個內部部署資料中心之間複寫 VMware VM 或 Windows/Linux 實體機器來保護它們，以下是您需要的項目。

- **內部部署主要**： 
	- **處理序伺服器**：設定您的主要網站的處理序伺服器元件，以處理快取、壓縮和資料最佳化。它也會處理您想要保護的機器的整合代理程式推入安裝。
	- **VMware ESX/ESXi 和 vCenter 伺服器**：如果您要保護 VMware VM，您需要有 VMware EXS/ESXi Hypervisor 或可管理多個 Hypervisor 的 VMware vCenter 伺服器。
	- 受保護的電腦：您想要保護的 VMware VM 或 Windows/Linux 實體伺服器需要安裝整合代理程式。整合代理程式也安裝在作為主要目標伺服器的電腦上。它會做為所有 InMage 元件之間的通訊提供者。
	
- **內部部署次要**：
	- **設定伺服器**：設定伺服器是您安裝的第一個元件，它會安裝在次要網站以管理、設定和監視您的部署，使用管理網站或 vContinuum 主控台。設定伺服器也包含遠端部署整合代理程式的推入機制。在部署中只有單一設定伺服器，且必須安裝在執行 Windows Server 2012 R2 的電腦上。
	- **vContinuum 伺服器**：與設定伺服器安裝在相同的位置 (次要網站)。它會提供主控台來管理及監視您的受保護的環境。在預設安裝中，vContinuum 伺服器是第一個主要目標伺服器，並且已安裝整合代理程式。
	- **主要目標伺服器**：主要目標伺服器保留複製的資料。它會從處理序伺服器接收資料，在次要網站中建立複本機器，並且保存資料保留點。您需要的主要目標伺服器的數目取決於您要保護的機器數目。如果您想要容錯回復到主要網站，您也需要主要目標伺服器。 

- **Azure**：您將需要 Azure 訂用帳戶。您會在建立 Site Recovery 保存庫之後下載 InMage Scout 來設定部署。您也會安裝所有 InMage 元件伺服器的最新更新。


	![VMware 至 VMware](./media/site-recovery-components/vmware-to-vmware.png)

在此案例中，差異複寫變更會從受保護電腦上執行的整合代理程式傳送到處理序伺服器。處理序伺服器會最佳化這項資料，並且將其傳輸至次要網站上的主要目標伺服器。設定伺服器會管理複寫程序。


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

## 後續步驟

[準備部署](site-recovery-best-practices.md)。

<!---HONumber=AcomDC_0218_2016-->