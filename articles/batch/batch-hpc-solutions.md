<properties
   pageTitle="雲端中的批次和 HPC 解決方案 |Microsoft Azure"
   description="介紹批次和高效能運算 (大量運算) 案例，以及在 Azure 中的解決方案選項"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/01/2015"
   ms.author="danlep"/>

# 批次和 HPC 解決方案

本文將介紹批次和高效能運算 (HPC) (又稱為*大數據*) 的 Azure 解決方案。雖然本文主要適用於技術決策者、IT 經理和獨立軟體廠商，但其他 IT 專業人員和開發人員也可以使用它來熟悉這些解決方案。

組織有大規模的運算問題，包括工程設計和分析、影像轉譯、複雜模型、Monte Carlo 模擬和財務風險計算。為了協助組織解決這些問題，以及制定所需的資源、調整和排程決策，Azure 提供了可調整、隨選計算功能和服務。有了 Azure，組織就可以：

* 建立混合式解決方案，擴充內部部署 HPC 叢集將尖峰工作負載卸載到雲端

* 使用現有的工具組和應用程式，在 Azure 中完全執行 HPC 叢集工作負載

* 使用受管理及可調整的 Azure 服務 (例如[批次](http://azure.microsoft.com/documentation/services/batch/)) 以執行大量運算工作負載，而不需要部署和管理運算基礎結構

Azure 還提供組織和軟體廠商開發工具與服務，以建立自訂的端對端大數據解決方案。


## 背景：批次和 HPC 應用程式

不同於 Web 應用程式和許多特定業務應用程式、批次和 HPC 應用程式擁有既定的開始和結束，而且它們可以排程或隨選執行，有時候數小時或更久。大部分可分成兩個主要類別：*本質平行* (有時稱為「窘迫平行」，因為他們解決的問題能讓他們在多部電腦或處理器上平行執行) 和*緊密結合*。如需這些應用程式類型的詳細資訊，請參閱下表。某些 Azure 解決方案方法比較適合一種類型或其他類型。

>[AZURE.NOTE]在批次和 HPC 解決方案中，應用程式正在執行的執行個體通常稱為*作業*，而每項作業可能會分成*工作*。且應用程式的叢集計算資源通常稱為*運算節點*。

類型 | 特性 | 範例
------------- | ----------- | ---------------
**本質平行**<br/><br/>![本質平行][parallel] |• 個別的電腦獨立執行應用程式邏輯<br/><br/>• 加入電腦允許應用程式調整並縮短計算時間<br/><br/>• 應用程式包含個別的可執行檔，或是分成用戶端叫用的服務群組 (服務導向的架構、或 SOA、應用程式) |• 財務風險模型<br/><br/>• 影像轉譯和影像處理<br/><br/>• 媒體編碼及轉碼<br/><br/>• Monte Carlo 模擬<br/><br/>• 軟體測試
**緊密結合**<br/><br/>![緊密結合][coupled] |• 應用程式需要進行互動或交換中繼結果的運算節點<br/><br/>• 運算節點可能會使用訊息傳遞介面 (MPI) 進行通訊，MPI 是適用於平行計算的常見通訊協定<br/><br/>• 應用程式很容易受到網路延遲和頻寬影響<br/><br/>• 可以改善應用程式效能，方法是使用支援高速網路技術，例如 InfiniBand 和遠端直接記憶體存取 (RDMA) 的計算基礎結構 |• 石油與天然氣貯存槽模型<br/><br/>• 工程設計和分析，例如：計算流體動力學<br/><br/>• 實體模擬，像是車輛衝撞和核子反應<br/><br/>• 天氣預報

### 在雲端中執行批次和 HPC 應用程式的考量

您可以輕易地移轉針對 Azure 或混合式 (跨單位) 環境設計，在內部部署 HPC 叢集中執行的許多應用程式。不過，可能會有一些限制或考量，包括：


* **雲端資源的不中斷可用性** - 根據針對解決方案選取的雲端計算資源類型，您可能無法在作業執行持續期間內保證機器持續運行。狀態處理和進度檢查指向是處理可能的暫時性失敗的常見技巧，且在運用雲端資源時更是不可或缺。


* **資料存取** - 企業網路叢集內經常使用的資料存取技術 (例如 NFS) 可能需要雲端中的特殊組態，或您可能需要為雲端採用不同的資料存取作法與模式。

* **資料移動** - 對於處理大量資料的應用程式，需要採行策略將資料移至雲端儲存體和計算資源，並可能需要考慮高速跨單位網路，例如 [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/)。也請考慮法律、法規或原則對儲存或存取該資料的限制。


* **授權** - 請向廠商確認任何商業應用程式在雲端中的執行授權或其他限制。並非所有廠商都提供隨用隨付授權。您可能需要視您的解決方案，在雲端中規劃授權伺服器，或連接至內部部署授權伺服器。


### 大量運算或大數據？

大量運算與大數據應用程式之間的界限不一定很清楚，而且有些應用程式可能兼具這兩種特性。兩者都涉及執行大規模計算，通常是在內部部署和 (或 ) 雲端中執行的電腦叢集。但是，解決方案和支援工具可能不同。

• **大量運算**傾向於涉及依賴 CPU 效能和記憶體的應用程式，例如工程模擬、財務風險模型和數位轉譯。具備大量運算解決方案的叢集可能包含具有特殊的多核心處理器來執行原始計算，和專用的高速網路硬體來連接電腦的電腦。

• **大數據**可解決單一電腦或資料庫管理系統無法管理的大量資料之資料分析問題，例如大量 Web 記錄或其他商業智慧資料。相較於 CPU 能力，大數據可能會更依賴磁碟容量和 I/O 效能，且大數據解決方案通常會使用專門的工具，像是 Apache Hadoop 來管理叢集和資料分割。(如需 Azure HDInsight 和其他 Azure Hadoop 解決方案的相關資訊，請參閱 [Hadoop](http://azure.microsoft.com/solutions/hadoop/)。)

## 資源管理和作業排程

執行批次和 HPC 應用程式通常包括*叢集管理員*和*工作排程器*來協助管理叢集的計算資源，以及將其配置給執行作業的應用程式。這些函式可能會透過不同的工具或某種整合式工具來完成。

* **叢集管理員** - 佈建、發行及管理計算資源 (或運算節點)。視工具而定，叢集管理員可能會在運算節點上自動安裝作業系統映像和應用程式；根據需求調整計算資源；以及監視節點的效能。

* **工作排程器** - 指定應用程式需要的資源 (例如處理器或記憶體)，以及執行時的條件。工作排程器會維護工作佇列，並根據指派的優先權或其他特性將資源分配給它們。

Windows 與 Linux 叢集的叢集和作業排程工具，或獨立開發的工具，都可順暢移轉至 Azure。例如，[Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029)是 Microsoft 的免費計算叢集解決方案，適用於 Windows Server 和 Windows 型電腦。若要減少對專用的內部部署運算資源的需求，您可以視需要擴充 HPC Pack 叢集來使用 Azure 運算節點，或在 Azure 虛擬機器叢集中完全部署叢集。

### 大量運算工作流程

叢集管理和作業排程工具可協助您執行可預測的步驟來完成大量運算工作流程。根據解決方案，您可能會省略下列清單中的一些步驟，或引入其他的自訂工具。資料密集的工作流程可能需要額外的資料前置和後置處理步驟 (未列出)。

1. **佈建** - 準備包含所需基礎結構、計算資源和儲存體的計算環境以執行應用程式

2. **階段** - 將輸入資料和應用程式移至計算環境

3. **排程** - 設定作業和工作，並在資源可用時將計算資源配置給他們

4. **監視器** - 提供有關作業和工作進度的狀態資訊；處理錯誤或例外狀況

5. **完成** - 傳回結果，並視需要後置處理輸出資料

## 適合大量運算的 Azure 服務

Azure 有某個範圍的計算、資料、網路和相關服務，您可將其使用於大量運算解決方案和工作流程。如需每一個服務的詳細指引，請參閱 Azure 服務文件。如需包含批次和 HPC 應用程式的一些常見方法，請參閱[解決方案案例](#solution-scenarios)。

>[AZURE.NOTE]新服務會定期導入 Azure 平台，且可能適用於您的案例。建議僅將預覽服務運用於測試或證明概念部署，而非生產工作負載。如果您有疑問，請連絡 [Azure 合作夥伴](https://pinpoint.microsoft.com/zh-tw/search?keyword=azure)或寄電子郵件到 **bigcompute@microsoft.com*。

### 計算服務

Azure 中的計算服務是大運算解決方案的核心。下表是經常使用的計算服務，並能讓不同的案例享有優勢。在基本層級中，這些服務為使用 Windows Server Hyper-V 技術，由 Azure 提供的虛擬機器計算執行個體上執行的應用程式提供不同的模式。根據服務，這些執行個體可以執行各種標準和自訂的 Linux 和 Windows 作業系統和工具。Azure 提供[各式各樣的執行個體大小](../virtual-machines/virtual-machines-sizes-specs.md)，搭配 CPU 核心、記憶體、磁碟容量和其他特性等不同組態。您可視您的需求將執行個體擴充至數千個核心，並在需要較少的資源時相應減少。

>[AZURE.NOTE]您可以利用 A8-A11 執行個體，以改善某些大量運算工作負載的效能，包括需要低延遲和高輸送量應用程式網路的平行 MPI 應用程式。請參閱[關於 A8、A9、A10 和 A11 計算密集型執行個體](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md)。

服務 | 說明
------------- | -----------
**[雲端服務](http://azure.microsoft.com/documentation/services/cloud-services)**<br/><br/> |• 可以在背景工作角色執行個體中執行大量運算應用程式，這是執行 Windows Server 版本，而且完全受 Azure 管理的虛擬機器<br/><br/>• 啟用含低管理負擔，在平台即服務 (PaaS) 模型中執行的可調整、可靠的應用程式<br/><br/>• 可能需要額外的工具或開發以整合內部部署 HPC 叢集解決方案
**[虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines)**<br/><br/> |• 使用 Microsoft Hyper-V 技術來提供運算基礎結構即服務 (IaaS)<br/><br/>• 可讓您從標準的 Windows Server 或 Linux 映像、您自行提供或從[Azure Marketplace](https://azure.microsoft.com/marketplace/) 的映像和資料磁碟中，更靈活地佈建和管理永續性雲端電腦<br/><br/>• 在雲端中完全執行內部部署運算叢集工具和應用程式
**[批次](http://azure.microsoft.com/documentation/services/batch)**<br/><br/> |• 在完全受管理的服務中執行大規模的平行與批次工作負載，例如影像轉譯和媒體編碼及轉碼<br/><br/>• 提供虛擬機器之受管理集區的作業排程和自動調整<br/><br/>• 允許開發人員建置和執行做為服務的應用程式，或啟用雲端的現有應用程式<br/>

### 儲存體服務

大量運算解決方案通常會在一組輸入資料上操作，並產生其結果的資料。許多大量運算解決方案中使用的部分 Azure 儲存體服務包括：

* [Blob、資料表和佇列儲存體](http://azure.microsoft.com/documentation/services/storage) - 分別管理大量非結構化資料、NoSQL 資料，和工作流程和通訊的訊息。例如，您可以為大型技術資料集，或應用程式處理的輸入影像或媒體檔案使用 blob 儲存體。您可以在解決方案中使用佇列以速行非同步通訊。如需這些儲存體解決方案的詳細資訊，請參閱〈[Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)〉。

* [Azure 檔案儲存體](http://azure.microsoft.com/services/storage/files/) - 在 Azure 中使用標準 SMB 通訊協定 (為一些 HPC 叢集解決方案必備) 來共用一般檔案和資料。

### 資料和分析服務

若干大量運算案例牽涉到大型資料流，或會產生需要進一步處理或分析的資料。為了處理這種情況，Azure 提供許多資料和分析服務，包括：

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory) - 建置資料導向的工作流程 (管線)，以聯結、彙總和轉換源自內部部署、雲端型和網際網路資料存放區的資料。

* [SQL Database](http://azure.microsoft.com/documentation/services/sql-database) - 在受管理平台服務中提供 Microsoft SQL Server 關聯式資料庫管理系統的主要功能。

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight) - 在雲端中部署和佈建 Windows Server 或 Linux 架構的 Apache Hadoop 叢集，以管理、分析和報告高度可靠與可用的巨量資料。

* [機器學習服務](http://azure.microsoft.com/documentation/services/machine-learning) - 可幫助您在完全管理的平台服務中建立、測試、操作和管理預測分析解決方案。

### 其他服務

大量運算解決方案可能需要包含其他 Azure 基礎結構和平台服務，才能連線至內部部署或其他環境中的資源。範例包括：

* [虛擬網路](http://azure.microsoft.com/documentation/services/virtual-network) - 在 Azure 中建立邏輯隔離的區段，以使用 IPSec 將 Azure 資源連接到您的內部部署資料中心或單一用戶端電腦；可讓大量運算應用程式存取內部部署資料、Active Directory 服務和授權伺服器

* [ExpressRoute](http://azure.microsoft.com/documentation/services/expressroute) - 比一般網際網路連接更高的安全性、更可靠、更快的速度及更低的延遲，在 Microsoft 資料中心與內部部署基礎結構之間，或共置環境中建立私人連接。

* [服務匯流排](http://azure.microsoft.com/documentation/services/service-bus) - 提供數種機制，讓應用程式進行通訊或交換資料，不管它們位於 Azure、其他雲端平台，還是資料中心。

## 解決方案案例

以下是利用現有的 HPC 叢集解決方案、Azure 服務或兩者的組合，在 Azure 中執行大量運算工作負載的常見案例。

>[AZURE.NOTE]對於使用大量計算工作負載而不適合標準的 HPC 叢集工具，或不要直接移轉至 Azure 服務的組織而言，Azure 提供開發人員和合作夥伴一整套計算功能、服務、架構選擇和開發工具。Azure 可支援自訂大量運算工作流程，可擴充至數千個計算核心。

### 案例 1.高載至 Azure 的內部部署 HPC 叢集

**選擇此選項的時機為何？** - 您可能已經有執行運算密集之工作負載的內部部署 HPC 叢集，但它在尖峰期間需要額外的運算資源，例如月底報告或特殊專案。不需要購買、部署和管理在大部分情況下可能處於閒置狀態的額外軟硬體，您可以使用 Azure，將隨選計算能力新增至現有的叢集。

例如，如果您現有的內部部署 HPC 叢集是使用 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 建置，您可以雲端服務中執行的 Azure 背景工作角色執行個體的形式，加入額外的運算資源。請參閱下圖。如需詳細資訊和逐步指示，請參閱〈[使用 Microsoft HPC Pack 將量擴大到 Azure](https://technet.microsoft.com/library/gg481749.aspx)〉。

![叢集高載][burst_cluster]

>[AZURE.NOTE]如果您想要將 HPC Pack 叢集使用量降到最低，您可以將內部部署叢集減少到只剩 HPC Pack 前端節點。然後，在 Azure 中隨選加入所有計算資源。如需逐步說明這個案例的教學課程，請參閱〈[使用 Microsoft HPC Pack 設定混合式運算叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)〉。

此混合式解決方案會運用內部部署叢集中的現有投資，但可讓您為一般 (非尖峰) 工作負載調整固定的內部部署基礎結構。如果您需要存取內部部署授權伺服器或資料存放區，您可以設定 Azure 虛擬網路，將內部部署叢集連線至 Azure。

### 案例 2.在 Azure 中完整建立 HPC 叢集

**選擇此選項的時機為何？** - 您可能已大量投資內部部署 Windows 或 Linux HPC 叢集，包括管理和排程工具和自訂應用程式。您可能需要額外的叢集容量來執行您現有的工具組和應用程式，卻又不希望額外投資內部部署基礎結構或修改您的應用程式。或者您可能需要建立短期或長期使用的新叢集，但您不想要付出購買、維護及操作成本，或配置空間來安裝及部署它。

您可以使用 Azure 自動化工具，在 Azure 虛擬機器中建立HPC 叢集來建立您需要的容量。取決於您部署的 VM，您可以執行各種 HPC 和平行工作負載，包括緊密結合的 MPI 應用程式。

>[AZURE.NOTE]請洽詢您的內部部署叢集解決方案和應用程式廠商，了解在提供基礎結構即服務 (IaaS) 的公用雲端中執行時的其他需求和最佳作法。

例如，您可以在 Azure 基礎結構服務虛擬機器 (IaaS) 中，使用 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 建立 Windows Server 架構 HPC 叢集來執行工作負載，如以下簡圖所示。叢集使用者可透過用戶端電腦上執行的標準 HPC Pack 作業提交工具，將作業安全地提交至雲端叢集。如需詳細資料和部署選項，請參閱〈[ Azure VM 中的 Microsoft HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)〉。

![IaaS 中的叢集][iaas_cluster]

**自動化部署** - 若要部署大量 Windows Server 或 Linux VM，您可以使用標準或自訂 VM 映像和 Azure 自動化工具，例如 [Azure 命令列介面](../xplat-cli.md)或 [Azure PowerShell](../powershell-install-configure.md)。範例包括：

* 若要在 Azure 基礎結構服務中部署 HPC Pack 叢集，您可以從用戶端電腦執行彈性的 [Azure PowerShell 指令碼](https://msdn.microsoft.com/library/azure/dn864734.aspx)；此指令碼使用預先安裝了 HPC Pack 的 Windows Server VM 映像。您也可以使用 Azure [快速啟動範本](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)搭配 Azure PowerShell 或 Azure CLI 來部署 HPC Pack 叢集。

* 您可以使用 Azure [快速啟動範本](https://azure.microsoft.com/documentation/templates/slurm/)搭配 Azure PowerShell 或 Azure CLI，以部署執行 [SLURM](https://computing.llnl.gov/linux/slurm/) 開放原始碼工作負載管理員的 Linux 叢集。

將整個 HPC 叢集放在雲端中可享有具大優勢。

* 組織可以執行 HPC 作業而無需購買和管理其他內部部署硬體，並可控制叢集的大小來有效使用計算資源。

* 可在雲端架構叢集中執行許多未變更或稍加修改的內部部署叢集應用程式。

* 相較於可將內部部署叢集擴充至雲端的混合式解決方案，完全在雲端中執行應用程式可以簡化資料存取。不需分割雲端和內部部署安裝之間的資料，或從應用程式中遠端存取資料，所有應用程式資料可以儲存在雲端。

* 有些軟體廠商會最佳化他們的應用程式，以便在雲端叢集中執行。例如，藉由在 Azure 虛擬機器 HPC Pack 叢集中的 MathWorks 部署 [MATLAB Distributed Computing Server](http://www.mathworks.com/products/distriben/)，您可以使用雲端運算資源完全且平行地執行 MATLAB 作業。

### 案例 3.將平行應用程式相應放大至 Azure

**選擇此選項的時機為何？** - 您可能會在內部部署工作站或小叢集中執行大量運算的應用程式，例如 Monte Carlo 模擬、動畫轉譯或媒體轉碼。您不想要管理計算資源或工作排程器 ；相反地，您想要專注且有效執行您的應用程式來解決商務問題。或者，您可能想要卸載計算密集應用程式或協力廠商應用程式，讓它在雲端完全做為服務執行。

根據工作負載，您可能會利用 Azure 中的現有大量計算服務 (由 Microsoft 或其他服務廠商裝載)，以簡化您解決方案的基礎結構和應用程式管理。某些服務會為所選業界的客戶裝載特定應用程式。某些服務會插入內部部署應用程式，提供混合式解決方案。此外，像 [Azure 媒體服務](http://azure.microsoft.com/documentation/services/media-services)是專用的平台服務。

為了輕鬆地從雲端啟用並相應放大您目前執行的應用程式，[Batch](http://azure.microsoft.com/documentation/services/batch) 提供之 API 可用來排程作業和管理服務中的運算資源。批次可管理虛擬機器的部署和自動調整、作業排程、嚴重損壞修復、資料移動、相依性管理、應用程式部署，及在雲端中執行作業所需的所有其他配管作業。批次目前適合本質平行應用程式，例如，影像轉譯、財務風險模型，和 Windows Server 計算資源中執行的 Monte Carlo 模擬。

請參閱下圖，了解開發人員可使用批次建立的一般工作流程。

![Azure 批次][batch_proc]

1. 將輸入檔案 (例如來源資料或影像、必要的應用程式、.exe 或指令碼檔案及其相依性) 上傳至 Azure 儲存體。

2. 建立批次服務以：

    a.部署相同的 Azure VM 集區以執行應用程式，類似於 HPC 叢集中的運算節點。開發人員指定其大小、其執行的作業系統及其他屬性，例如集區是否可以自動調整。當工作執行時，會從此集區中將 VM 自動指派給它。

    b.建立作業來執行應用程式。開發人員可以指定作業的排程和優先順序，或可隨選執行作業。

    c.將作業分割成工作。每一項工作基本上是在集區中執行其中一個 VM 的命令列，可處理上傳至儲存體的其中一個資料檔案的資訊。

3. 執行服務並監視結果。


## 後續步驟

* 請參閱〈[Batch 和 HPC 的技術資源](big-compute-resources.md)〉來尋找您的解決方案技術指導。

* 最新宣告請參閱 [Microsoft HPC 和批次的小組部落格](http://blogs.technet.com/b/windowshpc/)以及[Azure 部落格](http://azure.microsoft.com/blog/tag/hpc/)。

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=August15_HO6-->