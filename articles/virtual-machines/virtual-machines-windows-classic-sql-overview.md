<properties
	pageTitle="虛擬機器上的 SQL Server 概觀 | Microsoft Azure"
	description="本文章簡介 Azure 虛擬機器上架設的 SQL Server。本文章也提供深度內容的連結。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth"/>

# Azure 虛擬機器上的 SQL Server 概觀

## 開始使用
您可以利用各種設定來架設 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)，範圍包括單一資料庫伺服器到使用 AlwaysOn 可用性群組和 Azure 虛擬網路的多電腦設定。

>[AZURE.NOTE] 在 Azure VM 上執行 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。您也可以使用 Azure SQL Database 服務。如需詳細資訊，請參閱[了解 Azure SQL Database 和 Azure VM 中的 SQL Server](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)。

若要在 Azure 中建立 SQL Server 虛擬機器，必須先取得 Azure 平台訂用帳戶。您可以在 [[購買選項](https://azure.microsoft.com/pricing/purchase-options/)] 中購買 Azure 訂用帳戶。若要免費試用，請造訪 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### 在單一 VM 上部署 SQL Server 執行個體

當您註冊訂用帳戶之後，在 Azure 中部署 SQL Server 虛擬機器最簡單的方式，就是[在 Azure 中佈建 SQL Server 機器資源庫映像](virtual-machines-windows-portal-sql-server-provision.md)。這些映像包含的 SQL Server 授權，是以 VM 為計價單位。

請務必注意，建立及管理 Azure 虛擬機器的模型有兩種：傳統及資源管理員。Microsoft 建議讓大部分的新部署使用資源管理員模式。某些 Azure VM 的 SQL Server 文件仍舊只提到傳統模式。這些主題正隨時間更新，以便使用新的 Azure 入口網站和資源管理員模型。如需詳細資訊，請參閱[了解資源管理員部署和傳統部署](../resource-manager-deployment-model.md)。

>[AZURE.NOTE] 請盡可能使用最新的 [Azure 入口網站](https://portal.azure.com/)來佈建及管理 SQL Server 虛擬機器。預設使用進階儲存體，並提供自動化修補、自動化備份及 AlwaysOn 組態。

下表提供虛擬機器資源庫中的可用 SQL Server 映像比較。

|SQL Server 版本|作業系統|SQL Server 版本|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise、Standard 和 Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise、Standard 和 Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise、Standard 和 Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise、Standard 和 Web|
|SQL Server 2014|Windows Server 2012 R2|Enterprise、Standard 和 Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise、Standard 和 Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|評估|

除了這些預先設定的映像，您也可以[建立 Azure 虛擬機器](virtual-machines-windows-hero-tutorial.md)，而不需要預先安裝的 SQL Server。您可以安裝任何您擁有授權的 SQL Server 執行個體。將您的授權移轉至 Azure，以使用 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)，在 Azure 虛擬機器中執行 SQL Server。在此案例中，您只需支付與虛擬機器相關聯的 Azure 計算和儲存體[成本](https://azure.microsoft.com/pricing/details/virtual-machines/)。

為判斷 SQL Server 映像的最佳虛擬機器組態設定，請檢閱 [Azure 虛擬機器中的 SQL Server 效能最佳做法](virtual-machines-windows-classic-sql-perf.md)。針對生產環境的工作負載，建議的 SQL Server Enterprise Edition 的虛擬機器大小至少為 **DS3**，Standard Edition 的虛擬機器大小至少為 **DS2**。

除了檢閱效能最佳作法、 其他初始工作還包括：

- [檢閱 Azure VM 中的 SQL Server 安全性最佳作法](virtual-machines-windows-classic-sql-security.md)
- [設定連線](virtual-machines-windows-sql-connect.md)

### 遷移資料

在 SQL Server 虛擬機器啟動並執行之後，您可能想要將現有的資料庫移轉至機器。有數種技巧，但 SQL Server Management Studio 中的部署精靈最適合大部分的案例。如需這些案例的討論和精靈的教學課程，請參閱[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-classic-migrate-sql.md)。

## 高可用性

如果您需要高可用性，請考慮設定 SQL Server AlwaysOn 可用性群組。這牽涉到虛擬網路中多個 Azure VM。Azure 入口網站有一個範本，己經有您需要的設定。如需詳細資訊，請參閱 [ Azure 資源庫提供的 SQL Server AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)。

如果您想要手動設定可用性群組及相關聯的接聽程式，請參閱下列以傳統部署為基礎的文章：

- [在 Azure (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-windows-classic-portal-sql-availability.md)
- [設定 Azure 中 AlwaysOn 可用性群組的 ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [將內部部署 AlwaysOn 可用性群組延伸至 Azure](virtual-machines-windows-classic-sql-onprem-availability.md)

如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-classic-sql-dr.md)。

## 備份與還原
至於內部部署資料庫，Azure 可以充當次要資料中心，負責儲存 SQL Server 備份檔案。如需備份與還原選項的概觀，請參閱 [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-windows-classic-sql-br.md)。

[SQL Server 備份至 URL](https://msdn.microsoft.com/library/dn435916.aspx) 會將 Azure 的備份檔案儲存至 Azure Blob 儲存體。[SQL Server 託管備份](https://msdn.microsoft.com/library/dn449496.aspx)可以讓您在 Azure 中安排和保留備份。這些服務可以搭配內部部署 SQL Server 執行個體或 Azure VM 上執行的 SQL Server。Azure VM 也可以利用 SQL Server 專用的[自動備份](virtual-machines-windows-classic-ps-sql-backup.md)和[自動化修補](virtual-machines-windows-classic-ps-sql-patch.md)功能。

## SQL Server VM 映像組態詳細資料

下表說明平台提供之 SQL Server 虛擬機器映像的組態。

### Windows Server

平台映像中的 Windows Server 安裝包含下列組態設定和元件：

|功能|組態
|---|---|
|遠端桌面|已對系統管理員帳戶啟用|
|Windows Update|已啟用|
|使用者帳戶|依預設，在佈建期間所指定的使用者帳戶是本機 Administrator 群組的成員。此系統管理員帳戶也是 SQL Server 系統管理員伺服器角色的成員|
|工作群組|虛擬機器是名為 WORKGROUP 工作群組的成員|
|來賓帳戶|已停用|
|具有進階安全性的 Windows 防火牆|另一|
|.NET Framework|第 4 版|
|磁碟|選取的大小會限制可設定的資料磁碟數目。請參閱 [Azure 的虛擬機器大小](virtual-machines-linux-sizes.md)|

### SQL Server

平台映像中的 SQL Server 安裝包含下列組態設定和元件：

|功能|組態|
|---|---|
|資料庫引擎|已安裝|
|Analysis Services|已安裝|
|Integration Services|已安裝|
|Reporting Services|設定為原生模式|
|AlwaysOn 可用性群組|於 SQL Server 2012 或更新版本提供。需要[其他組態](virtual-machines-windows-classic-sql-dr.md)
|複寫|已安裝|
|全文檢索和語意擷取搜尋|已安裝 (語意擷取僅限 SQL Server 2012 或更新版本)|
|Data Quality Services|已安裝 (僅限 SQL Server 2012 或更新版本)|
|Master Data Services|已安裝 (僅限 SQL Server 2012 或更新版本)。需要[其他組態和元件](https://msdn.microsoft.com/library/ee633752.aspx)
|PowerPivot for SharePoint|可用 (僅限 SQL Server 2012 或更新版本)。需要其他組態和元件 (包括 SharePoint)|
|Distributed Replay Client|可用 (僅限 SQL Server 2012 或更新版本)，但未安裝。請參閱〈[從平台提供之 SQL Server 映像執行 SQL Server 安裝](#run-sql-server-setup-from-the-platform-provided-sql-server-image)〉|
|工具|所有工具，包括 SQL Server Management Studio、SQL Server 組態管理員、Business Intelligence Development Studio、SQL Server 安裝程式、用戶端工具連接性、用戶端工具 SDK 和 SQL 用戶端連接性 SDK，以及升級和移轉工具，例如資料層應用程式 (DAC)、備份、還原、附加及卸離|
|SQL Server 線上叢書|已安裝，但需要使用說明檢視器的組態|

### 資料庫引擎組態

下列資料庫引擎設定已設定。如需詳細設定，請檢查 SQL Server 執行個體。

|功能|組態|
|---|---|
|執行個體|包含 SQL Server Database Engine 預設 (未命名) 執行個體，僅接聽共用的記憶體通訊協定|
|驗證|根據預設，Azure 會在 SQL Server 虛擬機器安裝期間選取 Windows 驗證。如果您想要使用 sa 登入或建立新的 SQL Server 帳戶，需要變更驗證模式。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](virtual-machines-windows-classic-sql-security.md)。|
|系統管理員 (sysadmin)|安裝虛擬機器的 Azure 使用者最初是 SQL Server 系統管理員固定伺服器角色的唯一成員|
|記憶體|Database Engine 記憶體會設定為動態記憶體組態|
|自主資料庫驗證|關閉|
|預設語言|English|
|跨資料庫擁有權鏈結|關閉|

### 客戶經驗改進計畫 (CEIP)

已啟用[客戶經驗改進計畫 (CEIP)](https://technet.microsoft.com/library/cc730757.aspx)。您可以使用 SQL Server 錯誤和使用方式報告公用程式來停用 CEIP。如需啟動 SQL Server 錯誤和使用方式報告公用程式，請在 [開始] 功能表中依序按一下 [所有程式]、[Microsoft SQL Server 版本]、[組態工具] 及 [SQL Server 錯誤和使用方式報告]。如果您不要使用已啟用 CEIP 的 SQL Server 執行個體，也可以考慮將您自己的虛擬機器映像部署至 Azure。如需詳細資訊，請參閱[建立和上傳包含 Windows Server 作業系統的虛擬硬碟](virtual-machines-windows-classic-createupload-vhd.md)。

## 從平台提供之 SQL Server 映像執行 SQL Server 安裝

如果您使用平台提供的 SQL Server 映像來建立虛擬機器，可以在以下位置找到儲存在虛擬機器上的 SQL Server 安裝程式媒體：**C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full** 目錄。您可以從這個目錄執行安裝程式，以執行任何安裝程式的動作，包括新增或移除功能、加入新的執行個體，或在磁碟空間允許的情況下修復執行個體。

>[AZURE.NOTE] Azure 提供多個 SQL Server 映像版本。如果 SQL Server 平台提供的映像版本是在 2014 年 5 月 15 日或之後發行，則預設會包含產品金鑰。如果您用來佈建虛擬機器的平台提供 SQL Server 映像是在此日期之前發行，則該 VM 沒有包含產品金鑰。我們建議您在佈建新 VM 時，最佳做法是一律選取最新的映像版本。

## 資源

- [在 Azure (資源管理員) 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)
- [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-classic-migrate-sql.md)
- [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-classic-sql-dr.md)
- [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
- [Azure 虛擬機器](virtual-machines-linux-about.md)

<!---HONumber=AcomDC_0323_2016-->