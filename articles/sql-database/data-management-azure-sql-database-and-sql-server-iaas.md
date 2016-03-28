<properties
	pageTitle="SQL (PaaS) Database 與VM 上雲端中的 SQL Server (IaaS) | Microsoft Azure"
	description="了解哪一個雲端 SQL Server 選項適合您的應用程式：Azure SQL (PaaS) Database 或 Azure 虛擬機器上雲端中的 SQL Server。"
	services="sql-database, virtual-machines"
	keywords="SQL Server 雲端, 雲端中的 SQL Server, PaaS 資料庫, 雲端 SQL Server, DBaaS"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cjgronlund"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/02/2016"
	ms.author="jeffreyg"/>

# 選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)

Azure 有兩個選項可在雲端主控 SQL Server 工作負載：

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)：雲端原生的 SQL Database，也就是已針對軟體即服務 (SaaS) 應用程式開發而最佳化的平台即服務 (PaaS) 資料庫或資料庫即服務 (DBaaS)。它提供與大部分 SQL Server 功能的相容性。
* [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)：SQL Server 安裝並託管於 Azure 上執行之 Microsoft Azure 虛擬機器的雲端，也稱為基礎結構即服務 (IaaS)。

瞭解每個選項如何搭配 Microsoft 資料平台一起運作，並在比對適合您的商業需求的選項時獲得協助。無論您是以節省成本為優先考量，或將精簡管理視為首要條件，本文可依據您最重視的商務需求進行比較，以協助您決定要提供的方法。


## Microsoft 的資料平台

在 Azure 與內部部署 SQL Server 資料庫的任何討論中，您要了解的第一件事情是您可以兩者都用。Microsoft 資料平台會利用 SQL Server 技術，使其可在跨內部部署實體機器、私人雲端環境、協力廠商代管的私人雲端環境，和公用雲端中使用。這可讓您透過內部和雲端代管部署的結合來滿足獨特的多樣化業務需求，並同時在這些環境中使用相同的伺服器產品、開發工具和專業知識組合。

   ![雲端 SQL Server 選項：IaaS 上的 SQL Server 或雲端中的 SaaS SQL 資料庫。](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如圖所示，每個產品可依您在基礎結構 (在 X 軸) 中所擁有的管理層級，並依資料庫層級彙總和自動化 (在 Y 軸) 所達到的成本效益程度等特性加以分類。

設計應用程式時，有四個基本選項可用來主控屬於應用程式一部分的 SQL Server：

- 非虛擬化實體機器上的 SQL Server
- 內部部署虛擬機器中的 SQL Server (私人雲端)
- Azure 虛擬機器中的 SQL Server (公用雲端)
- Azure SQL Database (公用雲端)

在下列章節中，我們將了解公用雲端中的 SQL Server：Azure SQL Database 和 Azure VM 上的 SQL Server。此外，我們將探討常見的商業動機，以判斷哪一個選項最符合應用程式需求。

## 仔細看看 Azure SQL Database 和 Azure VM 上的 SQL Server

**Azure SQL Database** 是託管在 Azure 雲端的關聯式資料庫即服務 (DBaaS)，其產業類別屬於 *軟體即服務 (SaaS)* 和 *平台即服務 (PaaS)* 。SQL Database 會建立在 Microsoft 所擁有、代管及維護的標準化硬體和軟體上。有了 SQL 資料庫，您可以使用內建的特色與功能在服務上直接開發。使用 SQL Database 時，您可以隨用隨付，並使用相應增加或相應放大選項以取得不需中斷的更強大功能。

**Azure 虛擬機器 (VM) 中的 SQL Server** 屬於*基礎結構即服務 (IaaS)* 產業類別，可讓您在雲端的虛擬機器中執行 SQL Server。類似於 SQL Database，它會建立在 Microsoft 所擁有、代管及維護的標準化硬體上。使用 VM 上的 SQL Server 時，您可以採用自己的 SQL Server 授權，或使用 Azure 入口網站中預先授權的 SQL Server 映像。

一般而言，這兩個 SQL 選項適合用於不同的用途：

- **SQL Database** 已經過最佳化，可將佈建和管理許多資料庫的整體成本降到最低。由於您無需管理任何虛擬機器、作業系統或資料庫軟體，它會將進行中的系統管理成本降到最低。這包括升級、高可用性及備份。一般而言，Azure SQL Database 可能會大幅增加由單一 IT 或開發資源管理的資料庫數目。
- **Azure VM 上執行的 SQL Server** 已經過最佳化，可在混合式案例中將現有的內部部署 SQL Server 應用程式延伸雲端，或在移轉或開發/測試案例中將現有的應用程式部署到 Azure。混合式案例的範例之一是透過使用 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)，在 Azure 中保留次要資料庫複本。有了 Azure VM 上的 SQL server，您即擁有專用 SQL Server 執行個體和雲端架構 VM 的完整系統管理權限。當組織擁有可用來維護虛擬機器的 IT 資源時，此選項會是最佳選擇。有了 VM 上的 SQL Server，您可以建立高度自訂的系統，以解決應用程式的特定效能和可用性需求。

