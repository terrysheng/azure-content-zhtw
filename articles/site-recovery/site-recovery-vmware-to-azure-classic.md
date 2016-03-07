<properties
	pageTitle="使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure | Microsoft Azure" 
	description="本文描述如何部署 Azure Site Recovery，以協調內部部署 VMware 虛擬機器或 Windows/Linux 實體伺服器至 Azure 的複寫、容錯移轉和復原。" 
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
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="raynew"/>

# 使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure

> [AZURE.SELECTOR]
- [Enhanced](site-recovery-vmware-to-azure-classic.md)
- [Legacy](site-recovery-vmware-to-azure-classic-legacy.md)

Azure Site Recovery 服務可藉由協調虛擬機器與實體伺服器的複寫、容錯移轉及復原 (BCDR) 策略，為您的商務持續性與災害復原做出貢獻。機器可以複寫至 Azure，或次要的內部部署資料中心。如需快速概觀，請參閱[什麼是 Azure Site Recovery？](site-recovery-overview.md)。

## 概觀

這篇文章說明如何：

- **將 VMware 虛擬機器複寫至 Azure**—部署 Site Recovery 以協調內部部署 VMware 虛擬機器對 Azure 儲存體的複寫、容錯移轉和復原。
- **將實體伺服器複寫至 Azure**—部署 Azure Site Recovery 以協調內部部署實體 Windows 和 Linux 伺服器至 Azure 的複寫、容錯移轉和復原。

>[AZURE.NOTE] 本文說明如何複寫至 Azure。如果您想要將 VMware VM 或 Windows/Linux 實體伺服器複寫至次要資料中心，請依照[這篇文章](site-recovery-vmware-to-vmware.md)中的指示。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## 增強部署 

本文包含傳統 Azure 入口網站中增強部署的指示。建議您對所有新的部署使用這個版本。如果您已經使用舊版部署，建議您移轉到新的版本。[深入了解](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment)移轉。

增強部署是一項重大更新。以下是我們所做的改良功能的摘要：

- **Azure 中沒有基礎結構 VM**：資料會直接複寫到 Azure 儲存體帳戶。除了複寫和容錯移轉之外，未如同在舊版部署中所需一般，設定任何基礎結構 VM (組態伺服器、主要目標伺服器)。  
- **整合安裝**：單一安裝可提供內部部署元件的簡單設定及延展性。
- **安全部署**：所有流量都會加密，且複寫管理通訊會透過 HTTPS 443 傳送。
- **復原點**：支援 Windows 和 Linux 環境中的當機和應用程式一致復原點，並同時支援單一 VM 和多個 VM 一致組態。
- **測試容錯移轉**：支援非干擾性測試容錯移轉至 Azure，而不會影響實際執行或暫停複寫。
- **非計劃性容錯移轉**：支援使用增強選項的非計劃性容錯移轉至 Azure，以在容錯移轉之前自動關閉 VM。
- **容錯回復**：整合式容錯回復，僅將有差異的變更複寫回內部部署網站。
- **vSphere 6.0**：有限制支援 VMware Vsphere 6.0 部署。


## Site Recovery 如何協助保護虛擬機器與實體伺服器？


- VMware 系統管理員可以設定商務工作負載的 Azure 和在 VMware 虛擬機器上執行的應用程式的異地保護。伺服器管理員可以將實體內部部署 Windows 和 Linux 伺服器複寫至 Azure。
- Azure Site Recovery 主控台提供單一位置以進行簡單的設定和複寫管理、容錯移轉及復原程序。
- 如果複寫由 vCenter 伺服器管理的 VMware 虛擬機器，Site Recovery 就可以自動探索這些 VM。如果電腦位於 ESXi 主機，則 Site Recovery 會探索主機上的 VM。
- 執行從內部部署基礎結構容錯移轉至 Azure，以及從 Azure 至內部部署網站中的 VMware VM 的容錯回復 (還原) 的簡單方式。 
- 設定復原方案，將跨多部電腦分層的應用程式工作負載群組在一起。您可以容錯移轉這些方案，Site Recovery 會提供多 VM 一致性，以便執行相同工作負載的機器可以同時復原到一致的資料點。

## 案例架構

案例元件：

- **內部部署管理伺服器**：管理伺服器會執行 Site Recovery 元件：
	- **組態伺服器**：協調通訊和管理資料複寫與復原程序。
	- **處理序伺服器**：做為複寫閘道器。從受保護的來源機器接收資料、以快取最佳化、壓縮和加密，以及將複寫資料傳送至 Azure 儲存體。它還會處理用來保護機器的行動服務的推入安裝，並執行 VMWare VM 的自動探索。
	- **主要目標伺服器**：從 Azure 容錯回復期間處理複寫資料。您也可以部署管理伺服器，僅做為處理序伺服器，以便調整您的部署。
- **行動服務**：此元件會部署在您想要複寫至 Azure 的每個機器 (VMware VM 或實體伺服器)。它會擷取在電腦上寫入的資料，並將它們轉送到處理序伺服器。
- **Azure**：您不需要建立任何 Azure VM，以處理複寫及容錯移轉。Site Recovery 服務會處理資料管理，且資料會直接複寫至 Azure 儲存體。複寫的 Azure VM 只會在容錯移轉至 Azure 時自動啟動。不過，如果您想要從 Azure 容錯回復到內部部署網站，您必須設定 Azure VM 做為處理序伺服器。


下圖顯示這些元件如何互動。

![架構](./media/site-recovery-vmware-to-azure-classic/architecture.png)

## 容量規劃

當您規劃容量時，以下是需要考慮的項目：

- **來源環境**—容量規劃或 VMware 基礎結構和來源機器需求。
- **管理伺服器**—規劃執行 Site Recovery 元件的內部部署管理伺服器。
- **從來源到目標的網路頻寬**—規劃在來源與 Azure 之間複寫所需的網路頻寬

### 來源環境考量

- **每日變更率上限**—受保護的機器只能使用一個處理序伺服器，而且單一處理序伺服器的每日變更率最多 2 TB。因此 2 TB 是針對受保護機器支援的每日資料變更率上限。
- **最大輸送量**—複寫的機器可以屬於在 Azure 中的一個儲存體帳戶。標準儲存體帳戶每秒可處理最多 20000 個要求，建議您將來源機器的 IOPS 數保持為 20000。例如，如果您有一部具備 5 個磁碟的來源機器，並且在來源上的每個磁碟會產生 120 個 IOP (8K 大小)，則它會在 Azure 每個磁碟 IOPS 限制 500 之內。儲存體帳戶所需的數目 = 來源 IOP 總計/20000。 
 

### 管理伺服器考量

管理伺服器會執行 Site Recovery 元件，這些元件會處理資料最佳化、複寫及管理。它應該能夠處理受保護機器上執行的所有工作負載的每日變更率容量，並且有足夠頻寬可以持續將資料複寫到 Azure 儲存體。具體而言：

