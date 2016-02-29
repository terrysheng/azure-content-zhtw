<properties
	pageTitle="準備 Site Recovery 部署 | Microsoft Azure"
	description="本文說明如何準備利用 Azure Site Recovery 部署複寫。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

# 準備 Azure Site Recovery 部署

Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。機器可以複寫至 Azure，或次要的內部部署資料中心。如需快速概觀，請參閱[什麼是 Azure Site Recovery？](site-recovery-overview.md)。

## 概觀

Azure Site Recovery 支援將 VMware 和 Hyper-V VM 及實體伺服器複寫至 Azure 或次要資料中心。本文說明如何針對上述每個複寫案例準備 Azure Site Recovery 部署。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## Hyper-V 複寫的部署需求

下表摘要說明 Hyper-V 複寫至 Azure (具有或不具 VMM) 和次要網站的一般部署需求。此表格可協助您了解和比較每個複寫案例的一般需求。另外還有詳細部署必要條件的連結。

**複寫至 Azure (具有 VMM)** | **複寫至 Azure (不具 VMM)** | **複寫至次要網站 (具有 VMM)**
---|---|---
**VMM**：至少有一個 VMM 伺服器執行於 System Center 2012 R2 上。VMM 伺服器至少應該有一個雲端包含一或多個 VMM 主機群組。<br/><br/> **Hyper-V**：內部部署資料中心中，有一或多部 Hyper-V 主機伺服器至少執行 Windows Server 2012 R2。Hyper-V 伺服器必須位於 VM 雲端中的主機群組。<br/><br/> **虛擬機器**：來源 Hyper-V 伺服器上至少需有一部 VM。複寫至 Azure 的 VM 必須符合 [Azure 虛擬機器必要條件](#azure-virtual-machine-requirements)。<br/><br/> **Azure 帳戶**：您需要有 [Azure](https://azure.microsoft.com/) 帳戶和訂用帳戶。<br/><br/> **Azure 儲存體**：您需要 [Azure 儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。<br/><br/> **網路對應**：設定網路對應，以便無論使用哪個復原計劃，在相同 Azure 網路上容錯移轉的所有機器都能彼此連接。如果目標 Azure 網路上有網路閘道，則虛擬機器也可以連接到內部部署虛擬機器。如果您未設定網路對應，則只能連接在相同復原方案中容錯移轉的機器。<br/><br/> **提供者/代理程式**：在部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者以及在 Hyper-V 主機伺服器上安裝 Azure 復原服務代理程式。提供者會與 Azure Site Recovery 通訊。代理程式會處理資料來源與目標 Hyper-V 伺服器之間的複寫。不會在 VM 上安裝任何元件。<br/><br/> **網際網路連線**：從 VMM 伺服器和 Hyper-V 主機。<br/><br/> **提供者連線**：如果提供者會透過 Proxy 連接至 Site Recovery，您必須確定此 Proxy 可以存取 Site Recovery URL。<br/><br/> [詳細的部署先決條件](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V**：來源和目標網站中至少有一部 Hyper-V 伺服器至少執行 Windows Server 2012 R2。<br/><br/> **虛擬機器**：來源 Hyper-V 伺服器上至少有一部 VM。複寫至 Azure 的 VM 必須符合 [Azure 虛擬機器必要條件](#azure-virtual-machine-requirements)<br/><br/> **Azure 帳戶**：您將需 [Azure](https://azure.microsoft.com/) 帳戶和訂用帳戶。<br/><br/> **Azure 儲存體**：您需要 [Azure 儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)來儲存複寫的資料。<br/><br/> **提供者/代理程式**：在部署期間，您會在 Hyper-V 主機伺服器或叢集上安裝 Azure Site Recovery 提供者和 Azure 復原服務代理程式。不會在 VM 上安裝任何元件。<br/><br/> **網際網路連線**：從 Hyper-V 主機。<br/><br/> **提供者連線**：如果提供者會透過 Proxy 連接，您必須確定此 Proxy 可以存取 Site Recovery URL。<br/><br/> [詳細的部署先決條件](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM**：來源 VMM 伺服器至少應該有一個雲端包含一或多個 VMM 主機群組。雲端應該設定 Hyper-V 容量設定檔。<br/><br/>**Hyper-V**：來源和目標網站中，至少有一部 Hyper-V 伺服器至少執行已安裝最新更新的 Windows Server 2012。Hyper-V 伺服器必須位於 VMM 雲端中的主機群組。<br/><br/> **虛擬機器**：來源 VMM 雲端中至少有一部 VM。<br/><br/> **網路對應**：設定網路對應，以便在容錯移轉之後虛擬機器會連接到適當的網路，且複本虛擬機器位於最佳的目標 Hyper-V 主機伺服器。如果您沒有設定網路對應，則複寫的機器將不會在容錯移轉之後連接到任何 VM 網路。<br/><br/> **儲存體對應**：您可以選擇性地設定儲存體對應，以確保虛擬機器可在容錯移轉之後，以最佳方式連接至儲存體 (根據預設，複寫 VM 會儲存在目標 Hyper-V 伺服器上指示的位置)。<br/><br/> **SAN 複寫**：如果要使用 SAN 複寫節點，在兩個內部部署 VMM 網站之間執行複寫，您可以使用現有的 SAN 環境。檢視[支援的 SAN 陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。<br/><br/> **提供者/代理程式**：在部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery 提供者來與 Azure Site Recovery 通訊。複寫透過 LAN/VPN 在 Hyper-V 來源與目標伺服器之間發生。<br/><br/> **網際網路連線**：只在 VMM 伺服器上。<br/><br/> **提供者連線**：如果提供者會透過 Proxy 連接，您必須確定可以存取 Site Recovery URL。<br/><br/> [詳細的部署先決條件](site-recovery-vmm-to-vmm.md#before-you-start)


## 複寫 VMware VM 與實體伺服器的部署需求

下表摘要說明將 VMware VM 和 Windows/Linux 實體伺服器複寫至 Azure 和次要網站的需求。

>[AZURE.NOTE] 您可以使用[已增強](site-recovery-vmware-to-azure-classic.md)的部署模型，或利用早期部署所用的[舊版](site-recovery-vmware-to-azure-classic-legacy.md)模型，將 VMware VM 和實體伺服器複寫至 Azure。下表包含每個模型的部署需求。

**複寫至 Azure (已增強)** | **複寫至 Azure (舊版)** | **複寫至次要網站**
---|---|---
**內部部署管理伺服器**：在內部部署網站中，您需要有一部專用伺服器做為管理伺服器。此伺服器上會安裝所有的 Site Recovery 元件。<br/><br/> **額外的處理序伺服器**：管理伺服器上預設會安裝處理序伺服器，但您可以選擇性地安裝額外的內部部署處理序伺服器以調整您的部署。<br/><br/> **VMware vCenter ESXi**：如果您要複寫 VMware VM (或想要容錯回復實體伺服器)，您需要有 VM 所在的 vSphere ESX/ESXi。建議以 VMware vCenter 伺服器管理您的 ESXi 主機。</br><br/> **容錯回復**：即使您要複寫實體伺服器，也需要有 VMware 環境才能從 Azure 容錯回復。此外，您還必須設定處理序伺服器做為 Azure VM，如果您要容錯回復大量流量，您可以設定額外的內部部署主要目標伺服器。[深入了解](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/> **Azure 帳戶**：您需要有 [Azure](https://azure.microsoft.com/) 帳戶和訂用帳戶。<br/><br/> **Azure 儲存體**：您需要 [Azure 儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。<br/><br/> **Azure 虛擬網路**：容錯移轉發生時，您需要有 Azure VM 將連接的 Azure 虛擬網路。若要在容錯移轉之後容錯回復，您需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。<br/><br/> **受保護的機器**：至少一部 VMware 虛擬機器或實體 Windows/Linux 伺服器。在部署期間，於您要複寫的每部機器上安裝行動服務。<br/><br/> **連線**：如果管理伺服器會透過 Proxy 連接至 Site Recovery，您必須確定此 Proxy 可以連接至特定 URL。<br/><br/> [詳細的部署先決條件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)。 | **主要網站**：您必須設定處理序伺服器。<br/><br/> **容錯回復**：即使您要複寫實體伺服器，也需要有 VMware 環境才能從 Azure 容錯回復。在內部部署網站中，您必須設定 vContinuum 伺服器和主要目標伺服器。在 Azure 中，您必須設定處理序伺服器。[深入了解](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/> **Azure 帳戶**：您需要有 [Azure](https://azure.microsoft.com/) 帳戶和訂用帳戶。<br/><br/> **Azure 儲存體**：您需要 [Azure 儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。<br/><br/> **Azure 基礎結構 VM**：您必須將設定伺服器和主要目標伺服器設定為 Azure VM。<br/><br/> **Azure 虛擬網路**：您需要 Azure 虛擬網路以部署設定伺服器與主要目標伺服器。在容錯移轉之後，Azure VM 會連線到此網路。<br/><br/> **受保護的機器**：至少一部 VMware 虛擬機器或實體 Windows/Linux 伺服器。在部署期間，於您要複寫的每部機器上安裝行動服務。<br/><br/> **連線**：如果管理伺服器會透過 Proxy 連接至 Site Recovery，您必須確定此 Proxy 可以連接至特定 URL。<br/><br/> [詳細的部署先決條件](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start)。 | **主要網站**：專用的 Windows 伺服器 (實體或 VMware 虛擬機器。<br/><br/> **次要網站**：專用的設定和主要目標伺服器。<br/><br/> **受保護的機器**：至少一部 VMware 虛擬機器或實體 Windows/Linux 伺服器。在部署期間將於每部機器上安裝整合代理程式。





## Azure 虛擬機器需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。這包括大部分的 Windows 和 Linux 版本。您必須確認想要保護的內部部署虛擬機器均符合 Azure 要求。


**功能** | **支援** | **詳細資料**
---|---|---
Hyper-V 主機作業系統 | Windows Server 2012 R2 | 如果不支援，則先決條件檢查會失敗
VMware hypervisor 作業系統 | 執行受支援的作業系統 | [詳細資料](site-recovery-vmware-to-azure.md#before-you-start)
客體作業系統 | 對於從 Hyper-V 複寫到 Azure，Site Recovery 支援 [Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有作業系統。<br/><br/>對於 VMware 和實體伺服器複寫，請檢查 Windows 和 Linux [必要條件](site-recovery-vmware-to-azure.md#before-you-start) | 如果不支援，則先決條件檢查會失敗。
客體作業系統架構 | 64 位元 | 如果不支援，則先決條件檢查會失敗
作業系統磁碟大小 | 最多 1023 GB | 如果不支援，則先決條件檢查會失敗
作業系統磁碟計數 | 1 | 如果不支援，則先決條件檢查會失敗。
資料磁碟計數 | 16 或更少 (最大值是所建立之虛擬機器大小的函數。16 = XL) | 如果不支援，則先決條件檢查會失敗
資料磁碟 VHD 大小 | 最大 1023 GB | 如果不支援，則先決條件檢查會失敗
網路介面卡 | 支援多個介面卡 |
靜態 IP 位址 | 支援 | 如果主要虛擬機器使用靜態 IP 位址，您可以指定會在 Azure 中建立的虛擬機器靜態 IP 位址
iSCSI 磁碟 | 不支援 | 如果不支援，則先決條件檢查會失敗
共用 VHD | 不支援 | 如果不支援，則先決條件檢查會失敗
FC 磁碟 | 不支援 | 如果不支援，則先決條件檢查會失敗
硬碟格式| VHD <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
虛擬機器名稱| 介於 1 到 63 個字元。只能使用字母、數字和連字號。應該以字母或數字為開頭和結尾 | 更新 Site Recovery 中虛擬機器內容的值
虛擬機器類型 | <p>第 1 代</p> <p>第 2 代 - Windows</p> | 支援第 2 代虛擬機器：具備包含 1 或 2 個資料磁碟區、磁碟格式為 VHDX、磁碟大小小於 300GB 之基本磁碟的作業系統磁碟。不支援 Linux 第 2 代虛擬機器。[了解詳細資訊](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## 最佳化您的部署

使用下列秘訣可協助您最佳化並調整您的部署。

- **作業系統磁碟區大小**：當您將虛擬機器複寫到 Azure 時，作業系統磁碟區必須小於 1TB。如果磁碟區大小超過此值，您可以在開始部署之前，手動將磁碟區移動至不同的磁碟。
- 資料磁碟大小：如果複寫至 Azure，您可以在虛擬機器上擁有最多 32 個資料磁碟，每個資料磁碟的上限為 1 TB。您可以有效地複寫及容錯移轉最多 32 TB 的虛擬機器。
- 復原方案限制：Site Recovery 可擴充至數千個虛擬機器。復原方案是設計做為應用程式的模型，而由於應用程式應該一起進行容錯移轉，因此我們將一個復原方案中的機器數目限制為 50。
- Azure 服務限制：每個 Azure 訂用帳戶均隨附一組對核心、雲端服務等的預設限制。我們建議您執行容錯移轉測試，以驗證您訂用帳戶中的資源可用性。您可以透過 Azure 支援修改這些限制。
- **容量規劃**：深入了解 Site Recovery 的[容量規劃](site-recovery-capacity-planner.md)。
- 複寫頻寬：如果您的複寫頻寬不足，請注意：
	- **ExpressRoute**：Site Recovery 可搭配 Azure ExpressRoute 和 WAN 最佳化工具，例如 Riverbed。[深入了解](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) ExpressRoute。
	- 複寫流量：Site Recovery 只會使用資料區塊 (而非整個 VHD) 來執行智慧型初始複寫。只會在複寫進行期間複寫變更。
	- 網路流量：您可以根據目的地 IP 位址和連接埠，以原則設定 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)，藉此控制用於複寫的網路流量。此外，如果您使用 Azure 備份代理程式複寫至 Azure Site Recovery，則您可以設定該代理程式的節流。[閱讀更多資訊](https://support.microsoft.com/kb/3056159)。
- **RTO**：如果您想要測量可以預期的 Site Recovery 復原時間目標 (RTO)，我們建議您執行容錯移轉測試並檢視 Site Recovery 工作，來分析需要多少時間才能完成作業。如果您容錯移轉至 Azure，我們建議您藉由整合 Azure 自動化和復原方案，將所有的手動操作自動化，來取得最佳的 RTO。
- **RPO**：當您複寫至 Azure 時，Site Recovery 支援幾乎同步的復原點目標 (RPO)。這是假設您的資料中心與 Azure 之間有足夠的頻寬。





## 後續步驟

了解和比較一般部署需求之後，您可以閱讀詳細的先決條件並開始部署每個案例。

- [將 VMWare 虛擬機器複寫至 Azure](site-recovery-vmware-to-azure-classic.md)
- [將實體伺服器複寫到 Azure](site-recovery-vmware-to-azure-classic.md)
- [將 VMM 雲端中的 Hyper-V 伺服器複寫至 Azure](site-recovery-vmm-to-azure.md)
- [將 Hyper-V 虛擬機器 (不具 VMM) 複寫至 Azure](site-recovery-hyper-v-site-to-azure.md)
- [將 Hyper-V VM 複寫至次要網站](site-recovery-vmm-to-vmm.md)
- [利用 SAN 將 Hyper-V VM 複寫至次要網站](site-recovery-vmm-san.md)
- [利用單一 VMM 伺服器複寫 Hyper-V VM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0218_2016-->