<properties 
	pageTitle="SQL Server 效能最佳作法 | Microsoft Azure"
	description="提供將 Microsoft Azure VM 中 SQL Server 效能最佳化的最佳作法。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management" />
	
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/13/2015"
	ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server 效能最佳作法

## 概觀

本主題提供將 Microsoft Azure 虛擬機器中 SQL Server 的效能最佳化的最佳作法。在 Azure 虛擬機器中執行 SQL Server 時，我們建議您繼續使用相同的資料庫效能微調選項，這些選項適用於內部部署伺服器環境中的 SQL Server。不過，公用雲端中關聯式資料庫的效能優劣取決於許多因素，例如虛擬機器的大小和資料磁碟的組態。

建立 SQL Server 映像時可考慮使用[新入口網站](https://manage.windowsazure.com)，以善用各項功能 (例如預設使用進階儲存體) 以及其他選項 (例如自動修補、自動備份和 AlwaysOn 組態)。

本文的主題為如何讓 Azure VM 上的 SQL Server 達到最佳效能。如果您的工作負載需求較低，可能就不需要採用下列每一項最佳化條件。評估以下建議時，請考慮您的效能需求和工作負載模式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 快速檢查清單

下列快速檢查清單能協助您讓 Azure 虛擬機器上的 SQL Server 達到最佳效能：

|領域|最佳化|
|---|---|
|**VM 大小**|適用於 SQL Enterprise Edition 的 [DS3](virtual-machines-size-specs.md#standard-tier-ds-series) 或更高版本。<br/><br/>適用於 SQL Standard Edition 和 Web Edition 的 [DS2](virtual-machines-size-specs.md#standard-tier-ds-series) 或更高版本。|
|**儲存體**|使用[進階儲存體](../storage/storage-premium-storage-preview-portal.md)。<br/><br/>在相同的區域中保留[儲存體帳戶](../storage/storage-create-storage-account.md)和 SQL Server VM。<br/><br/>在儲存體帳戶上停用 Azure [異地備援儲存體](../storage/storage-redundancy.md) (異地複寫)。|
|**磁碟**|使用至少 2 個 [P30 磁碟](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whzh-TWing-premium-storage) (一個用於記錄檔，另一個用於資料檔案和 TempDB)。<br/><br/>避免使用作業系統或暫存磁碟進行資料儲存或記錄。<br/><br/>在裝載資料檔案和 TempDB 的磁碟上啟用讀取快取。<br/><br/>不要在裝載記錄檔的磁碟上啟用快取。<br/><br/>分割多個 Azure 資料磁碟，以提高 IO 輸送量。<br/><br/>以文件上記載的配置大小格式化。|
|**I/O**|啟用資料庫頁面壓縮。<br/><br/>啟用資料檔案的立即檔案初始化。<br/><br/>限制或停用資料庫的自動成長。<br/><br/>停用資料庫的自動壓縮。<br/><br/>將所有資料庫移至資料磁碟，包括系統資料庫。<br/><br/>將 SQL Server 錯誤記錄檔和追蹤檔案目錄移至資料磁碟。<br/><br/>設定預設備份和資料庫檔案位置。<br/><br/>啟用鎖定的頁面。<br/><br/>套用 SQL Server 效能修正程式。|
|**特定功能**|直接備份至 Blob 儲存體。|

如需詳細資訊，請參閱下列小節中的指導方針。

## 虛擬機器大小與儲存體帳戶的考量

對於需要高效能的應用程式，建議您採用下列虛擬機器大小：

- **SQL Server Enterprise Edition**：DS3 或更高版本

- **SQL Server Standard Edition 和 Web Edition**：DS2 或更高版本

如需支援的虛擬機器大小的最新資訊，請參閱[虛擬機器的大小](virtual-machines-size-specs.md)。

此外，我們建議您在存放 SQL Server 虛擬機器的同一個資料中心內建立 Azure 儲存體帳戶，以減少傳輸延遲的狀況。建立儲存體帳戶時，請停用 [異地複寫] 功能，因為跨多個磁碟時無法保證寫入順序一致。請考慮另一個方法，在兩個 Azure 資料中心之間設定 SQL Server 災害復原技術。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。

## 磁碟和效能考量

建立 Azure 虛擬機器時，該平台會至少將一個磁碟連接至 VM 作為作業系統磁碟。此磁碟是以分頁 Blob 的形式儲存於儲存體的 VHD。您也可以將其他磁碟連接至虛擬機器作為資料磁碟，這些磁碟將會以分頁 Blob 形式儲存於儲存體。Azure 虛擬機器中還有另一個磁碟，稱為暫存磁碟。此磁碟位於可用於塗銷空間的節點上。

### 作業系統磁碟

作業系統磁碟是指可開機，並掛接為作業系統執行版本，且標示為 **C** 磁碟機的 VHD。

作業系統磁碟上的預設快取原則是**讀取/寫入**。對於需要高效能的應用程式，我們建議您使用資料磁碟取代作業系統磁碟。請參閱下面的＜資料磁碟＞一節。

### 暫存磁碟

標示為 **D**: 磁碟機的暫存磁碟機不會保存至 Azure Blob 儲存體。請勿將資料或記錄檔案儲存在 **D**: 磁碟機上。

使用 D 系列或 G 系列的虛擬機器 (VM) 時，才會在 **D** 磁碟機上儲存 TempDB 和/或緩衝集區擴充功能。不同於其他 VM 系列，D 系列和 G 系列 VM 中的 **D** 磁碟機為 SSD 式。這可改善大量使用暫存物件，或工作集超過記憶體容量的工作負載的效能。如需詳細資訊，請參閱[使用 Azure VM 中的 SSD 來儲存 SQL Server TempDB 和緩衝集區擴充功能](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx)。

### 資料磁碟

- **資料和記錄檔的資料磁碟數量**：至少使用 2 個 [P30 磁碟](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whzh-TWing-premium-storage)，一個儲存記錄檔，另一個儲存資料檔案及存放 TempDB。若要提高輸送量，您可能需要額外的資料磁碟。若要判斷需要的資料磁碟數量，您需要分析資料和記錄磁碟可用的 IOPS 數量。如需相關資訊，請參閱根據 [VM 大小](virtual-machines-size-specs.md)分類的 IOPS 表格，以及下列[針對磁碟使用進階儲存體](../storage/storage-premium-storage-preview-portal.md)文章中的磁碟大小部分。如果您需要更多的頻寬，可以附加額外磁碟，並使用磁碟分割。如果您並非使用進階儲存體，建議您新增 [VM 大小](virtual-machines-size-specs.md)所支援的資料磁碟數目上限，並使用磁碟分割。如需有關磁碟分割的詳細資訊，請參閱以下相關章節。

- **快取原則**：僅在裝載資料檔案和 TempDB 的資料磁碟上啟用讀取快取。如果您並非使用進階儲存體，請勿啟用任何資料磁碟上的任何快取功能。如需設定磁碟快取的指示，請參閱下列主題：[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。

- **NTFS 配置單位大小**：將資料磁碟格式化時，建議您針對資料、記錄檔以及 TempDB，採用 64 KB 的配置單位大小。

- **磁碟分割**：建議您依照以下指導方針執行：

	- 若為 Windows 8/Windows Server 2012 以上版本，請參閱[儲存空間](https://technet.microsoft.com/library/hh831739.aspx)。將 OLTP 工作負載的等量磁碟區大小設為 64 KB，資料倉儲的工作負載則設為 256 KB，以避免分割對齊錯誤影響效能。此外，設定資料行計數 = 實體磁碟數量。若要設定磁碟超過 8 個的儲存空間，您必須使用 PowerShell (非伺服器管理員 UI)，以明確地將資料行計數設定為符合磁碟的數量。如需如何設定[儲存空間](https://technet.microsoft.com/library/hh831739.aspx)的詳細資訊，請參閱 [Windows PowerShell 中的儲存空間 Cmdlet](https://technet.microsoft.com/library/jj851254.aspx)
	
	- 對於 Windows 2008 R2 之前的版本，可以使用動態磁碟 (OS 分割的磁碟區)，且等量磁碟區的大小一律為 64 KB。請注意，Windows 8/Windows Server 2012 已不再提供此選項。如需相關資訊，請參閱[虛擬磁碟服務正轉換為 Windows 存放管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx) 中的支援聲明。
	
	- 如果您的工作負載不需大量記錄及，且不需專屬於 IOP，您可以只設定一個儲存體集區。否則，請建立兩個儲存體集區，一個用於儲存記錄檔案，另一個用於儲存資料檔案和存放 TempDB。請根據您預期的負載量，決定與每個儲存體集區相關聯的磁碟數量。請注意，各 VM 大小所允許連接的資料磁碟數量皆不同。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-size-specs.md)。

## I/O 效能考量

- 平行處理應用程式和要求時，進階儲存體可以達到最佳效能。進階儲存體是專為 IO 佇列深度大於 1 的案例所設計，所以您會發現單一執行緒的序列要求 (即使其為儲存密集型) 的效能只有微幅提升，或沒有提升。舉例來說，這可能會影響效能分析工具 (如 SQLIO) 的單一執行緒測試結果。

- 請考慮使用[資料庫頁面壓縮](https://msdn.microsoft.com/library/cc280449.aspx)功能，其有助於改善 I/O 密集型工作負載的效能。不過，資料壓縮可能會增加資料庫伺服器的 CPU 使用量。

- 將資料檔案傳輸至 Azure，或從 Azure 往外傳輸時，請考慮先壓縮所有資料檔案。

- 請考慮啟用 [立即檔案初始化] 功能，以減少配置初始檔案所需的時間。若要發揮「立即檔案初始化」的優點，請將 SE\_MANAGE\_VOLUME\_NAME 授與 SQL Server (MSSQLSERVER) 服務帳戶，並將該帳戶加入 [執行磁碟區維護工作] 安全性原則。如果您使用的是 Azure 的 SQL Server 平台映像，則預設服務帳戶 (NT Service\\MSSQLSERVER) 不會加入 [執行磁碟區維護工作] 安全性原則。也就是說，SQL Server Azure 平台映像未啟用 [立即檔案初始化] 功能。將 SQL Server 服務帳戶加入 [執行磁碟區維護工作] 安全性原則之後，請重新啟動 SQL Server 服務。使用此功能時，可能有安全性考量。如需詳細資訊，請參閱[資料庫檔案初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

- **自動成長**只是發生非預期成長時的應變方案。請勿每天使用「自動成長」功能，管理資料和記錄成長。若已使用「自動成長」功能，請透過 大小參數預先放大檔案。

- 請確定已停用**自動壓縮**，以避免不必要的額外負荷，而對效能造成負面影響。

- 如果您執行的是 SQL Server 2012，請安裝 Service Pack 1 累計更新 10。此更新包含一個修正程式，可修正在 SQL Server 2012 中執行 select into 暫存資料表陳述式時，I/O 效能不佳的狀況。如需相關資訊，請參閱此[知識庫文章](http://support.microsoft.com/kb/2958012)。

- 將 SQL Server 的系統資料庫 (例如 msdb 和 TempDB)、備份，以及預設資料和記錄目錄移至非快取的資料磁碟，以改善效能。然後，執行下列動作：

	- 修改 XEvent 和追蹤檔案的路徑。
	
	- 修改 SQL 錯誤記錄檔的路徑。
	
	- 修改預設備份的路徑。
	
	- 修改預設資料庫的位置。

- 建立鎖定的頁面，以減少 IO 和任何分頁活動。

## 功能特定的效能考量

有些部署作業可能會使用更進階的組態技術，提供額外的效能優點。下列清單特別強調了一些可協助您達到更佳效能的 SQL Server 功能：

- **備份至 Azure 儲存體**：備份在 Azure 虛擬機器中執行的 SQL Server 時，可以使用 [SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx) 功能。此功能從 SQL Server 2012 SP1 CU2 開始提供，建議在備份至連接的資料磁碟時使用。在備份至 Azure 儲存體 (或從中還原) 時，請遵循 [SQL Server 備份至 URL 的最佳作法和疑難排解，以及從儲存在 Azure 儲存體中的備份還原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建議進行。您也可以使用 [Azure 虛擬機器中的 SQL Server 自動備份](virtual-machines-sql-server-automated-backup.md)，自動執行這些備份作業。

	對於 SQL Server 2012 之前的版本，您可以使用 [SQL Server Backup to Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740)。這項工具可以透過多個備份等量磁碟區目標協助您提高備份的輸送量。

- **Azure 中的 SQL Server 資料檔案**：從 SQL Server 2014 開始提供 [Azure 中的 SQL Server 資料檔案](https://msdn.microsoft.com/library/dn385720.aspx)這項新功能。在 Azure 中執行具有資料檔案的 SQL Server 的效能與使用 Azure 資料磁碟的效能特性相當。

## 後續步驟

如果您有興趣深入了解 SQL Server 和進階儲存體，請參閱[在虛擬機器上將 Azure 進階儲存體和 SQL Server 搭配使用](virtual-machines-sql-server-use-premium-storage.md)文章。

如需安全性的最佳作法，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](virtual-machines-sql-server-security-considerations.md)。

請檢閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-sql-server-infrastructure-services.md)中的其他 SQL Server 虛擬機器主題。

<!---HONumber=Nov15_HO4-->