下表摘要說明 SQL Database 和 Azure VM 上的 SQL Server 的主要特性：

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">SQL Database</th>
   <th align="left" valign="middle">Azure VM 中 SQL Server</th>

</tr>
<tr>
   <td valign="middle"><p><b>適用於</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>開發與行銷階段有時間限制的新雲端式設計應用程式。
          <li type=round>需有內建高可用性、災害復原及升級機制的應用程式。
          <li type=round>不想要管理基礎作業系統和組態設定的團隊。
         <li type=round>使用向外延展模式的應用程式。
         <li type=round>大小高達 1 TB 的資料庫。
         <li type=round>建置軟體即服務 (SaaS) 應用程式。
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>需要幾乎無需進行任何變更即可快速移轉至雲端的現有應用程式。
      <li type=round>需要透過安全通道，從 Azure 存取內部部署資源 (例如 Active Directory) 的 SQL Server 應用程式。
      <li type=round>如果您需要包含完整系統管理權限的自訂 IT 環境。
      <li type=round>當您不想購買內部部署 SQL Server 非生產硬體時的快速開發和測試案例。
      <li type=round>使用 [備份到 Azure 儲存體](http://msdn.microsoft.com/library/jj919148.aspx) 或 [Azure VM 中的 AlwaysOn 複本](../virtual-machines/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md) 進行內部部署 SQL Server 應用程式的災害復原。
      <li type=round>大於 1 TB 的大型資料庫。
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>資源</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>您不想要運用 IT 資源來支援和維護基礎結構。
       <li type=round>您想要將焦點放在應用程式層上。
       </ul></td>
   <td valign="middle"><ul><li type=round>您擁有可支援和維護的 IT 資源。</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>擁有權的總成本</b></p></td>
   <td valign="middle"><ul><li type=round>排除硬體成本。降低管理成本。</ul></td>
   <td valign="middle"><ul><li type=round>排除硬體成本。</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>業務持續性</b></p></td>
   <td valign="middle"><ul><li type=round>除了內建的容錯基礎結構功能以外，Azure SQL Database 還提供可增加業務持續性的功能，例如時間點還原時間、地理還原和地理複寫。如需詳細資訊，請參閱 [SQL Database 業務持續性概觀](sql-database-business-continuity.md)。</ul></td>
   <td valign="middle"><ul><li type=round>Azure VM 上的 SQL Server 可讓您設定高可用性和災害復原解決方案，以滿足您的資料庫特定需求。因此，您可以有已針對您的應用程式進行高度最佳化的系統。您可以視需要自我測試並執行容錯移轉。如需詳細資訊，請參閱 [Azure 虛擬機器上 SQL Sever 的高可用性和災害復原]((../virtual-machines/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)。</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>混合式雲端</b></p></td>
   <td valign="middle"><ul><li type=round>您的內部部署應用程式可以存取 Azure SQL Database 中的資料。</ul></td>
   <td valign="middle"><ul>
      <li type=round>有了 Azure VN 上的 SQL Server，您的應用程式可以部分在雲端中執行和部分在內部部署中執行。例如，您可以透過 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 將內部部署網路和 Active Directory 網域延伸到雲端。此外，您可以使用 [Azure 中的 SQL Server 資料檔案](http://msdn.microsoft.com/library/dn385720.aspx)，將內部部署資料檔案儲存在 Azure 儲存體中。如需詳細資訊，請參閱 [SQL Server 2014 混合式雲端簡介](http://msdn.microsoft.com/library/dn606154.aspx)。
      <li type=round>支援使用 [SQL Server Backup and Restore with Azure Blob Storage](http://msdn.microsoft.com/library/jj919148.aspx) 或 [Azure VM 中的 AlwaysOn 複本](../virtual-machines/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md) 進行內部部署 SQL Server 應用程式的災害復原。
      </ul></td>

</tr>
</table>

## 選擇 Azure VM 上的 Azure SQL Database 或 SQL Server 時的商業動機

### 成本

無論您是現金不足的新公司，或是在預算有限的情況下運作的已成立公司內部小組，有限資金經常會是決定主控資料庫方式的主要關鍵。在本節中，我們將首先了解 Azure 中，下列這兩個關聯式資料庫選項的相關計費和授權基本概念：SQL Database 和 Azure VM 上的 SQL Server。然後，我們將了解應該如何計算應用程式總成本。

#### 計費和授權基本概念

**SQL Database** 會以服務 (不含授權) 的形式銷售給客戶，而 Azure VM 上的 SQL Server 則需要傳統的 SQL Server 授權。

目前，我們在數個服務層中提供 **SQL Database**，並根據您所選擇的服務層和效能層級，以固定費率計算每小時的費用。此外，傳出的網際網路流量也會計費。基本、標準和高階服務層的設計目的在於提供多個效能層級的可預測效能，以滿足應用程式的尖峰需求。您可以在服務層和效能層級之間進行變更，以滿足應用程式的不同輸送量需求。如果您的資料庫具有高交易量，且必須支援許多並行使用者，建議使用高階服務層。如需目前支援的服務層最新資訊，請參閱 [Azure SQL Database 服務層](sql-database-service-tiers.md)。

有了 **SQL Database**，Microsoft 便會自動設定、修補和升級資料庫軟體，以降低您的系統管理成本。此外，它[內建的備份](sql-database-business-continuity.md)功能可協助您達到有效節省成本，尤其是當您擁有為數眾多的資料庫時效果更為顯著。

有了 **Azure VM 上的 SQL Server**，您可以利用傳統的 SQL Server 授權。您可以使用平台所提供的 SQL Server 映像 (其中包含授權)，或採用您的 SQL Server 授權。使用 Azure 所提供的映像時，營運成本取決於您所選擇的 VM 大小以及 SQL Server 版本。不論 VM 大小或 SQL Server 版本為何，您須支付 SQL Server 和 Windows Server 的每分鐘授權成本，以及 VM 磁碟的 Azure 儲存體成本。每分鐘計費選項可讓您隨時使用 SQL Server，而無需購買其他 SQL Server 授權。如果在 Azure 中採用自己的 SQL Server 授權，您僅需支付 Windows Server 和儲存體成本。如需採用自己的授權的詳細資訊，請參閱 [Azure 上透過軟體保證的授權機動性](https://azure.microsoft.com/pricing/license-mobility/)。

#### 計算應用程式總成本

當您開始使用雲端平台時，執行應用程式的成本主要包括開發和管理成本，以及公用雲端平台所需的服務成本。

以下是針對在 SQL Database 和 Azure VM 上的 SQL Server 上執行之應用程式的詳細成本計算：

**使用 Azure SQL Database 時：**

*應用程式的總成本 = 降到最低的系統管理成本 + 軟體開發成本 + SQL Database 服務成本*

**使用 Azure VM 上的 SQL Server 時：**

*應用程式的總成本 = 降到最低的軟體開發/修改成本 + 系統管理成本 + SQL Server 與 Windows Server 授權成本 + Azure 儲存體成本*

如需價格的詳細資訊，請參閱下列資源：

- [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 的[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] SQL Server 上有一小部分不適用於或無法使用於 SQL Database 的功能。如需詳細資訊，請參閱 [SQL Database 一般限制和指導方針](sql-database-general-limitations.md)和 [SQL Database Transact-SQL 資訊](sql-database-transact-sql-information.md)。如果您要將現有的 SQL Server 解決方案移至雲端，請參閱[將 SQL Server 資料庫移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。當您將現有的內部部署 SQL Server 應用程式移轉至 SQL Database 時，建議更新應用程式以利用雲端服務所提供的功能。例如，您可以考慮使用 [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) 或 [Azure 雲端服務](https://azure.microsoft.com/services/cloud-services/)來裝載您的應用程式，以增加成本利益。

### 系統管理

對許多企業來說，決定轉換至雲端服務的關鍵主要在於降低系統管理的複雜度。有了 **SQL Database**，Microsoft 可以管理基礎硬體、自動複製所有資料以提供高可用性、設定及升級資料庫軟體、管理負載平衡，並在伺服器故障時進行透明容錯移轉。您可以繼續管理您的資料庫，但不再需要管理資料庫引擎、伺服器作業系統或硬體。您可以繼續管理的項目範例包括資料庫和登入、索引和查詢微調，以及稽核和安全性。

相反地，您可能有內部專業人員，並想保持由磁碟位置控制資料庫位置。有了 **Azure VM 上執行的 SQL Server**，您可以完全掌控作業系統和 SQL Server 執行個體組態。有了 VM，您可以決定何時更新/升級作業系統與資料庫軟體，以及何時安裝任何其他軟體 (例如防毒和備份工具)。此外，您還可以控制 VM 的大小、磁碟的數目及其儲存體組態。例如，Azure 可讓您視需要變更 VM 大小。如需相關資料，請參閱 [Azure 的虛擬機器和雲端服務大小](../virtual-machines/virtual-machines-size-specs.md)。

### 服務等級協定 (SLA)

對於許多 IT 部門而言，達到服務等級協定 (SLA) 的正常運作時間義務是首要任務。在本節中，我們將瞭解 SLA 對每個資料庫主控選項的作用。

對於 **SQL Database** 基本、標準和高階服務層，Microsoft 提供 99.99% 的可用性 SLA。如需最新資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/sql-database/)。如需 SQL Database 服務層和支援的業務持續性方案最新資訊，請參閱[服務層](sql-database-service-tiers.md)。

對於 **Azure VM 上執行的 SQL Server**，Microsoft 提供 99.95% 的可用性 SLA (僅涵蓋虛擬機器)。本 SLA 未涵蓋在 VM 上執行的程序 (例如 SQL Server)，而且您必須裝載一個可用性設定組中的至少兩個 VM 執行個體。如需最新資訊，請參閱 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。如需 VM 內的資料庫高可用性 (HA)，您應在 SQL Server 中設定其中一個支援的高可用性選項，例如 [AlwaysOn 可用性群組](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)。

### <a name="market"></a>產品上市時間

當開發人員生產力和快速產品上市時間為關鍵所在時，**SQL Database** 是雲端式設計應用程式的理想解決方案。有了程式設計 DBA 類似功能，此選項非常適合雲端架構設計人員和開發人員，因為它會降低管理基礎作業系統和資料庫的需求。例如，您可以使用 [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) 和 [PowerShell Cmdlet](http://msdn.microsoft.com/library/azure/dn546726.aspx) 來自動化及管理數千個資料庫的管理作業。[彈性資料庫集區](sql-database-elastic-pool.md)等功能可讓您將重點放在應用程式層，並且更快速地讓解決方案上市。

如果現有或新的應用程式需要存取與控制 SQL Server 執行個體的所有功能，**Azure VM 上執行的 SQL Server** 會是理想選項。這也很適合於想要依現狀將現有的內部部署應用程式和資料庫移轉至 Azure。由於您無需變更簡報、應用程式和資料層，您會在重新架構現有解決方案時節省時間和預算。相反地，您可以將重點放在將所有解決方案移轉至 Azure，並進行 Azure 平台可能需要的某些效能最佳化作業。如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md)。

## 摘要

本文探討了 SQL Database 和 Azure 虛擬機器 (VM) 上的 SQL Server，並討論可能會影響您的決策的常見商業動因。下列是建議摘要，可用來考慮：

如果是下列情形，請選擇 **Azure SQL Database**：

- 您正在建置新的雲端應用程式或您想要移轉現有的 SQL Server 解決方案，以善用雲端服務所提供的成本節省和效能最佳化。此方法提供全面管理雲端服務的優點，有助於減少產品上市時間，並可提供長期的成本最佳化。

- 您想要讓 Microsoft 在資料庫上執行一般管理作業，因而資料庫需要更強大的可用性 SLA。

若要開始使用，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。

如果是下列情形，請選擇 [Azure VM 上的 SQL Server]：

- 您有現有的內部部署應用程式，而且想要停止維護自己的硬體，或考慮混合式解決方案。這種方法可讓您更快速地存取高資料庫容量，並透過安全通道連接內部部署應用程式。

- 您有現有的 IT 資源、需要 SQL Server 的完整系統管理權限，並需要與內部部署 SQL Server 的完全相容。這種方法可讓您彈性地執行大部分的應用程式，將開發或修改現有應用程式的成本降至最低。此外，它還提供 VM、作業系統和資料庫組態的完整控制權。

若要開始使用，請參閱[在 Azure 中佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

> [AZURE.NOTE] 想要試用 SQL Server 2016 CTP2 嗎？ 註冊 Microsoft Azure，然後移至[這裡](http://aka.ms/sql2016vm "開始")以建立已安裝 SQL Server 2016 CTP2 的虛擬機器。

<!----HONumber=AcomDC_0302_2016-->