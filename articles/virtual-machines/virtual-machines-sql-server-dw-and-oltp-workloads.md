<properties 
	pageTitle="SQL Server 資料倉儲和 Azure 虛擬機器中的交易式工作負載"
	description="說明已針對資料倉儲和 OLTP 工作負載進行預先設定，並最佳化的 SQL Server 虛擬機器映像。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# SQL Server 資料倉儲和 Azure 虛擬機器中的交易式工作負載

若要在 Azure 虛擬機器中使用 SQL Server 進行資料倉儲或交易式工作負載，建議您在 Azure 虛擬機器資源庫中使用其中一個預先設定的虛擬機器映像。這些映像已根據 [Azure 虛擬機器中 SQL Server 的效能最佳作法](https://msdn.microsoft.com/library/azure/dn133149.aspx)中的建議最佳化。

本文的主題為在 Azure 虛擬機器上執行上述工作負載 (也稱為基礎結構即服務或 IaaS)。您也可以將資料倉儲和交易式工作負載作為 Azure 中的服務執行。如需詳細資訊，請參閱 [SQL 資料倉儲文件](http://azure.microsoft.com/documentation/services/sql-data-warehouse/)和 [Azure SQL Database](http://azure.microsoft.com/documentation/services/sql-database/)。

## 我們提供哪些預先設定的 VM 映像？

Azure VM 資源庫提供下列預先設定的 VM 映像：

- [已針對 Windows Server 2012 R2 上的交易式工作負載最佳化的 SQL Server 2014 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [已針對 Windows Server 2012 R2 上的資料倉儲最佳化的 SQL Server 2014 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [已針對 Windows Server 2012 R2 上的交易式工作負載最佳化的 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [已針對 Windows Server 2012 R2 上的資料倉儲工作負載最佳化的 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [已針對 Windows Server 2012 上的交易式工作負載最佳化的 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [已針對 Windows Server 2012 上的資料倉儲最佳化的 SQL Server 2012 SP2 Enterprise](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

目前我們會在允許連接最多 16 個資料磁碟的 VM 執行個體上支援上述映像，以提供最高的輸送量 (或彙總頻寬)。這些執行個體為標準層 A4、A7、A8、A9、D4、D13、D14、F3、G4、G5，和基本層 A4。請參閱 [Azure 中的虛擬機器大小](virtual-machines-size-specs.md)，了解大小和選項的進一步詳細資料。

>[AZURE.NOTE]在 2014 年 9 月以前，我們仍提供舊版的交易式和 DW 資源庫映像。不過，必須透過連接資料磁碟才能使用這些映像。建議使用上述較新的映像，因為它們在被佈建的當下就可使用。

## 透過交易式/DW 映像佈建來自資源庫的 SQL VM

1. 登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。

1. 按一下左窗格中 [Azure] 功能表項目中的 [**虛擬機器**]。

1. 按一下左下角的 [**新增**]，再依序按一下 [**運算**]、[**虛擬機器**] 和 [**從資源庫**]。

1. 在 [選取虛擬機器映像] 頁面上，針對交易式或資料倉儲映像選取一個 SQL Server。

	![Azure VM 資源庫](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. 從 [**虛擬機器組態**] 頁面的 [**大小**] 選項中，選擇一個支援的大小。

	![Azure VM 資源庫組態](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]目前僅支援標準層 A4、A7、A8、A9、D4、D13、D14、G3、G4、G5 和基本層 A4。若嘗試佈建不支援的 VM 大小，作業將會失敗。

1. 等候佈建完成。在等待期間，您可以觀察虛擬機器頁面上的佈建狀態 (如下圖所示)。佈建完成時，狀態會變成 [**執行**]，旁邊加上勾號。

	![Azure VM 資源庫狀態](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## 使用 PowerShell 建立交易式/DW 映像

您也可以使用 PowerShell Cmdlet **New-AzureQuickVM** 建立 VM。您必須將您的雲端服務名稱、VM 名稱、映像名稱、系統管理員使用者名稱和密碼，以及類似的資訊作為參數傳遞。取得映像名稱的其中一個簡單的方法是使用 **Get-AzureVMImage** 列出所有可用的 VM 映像。

例如，下列 PowerShell 命令會傳回符合上一節清單中映像標籤 **已針對 Windows Server 2012 R2 上的資料倉儲工作負載最佳化的 SQL Server 2012 SP2 Enterprise** 的最新映像。

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

如需有關透過 PowerShell 建立映像的詳細資訊，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)。

## 交易式/DW 映像中包含的特定組態

這些映像是根據 [Azure 虛擬機器中 SQL Server 的效能最佳作法](https://msdn.microsoft.com/library/azure/dn133149.aspx)中的內容進行最佳化。具體而言，這些映像的組態包括下列最佳化功能。

>[AZURE.NOTE]如果您自備授權，或從頭開始建立資料倉儲或交易式虛擬機器，您可以根據效能文章，以及下方預先設定資源庫映像中的最佳化範例進行最佳化。

### 磁碟組態


|---|---|
|連接的資料磁碟數量|15|
|儲存空間|兩個儲存體集區：<br/>– 1 個具有 12 個資料磁碟的資料集區；固定大小 12 TB；資料行 = 12<br/> – 1 個具有 3 個資料磁碟的記錄集區；固定大小 3 TB；資料行 = 3<br/><br/>剩餘一個資料磁碟供使用者連接和自行決定使用方式。<br/><br/>**DW**：等量磁碟區大小 = 256 KB<br/>**交易式**：等量磁碟區大小 = 64 KB|
|磁碟大小，快取，配置大小|每個磁碟大小 1 TB，HostCache = 無，NTFS 配置單位大小 = 64 KB|

### SQL Server 組態

|---|---|
|啟動參數|-T1117 可在資料庫需要自動成長時，讓資料檔案保持在同樣的大小 協助保護資料的檔案大小相同<br/><br/>-T1118 可提供增強 tempdb 的延展性 (如需詳細資訊，請參閱 [SQL Server (2005年和 2008年) 的追蹤旗標 1118 (-T1118) 的使用方式](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce)。)|
|復原模型|**DW**：透過 ALTER DATABASE 將模型資料庫設為 SIMPLE<br/>**交易式**：無變更|
|設定預設位置|將 SQL Server 錯誤記錄檔和追蹤檔案目錄移至資料磁碟|
|資料庫的預設位置|系統資料庫已移至資料磁碟。<br/><br/>建立使用者資料庫已變更為資料磁碟。|
|立即檔案初始化|已啟用|
|在記憶體中鎖定頁面|已啟用 (如需詳細資訊，請參閱[啟用鎖定記憶體分頁選項 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx))。|

## 常見問題集

- 已最佳化映像和未最佳化映像的價格是否有差異？

	否。已最佳化映像採用相同的價格模型 (詳細資料請參閱[這裡](http://azure.microsoft.com/pricing/details/virtual-machines/))，不需要支付額外費用。請注意，大型的 VM 執行個體會產生較高的成本。

- 還有其他應採用的效能修正程式嗎？

	有。請考慮套用適用於 SQL Server 的相關效能修正程式：

	- [修正：當您在 SQL Server 2012 中執行 select into 暫存資料表作業時，I/O 的效能不佳](https://support.microsoft.com/kb/2958012)

	- [修正：在 SQL Server 2014 中移動 SQL Server 資源時，出現「SQL Server 效能計數器已停用」](http://support.microsoft.com/kb/2973444)

- 哪裡可以找到關於儲存空間的詳細資訊？

	如需有關儲存空間的進一步詳細資料，請參閱[儲存空間的常見問題集 (FAQ)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)

- 新的 DW 映像與舊版映像之間的差異為何？

	舊版 DW 映像需要執行額外步驟 (例如在建立 VM 之後，連接資料磁碟) 才能使用，但新的 DW 映像在建立當下就可以馬上使用，所以其可用性更高，發生錯誤的機率也較小。

- 若我需要舊版 DW 映像該怎麼辦？ 能透過哪些方式存取？

	我們仍然提供舊版 VM 映像，只是不是直接放在資源庫中。不過，Powershell Commandlet 可協助您找出這些映像。比方說，**Get-AzureVMImage** 可以用來列出出所有映像；根據說明和發佈日期找到先前的 DW 映像後，可以使用 **New-AzureVM** 進行佈建。

## 後續步驟

透過 SQL Server 安裝虛擬機器之後，您可能會需要：

- [移轉資料](virtual-machines-migrate-onpremises-database.md)
- [設定連線能力](virtual-machines-sql-server-connectivity.md)

如需在 Azure VM 中執行 SQL Server 的其他主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-sql-server-infrastructure-services.md)。

<!---HONumber=August15_HO8-->