- 處理序伺服器會從受保護的機器接收複寫資料，以快取最佳化、壓縮，並且在傳送至 Azure 之前加密。管理伺服器應該有足夠的資源來執行這些工作。
- 處理序伺服器使用磁碟快取。我們建議每個快取磁碟有 600 GB 以上的空間，以處理發生網路瓶頸或中斷時儲存的資料變更。在部署期間，您可以在具有至少 5 GB 的可用儲存體的任何磁碟機上設定快取，但是 600 GB 是最低的建議。
- 我們建議的最佳做法是管理伺服器位於與您想要保護的機器的相同網路與 LAN 區段上。它可以位於不同的網路，但是您想要保護的機器應該具有 L3 網路可見性。 

下表摘要說明管理伺服器的大小建議。

**管理伺服器 CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的機器**
--- | --- | --- | --- | ---
8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz) | 16 GB | 300 GB | 500 GB 或更少 | 以這些設定部署管理伺服器，以複寫少於 100 部的機器。
12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz) | 18 GB | 600 GB | 500 GB 至 1 TB | 以這些設定部署管理伺服器，以複寫 100-150 部的機器。
16 個 vCPU (2 個通訊端 * 八核心 @ 2.5GHz) | 32 GB | 1 TB | 1 TB 至 2 TB | 以這些設定部署管理伺服器，以複寫 150-200 部的機器。
部署另一個處理序伺服器 | | | > 2 TB | 如果您要複寫 200 部以上的機器，或如果每日資料變更率超過 2 TB，部署額外的處理序伺服器。

其中：

- 每個來源機器已設定各 100 GB 的 3 個磁碟。
- 我們使用具有 RAID 10 的 8 個 10 K RPM 的 SAS 磁碟機的效能評定儲存體以進行快取磁碟度量。

### 從來源到目標的網路頻寬
請確定您使用[容量規劃工具](site-recovery-capacity-planner.md)計算初始複寫和差異複寫所需的頻寬

#### 用於複寫的節流頻寬

複寫到 Azure 的 VMware 流量會通過特定的處理序伺服器。您可以節流可用於該伺服器上 Site Recovery 複寫的頻寬，如下所示：

1. 開啟主要管理伺服器上或執行其他佈建處理序伺服器的管理伺服器上的 Microsoft Azure 備份 MMC 嵌入式管理單元。根據預設，Microsoft Azure 備份會在桌面上建立捷徑，或者您可以下列位置中找到：C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin。
2. 在嵌入式管理單元，按一下 [變更屬性]。

	![節流頻寬](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. 在 [節流] 索引標籤中指定頻寬，可以用於 Site Recovery 複寫和適用的排程。

	![節流頻寬](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

您也可以選擇使用 PowerShell 設定節流。以下是範例：

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024) 

#### 最大化頻寬使用量 
若要增加用於 Azure Site Recovery 複寫的頻寬，您必須變更登錄機碼。

下列機碼可控制複寫時每個複寫磁碟使用的執行緒數目

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 在「overprovisioned」網路中，此登錄機碼必須從它的預設值變更。我們支援最多 32 個。


[深入了解](site-recovery-capacity-planner.md)詳細的容量規劃。

### 額外處理序伺服器

如果您要保護超過 200 部機器，或每日變更率大於 2 TB，您可以新增額外的伺服器以處理負載。若要擴充，您可以：

- 增加管理伺服器的數目。例如，您可以使用兩個管理伺服器保護最多 400 部機器。
- 新增額外的處理序伺服器和使用這些項目來處理流量，而不是管理伺服器。

下表描述案例，其中：

- 您設定原始管理伺服器僅做為組態伺服器。
- 您設定額外的處理序伺服器。
- 您設定受保護的虛擬機器以使用額外的處理序伺服器。
- 每個受保護的來源機器已設定各 100 GB 的 3 個磁碟。

**原始管理伺服器**<br/><br/>(組態伺服器) | **額外處理序伺服器**| **快取磁碟大小** | **資料變更率** | **受保護的機器**
--- | --- | --- | --- | --- 
8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz)，16 GB 記憶體 | 4 個 vCPU (2 個通訊端 * 雙核心 @ 2.5GHz)，8 GB 記憶體 | 300 GB | 250 GB 或更少 | 您可以複寫 85 部或更少的機器。
8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz)，16 GB 記憶體 | 8 個 vCPU (2 個通訊端 * 四核心 @ 2.5GHz)，12 GB 記憶體 | 600 GB | 250 GB 至 1 TB | 您可以複寫 85-150 部機器。
12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz)，18 GB 記憶體 | 12 個 vCPU (2 個通訊端 * 六核心 @ 2.5GHz)，24 GB 記憶體 | 1 TB | 1 TB 至 2 TB | 您可以複寫 150-225 部機器。


您調整您的伺服器的方式取決於您的相應增加或相應放大模型的喜好設定。您部署幾個高階管理和處理序伺服器以相應增加，或使用較少的資源部署更多伺服器以相應放大。例如：如果您需要保護 220 部機器，您可以執行下列任一項：

- 設定具有 12 個 vCPU、18 GB 記憶體的原始管理伺服器，具有 12 個 vCPU、24 GB 記憶體的額外處理序伺服器，並設定受保護的機器僅使用額外的處理序伺服器。
- 或者，您也可以設定兩部管理伺服器 (2 x 8vCPU、16 GB RAM) 和兩個額外的處理序伺服器 (1 x 8vCPU 和 4vCPU x 1 以處理 135 + 85 (220) 部機器)，並且設定受保護的機器僅使用額外的處理序伺服器。
  

[遵循這些指示](#deploy-additional-process-servers)以設定額外的處理序伺服器。

## 開始部署之前

資料表摘要說明部署此案例的必要條件。


### Azure 必要條件

**必要條件** | **詳細資料**
--- | ---
**Azure 帳戶**| 您將需要 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。您可以從[免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。[深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 定價。 
**Azure 儲存體** | 您需要 Azure 儲存體帳戶來儲存複寫的資料。複寫的資料會儲存在 Azure 儲存體，容錯移轉時會啟動 Azure VM。<br/><br/>您需要[標準異地備援儲存體帳戶](../storage/storage-redundancy.md#geo-redundant-storage)。此帳戶應與 Site Recovery 服務位於相同的區域，且與相同的訂用帳戶相關聯。請注意，複寫到進階儲存體帳戶目前不支援，不應該使用。<br/><br/>[深入了解](../storage/storage-introduction.md) Azure 儲存體。
**Azure 網路** | 容錯移轉發生時，您需要 Azure VM 會連接的 Azure 虛擬網路。Azure 虛擬網路必須位於與 Site Recovery 保存庫相同的區域。<br/><br/>請注意，若要在容錯移轉至 Azure 之後容錯回復，您需要設定從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。 


### 內部部署必要條件

**必要條件** | **詳細資料**
--- | ---
**管理伺服器** | 您需要在虛擬機器或實體伺服器上執行的內部部署 Windows 2012 R2 伺服器。所有內部部署 Site Recovery 元件是安裝在此管理伺服器上<br/><br/>建議您部署伺服器做為高可用性的 VMware VM。從 Azure 容錯回復到內部部署網站一律是 VMware VM，無論您容錯移轉 VM 或實體伺服器。如果您不把管理伺服器設定為 VMware VM，就必須把不同的主要目標伺服器設定為 VMware VM，以便接收容錯回復流量。<br/><br/>伺服器應該要有靜態 IP 位址。<br/><br/>伺服器的主機名稱長度不能超過 15 個字元。<br/><br/>作業系統的地區設定應該僅可使用英文。<br/><br/>管理伺服器必須能夠存取網際網路。<br/><br/>您需要從伺服器對外存取的權限，如下所示：安裝 Site Recovery 元件期間的 HTTP 80 暫時存取權 (以便下載 MySQL)；複寫管理用的 HTTPS 443 持續對外存取權；複寫流量用的 HTTPS 9443 持續對外存取權 (這個連接埠可以修改)<br/><br/>請確定您可從管理伺服器存取這些 URL：<br/>- *.hypervrecoverymanager.windowsazure.com<br/>- *.accesscontrol.windows.net<br/>- *.backup.windowsazure.com<br/>- *.blob.core.windows.net<br/>- *.store.core.windows.net<br/>-http://www.msftncsi.com/ncsi.txt<br/>- [ http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://dev.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi "http://dev.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>如果您的伺服器有 IP 位址式的防火牆規則，請檢查規則是否允許伺服器與 Azure 通訊。您必須允許 [Azure Datacenter IP Ranges (Azure Datacenter IP 範圍)](https://www.microsoft.com/download/details.aspx?id=41653) 和 HTTPS (433) 通訊協定。您也必須將訂用帳戶的 Azure 區域和美國西部的 IP 位址範圍列於白名單。URL [http://dev.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://dev.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi "http://dev.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi") 是用來下載 MySQL 的。 
**VMware vCenter/ESXi host**： | 您需要一或多個執行 ESX/ESXi 版本 6.0、5.5 或 5.1 及最新更新程式的 vMware vSphere ESX/ESXi Hypervisor，來管理您的 VMware 虛擬機器。<br/><br/> 建議您部署 VMware vCenter 伺服器來管理您的 ESXi 主機。它應該執行 vCenter 版本 6.0 或 5.5 及最新的更新程式。<br/><br/>請注意，Site Recovery 不支援新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虛擬磁碟區和儲存體 DRS。Site Recovery 支援僅限於 5.5 版中可用的功能。
**受保護的機器**： | **AZURE**<br/><br/>您要保護的機器應該要符合建立 Azure VM 用的 [Azure 先決條件](site-recovery-best-practices.md#azure-virtual-machine-requirements)。<br><br/>如果您要在容錯移轉之後連線到 Azure VM，就必須在本機防火牆上啟用遠端桌面連線。<br/><br/>受保護機器上的個別磁碟容量不應該超過 1023 GB。VM 可以有多達 64 個磁碟 (因此多達 64 TB)。如果您有容量大於 1 TB 的磁碟，請考慮使用資料庫複寫，例如 SQL Server Always On 或 Oracle Data Guard<br/><br/>不支援共用磁碟客體叢集。如果您有叢集部署，請考慮使用資料庫複寫，例如 SQL Server Always On 或 Oracle Data Guard。<br/><br/>不支援整合可延伸韌體介面 (UEFI)/可延伸韌體介面 (EFI) 開機。<br/><br/>機器名稱應包含 1 到 63 個字元 (字母、數字和連字號)。名稱必須以字母或數字開頭，並以字母或數字結尾。機器受到保護之後，您就可以修改 Azure 的名稱。<br/><br/>**VMware VM**<br/><br>您需要在管理伺服器 (組態伺服器) 上安裝 VMware vSphere PowerCLI 6.0。<br/><br/>您想要保護的 VMware VM 應該已安裝並執行 VMware 工具。<br/><br/>如果來源 VM 有 NIC 小組，它會在容錯移轉至 Azure 之後轉換成單一 NIC。<br/><br/>如果受保護的 VM 具有 iSCSI 磁碟，則 Site Recovery 會在 VM 容錯移轉至 Azure 時，將受保護的 VM iSCSI 磁碟轉換為 VHD 檔案。如果 Azure VM 可以觸達 iSCSI 目標，則它會連接到 iSCSI 目標，並且基本上查看兩個磁碟 – Azure VM 上的 VHD 磁碟和來源 iSCSI 磁碟。在這種情況下，您必須與已容錯移轉的 Azure VM 上出現的 iSCSI 目標中斷連線。<br/><br/>[深入了解](#vmware-permissions-for-vcenter-access) Site Recovery 所需的 VMware 使用者權限。<br/><br/> **WINDOWS SERVER 機器 (在 VMware VM 或實體伺服器上)**<br/><br/>伺服器應執行受支援的 64 位元作業系統：Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 (至少 SP1)。<br/><br/>主機名稱、掛接點、裝置名稱、Windows 系統路徑 (例如 C:\\Windows) 僅可使用英文。<br/><br/>作業系統應該要安裝在 C:\\ 磁碟機上，且作業系統磁碟應該是 Windows 基本磁碟 (作業系統不應該安裝在 Windows 動態磁碟上)。<br/><br/>您必須提供系統管理員帳戶 (必須是 Windows 電腦上的本機系統管理員)，以便在 Windows 伺服器上進行行動服務的推入安裝。如果提供的帳戶是非網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。[深入了解](#install-the-mobility-service-with-push-installation)。<br/><br/>Site Recovery 支援具有 RDM 磁碟的 VM。在容錯回復期間，如果原始來源 VM 和 RDM 磁碟可用，則 Site Recovery 會重複使用 RDM 磁碟。如果它們都無法使用，Site Recovery 會在容錯回復期間為每個磁碟建立新的 VMDK 檔案。<br/><br/>**LINUX 機器**<br/><br/>必須為受支援的 64 位元作業系統：Red Hat Enterprise Linux 6.7；Centos 6.5、6.6、6.7；Oracle Enterprise Linux 6.4、6.5，且必須執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)、SUSE Linux Enterprise Server 11 SP3。<br/><br/>受保護機器上的 /etc/hosts 檔案應該包含將本機主機名稱對應至與所有網路介面卡相關聯的 IP 位址的項目。<br/><br/>如果您想要在容錯移轉之後，使用安全殼層用戶端 (ssh) 連線到執行 Linux 的 Azure 虛擬機器，請確定受保護的機器上的安全殼層服務設定為在系統開機時自動啟動，且防火牆規則允許 ssh 連線。<br/><br/>主機名稱、掛接點、裝置名稱，以及 Linux 系統路徑和檔案名稱 (例如 /etc/；/usr) 僅可使用英文。<br/><br/>只針對具有下列儲存體的 Linux 機器啟用保護：檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具 (多重路徑)；磁碟區管理員：(LVM2)。不支援使用 HP CCISS 控制站儲存體的實體伺服器。只有在 SUSE Linux Enterprise Server 11 SP3 上的 ReiserFS 檔案系統才受支援。<br/><br/>Site Recovery 支援具有 RDM 磁碟的 VM。在 Linux 的容錯回復期間，Site Recovery 不會重複使用 RDM 磁碟。而是會針對每個對應的 RDM 磁碟建立新的 VMDK 檔案。 


## 步驟 1：建立保存庫

1. 登入[管理入口網站](https://manage.windowsazure.com/)。
2. 展開 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建] > [快速建立]。
4. 在 [名稱] 中，輸入保存庫的易記識別名稱。
5. 在 [區域] 中，選取保存庫的地理區域。若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/) (英文) 中的＜各地區上市情況＞。
6. 按一下 [建立保存庫]。![新增保存庫](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要 [復原服務] 頁面上會列為 [使用中]。

## 步驟 2：設定 Azure 網路

設定 Azure 網路，讓 Azure VM 在容錯移轉之後連接到網路，以便容錯回復到內部部署網站可以正常運作。

1. 在 Azure 入口網站 > [建立虛擬網路] 頁面中，指定網路名稱。IP 位址範圍和子網路名稱。
2. 如果您需要進行容錯回復，您必須將 VPN/ExpressRoute 新增至網路。即使在容錯移轉之後，VPN/ExpressRoute 也可以新增至網路。 

[深入了解](../virtual-network/virtual-networks-overview.md) Azure 網路。

## 步驟 3：安裝 VMware 元件

如果您想要複寫 VMware 虛擬機器，請在管理伺服器上安裝下列 VMware 元件：

1. [下載](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)並安裝 VMware vSphere PowerCLI 6.0。
2. 重新啟動伺服器。


## 步驟 4：下載保存庫註冊金鑰

1. 在 Azure 中從管理伺服器開啟 Site Recovery 主控台。在 [復原服務] 頁面中，按一下保存庫來開啟 [快速啟動] 頁面。您也可以使用圖示隨時開啟 [快速啟動]。

	![快速啟動圖示](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. 在 [快速啟動] 頁面中，按一下 [準備目標資源] > [下載註冊金鑰]。註冊檔案會隨即自動產生。該金鑰在產生後會維持 5 天有效。


## 步驟 5：安裝管理伺服器
> [AZURE.TIP] 確定可以從管理伺服器存取這些 URL：
>
- *.hypervrecoverymanager.windowsazure.com
- *.accesscontrol.windows.net
- *.backup.windowsazure.com
- *.blob.core.windows.net
- *.store.core.windows.net
- http://dev.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi
- http://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. 在 [快速啟動] 頁面中，將已整合的安裝檔案下載至伺服器。
2. 執行安裝檔案以在 Site Recovery 整合安裝精靈啟動安裝程式。
3. 在 [開始之前] 中，選取 [安裝組態伺服器和處理序伺服器]。根據您部署的大小，稍後可能需要額外的處理序伺服器，但是第一次設定此部署時不需要。

	![開始之前](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)

4. 在 [協力廠商軟體安裝] 中，按一下 [我接受] 來下載並安裝 MySQL。

	![第三方=廠商軟體](./media/site-recovery-vmware-to-azure-classic/combined-wiz2.png)

5. 在 [網際網路設定] 中，指定將安裝在伺服器上的提供者，會如何透過網際網路連線到 Azure Site Recovery。

	- 如果您要讓提供者直接連線，請選取 [不使用 Proxy 直接連線]。
	- 如果您要使用目前在伺服器上設定的 Proxy 來連線，請選取 [以現有的 Proxy 設定連線]。
	- 如果您現有的 Proxy 需要驗證，或是您要讓提供者使用自訂 Proxy 來連線，請選取 [以自訂 Proxy 設定連線]。
	- 如果您使用自訂 proxy，您必須指定位址、連接埠以及認證
	- 如果您使用 proxy，則應該可以透過它存取下列 URL：

	![防火牆](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

7. 在 [先決條件檢查] 中，安裝程式會針對伺服器執行先決條件檢查。

	![必要條件](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

>[AZURE.WARNING] 如果您看到 [全域時間同步] 必要條件檢查的警告，請檢查您的系統時鐘的時間與時區相同。+ + ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. 在 [MySQL 組態] 中，建立認證來登入 MySQL 伺服器執行個體。您可以指定這些特殊字元：'\_'、'!'、'@'、'$'、'\\'、'%'。

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

9. 在 [環境詳細資料] 中，指定您是否要複寫 VMware VM。如果是的話，安裝程式會檢查是否已安裝 PowerCLI 6.0。

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

10. 在 [安裝位置] 中，選取您要安裝二進位檔及儲存快取的位置。我們建議快取磁碟機有 600 GB 或更大的可用空間。

	![安裝位置](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

11. 在 [網路選取] 中，指定伺服器會用來傳送和接收複寫資料的接聽程式 (網路介面卡和 SSL 連接埠)。您可以修改預設連接埠 (9443)。除了此連接埠之外，會在伺服器上開啟連接埠 443 以傳送和接收複寫協調流程的相關資訊。443 不應該用於複寫資料。


	![網路選擇](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

12. 在 [註冊] 中，瀏覽並選取您從保存庫下載的註冊金鑰。

	![註冊](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)

13.  在 [摘要] 中檢閱資訊。

	![摘要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING] 必須安裝 Microsoft Azure 復原服務代理程式的 Proxy。安裝完成之後，從 Windows [開始] 功能表啟動名為「Microsoft Azure 復原服務殼層」的應用程式。在開啟的命令視窗中，執行下列命令組來設定 Proxy 伺服器設定。
>
	$pwd = ConvertTo-SecureString -String ProxyUserPassword
	Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\\username -ProxyPassword $pwd
	net stop obengine
	net start obengine
	 


### 從命令列執行安裝程式

您也可以從命令列執行整合精靈，如下所示：

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

其中：

- / ServerMode：必要。指定安裝是否應該安裝組態和處理序伺服器或僅安裝處理序伺服器 (用來安裝額外的處理序伺服器)。輸入值：CS、PS
- InstallDrive：必要。指定要安裝元件的資料夾。
- /MySQLCredFilePath。必要。指定儲存 MySQL 伺服器認證的檔案的路徑。取得範本以建立檔案。
- /VaultCredFilePath。必要。保存庫認證檔的位置
- /EnvType。必要。安裝類型。值：VMware、NonVMware
- /PSIP 和 /CSIP。必要。處理序伺服器和組態伺服器 IP 位址。
- /PassphraseFilePath。必要。複雜密碼檔案的位置。
- /ByPassProxy。選用。指定管理伺服器不使用 proxy 連接至 Azure。
- /ProxySettingsFilePath。選用。指定自訂 proxy 的設定 (伺服器上需要驗證的預設 proxy，或是自訂的 proxy) 




## 步驟 6：設定 vCenter 伺服器的認證

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

處理序伺服器可以自動探索 vCenter 伺服器所管理的 VMware VM。對於自動探索，Site Recovery 需要可以存取 vCenter 伺服器的帳戶和認證。如果您僅是複寫實體伺服器，則這個動作不相關。

以下列方式來執行此動作：

1. 在 vCenter 伺服器上，建立擁有[必要權限](#vmware-permissions-for-vcenter-access)的 vCenter 層級角色 (**Azure\_Site\_Recovery**)。
2. 將 **Azure\_Site\_Recovery** 角色指派給 vCenter 使用者。

	>[AZURE.NOTE] VCenter 使用者帳戶具有唯讀角色，可以執行容錯移轉，而不需要關閉受保護的來源機器。如果您想要關閉這些機器，您需要 Azure\_Site\_Recovery 角色。請注意，如果您只是從 VMware 將 VM 移轉至 Azure，且不需要容錯回復，則唯讀角色已足夠。

3. 若要新增帳戶，請開啟 **cspsconfigtool**。它會在桌面上的捷徑，位於 [安裝位置]\\home\\svsystems\\bin 資料夾。
2. 在 [管理帳戶] 索引標籤中，按一下 [加入帳戶]。

	![新增帳戶](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. 在 [帳戶詳細資料] 中，新增可用來存取 vCenter 伺服器的認證。請注意，可能要超過 15 分鐘帳戶名稱才會出現在入口網站。如要立刻更新，請按一下 [組態伺服器] 索引標籤上的 [重新整理]。

	![詳細資料](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## 步驟 7：新增 vCenter 伺服器和 ESXi 主機

如果您是複寫 VMware VM，則需要新增 vCenter 伺服器 (或 ESXi 主機)。

1. 在 [伺服器] > [組態伺服器] 索引標籤上，選取組態伺服器 > [新增 vCenter 伺服器]。

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. 新增 vCenter 伺服器或 ESXi 主機詳細資料、您在上一個步驟中指定來存取 vCenter 伺服器的帳戶的名稱，以及將用來探索由 vCenter 伺服器管理的 VMware VM 的處理序伺服器。請注意，vCenter 伺服器或 ESXi 主機應位於與安裝處理序伺服器所在的伺服器相同的網路中。

	>[AZURE.NOTE] 如果您要以沒有 vCenter 或主機伺服器的系統管理員權限的帳戶新增 vCenter 伺服器或 ESXi 主機，則確認 vCenter 或 ESXi 帳戶已啟用這些權限：資料中心、資料存放區、資料夾、Jost、網路、資源、虛擬機器，vSphere 分散式切換。此外，vCenter 伺服器需要儲存體檢視權限。

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. 探索完成之後，vCenter 伺服器將會列在 [組態伺服器] 索引標籤中。

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)
		

## 步驟 8：建立保護群組

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


保護群組包含虛擬機器或實體伺服器，它們共用相同的複寫設定。

1. 開啟 [受保護項目] > [保護群組] 並且按一下以加入保護群組。

	![建立保護群組](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. 在 [指定保護群組設定] 頁面上，指定群組名稱，並在 [從] 中選取您要建立群組的組態伺服器。[目標] 則為 Azure。

	![保護群組設定](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. 在 [指定複寫設定] 頁面上，進行將用於群組中所有機器的複寫設定。

	![保護群組複寫](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

	- **多部 VM 一致性**：如果您開啟這項功能，則會在保護群組中的機器之間，建立共用的應用程式一致復原點。當保護群組中的所有機器都執行相同的工作負載時，這項設定就可以發揮最高的重要性。所有機器都將復原到相同的資料點。無論您是複寫 VMware VM 或 Windows/Linux 實體伺服器，都可以使用。
	- **RPO 臨界值**：設定 RPO。當連續資料保護複寫超過設定的 RPO 臨界值時，就會產生警示。
	- **復原點保留期**：指定保留週期。受保護的機器可以復原到這個週期內的任意點。
	- **應用程式一致的快照頻率**：指定建立包含應用程式一致快照的復原點的頻率。

當您按一下核取記號時，會以您指定的名稱建立保護群組。此外會以名稱 <protection-group-name-Failback) 建立第二個保護群組。如果您在容錯移轉至 Azure 之後容錯回復到內部部署網站，會使用此保護群組。您可以監視保護群組，因為它們是在 [受保護項目] 頁面建立的。

## 步驟 9：安裝行動服務

啟用虛擬機器和實體伺服器保護的第一個步驟是安裝行動服務。您可以使用兩種方式執行此動作：

- 從處理序伺服器自動推入並在每一部機器上安裝服務。請注意，當您將機器新增至已在執行適當版本行動服務的保護群組時，就不會發生推入安裝。
- 使用您的企業推入方法自動安裝，例如 WSUS 或 System Center Configuration Manager。請確定您在執行這項操作之前已設定管理伺服器。
- 在您想要保護的每部機器上手動安裝。確定您在執行這項操作之前已設定管理伺服器。


### 使用推入安裝來安裝行動服務

當您將機器加入保護群組時，行動服務會自動由處理序伺服器推入並安裝於每部機器上。


#### 準備在 Windows 機器上自動推入 

以下是如何準備 Windows 機器，讓處理序伺服器可以自動安裝行動服務。

1.  建立可以由處理序伺服器用來存取機器的帳戶。帳戶應該具有系統管理員權限 (本機或網域)。請注意，這些認證僅可用於行動服務的推入安裝。

	>[AZURE.NOTE] 如果您未使用網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。若要執行此動作，請在 HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System 下加入值為 1 的 LocalAccountTokenFilterPolicy DWORD 登錄項目。如要透過 CLI 開啟命令或使用 PowerShell 新增登錄項目，請輸入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。

2.  在您想要保護之機器的 Windows 防火牆上，選取 [允許應用程式或功能通過防火牆]，並啟用 [檔案及印表機共用] 和 [Windows Management Instrumentation]。針對隸屬於網域中的機器，您可以利用 GPO 設定防火牆原則。

	![防火牆設定](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. 加入您所建立的帳戶：

	- 開啟 **cspsconfigtool**。它會在桌面上的捷徑，位於 [安裝位置]\\home\\svsystems\\bin 資料夾。
	- 在 [管理帳戶] 索引標籤中，按一下 [加入帳戶]。
	- 加入您所建立的帳戶。加入帳戶之後，當您將機器加入保護群組時，您必須提供這些認證。


#### 準備在 Linux 伺服器上自動推入

1.	確定您要保護的 Linux 機器受到支援，如[內部部署必要條件](#on-premises-prerequisites)一節中所述。請確保您想要保護的電腦與執行處理序伺服器的管理伺服器之間的網路連線能力。 

2.	建立可以由處理序伺服器用來存取機器的帳戶。帳戶應該是來源 Linux 伺服器上的根使用者。請注意，這些認證僅可用於行動服務的推入安裝。

	- 開啟 **cspsconfigtool**。它會在桌面上的捷徑，位於 [安裝位置]\\home\\svsystems\\bin 資料夾。
	- 在 [管理帳戶] 索引標籤中，按一下 [加入帳戶]。
	- 加入您所建立的帳戶。加入帳戶之後，當您將機器加入保護群組時，您必須提供這些認證。

3.	檢查來源 Linux 伺服器上的 /etc/hosts 檔案包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
4.	在您想要保護的電腦上安裝最新的 openssh、openssh 伺服器、openssl 套件。
5.	請確定 SSH 已啟用且正在連接埠 22 上執行。 
6.	在 sshd\_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示： 

	- 以 root 的身分登入。
	- 在 /etc/ssh/sshd\_config 檔案中，尋找以 PasswordAuthentication 開頭的那一行。
	- 取消該行的註解並將值從 **no** 變更為 **yes**。
	- 尋找以 **Subsystem** 開頭的行並取消其註解。
 
		![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### 手動安裝行動服務

安裝程式位於 C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository。

來源作業系統 | 行動服務安裝檔案
--- | ---
Windows Server (僅限 64 位元) | Microsoft-ASR\_UA\_9.*.0.0\_Windows\_* release.exe
CentOS 6.4、6.5、6.6 (僅限 64 位元) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (僅限 64 位元) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4、6.5 (僅限 64 位元) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### 在 Windows 伺服器上手動安裝


1. 下載並執行相關安裝程式。
2. 在 [開始之前] 中，選取 [行動服務]。

	![行動服務](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. 在 [組態伺服器詳細資料] 中，指定管理伺服器的 IP 位址，以及當您安裝管理伺服器元件時所產生的複雜密碼。您可以在管理伺服器上執行下列命令來取得複雜密碼：**<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n**。

	![行動服務](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. 在 [安裝位置] 中，不要變更預設位置，然後按 [下一步] 來開始安裝。
5. 在 [安裝進度] 中，監視安裝的進度，並在系統提示時重新啟動機器。

您也可以從命令列安裝：

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <安裝目錄>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <複雜密碼檔案路徑>] [/LogFilePath <Log File Path>]

其中：

- /Role：必要。指定是否應該要安裝行動服務。
- /InstallLocation：必要。指定安裝服務的位置。
- /PassphraseFilePath：必要。指定組態伺服器複雜密碼。
- /LogFilePath：必要。指定記錄安裝檔案位置 

#### 修改管理伺服器的 IP 位址

在執行精靈之後，您可以修改管理伺服器的 IP 位址，如下所示：

1. 開啟檔案 hostconfig.exe (位於桌面上)。
2. 在 [全域] 索引標籤中，您可以變更管理伺服器的 IP 位址。

	>[AZURE.NOTE] 您應該只變更管理伺服器的 IP 位址。管理伺服器通訊的連接埠號碼必須是 443，且 [使用 HTTPS] 應保持啟用。複雜密碼不得修改。

	![管理伺服器 IP 位址](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### 在 Linux 伺服器上手動安裝：

1. 根據上表，將適當的 tar 封存檔複製到您要保護的 Linux 機器。
2. 開啟殼層程式，並將壓縮的 tar 封存檔解壓縮到本機路徑，方法是執行：`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. 在解壓縮 tar 封存檔內容的本機目錄中建立 passphrase.txt 檔案。若要執行這項操作，在管理伺服器上從 C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase 複製複雜密碼，並將它儲存在 passphrase.txt，方法是在殼層中執行 *`echo <passphrase> >passphrase.txt`*。
4. 輸入 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* 來安裝行動服務。
5. 指定管理伺服器的內部 IP 位址，並確定已選取連接埠 443。

**您也可以使用命令列來安裝**：

1. 在管理伺服器上從 C:\\Program Files (x86)\\InMage Systems\\private\\connectio 複製複雜密碼，並且在管理伺服器上將它儲存為 "passphrase.txt"。然後執行以下命令。在我們的範例中，管理伺服器的 IP 位址是 104.40.75.37 且 HTTPS 連接埠應該是 443：

若要在實際執行伺服器上安裝：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt
 
若要在主要目標伺服器上安裝：


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## 步驟 10：對機器啟用保護

若要啟用保護，您會將虛擬機器和實體伺服器加入至保護群組。在開始之前，如果您要保護 VMware 虛擬機器，請注意下列項目：

- 系統會每隔 15 分鐘探索 VMware VM 一次，而且在探索之後，需要最多 15 分鐘的時間，VMware VM 才會出現在 Site Recovery 入口網站中。
- 虛擬機器上的環境變更 (例如 VMware 工具安裝) 也可能需要超過 15 分鐘的時間，才能在 Site Recovery 中更新。
- 您可以透過 [組態伺服器] 索引標籤上 vCenter 伺服器/ESXi 主機的 [上次連絡時間] 欄位，查看 VMware VM 的上次探索時間。
- 如果您已經建立保護群組，並隨後新增 vCenter Server 或 ESXi 主機，就可能需要 15 分鐘以上的時間，才能讓 Azure Site Recovery 入口網站重新整理，並且讓 [將機器加入保護群組] 對話方塊列出虛擬機器。
- 如果您想要立即繼續將機器加入至保護群組，而不想等候排程的探索，請反白顯示設定伺服器 (不要按它)，然後按一下 [重新整理] 按鈕。

此外，請注意：

- 建議您架構保護群組，使其反映您的工作負載。例如，將執行特定應用程式的機器加入至相同群組。
- 當您將機器加入至保護群組時，如果尚未安裝行動服務，則處理序伺服器會自動推入並安裝行動服務。請注意，您必須準備上一個步驟中所述的推入機制。


將機器加入保護群組：

1. 按一下 [受保護項目] > [保護群組] > [機器] > [加入機器]。\\為最佳做法 
2. 在 [選取虛擬機器] 中，如果您要保護 VMware 虛擬機器，請選取正在負責管理您的虛擬機器 (或其執行所在的 EXSi 主機) 的 vCenter 伺服器，然後選取機器。

	![啟用保護。](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  在 [選取虛擬機器] 中，如果您要保護實體伺服器，在 [加入實體機器] 精靈中，提供 IP 位址和易記名稱。然後選取作業系統系列。

	![啟用保護。](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
		
4. 在 [指定目標資源] 中，選取要用於複寫的儲存體帳戶，並選取是否讓該設定用於所有工作負載。請注意，進階儲存體帳戶目前不支援。

	![啟用保護。](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. 在 [指定帳戶] 中，選取您[已設定](#install-the-mobility-service-with-push-installation)要用來自動安裝行動服務的帳戶。

	![啟用保護。](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. 按一下核取記號，以完成將機器加入保護群組，並啟動每部機器的初始複寫。

	>[AZURE.NOTE] 如果已備妥推入安裝，則行動服務會在機器加入至保護群組時自動安裝在沒有該服務的機器上。服務安裝之後，保護作業會開始，並且會失敗。在失敗之後您必須手動重新啟動已安裝行動服務的每一部機器。重新啟動之後，保護作業再次開始，並且會發生初始複寫。

您可以在 [工作] 頁面上監視狀態。

![啟用保護。](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

此外，您可以在 [受保護項目] > <protection group name> > [虛擬機器] 中監視保護狀態。初始複寫完成並且同步處理資料之後，機器狀態會變更為 **受保護**。

![啟用保護。](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## 步驟 11：設定受保護機器屬性

1. 在機器具有**受保護**狀態之後，您可以設定其容錯移轉屬性。在保護群組詳細資料中，選取機器並開啟 [設定] 索引標籤。
2. Site Recovery 會自動建議 Azure VM 的屬性，並偵測內部部署網路設定。 

	![設定虛擬機器屬性](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. 您可以修改這些設定：

	-  **Azure VM 名稱**：這是將在容錯移轉之後提供給 Azure 中機器的名稱。名稱必須符合 Azure 需求。
	-  **Azure VM 大小**：網路介面卡的數目會視您指定給目標虛擬機器的大小而有所不同。[深入了解](../virtual-machines/virtual-machines-size-specs.md#size-tables)大小和介面卡。請注意：
		- 當您修改虛擬機器的大小並儲存設定之後，下次您開啟 [設定] 索引標籤時，網路介面卡的數量將會改變。目標虛擬機器的網路介面卡數目，是來源虛擬機器上的網路介面卡數目下限，以及所選虛擬機器大小支援的網路介面卡數目上限。 
			- 如果來源電腦上的網路介面卡數目小於或等於針對目標機器大小所允許的介面卡數目，則目標將具備與來源相同的介面卡數目。
			- 如果來源虛擬機器的介面卡數目超過針對目標大小所允許的數目，則將使用目標大小的最大值。
			- 例如，如果來源機器具有兩張網路介面卡，而目標機器大小支援四張，則目標機器將會有兩張介面卡。如果來源機器具有兩張介面卡，但支援的目標大小僅支援一張，則目標機器將只會有一張介面卡。
		- 如果虛擬機器具有多張網路介面卡，則所有的介面卡都應該連接到同一個 Azure 網路。 
	- **Azure 網路**：您必須指定在容錯移轉之後 Azure VM 將會連接的 Azure 網路。如果您未指定一個網路，則 Azure VM 不會連接到任何網路。此外，如果您想要從 Azure 容錯回復到內部部署網站，您必須指定 Azure 網路。容錯回復需要 Azure 網路與內部部署網路之間的 VPN 連線。	
	- **Azure IP 位址/子網路**：您可以針對每個網路介面卡選取 Azure VM 應該連接的子網路。請注意：
		- 如果來源機器的網路介面卡設定為使用靜態 IP 位址，您可以為 Azure VM 指定靜態 IP 位址。如果您未指定靜態 IP 位址，則將會配置任何可用的 IP 位址。如果已指定目標 IP 位址，但是已由 Azure 中的另一個 VM 使用，則容錯移轉將會失敗。如果來源機器的網路介面卡設定為使用 DHCP，則您將具有 DHCP 做為 Azure 的設定。

## 步驟 12：建立復原計畫並執行容錯移轉

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

您可以執行單一機器的容錯移轉，或容錯移轉執行相同工作或執行相同工作負載的多部虛擬機器。若要同時容錯移轉多部機器，將它們加入至復原計畫。

### 建立復原計畫

1. 在 [復原計畫] 頁面上，按一下 [加入復原計畫] 並加入復原計畫。指定計畫的詳細資料並選取 **Azure** 做為目標。

	![設定復原計畫](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. 在 [選取虛擬機器] 中，選取保護群組，然後在群組中選取要加入復原計畫的機器。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

您可以自訂計畫以建立群組並決定機器在復原計劃中容錯移轉的順序。您也可以加入進行手動動作的指令碼和提示。指令碼可以手動建立，或使用 [Azure 自動化 Runbook](site-recovery-runbook-automation.md) 建立。[深入了解](site-recovery-create-recovery-plans.md)自訂復原計畫。

## 執行容錯移轉

在執行容錯移轉之前，請注意：


- 請確定管理伺服器正在執行並且可以使用，否則容錯移轉將會失敗。
- 如果您執行非計劃性容錯移轉，請注意：

	- 如果可能的話您應該在執行非計劃性容錯移轉之前關閉主要機器。這可確保您不需要同時執行來源和複本機器。如果您是複寫 VMware VM，則當您執行非計劃性容錯移轉時，您可以指定 Site Recovery 應該要盡全力關閉來源機器。根據主要網站的狀態，可能可以運作。如果您是複寫實體伺服器，則 Site Recovery 不提供此選項。 
	- 當您執行非計劃性容錯移轉時，會停止從主要機器的資料複寫，讓任何資料差異不會在開始非計劃性容錯移轉之後傳送。
	
- 如果您要在容錯移轉之後連接到 Azure 中的複本虛擬機器，請先在來源機器上啟用遠端桌面連線，再執行測試容錯移轉，並且允許 RDP 連接通過防火牆。您也需要在容錯移轉之後，在 Azure 虛擬機器的公用端點上允許 RDP。請依照這些[最佳做法](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)進行，以確保 RDP 在容錯移轉之後能夠運作。


### 執行測試容錯移轉

執行測試容錯移轉，以在不會影響生產環境的隔離網路中模擬您的容錯移轉和復原程序，一般複寫會如往常繼續。測試容錯移轉會在來源上啟動，您可以用數種方式執行：

- **不指定 Azure 網路**：如果您在沒有網路的環境執行測試容錯移轉，該測試只會檢查虛擬機器是否啟動，且是否在 Azure 中正確顯示。在容錯移轉之後，虛擬機器不會連線到 Azure 網路。
- **指定 Azure 網路**：這種容錯移轉會檢查整個複寫環境是否如預期般出現，且該 Azure 虛擬機器會連線到指定的網路。 


1. 在 [復原計畫] 頁面中，選取計畫並按一下 [測試容錯移轉]。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. 在 [確認測試容錯移轉] 中，選取 [無]，表示您不打算針對測試容錯移轉使用 Azure 網路，或選取將在容錯移轉之後測試 VM 連線的網路。按一下核取記號以啟動容錯移轉。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. 在 [作業] 索引標籤上監視容錯移轉的進度。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. 容錯移轉完成之後，您應該也會看到複本 Azure 機器出現在 Azure 入口網站 > [虛擬機器]。如果您想要起始 RDP 連線至 Azure VM，您必須在 VM 端點上開啟連接埠 3389。

5. 完成之後，在容錯移轉到達完成測試階段時，請按一下 [完成測試] 來完成。在 [記事] 中，記錄並儲存關於測試容錯移轉的任何觀察。

6. 按一下 [測試容錯移轉已完成]，即可自動清除測試環境。此動作完成之後，測試容錯移轉將會顯示 [完成] 狀態。任何在測試容錯移轉期間自動建立的元素或 VM 都將刪除。請注意，如果測試容錯移轉持續兩週以上，系統即會強制完成該測試容錯移轉。


	


### 執行非計劃性容錯移轉

非計劃性容錯移轉是從 Azure 起始，即使主要網站無法使用也可以執行。


1. 在 [復原計畫] 頁面中，選取計畫並按一下 [容錯移轉] > [非計劃性容錯移轉]。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. 如果您是複寫 VMware 虛擬機器，您可以選取以嘗試關閉內部部署 VM。這是最佳方式，而且無論成功與否容錯移轉都會繼續。如果不成功，則 [作業] 索引標籤 > [未規劃的容錯移轉作業] 會出現錯誤的詳細資料。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

	>[AZURE.NOTE] 如果您是複寫實體伺服器，則這個選項無法使用。您必須盡可能嘗試手動關閉。
	
3. 在 [確認容錯移轉] 中，確認容錯移轉的方向 (朝向 Azure)，然後選取您要用於容錯移轉的復原點。如果您啟用多個 VM，您在設定複寫屬性時可以復原到最新的應用程式或當機時保持一致復原點。您也可以選取 [自訂復原點]，來復原至較早的時間點。按一下核取記號以啟動容錯移轉。

	![新增虛擬機器](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. 等候非計劃性容錯移轉作業完成。您可以在 [作業] 索引標籤上監視容錯移轉的進度。請注意，即使在非計劃性容錯移轉期間發生錯誤，復原方案還是會執行，直到它完成為止。您也應該可以看到複本 Azure 機器出現在 Azure 入口網站中的 [虛擬機器]。

### 在容錯移轉之後連接到複寫的 Azure 虛擬機器

若要在容錯移轉之後連接到 Azure 中的複寫的虛擬機器，以下是您需要的內容：

1. 應該在主要機器上啟用遠端桌面連線。
2. 主要機器上的 Windows 防火牆允許 RDP。
3. 在容錯移轉之後，您必須將 RDP 新增至 Azure 虛擬機器的公用端點。

[深入了解](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)設定。


## 部署額外處理序伺服器

如果您必須相應放大您的部署超過 200 部來源機器，或每日變換率總計超過 2 TB，您將需要額外的處理序伺服器來處理流量。若要設定額外處理序伺服器，請檢查[額外處理序伺服器](#additional-process-servers)中的需求，然後依照此處的指示來設定處理序伺服器。設定伺服器之後，您可以設定要使用它的來源機器。

### 設定額外處理序伺服器

您設定額外處理序伺服器，如下所示：

- 執行整合精靈，設定管理伺服器僅做為處理序伺服器。
- 如果您想要僅使用新的處理序伺服器來管理資料複寫，必須移轉受保護的機器以執行這項操作。

### 安裝處理序伺服器

1. 在 [快速啟動] 頁面下載整合安裝檔案以進行 Site Recovery 元件安裝。執行安裝程式。
2. 在 [開始之前] 中，選取 [新增額外處理序伺服器以相應放大部署]。

	![新增處理序伺服器](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. 以您[設定](#step-5-install-the-management-server)第一個管理伺服器時相同的方式來完成精靈。
4. 在 [組態伺服器詳細資料] 中，為您之前已安裝組態伺服器的原始管理伺服器指定 IP 位址及複雜密碼。在原始管理伺服器上執行 **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** 以取得複雜密碼。

	![新增處理序伺服器](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### 移轉機器以使用新的處理序伺服器

1. 開啟 [組態伺服器] > [伺服器] > 原始管理伺服器的名稱 > [伺服器詳細資料]。

	![更新處理序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. 在 [**處理序伺服器**] 清單中，按一下您要修改的伺服器旁的 [**變更處理序伺服器**]。

	![更新處理序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. 在 [變更處理序伺服器] > [目標處理序伺服器] 中，選取新的管理伺服器，然後選取新的處理序伺服器將處理的虛擬機器。按一下資訊圖示以取得伺服器的相關資訊。隨即顯示將每個選取的虛擬機器複寫到新的處理序伺服器所需的平均空間，協助您進行負載的判斷。按一下核取記號以開始複寫到新處理序伺服器。

	![更新處理序伺服器](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

	


## vCenter 存取的 VMware 權限

處理序伺服器可以自動探索 vCenter 伺服器上的 VM。若要執行自動探索，您必須在 vCenter 層級定義角色 (Azure\_Site\_Recovery)，以允許 Site Recovery 存取 vCenter 伺服器。請注意，如果您只需要將 VMware 機器移轉至 Azure，而不需要從 Azure 容錯回復，您可以定義唯讀角色，這個角色已足夠。如[步驟 6：設定 vCenter 伺服器的認證](#step-6-set-up-credentials-for-the-vcenter-server)中所述設定權限。下表摘要說明角色權限。

**角色** | **詳細資料** | **權限**
--- | --- | ---
Azure\_Site\_Recovery 角色 | VMware VM 探索 |請針對 v-Center 伺服器指派這些權限：<br/><br/>資料存放區 -> 配置空間、瀏覽資料存放區、低階檔案作業、移除檔案、更新虛擬機器檔案<br/><br/>網路 -> 網路指派<br/><br/>資源 -> 指派虛擬機器至資源集區、移轉已關閉電源的虛擬機器、移轉已開啟電源的虛擬機器<br/><br/>工作 -> 建立工作、更新工作<br/><br/>虛擬機器 -> 組態<br/><br/>虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟機媒體、關閉電源、開啟電源、VMware 工具安裝<br/><br/>虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/>虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/>虛擬機器 -> 快照 -> 移除快照
vCenter 使用者角色 | VMware VM 探索/容錯移轉而不關閉來源 VM | 請針對 v-Center 伺服器指派這些權限：<br/><br/>資料中心物件 –> 傳播至子物件、角色=唯讀<br/><br/>使用者是在資料中心層級指派的，因此具有資料中心內所有物件的存取權。如果您想要限制存取權，請將具有 [傳播至子物件] 的 [沒有存取權] 角色指派給子物件 (ESX 主機、資料存放區、VM 及網路)。 
vCenter 使用者角色 | 容錯移轉和容錯回復 | 請針對 v-Center 伺服器指派這些權限：<br/><br/>資料中心物件 –> 傳播至子物件、角色=Azure\_Site\_Recovery<br/><br/>使用者是在資料中心層級指派的，因此具有資料中心內所有物件的存取權。如果您想要限制存取，請將具有 [傳播至子物件] 的 [沒有存取權] 角色指派給子物件 (ESX 主機、資料存放區、VM 及網路)。 



## 第三方廠商軟體注意事項和資訊

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).Microsoft is the not original author of the Third Party Code.The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below.Such licenses and information are provided for informational purposes only.This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428).Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## 後續步驟

[深入了解容錯回復](site-recovery-failback-azure-to-vmware-classic.md)，以便在 Azure 中將執行失敗的機器還原到您的內部部署環境。

<!----HONumber=AcomDC_0218_2016-->