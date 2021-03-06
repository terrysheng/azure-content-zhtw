<properties
   pageTitle="調整 Azure 上 Elasticsearch 資料擷取的效能 | Microsoft Azure"
   description="如何利用 Azure 上的 Elasticsearch 最佳化資料擷取效能。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# 調整 Azure 上 Elasticsearch 資料擷取的效能

本文是[系列文章的其中一篇](guidance-elasticsearch.md)。

## 概觀

建立任何搜尋資料庫的重要層面是判斷建構系統的最佳方式，才能快速且有效率地擷取可搜尋的資料。這項需求的相關考量不但顧及您在其中實作系統的基礎結構選擇，也顧及各種最佳化，讓您可用來協助確保系統可以掌握預期的資料流入層級。

本文件說明您實作預期較高資料擷取率的 Elasticsearch 叢集時應考量的部署和組態選項。為了提供穩固的資料做為說明用途，這份文件也會顯示使用大量資料擷取工作負載對不同組態進行效能評比的結果。工作負載的詳細資料將在本文件最後的[附錄](#appendix-the-bulk-load-data-ingestion-performance-test)中說明。

此效能評比的目的不是產生執行 Elasticsearch 的絕對效能數據，甚至不是建議特定拓撲，而是要說明您可以用來評估效能、調整資料節點大小和實作符合效能需求之叢集的方法。

調整系統的大小時，請務必根據自己的工作負載徹底測試效能。收集可讓您取得要使用之最佳硬體組態的遙測，以及您應該考量的水平縮放因素。特別是，您應該：

- 請考慮已傳送承載的整體大小，而不只是每個大量插入要求中的項目數目。每個要求中較少數量的大量項目可能比較大數量理想，取決於可用來處理每個要求的資源。

您可以使用 Marvel，使用 *readbytes*/*writebytes* I/O 計數器與 JMeter，以及 Ubuntu 上的 *iostat* 和 *vmstat* 等作業系統來監視不同大量插入要求的效果。

- 請執行效能測試並收集遙測來測量 CPU 處理和 I/O 等待時間、磁碟延遲、輸送量和回應時間。此資訊可協助找出潛在的瓶頸，並評估使用進階儲存體的優點與成本。請記住，跨所有節點的 CPU 和磁碟使用率可能不平均，取決於叢集中分區和複本的分散方式 (某些節點可能比其他節點包含更多分區)。

- 請考慮工作負載的並行要求數目如何在叢集中分散，並評估使用不同數目的節點對於處理此工作負載的影響。

- 請考慮工作負載可能隨著業務拓展而如何成長。評估此成長對節點所使用之 VM 和儲存體成本的影響。

- 如果您的案例需要大量的要求，且磁碟基礎結構會維護滿足 SLAS 的輸送量，請了解搭配使用具有較大數目節點的叢集與一般的磁碟可能會更經濟實惠。不過，增加節點的數目可能會以其他節點間通訊和同步處理的形式引發額外負荷。

- 了解每個節點較高數目的核心可能會產生更多的磁碟流量，因為可以處理更多文件。在此情況下，請測量磁碟使用率以評估 I/O 子系統是否可能成為瓶頸，並判斷使用進階儲存體的優點。

- 測試和分析較高數目節點與較少核心以及較少節點與較多核心的取捨。請記住，增加複本數目會增加對叢集的需求，並可能需要新增節點。

- 請考慮使用暫時磁碟可能需要更頻繁地復原索引。

- 測量儲存體磁碟區使用量來評估儲存體的容量和使用率過低。比方說，在我們的案例中，我們會儲存使用 350 GB 儲存體的 15 億個文件。

- 測量工作負載的傳輸速率，並考慮對於您在其中建立虛擬磁碟的任何指定儲存體帳戶，您達到總 I/O 速率傳輸限制的可能性。

## 節點和索引的設計

在必須支援大規模資料擷取的系統中，您可以詢問下列問題：

- **資料快速移動或相當靜態？** 資料越動態，Elasticsearch 的維護額外負荷更大。如果複寫資料，每個複本會同步維護。只有限制週期或可輕易重新建構的快速移動資料可能受益於完全停用複寫。在[調整大規模資料擷取的](#tuning-large-scale-data-ingestion)一節中會進一步考量此選項。

- **您需要藉由搜尋探索到的資料更新到何種程度？** 為維護效能，Elasticsearch 會盡其所能緩衝處理資料。這表示並非所有的變更可立即供搜尋要求使用。Elasticsearch 使變更持續並使其顯示的程序，可在線上文件[使變更持續](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog)中找到。

    資料顯示的速率由相關索引的 *refresh\_interval* 設定控管。根據預設，這個間隔設定為 1 秒。不過，不是每一種情況都需要如此快速重新整理。例如，記錄記錄資料的索引可能需要應付資訊的快速持續流入，這些資訊必須快速擷取，但不需要立即可供查詢。在此情況下，請考慮減少重新整理的頻率。[調整大規模資料擷取](#tuning-large-scale-data-ingestion)一節中也會描述此功能。

- **資料可能的成長速度有多快？** 索引容量取決於建立索引時指定的分區數目。若要允許成長，請指定足夠數目的分區 (預設值為五)。如果一開始在單一節點上建立索引，五個分區都會位於該節點上，但隨著資料量增加，可能會新增其他節點，而 Elasticsearch 會以動態方式將分區分散到各節點。不過，每個分區都有額外負荷；單一索引中所有的搜尋都會查詢所有分區，因此對小量的資料建立大量的分區可能會讓資料擷取變慢 (避免 [Kagillion 分區](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html)案例)。

    某些工作負載 (例如記錄) 可能會每天建立新的索引，如果您發現分區數目並不足以應付資料量，您應該在建立下一個索引之前變更分區數目 (現有的索引不會受到影響)。如果您必須將現有的資料分散至多個分區，其中一個選項是將資訊重新索引；利用適當的組態建立新的索引，並將資料複製到其中。此程序可使用[索引別名](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)對應用程式保持透明。

- **在多租用戶案例中，資料需要在使用者之間進行資料分割嗎？** 您可以為每位使用者建立個別的索引，但是如果每一位使用者僅具有適量的資料，這樣做可能非常昂貴。相反地，請考慮建立[共用索引](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html)並使用[以篩選為根據的別名](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html)來將要求導向到每個使用者的資料。若要將使用者的資料一起保留在相同的分區，請覆寫索引的預設路由組態，並根據使用者的某些識別屬性路由資料。

- **資料是長期或短期？** 如果您使用一組 Azure VM 實作 Elasticsearch 叢集，您可以在本機資源系統磁碟儲存暫時的資料，而不是在連接的磁碟機。使用利用資源磁碟之 SSD 的 VM SKU 可以改善 I/O 效能。不過，在資源磁碟上保留的任何資訊都是暫時的，如果 VM 重新啟動，就可能會遺失 (如需詳細資料，請參閱[了解 Microsoft Azure 虛擬機器上的暫存磁碟機](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx)文件中的＜暫存磁碟機上的資料何時會遺失＞一節)。如果您需要在重新啟動之間保留資料，請建立資料硬碟來保存此資訊，並將它們附加至 VM。

- **資料的活躍度如何？** 如果讀取/寫入活動量超過指定的參數，Azure VHD 會受限於節流 (目前對於連接至標準層 VM 的磁碟為 500 IOPS，對於進階儲存體磁碟者為 5000 IOPS)。

    若要降低節流的機會，並增加 I/O 效能，請考慮為每個 VM 建立多個資料磁碟，並將 Elasticsearch 設定為跨這些磁碟分割資料，如[磁碟和檔案系統需求](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements)中所述。

    您應該藉由確保有足夠的記憶體可經常快取存取的資料，以選取可協助減少磁碟 I/O 讀取作業數目的硬體組態。請參閱＜在 Azure 上實作 Elasticsearch＞文件中的[記憶體需求](guidance-elasticsearch-running-on-azure.md#memory-requirements)一節。

- **每個節點需要支援何種工作負載？** Elasticsearch 因為記憶體可用於快取資料 (以檔案系統快取的形式) 和用於 JVM 堆積而受惠，如＜在 Azure 上實作 Elasticsearch＞文件中的[記憶體需求](guidance-elasticsearch-running-on-azure.md#memory-requirements)一節所述。

    記憶體數量、CPU 核心數目和可用的磁碟數量都由於虛擬機器的 SKU 決定。如需詳細資訊，請參閱 Azure 網站上的[虛擬機器價格](http://azure.microsoft.com/pricing/details/virtual-machines/)頁面。

### 虛擬機器選項

您可以在 Azure 中使用許多不同的 SKU 佈建 VM。Azure VM 的可用資源取決於選取的 SKU。每個 SKU 提供不同核心、記憶體和儲存體的混合。您必須選取適當的 VM 大小，以處理預期的工作負載，但也符合成本效益。一開始請使用符合目前需求的組態 (執行效能評比以測試，如本文件稍後所述)。您可以在稍後藉由新增更多執行 Elasticsearch 節點的 VM 來調整叢集。

Azure 網站上的[虛擬機器的大小](virtual-machines-size-specs/)頁面中記錄不同選項與可供 VM 使用的 SKU。

您應該讓 VM 的大小和資源符合在 VM 上執行的節點會執行的角色。

對於資料節點：

- 配置最多 30 GB 或可用 RAM 記憶體的 50% 給 Java 堆積，取兩者中較低者。保留其餘部分給作業系統用於快取檔案。如果您使用 Linux，您可以在執行 Elasticsearch 之前設定 ES\_HEAP\_SIZE 環境變數，以指定要配置給 Java 堆積的記憶體數量。或者，如果您使用 Windows 或 Linux，您可以在啟動 Elasticsearch 時利用 *Xmx* 和 *Xms* 參數規定記憶體大小。

    根據工作負載，較少的大型 VM 在效能上可能無法發揮和使用大量一般尺寸 VM 一樣的效果；您應該執行測試，以測量額外網路流量和相關維護，以及增加可用核心數目的成本和每個節點上減少的磁碟爭用之間的取捨。

- 使用進階儲存體來儲存 Elasticsearch 資料。這一點會在＜[儲存體選項](#storage-options)＞一節中詳細討論。

- 使用多個磁碟 (相同大小) 並分割資料至這些磁碟。VM 的 SKU 將指定您可以連接的資料磁碟數目上限。如需詳細資訊，請參閱[磁碟和檔案系統需求](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements)。

- 使用多核心 CPU SKU；至少 2 個核心，最好是 4 個或更多。

對於用戶端節點：

- 請勿配置 Elasticsearch 資料的磁碟儲存體；專用的用戶端不會將資料儲存在磁碟上。

- 請確定有足夠的記憶體可處理工作負載。大量插入要求會在資料傳送到不同資料節點之前讀取到記憶體中，彙總與查詢的結果會在傳回至用戶端應用程式之前累積在記憶體中。效能評比您自己的工作負載，並利用 Marvel 或 [JVM 資訊](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section) (使用*節點/統計* API (`GET _nodes/stats`) 傳回) 等工具來監視記憶體使用以存取最佳的需求：特別是，監視每個節點的 *heap\_used\_percent* 度量並將目的設定為保持堆積大小低於可用空間的 75%。

- 確保有足夠的 CPU 核心可接收和處理預期的要求數量。收到要求時會在處理之前排入佇列，可排入佇列的項目數量是每個節點上 CPU 核心數目的功能。您可以使用 [Threadpool 資訊](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section)中的資料 (使用節點/統計 API 傳回) 來監視佇列長度。

    如果佇列的 *rejected* 計數表示要求遭到拒絕，這就表示叢集開始遇到瓶頸。這可能是因為 CPU 頻寬，但也可能是因為其他因素，例如缺乏記憶體或 I/O 效能變慢，因此請使用這項資訊搭配其他的統計資料協助判斷根本原因。

    用戶端節點不一定是必要的，取決於您的工作負載。資料擷取工作負載一向不會受益於使用專用的用戶端，但某些搜尋和彙總可以更快速執行；請準備對您的案例進行效能評比。

    用戶端節點的主要實用對象是使用傳輸用戶端 API 連接到叢集的應用程式。您也可以使用節點用戶端 API，它會使用應用程式主機環境的資源，以動態方式建立應用程式的專用用戶端。如果您的應用程式使用節點用戶端 API，則您的叢集不一定會包含預先設定的專用用戶端節點。
    
    不過，請注意，使用用戶端節點 API 所建立的節點是叢集的頂級成員，因此會和其他節點一起參與網路通話；經常啟動和停止的用戶端節點可能會在整個叢集中建立不必要的干擾。

對於主要節點：

- 請勿配置 Elasticsearch 資料的磁碟儲存體；專用的主要節點不會將資料儲存在磁碟上。

- CPU 需求應該最小。

- 記憶體需求取決於叢集的大小。叢集狀態的相關資訊會保留在記憶體中。對於小型叢集，所需的記憶體量很少，但對於大型的高度活躍叢集而言，經常建立索引且分區會四處移動，狀態資訊的數量可能會明顯成長。監視 JVM 堆積大小，以判斷是否需要新增更多記憶體。

> [AZURE.NOTE]  對於叢集的可靠性，請務必建立多個主要節點，並設定其餘節點以避免發生核心分裂的可能性。在理想情況下，應該有奇數數目的主要節點。本主題會在[在 Azure 上設定 Elasticsearch 的恢復與復原][]文件中詳述。

### 儲存體選項

Azure VM 上有許多可用的儲存體選項，其包含會影響成本、效能、可用性和復原的不同取捨，您必須仔細考慮。

請注意，您應該將 Elasticsearch 資料儲存在專用的資料磁碟上。這有助於減少與作業系統的爭用，並確保大量的 Elasticsearch I/O 不會和作業系統功能競爭 I/O 資源。

Azure 磁碟受限於效能條件約束。如果您發現叢集會定期遭遇活動高載，則 I/O 要求可能會節流。為了防止這種情形，請微調您的設計，以針對每個磁碟可能收到的要求量來平衡 Elasticsearch 中的文件大小。

以標準儲存體為基礎的磁碟支援最高 500 IOPS 的要求率，而以進階儲存體為基礎的磁碟可在高達 5000 IOPS 的情況下運作。進階儲存體磁碟只適用於 DS 與 GS 系列的 VM。Azure VM 的最高磁碟 IOPS 率[在線上記錄](virtual-machines-size-specs/)。

**持續性資料磁碟**

持續性資料磁碟是受到 Azure 儲存體支援的 VHD。如果在重大失敗之後需要重新建立 VM，現有的 VHD 可以輕鬆地連接至新的 VM。VHD 可以根據標準儲存體 (轉動式媒體) 或進階儲存體 (SSD) 建立。如果您想要使用 SSD，您必須使用 DS 系列或更好的系列建立 VM。DS 機器的成本和對等的 D 系列 VM 相同，但您需要額外支付使用進階儲存體的費用。

在每個磁碟的最大傳輸速率不足以支援預期的工作負載的情況下，請考慮建立多個資料磁碟，並允許 Elasticsearch [分割資料至這些磁碟](guidance-elasticsearch-running-on-azure.md#disk-and-file-system-requirements)，或實作系統層級[使用虛擬磁碟的 RAID 0 等量分割](virtual-machines-linux-configure-raid/)。

> [AZURE.NOTE] Microsoft 內部的經驗顯示，使用 RAID 0 特別有利於平滑產生頻繁活動高載之 *spiky* 工作負載的 I/O 影響。

將本機進階備援 (或是低階或 QA 工作負載的本機備援) 用於保留磁碟的儲存體帳戶；Elasticsearch HA 不需要跨地理位置和區域複寫。

**暫時的磁碟**

使用以 SSD 為基礎的持續性磁碟必須建立支援進階儲存體的 VM。這會對價格造成影響。對於需要大約 800 GB 儲存體的中等大小節點，使用本機暫時磁碟來保存 Elasticsearch 資料是符合成本效益的解決方案。在標準 D 系列的 VM 上，會使用 SSD 實作暫時的磁碟，SSD 可提供比一般磁碟好很多的效能以及低許多的延遲。

使用 Elasticsearch 時，效能也相當於使用進階儲存體，且不會產生成本 – 如需詳細資訊，請參閱[處理磁碟延遲問題](#addressing-disk-latency-issues)。

VM 的大小會限制暫時儲存體中的可用空間量，一如 [D 系列效能期望](https://azure.microsoft.com/blog/d-series-performance-expectations/)文件所述。

例如，Standard\_D1 VM 提供 50 GB 的暫時儲存體、Standard\_D2 VM 有 100 GB 的暫時儲存體，而 Standard\_D14 VM 提供 800 GB 的暫時空間。對於節點只需要此空間量的叢集，搭配使用 D 系列 VM 和暫時儲存體可符合成本效益。

您必須根據機器重新啟動後復原此資料的相關時間和成本，平衡增加的可用輸送量和暫時儲存體。如果 VM 移到不同的主機伺服器，如果主機更新，或者如果主機發生硬體失敗，暫時磁碟的內容都會遺失。如果資料本身有限制的週期，則遺失資料可能可以接受。對於存留較久的資料，或許能重建索引，或從備份復原遺失的資訊。您可使用其他 VM 上保留的複本將遺失的可能性降到最低。

> [AZURE.NOTE] 請勿使用**單一** VM 來保存重要的生產資料。如果節點失敗，所有的資料都無法使用。對於重要資訊，請確定至少在一個其他節點上複寫資料。

**Azure 檔案**

[Azure 檔案服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)提供使用 Azure 儲存體的共用檔案存取。您可以建立隨後可在 Azure VM 上裝載的檔案共用。多個 VM 可以裝載相同的檔案共用，讓他們存取相同的資料。

基於效能考量，不建議您使用檔案共用來保留不需要在節點之間共用的 Elasticsearch 資料；一般資料磁碟更適合此用途。檔案共用可用於建立 Elasticsearch [陰影複本索引](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html)。不過，這項功能目前仍屬實驗性質，此時不應在生產環境中實作。基於這個理由，此指引中不會進一步探討陰影索引。

**網路選項**

Azure 會實作共用的網路配置。使用相同硬體機架的 VM 會競爭網路資源。因此，可用的網路頻寬可能會根據一天的時間和每天在 VM 上執行的工作循環而有所不同，共用相同的實體網路基礎結構。您幾乎無法控制這些因素。請務必了解網路效能可能會隨時間變動，因此請據以設定使用者期望。

## 相應增加節點以支援大規模資料擷取

您可以使用相當一般的硬體建置 Elasticsearch 叢集，並隨著資料量的成長及要求數目的增加進行相應增加或相應放大。有了 Azure，您可以藉由在更大更昂貴的 VM 上執行來相應增加，或者藉由使用額外的較小較便宜 VM 來相應放大。

您也可以執行這兩種策略的組合。沒有任何單一大小的解決方案可以適用所有案例，所以請針對您必須做準備的任何指定情況來評估最佳方法，以進行一系列的效能測試。

本節焦點在於相應增加方法；相應放大將在[相應放大：結論](#scaling-out-conclusions)一節中討論。本節說明一系列針對一組包含不同大小 VM 的 Elasticsearch 叢集執行之效能評比的結果。叢集指定為小型、中型和大型。下表摘要說明每個叢集中配置給 VM 的資源。

| 叢集 | VM SKU | 核心數目 | 資料磁碟數量 | RAM |
|---------|-------------|-----------------|----------------------|------|
| 小型 | 標準 D2 | 2 | 4 | 7 GB |
| 中型 | 標準 D3 | 4 | 8 | 14 GB |
| 大型 | 標準 D4 | 8 | 16 | 28 GB |

每個 Elasticsearch 叢集包含 3 個資料節點。這些資料節點會處理用戶端要求，以及進行資料處理；因為個別用戶端節點對於測試使用的資料擷取案例並沒有好處，所以並未加以採用。叢集也包含三個主要節點，Elasticsearch 選擇其中一個來協調叢集。

使用 Elasticsearch 1.7.3 來執行測試。一開始先在執行 Ubuntu Linux 14.0.4 的叢集上執行測試，然後使用 Windows Server 2012 重複。測試執行的工作負載詳細資料將在[附錄](#appendix-the-bulk-load-data-ingestion-performance-test)中說明。

### 資料擷取效能 – Ubuntu Linux 14.0.4

下表摘要說明針對每個組態執行測試兩個小時的整體結果：

| 組態 | #範例 | 平均回應時間 (毫秒) | 輸送量 (作業/秒) |
|---------------|-----------|----------------------------|---------------------------|
| 小型 | 67057 | 636 | 9\.3 |
| 中型 | 123482 | 692 | 17\.2 |
| 大型 | 197085 | 839 | 27\.4 |

三個組態的輸送量和處理範例數比例大約為 1:2:3。不過，記憶體、CPU 核心和磁碟等方面的可用資源比例為 1:2:4。調查叢集中節點的低階效能詳細資料以評估可能的原因是值得的動作。這項資訊可以協助判斷是否有相應增加的限制，以及考慮相應放大的可能較佳時機。

### 判斷限制因素：網路使用率

Elasticsearch 取決於具有足夠的網路頻寬來支援用戶端要求的流入，以及叢集中節點之間的同步處理資訊。如同稍早強調的，您對頻寬可用性的控制權有限且取決於許多變數，例如使用中的資料中心，以及其他共用相同網路基礎結構之 VM 的目前網路負載。不過，檢查每個叢集的網路活動仍然有其價值，前提是要確認流量不會過多。下圖顯示節點 2 在每個叢集中收到的網路流量比較 (每個叢集中其他節點的流量非常類似)。

![](media/guidance-elasticsearch/data-ingestion-image1.png)

在兩小時期間，節點 2 在每個叢集組態中每秒收到的平均位元組如下所示：

| 組態 | 平均接收位元組數目/秒 |
|---------------|--------------------------------------|
| 小型 | 3993640\.3 |
| 中型 | 7311689\.9 |
| 大型 | 11893874\.2 |

在系統以**穩定狀態**執行時，進行測試。在索引重新平衡或是發生節點復原的情況下，保留主要與複本分區的節點之間的資料傳輸可能會產生大量的網路流量。此程序的影響會在[在 Azure 上設定 Elasticsearch 的恢復與復原][]文件中詳述。

### 判斷限制因素：CPU 使用率

可用的處理容量至少可部分控制要求處理的速率。Elasticsearch 會在大量插入佇列上接受大量插入要求。每個節點都有一組大量插入佇列取決於可用的處理器數目。根據預設，每個處理器都有一個佇列，而每個佇列可以保存多達 50 個未完成的要求才會開始拒絕要求。

應用程式傳送要求的速率不可讓佇列溢出。每個佇列中的項目數應隨時維持用戶端應用程式傳送要求的速率，以及 Elasticsearch 擷取及處理相同要求之速率的函數。基於這個理由，有一個重要的擷取統計資料考量到下表摘要說明的錯誤率。

| 組態 | 範例總數 | # 錯誤數 | 錯誤率 |
|---------------|---------------|-----------|------------|
| 小型 | 67057 | 0 | 0\.00% |
| 中型 | 123483 | 1 | 0\.0008% |
| 大型 | 200702 | 3617 | 1\.8 % |

每個錯誤的導因皆為下列 Java 例外狀況：

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

增加佇列數目及/或每個佇列的長度可能會減少錯誤數目，但這種方法只可以應付短時間內的高載。執行一系列持續的資料擷取工作時執行此動作只能延遲錯誤開始發生的時間點。此外，這項變更無法改善輸送量，並且可能傷害用戶端應用程式的回應時間，因為要求在處理之前需要更長的時間加入佇列。

5 個分區和 1 個複本的預設索引結構 (總共 10 個分區)，會導致叢集中的節點之間微幅的不平衡；兩個節點會包含 3 個分區，而另一個節點將包含 4 個分區。最忙碌的節點最有可能是限制最多輸送量的項目，因此也是每個案例中選取這個節點的原因。

下列圖形組說明每個叢集中最忙碌節點的 CPU 使用率。

![](media/guidance-elasticsearch/data-ingestion-image2.png)

![](media/guidance-elasticsearch/data-ingestion-image3.png)

![](media/guidance-elasticsearch/data-ingestion-image4.png)

對於小型、中型和大型叢集，這些節點的平均 CPU 使用率是 75.01%、64.93% 及 64.64%。使用率很少實際達到 100%，而且在節點大小和可用的 CPU 電源增加時，使用率會下跌。因此 CPU 電源不太可能是限制大型叢集效能的因素。

### 判斷限制因素：記憶體

記憶體使用情況是可能會影響效能的另一個重要層面。為了進行測試，已配置 50%的可用記憶體給 Elasticsearch；此點和[記載的建議](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene)一致。在執行測試時，會監視 JVM 是否有過多的記憶體回收活動 (堆積記憶體不足的指示)。在所有情況下，堆積大小相當穩定，JVM 表現低記憶體回收活動。以下的螢幕擷取畫面顯示 Marvel 的快照，強調在大型叢集上執行測試時，短期間的主要 JVM 統計資料。

![](media/guidance-elasticsearch/data-ingestion-image5.png)

***大型叢集上的 JVM 記憶體和記憶體回收活動。***

### 判斷限制因素：磁碟 I/O 速率

可能會限制效能的伺服器端上的剩餘實際功能是磁碟 I/O 子系統的效能。下圖比較每個叢集中最忙碌的節點在寫入位元組數方面的磁碟活動。

![](media/guidance-elasticsearch/data-ingestion-image6.png)

下表顯示在兩小時期間，節點 2 在每個叢集組態中每秒寫入的平均位元組：

| 組態 | 已寫入的位元組數目/秒 |
|---------------|-------------------------------------|
| 小型 | 25502361\.94 |
| 中型 | 48856124\.5 |
| 大型 | 88137675\.46 |

寫入的資料量會隨著叢集處理的要求數目增加，但是 I/O 速率會維持在 Azure 儲存體的限制內 (使用 Azure 儲存體所建立的磁碟可以持續支援數十到數百 MB/s 的速率，取決於使用標準或進階儲存體)。檢查等待磁碟 I/O 所花的時間量有助於解釋磁碟輸送量為什麼低於理論的最大值。下圖和下表顯示相同三個節點的統計資料：

> [AZURE.NOTE] 磁碟等候時間可由監視 CPU 時間百分比來測量，在此期間處理器會封鎖，等候 I/O 作業完成。

![](media/guidance-elasticsearch/data-ingestion-image7.png)

![](media/guidance-elasticsearch/data-ingestion-image8.png)

![](media/guidance-elasticsearch/data-ingestion-image9.png)

| 組態 | 平均磁碟等候 CPU 時間 (%) |
|---------------|--------------------------------|
| 小型 | 21\.04 |
| 中型 | 14\.48 |
| 大型 | 15\.84 |

此資料表示絕大部分的 CPU 時間 (大約介於 16%到 21%之間) 都花在等候磁碟 I/O 完成。這樣會限制 Elasticsearch 處理要求和儲存資料的能力。

在測試執行期間，大型叢集會插入超過**五億個文件**。在資料庫包含超過六億個文件時，允許繼續進行測試會顯示等候時間大幅增加。此行為的原因尚未完全調查，但可能是由於磁碟片段造成磁碟延遲增加。

增加多個節點的叢集大小可能有助於減輕這種行為的影響。在極端情況下，可能需要將顯示超量 I/O 時間的磁碟重組。不過，重組大型磁碟可能需要相當長的時間 (對於 2TB 的 VHD 磁碟機可能會超過 48 小時)，而只重新格式化磁碟機，並允許 Elasticsearch 從複本分區復原遺失的資料可能是比較符合成本效益的方法。

### 處理磁碟延遲問題

測試一開始會使用以標準磁碟設定的 VM 來執行。標準磁碟以轉動式媒體為基礎，如此一來會受限於旋轉延遲及其他可能限制 I/O 速率的瓶頸。Azure 也提供進階儲存體，可在其中使用 SSD 裝置建立磁碟。這些裝置沒有旋轉延遲，因此應該可提供改善的 I/O 速度。

下表比較在大型叢集中將標準磁碟取代為進階磁碟的結果 (大型叢集中的標準 D4 VM 取代為標準 DS4 VM；兩者的核心、記憶體和磁碟數目相同，唯一的差別在於 DS4 VM 使用 SSD)。

| 組態 | #範例 | 平均回應時間 (毫秒) | 輸送量 (作業/秒) |
|------------------|-----------|----------------------------|---------------------------|
| 大型 - 標準 | 197085 | 839 | 27\.4 |
| 大型 - 進階 | 255985 | 581 | 35\.6 |

回應時間明顯較佳，導致平均輸送量相當接近小型叢集的 4 倍。這一點與標準 DS4 VM 上可用的資源更趨一致。叢集中最忙碌節點 (在此案例中為節點 1) 上的平均 CPU 使用率會增加，因為它花較少的時間在等候 I/O 完成：

![](media/guidance-elasticsearch/data-ingestion-image10.png)

當您考量到下圖時，磁碟等候時間的減少幅度會變明顯，該圖顯示對於最忙碌的節點，此統計資料平均降到大約 1%：

![](media/guidance-elasticsearch/data-ingestion-image11.png)

不過這樣的改善是有代價的。擷取錯誤的數目增加 10 到 35797 倍 (12.3%)。同樣地，大部分的錯誤都是大量插入佇列溢位的結果。假設目前執行的硬體看起來接近容量上限，可能需要新增更多節點，或節流大量插入速率來減少錯誤量。本文件稍後會討論這些問題。

### 利用暫時儲存體測試

相同的測試會使用暫時儲存體在 D4 VM 的叢集上重複。在 D4 VM 上，暫時儲存體會實作為單一 400 GB SSD。處理範例數目、回應時間和輸送量都和叢集 (以具備進階儲存體的 DS14 VM 為基礎) 的回報數據非常類似。

| 組態 | #範例 | 平均回應時間 (毫秒) | 輸送量 (作業/秒) |
|-----------------------------------|-----------|----------------------------|---------------------------|
| 大型 - 進階 | 255985 | 581 | 35\.6 |
| 大型 – 標準 (暫時磁碟) | 255626 | 585 | 35\.5 |

錯誤率也類似 (總計 289488 次要求中有 33862 次失敗 – 11.7%)。

下圖顯示叢集中最忙碌節點 (此時為節點 2) 的 CPU 使用率和磁碟等候統計資料：

![](media/guidance-elasticsearch/data-ingestion-image12.png)

![](media/guidance-elasticsearch/data-ingestion-image13.png)

在此情況下，單獨以效能而言，使用暫時儲存體可視為比使用進階儲存體更符合成本效益的解決方案。

### 資料擷取效能 – Windows Server 2012

相同的測試會搭配使用一組 Elasticsearch 叢集與執行 Windows Server 2012 的節點重複。這些測試的目的是建立作業系統的選擇可能會對叢集效能產生的影響 (如果有的話)。

為了說明 Windows 上 Elasticsearch 的延展性，下表顯示小型、中型和大型叢集組態所達成的輸送量和回應時間。請注意，這些測試都利用設定為使用 SSD 暫時儲存體的 Elasticsearch 執行，因為利用 Ubuntu 的測試顯示磁碟延遲可能是達到最大效能的關鍵因素：

| 組態 | #範例 | 平均回應時間 (毫秒) | 輸送量 (作業/秒) |
|---------------|-----------|----------------------------|---------------------------|
| 小型 | 90295 | 476 | 12\.5 |
| 中型 | 169243 | 508 | 23\.5 |
| 大型 | 257115 | 613 | 35\.6 |

這些結果會指出 Elasticsearch 如何配合 VM 大小與 Windows 上的可用資源進行調整。

下表會比較 Ubuntu 和 Windows 上的大型叢集結果：

| 作業系統 | #範例 | 平均回應時間 (毫秒) | 輸送量 (作業/秒) | 錯誤率 (%) |
|------------------|-----------|----------------------------|---------------------------|----------------|
| Ubuntu | 255626 | 585 | 35\.5 | 11\.7 |
| Windows | 257115 | 613 | 35\.6 | 7\.2 |

輸送量和大型 Ubuntu 叢集一致，不過回應時間稍高，這可能是因為錯誤率較低 (錯誤的回報速度會比成功的作業快，因此具有較低的回應時間)。

Windows 監視工具所回報的 CPU 使用率稍微高於 Ubuntu。不過，您應該極度謹慎看待這些跨作業系統的測量比較，因為不同的作業系統回報這些統計資料的方式也不同。此外，以 CPU 花在等候 I/O 方面的磁碟延遲資訊無法以用於 Ubuntu 的相同方式使用。重點在於 CPU 使用率偏高，這表示等候 I/O 所花費的時間很少：

![](media/guidance-elasticsearch/data-ingestion-image14.png)

### 相應增加：結論

Windows 和 Ubuntu 上微調叢集的 Elasticsearch 效能可能相等，而且它會在兩個作業系統上以類似的模式相應增加。為了達到最佳效能，請**使用進階儲存體保存 Elasticsearch 資料**。

## 相應放大叢集以支援大規模資料擷取

相應放大是上一節中調查之相應增加的免費方法。Elasticsearch 的其中一個重要功能是內建在軟體的固有水平延展性。增加叢集的大小只是新增更多節點。您不需要執行任何手動作業即可重新分配索引或分區，因為這些工作會自動處理，但是有許多組態選項可供您用來影響此程序。

新增更多節點有助於藉由將負載分散在多個機制來改善效能。當您新增更多節點，您可能也需要考慮重新索引資料，以增加可用的分區數目。您可以建立其分區多於一開始可用節點數目的索引，以在某個程度上優先佔用此程序。進一步新增節點時，就可以分散分區。

除了善用 Elasticsearch 的水平延展性，還有其他原因需要實作其分區多於節點數的索引。每個分區會以個別的資料結構實作 ([Lucene](https://lucene.apache.org/) 索引)，而且有它自己內部的機制來維護一致性及處理並行。建立多個分區可協助提高一個節點內的平行處理原則，並可改善效能。

不過，維護效能同時進行調整是個需要平衡的動作。一個叢集包含越多節點和分區，需要更多工作量才能同步處理叢集執行的工作，這樣可能會減少輸送量。任何指定的工作負載都有一個甜蜜點，可以充分發揮擷取效能，同時將維護額外負荷降至最低。這個甜蜜點主要取決於工作負載和叢集的本質；具體而言，就是文件的數量、大小和內容、擷取發生的速率，以及系統執行所在的硬體。

本節摘要說明調查調整叢集大小的結果，用意在於支援先前所述之效能測試所使用的工作負載。在叢集上利用叢集並根據執行 Ubuntu Linux 14.0.4 的大型 VM 大小 (標準 D4 具有 8 個 CPU 核心、16 個資料磁碟和 28 GB 的 RAM) 來執行相同的測試，但利用不同數目的節點和分區設定。結果的意圖並非限定為只套用一種特定情況，但是它們可以做為不錯的起點，協助您分析叢集的水平延展性，並產生最佳比例的分區對節點數目以符合您的需求。

### 基準結果 – 3 個節點

若要取得基準數據，請針對具有 5 個分區及 1 個複本的 3 節點叢集來執行資料擷取效能測試。這是 Elasticsearch 索引的預設組態。在此組態中，Elasticsearch 會分散 2 個主要分區至其中 2 個節點，而其餘主要分區則儲存在第三個節點上。下表摘要說明每秒大量擷取作業的輸送量，以及測試成功儲存的文件數目。

> [AZURE.NOTE] 在本節後續的資料表中，主要分區的分散會以連字號分隔的每個節點之數字表示。例如，5 分區 3 節點版面配置會描述為 2-2-1。複本分區的版面配置並不包含在內；他們會遵循類似主要分區的配置。

| 組態 | # 文件 | 輸送量 (作業/秒) | 分區版面配置 |
|---------------|--------------|-----------------------------|--------------|
| 5 個分區 | 200560412 | 27\.86 | 2-2-1 |

### 6 個節點的結果

測試會在 6 節點叢集上重複。這些測試的目的是更精確地嘗試與確認在一個節點上儲存多個分區的效果。

| 組態 | # 文件 | 輸送量 (作業/秒) | 分區版面配置 |
|---------------|--------------|-----------------------------|--------------|
| 4 個分區 | 227360412 | 31\.58 | 1-1-0-1-1-0 |
| 7 個分區 | 268013252 | 37\.22 | 2-1-1-1-1-1 |
| 10 個分區 | 258065854 | 35\.84 | 1-2-2-2-1-2 |
| 11 個分區 | 279788157 | 38\.86 | 2-2-2-1-2-2 |
| 12 個分區 | 257628504 | 35\.78 | 2-2-2-2-2-2 |
| 13 個分區 | 300126822 | 41\.68 | 2-2-2-2-2-3 |

這些結果似乎會表示下列趨勢：

* 每個節點有越多分區可以改善輸送量。每個節點具有為這些測試建立的少量分區，這種現象是預期中的，原因如稍早所述。

* 奇數的分區可提供優於偶數的效能。這一點的理由較不清楚，但它*可能*是因為 Elasticsearch 使用的路由演算法比較能夠在此情況下將資料分散至分區，導致每個節點中更均勻的負載。

若要測試這些假設，需利用大量分區執行數個進一步的測試。根據 Elasticsearch 的口頭建議，我們決定使用分區的質數進行測試，因為這些分區針對問題範圍提供合理的奇數分散。

| 組態 | # 文件 | 輸送量 (作業/秒) | 分區版面配置 |
|---------------|--------------|-----------------------------|-------------------|
| 23 個分區 | 312844185 | 43\.45 | 4-4-4-3-4-4 |
| 31 個分區 | 309930777 | 43\.05 | 5-5-5-5-6-5 |
| 43 個分區 | 316357076 | 43\.94 | 8-7-7-7-7-7 |
| 61 個分區 | 305072556 | 42\.37 | 10-11-10-10-10-10 |
| 91 個分區 | 291073519 | 40\.43 | 15-15-16-15-15-15 |
| 119 個分區 | 273596325 | 38\.00 | 20-20-20-20-20-19 |

這些結果建議即將在大約 23 個分區達到臨界點。在此臨界點之後，增加分區數目會導致效能的小幅降低 (43 個分區的輸送量可能是異常狀況)。

### 9 個節點的結果

測試使用 9 個節點的叢集重複，並再次使用質數的分區。

| 組態 | # 文件 | 輸送量 (作業/秒) | 分區版面配置 |
|---------------|--------------|-----------------------------|----------------------------|
| 17 個分區 | 325165364 | 45\.16 | 2-2-2-2-2-2-2-2-1 |
| 19 個分區 | 331272619 | 46\.01 | 2-2-2-2-2-2-2-2-3 |
| 29 個分區 | 349682551 | 48\.57 | 3-3-3-4-3-3-3-4-3 |
| 37 個分區 | 352764546 | 49\.00 | 4-4-4-4-4-4-4-4-5 |
| 47 個分區 | 343684074 | 47\.73 | 5-5-5-6-5-5-5-6-5 |
| 89 個分區 | 336248667 | 46\.70 | 10-10-10-10-10-10-10-10-9 |
| 181 個分區 | 297919131 | 41\.38 | 20-20-20-20-20-20-20-20-21 |

這些結果顯示類似的模式，臨界點大約是 37 個分區。

### 相應放大：結論

針對此特定案例使用粗糙推斷，6 個節點和 9 個節點的測試結果指出，最大化效能的理想分區數目是 4n+/-1，其中 n 是節點數目。這*可能*是可用大量插入執行緒的數目函數，取決於 CPU 核心數目，基本原理如下所示 (如需詳細資料，請參閱[多文件模式](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc))：

- 用戶端應用程式傳送的每個大量插入要求會由單一資料節點收到。

- 資料節點會為受原始要求影響的每個主要分區建置新的大量插入要求，並以平行方式將它們轉送到其他節點。

- 寫入每個主要分區時，另一個要求會傳送至該分區的每個複本。主要分區會在結束前等候傳送至複本的要求完成。

根據預設，Elasticsearch 會為 VM 中每個可用的 CPU 核心建立一個大量插入執行緒。如果這項測試使用 D4 VM，每個 CPU 包含 8 個核心，因此會建立 8 個大量插入執行緒。索引在每個節點上使用合併的 4 個 (在其中一個案例為 5 個) 主要分區，但是每個節點上也有 4 (5) 個複本。將資料插入這些分區和複本時，每個要求可能會耗用最多 8 個執行緒，符合可用的數目。增加或減少分區數目可能會導致執行緒效率不佳，因為執行緒可能保留未佔用或要求已排入佇列。不過，在沒有進一步實驗的情況下，這只是理論，不可能會明確。

測試也說明了另一個重點。在此案例中，增加節點數目可能會改善資料擷取輸送量，但結果不一定會以線性方式調整。利用 12 和 15 個節點的叢集進行進一步測試時，可能會顯示相應放大無法展現其他優勢的時候。如果節點數目未提供足夠的儲存體空間，可能需要回到相應增加策略，並開始使用以進階儲存體為基礎的更多或更大的磁碟。

> [AZURE.IMPORTANT] 請勿將 4n+/-1 視為每個叢集一律適用的魔術公式。如果您有較少或較多可用的 CPU 核心，最佳的分區組態可能不同。結果會依據只執行資料擷取的特定工作負載。對於也包含查詢和彙總混合的工作負載，結果可能大不相同。

> 此外，資料擷取工作負載會使用單一索引。在許多情況下，資料很可能分散到多個導致不同模式或資源使用的索引。

> 本練習的重點是了解使用的方法，而非取得的結果。您應該準備好根據自己的工作負載來執行延展性評估，以取得最適合本身案例的資訊。

## 調整大規模資料擷取

Elasticsearch 可進行大幅設定，包含許多參數與設定可供您用來針對特定使用個案和案例最佳化效能。本節說明一些常見的範例。請注意，Elasticsearch 在這方面提供的彈性也伴隨著相關的警告；也就是 Elasticsearch 非常容易失調並使效能變差。調整時，一次只進行一個變更，並且請隨時測量任何變更的影響，以確保變更不會危害到您的系統。

### 編製索引作業的最佳化資源

下列清單說明您在調整 Elasticsearch 叢集以支援大規模資料擷取時應考量的一些重點。前兩個項目最有可能對效能產生立即明顯的影響，而其餘部分影響較輕微，視工作負載而定：

*  重新整理索引時，新增至索引的新文件只有搜尋可以看見。重新整理索引是昂貴的作業，因此它只會定期執行，而不是在每個文件建立時執行。預設重新整理間隔是 1 秒。如果您執行大量作業，應該考慮暫時停用索引重新整理；將索引 *refresh\_interval* 設為 -1。

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"refresh_interval": -1
		}
	}
	```

	在作業結尾使用 [*\_refresh*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html) API 來手動觸發重新整理。如需詳細資訊，請參閱[大量索引使用量](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk)。[變更資料擷取的重新整理間隔所產生的影響](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance)的詳細資料將於稍後說明。

* 如果複寫索引，在主要分區進行的每個編製索引作業 (文件建立、更新或刪除) 會在複本分區上重複。請考慮在大量匯入作業期間停用複寫，然後在匯入完成時重新啟用：

    ```http
	PUT /my_busy_index
	{
		"settings" : {
			"number_of_replicas": 0
		}
	}
	```

	當您重新啟用複寫時，Elasticsearch 會以位元組為單位，執行從索引到每個複本的資料網路傳輸。這樣比在每個節點上以文件為單位重複編製索引程序更有效率。風險在於資料可能遺失且主要節點會在執行大量匯入時失敗，但是復原可能只是再次開始匯入。[資料擷取效能之複寫的影響](#the-impact-of-replicas-on-data-ingestion-performance)會在稍後詳述。

* Elasticsearch 會嘗試在查詢和擷取資料所需的可用資源之間取得平衡。如此一來，它可以對資料擷取效能進行節流 (節流事件記錄在 Elasticsearch 記錄檔中)。這項限制的目的是為了防止同時建立大量索引區段，如此會需要合併以及儲存至磁碟；程序可能獨佔資源。如果您的系統目前未執行查詢，您可以停用資料擷取節流。這樣應該可以讓索引編製達到最佳效能。您可以停用整個叢集的節流，如下所示：

	```http
	PUT /_cluster/settings
	{
		"transient" : {
			"indices.store.throttle.type": "none"
		}
	}
	```

    擷取完成時將叢集的節流類型設定回「合併」。另外請注意，停用節流可能會導致叢集不穩定，因此請確保您已備妥可在必要時復原叢集的程序。

* Elasticsearch 會保留部分的堆積記憶體進行編製索引作業；剩餘的部份大部分供查詢和搜尋使用。這些緩衝區的目的是要減少磁碟 I/O 作業的數目，其目標是執行較少較大的寫入，而不是較多較小的寫入。預設的配置堆積記憶體比例為 10%。如果您編製大量資料的索引，這個值可能不足。對於支援大量資料擷取的系統，您應該允許最多 512 MB 的記憶體供節點中每個作用中的分區使用。比方說，如果您在 D4 VM 上執行 Elasticsearch (28 GB RAM)，並已配置 50%的可用記憶體至 JVM (14 GB)，則 1.4 GB 可供索引編製作業使用。如果節點包含 3 個作用中的分區，此設定綽綽有餘。不過，如果節點包含更多的分區，請考慮在 elasticsearch.yml 組態檔中增加 *indices.memory.index\_buffer\_size* 參數的值。如需詳細資訊，請參閱[Elasticsearch 編製索引的效能考量](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing)。

    為每個作用中分區配置超過 512 MB，很可能無法改善索引效能，但實際上可能會有不利的影響，因為可供執行其他工作使用的記憶體較少。另外請注意，配置更多堆積空間給索引緩衝區會移除其他作業的記憶體，例如搜尋和彙總資料，並且可能減緩查詢作業的效能。

* Elasticsearch 會限制可同時在分區中執行索引編製作業的執行緒數目 (預設值為 8)。如果節點只包含少數的分區，請考慮為受限於大量索引編製作業或做為大量插入目標的索引增加 *index\_concurrency* 設定，如下所示：

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"index_concurrency": 20
		}
	}
	```

* 如果您要在短時間內執行大量索引編製和大量作業，可以在執行緒集區中增加可用*索引*和*大量*執行緒的數目，並為每個資料節點擴充*大量插入*佇列的大小。這樣可讓更多要求排入佇列，而不會被捨棄。如需詳細資訊，請參閱[執行緒集區](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html)。如果您執行持續的高階資料擷取，則不建議增加大量執行緒的數目；建議改為建立額外的節點，並使用分區化來將索引編製負載分散到這些節點。或者，請考慮以序列方式傳送大量插入批次，而非以平行方式，因為這是自然的節流機制，可減少由於大量插入佇列溢位造成錯誤的機會。

### 變更索引重新整理間隔對資料擷取效能的影響

重新整理間隔可控管查詢和彙總看件擷取資料的速率，但經常重新整理可能會影響資料擷取作業的效能。預設重新整理間隔是 1 秒。您可以完全停用重新整理，但這樣可能不適用於您的工作負載。您可以實驗嘗試不同的時間間隔，並建立中心點以平衡擷取效能和提供最新資訊的需求。

影響的範例為，在包含 7 個分區分散於 3 個資料節點的 Elasticsearch 叢集上重複資料擷取效能測試。索引具有單一複本。每個資料節點都是以使用 SSD 型暫時儲存體來保留資料的 D4 VM (28 GB RAM，8 個處理器核心) 為基礎。每項測試執行 1 小時。

在這項測試中，重新整理速率的預設值設定為 1 秒。下表顯示這項測試的輸送量和回應時間，用來與個別執行回合比較，其重新整理速率降低至每隔 30 秒一次。

| 重新整理速率 | # 範例 | 平均回應時間 – 成功的作業 (毫秒) | 輸送量 – 成功的作業 (作業/秒) |
|--------------|------------|----------------------------------------------------|---------------------------------------------------|
| 1 秒 | 93755 | 460 | 26\.0 |
| 30 秒 | 117758 | 365 | 32\.7 |

在這項測試中，降低重新整理速率導致輸送量提升 18%，平均回應時間也減少了 21%。藉由使用 Marvel 產生的下圖說明這項差異的主要原因。下列各圖顯示重新整理間隔設定為 1 秒和 30 秒時發生的索引合併活動。

執行索引合併以防止記憶體中索引區段數目過多。1 秒的重新整理間隔會產生必須經常合併的大量小型區段，而 30 秒的重新整理間隔會產生較少的大型區段，可以最佳方式合併。

![](media/guidance-elasticsearch/data-ingestion-image15.png)

***索引重新整理速率 1 秒的索引合併活動***

![](media/guidance-elasticsearch/data-ingestion-image16.png)

***索引重新整理速率 30 秒的索引合併活動***

### 複本對資料擷取效能的影響

複本是任何恢復叢集的重要功能，不使用複本的風險是節點失敗時可能會遺失資訊。不過，複本會增加執行的磁碟和網路 I/O 數量，而且可能會危害到資料擷取的速率。如稍早所述的理由，在大規模資料上傳作業期間暫時停用複本或許很有幫助。

資料擷取效能測試已使用三種組態重複：

* 使用沒有複本的叢集、

* 使用有 1 個複本的叢集和

* 使用 2 個複本的叢集。

在所有情況下，叢集包含跨 3 個節點的 7 個分區，並且會在 VM (設定方式如同上一組測試所述) 上執行。測試索引使用的重新整理間隔為 30 秒。

下表摘要說明每個測試的回應時間和輸送量以供比較：

| 組態 | # 範例 | 平均回應時間 – 成功的作業 (毫秒) | 輸送量 – 成功的作業 (作業/秒) | # 資料擷取錯誤 |
|---------------|------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| 0 複本 | 215451 | 200 | 59\.8 | 0 |
| 1 個複本 | 117758 | 365 | 32\.7 | 0 |
| 2 個複本 | 94218 | 453 | 26\.1 | 194262 |


效能會隨著複本數目增加而明顯降低，但您也應該注意到第三個測試中的大量資料擷取錯誤。這些錯誤所產生的訊息指出，錯誤原因是大量插入佇列溢位造成要求被拒。這些拒絕的發生速度很快，因此數量很多。

> [AZURE.NOTE] 第三個測試的結果強調這類的暫時性錯誤發生時，使用智慧性重試策略的重要性—短時間的輪詢可讓大量插入佇列清空，然後再重新嘗試重複大量插入作業。

接下來幾組圖形比較測試期間的回應時間。在每個案例中，第一個圖顯示整體回應時間，第二個圖放大最快速作業的回應時間 (請注意，第一個圖的規模是第二個圖的十倍)。您可以看到回應時間的設定檔如何隨著三個測試變化。

沒有複本時，大多數作業花費 75 毫秒到 750 毫秒之間，最快的回應時間大約 25 毫秒：

![](media/guidance-elasticsearch/data-ingestion-image17.png)

1 個複本的情況下，最常見的作業回應時間範圍介於 125 毫秒到 1250 毫秒。最快速的回應大約花費 75 毫秒，不過這些快速回應的數量比 0 複本的案例還少。另外有更多回應需要比最常見案例還要長的時間，超過 1250 毫秒：

![](media/guidance-elasticsearch/data-ingestion-image18.png)

2 個複本的情況下，最常見的作業回應時間範圍介於 200 毫秒到 1500 毫秒，但是低於最低範圍的結果數目比 1 個複本的測試還少。然而，高於上限的結果數目和 1 個複本的測試類似。最可能的原因是大量插入佇列溢位的效果 (超過 50 個要求的佇列長度)。維護 2 個複本所需的工作會導致更頻繁的佇列溢位，避免擷取作業的回應時間過多；作業會快速地遭到拒絕，而不會花費較長的時間，可能會造成逾時例外狀況，或影響用戶端應用程式的回應能力 (這是大量插入佇列機制的用途)：

![](media/guidance-elasticsearch/data-ingestion-image19.png)

<span id="_The_Impact_of_1" class="anchor"><span id="_Impact_of_Increasing" class="anchor"></span></span>使用 Marvel 時，您可以看到複本數目對大量索引佇列的影響。下圖顯示來自 Marvel 的資料，描述如何在測試期間填入大量插入佇列。平均佇列長度大約為 40 個要求，但定期高載會使其溢位，並因此導致要求遭到拒絕：

![](media/guidance-elasticsearch/data-ingestion-image20.png)

***大量索引佇列大小和遭到 2 個複本拒絕的要求數目。***

您應該將它與下圖 (顯示單一複本的結果) 做比較。Elasticsearch 引擎處理要求的速度夠快，可將平均佇列長度保持在大約 25，且佇列長度絕對不會超過 50 個要求，所以不會有工作遭到拒絕。

![](media/guidance-elasticsearch/data-ingestion-image21.png)

***大量索引佇列大小和遭到 1 個複本拒絕的要求數目。***

## 用戶端將資料傳送至 Elasticsearch 的最佳作法

效能的許多層面不但與系統內部相關，也與用戶端應用程式如何使用系統相關。Elasticsearch 提供許多資料擷取程序可以利用的功能；產生文件的唯一識別碼、執行文件分析，甚至是在儲存資料時使用指令碼轉換資料，以上是一些範例。不過，這些功能都會增加 Elasticsearch 引擎的負載，在許多情況下，可在傳輸前由用戶端應用程式更有效率地執行。

> [AZURE.NOTE] 這份最佳作法清單的主要考量是擷取新的資料，而不是修改已儲存在索引中的現有資料。Elasticsearch 會執行擷取工作負載做為附加作業，而執行資料修改做為刪除/附加作業。這是因為索引中的文件是不變的，因此修改文件包含以新版本取代整份文件。您可以執行 HTTP PUT 要求來覆寫現有的文件，或者您可以使用 Elasticsearch *update* API，可抽象化查詢來擷取現有的文件、將變更合併，然後執行 PUT 來儲存新的文件。

此外，請考慮在適當時實作下列作法：

* 停用不需要分析之索引欄位的文字分析。分析包括 token 化文字，以啟用可搜尋特定字詞的查詢。不過，它可能是需要大量 CPU 的工作，因此是選擇性的。如果您使用 Elasticsearch 儲存記錄資料，可能對於 token 化詳細的記錄檔訊息非常實用，可允許複雜的搜尋。其他欄位，例如包含錯誤碼或識別碼的欄位，應該不可能 token 化 (例如，您可能會要求錯誤碼包含 "3" 之所有訊息的詳細資料的頻率有多高？) 下列程式碼會停用 *systembase* 索引的 *logs* 類型中 *name* 和 *hostip* 欄位的分析：

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			...
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			...
		}
	}
	```

* 如果不需要，請停用索引的 *\_all* 欄位。*\_all* 欄位會串連文件中其他欄位的值進行分析與索引編製。它可用於執行查詢，可比對文件中的任何欄位。如果用戶端應該要比對具名欄位，則啟用 *\_all* 只會造成 CPU 和儲存體的額外負荷。下列範例顯示如何停用 *systembase* 索引中 *logs* 類型的 *\_all* 欄位。

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			"_all": {
				"enabled" : false
			},
			...,
		...
		}
	}
	```

    請注意，您可以建立 *\_all* 的選擇性版本，它僅包含特定欄位的資訊。如需詳細資訊，請參閱[停用 \_all 欄位](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field)。

* 避免索引中的動態對應。動態對應是一項強大功能，但將新欄位新增到現有的索引需要協調變更至跨節點的索引結構，這樣可能會造成索引暫時鎖定。如果不小心使用，動態對應也可能導致欄位數目以及後續索引中繼資料量的暴增。接著，這樣會導致儲存需求和 I/O 增加，兩者都適用於擷取資料和執行查詢時。這兩種問題都會影響效能。請考慮停用動態對應，並明確地定義索引結構。如需詳細資訊，請參閱[動態欄位對應](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping)。

* 了解如何平衡工作負載以符合衝突的需求。您應該隨時考量到資料擷取可能會對其他並行作業的效能產生重大影響，例如使用者執行的查詢。資料擷取可能會受限於突然的高載，而且如果系統嘗試耗用立即抵達的所有資料，流入可能會導致查詢速率非常緩慢。Elasticsearch 嘗試藉由規範其處理經由大量插入佇列處理擷取要求的速率，來避免這種狀況發生 (如需詳細資訊，請參閱[判斷限制因素 – CPU 使用率](#determining-limiting-factors-cpu-utilization)一節)，但這項機制應該視為最後手段；如果您沒有準備應用程式程式碼來處理拒絕的要求，則您可能有遺失資料的風險。相反地，請考慮使用[佇列型負載調節](https://msdn.microsoft.com/library/dn589783.aspx)等模式來控制資料傳遞給 Elasticsearch 的速率。

* 請確定您的叢集具有足夠資源，可以處理工作負載，特別是索引利用多個複本設定時。

* 使用大量插入 API 來上傳大型批次的文件。適當調整大量要求的大小。有時候更大型的批次對效能無益，而且可能會導致 Elasticsearch 執行緒和其他資源負載過重，延遲其他並行作業。大量插入批次中的文件在執行作業時保留在記憶體中的協調節點；每個批次的實際大小比文件計數更重要。關於理想批次大小的組成並沒有固定快速的規則，不過 Elasticsearch 文件建議使用 5 MB 和 15 MB 之間做為自行調查的起點。執行效能測試來建立適合自己案例和工作負載混合的最佳批次大小。

* 請確定，大量插入要求會分散在各節點，而不是導向至單一節點。將所有要求導向到單一節點可能會導致記憶體耗盡，因為每個正在處理的大量插入要求會儲存在節點中的記憶體。它也可能會增加網路延遲，因為要求會重新導向至其他節點。

* Elasticsearch 會在寫入資料時使用包含大部分主要和複本節點的仲裁。寫入作業要到仲裁回報成功才算完成。如果大部分的節點因為網路磁碟分割 (失敗) 事件而無法使用，這種方法有助於確保不會寫入資料。使用仲裁會讓寫入作業的效能遲緩。您可以藉由在寫入資料時將 *consistency* 參數設定為 *one* 來停用仲裁式寫入。下列範例會新增文件，但要到主要分區寫入完成時才算完成。

	```http
	PUT /my_index/my_data/104?consistency=one
	{
		"name": "Bert",
		"age": 23
	}
	```

	請注意，如同非同步複寫一般，停用仲裁式寫入可能會導致主要分區和每個複本之間不一致的情況。

* 使用仲裁時，如果沒有足夠的節點可判斷是否應該因為無法連接仲裁而中止寫入作業，Elasticsearch 將會等候。這段等候期間取決於逾時查詢參數 (預設值為 1 分鐘)。您可以使用逾時查詢參樹修改這項設定。下列範例會建立新的文件，並在中止之前等候最多 5 秒鐘，待仲裁回應：

	```http
	PUT /my_index/my_data/104?timeout=5s
	{
		"name": "Sid",
		"age": 27
	}
	```

	Elasticsearch 也可讓您使用自己[外部產生](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types)的版本號碼。

* 請考慮停用索引的 *\_source* 欄位。此欄位包含一份儲存文件時所使用的原始 JSON 文件。儲存此欄位會產生額外的儲存體成本和磁碟 I/O。不過，這些成本可能相當低，取決於文件結構，而且您也應該注意，停用 *\_source* 欄位可防止用戶端有能力執行下列作業：

	* 使用更新 API 來修改文件。
	* 執行查詢時即時執行反白顯示。
	* 重新編製資料的索引。
	* 藉由檢視原始文件來偵錯查詢和彙總。

	下列範例停用 *systembase* 索引中 *logs* 類型的 *\_source* 欄位。

  ```http
  PUT /systembase
  {
		"settings" : {
			...
		},
		"logs" : {
			"_source": {
				"enabled": false
			},
			...,
		...
		}
  }
  ```

## 利用 Elasticsearch 進行資料擷取效能測試的一般指導方針

以下重點強調您在利用 Elasticsearch 執行效能測試並分析結果時應該考慮的一些項目。

* 效能測試一定會耗費時間和成本。至少需要收集統計資料，用來測量磁碟和網路上的傳輸速率、CPU 使用率、CPU 等候時間和磁碟延遲 (如果可能)。這樣可利用良好的投報率，提供測試工作的快速回應。

* 利用負載測試工具提供的指令碼處理功能收集其他地方可能無法使用的度量。比方說，Linux 會提供許多可靠的良好效能統計資料，您可以使用 *vmstat* 和 *iostat* 等公用程式加以收集。您可以使用指令碼和 JMeter 擷取此資料做為測試計劃的一部分。

* 效能工程設計主要是在根據可靠且可重複的資料來分析統計資料。請勿在未提供必要見解的高階度量停止。讓自己熟悉資料，並且讓效能工程設計成為具備快速意見反饋能力的營運開發程序。隨時查看比較趨勢和過去結果/組態的統計資料。定期執行此動作，您將會產生您了解的資料，可隨工作負載重複，您也可以藉此評估組態與部署變更的影響。

* 在測試時使用 Marvel 等工具來監視叢集和節點效能，以取得額外的見解。JMeter 可以有效地擷取未經處理的資料以進行後續分析，但使用 Marvel 可以讓您立即了解效能的代價，以及 faring 的方式和故障和速度減慢的可能原因。此外，許多負載測試工具不會提供 Elasticsearch 內部度量的可見性。使用並比較索引統計資料中可用的索引輸送量速率、合併區段計數、GC 統計資料和節流時間。定期重複這項分析。

* 比較負載測試工具統計資料和 Marvel 中的節點統計資料 (磁碟和網路流量、CPU 使用率、記憶體和執行緒集區使用方式) 以了解基礎結構和特定 Elasticsearch 統計資料所回報之數據間的關聯模式。

* 一般而言，請考慮「一個節點搭配一個分區」做為效能測試的基準並藉由新增節點來評估應用程式的成本。不過，請勿完全依賴以少數節點和分區為基礎的推斷效能。節點和分區的數目越大，叢集中的同步處理和通訊成本會產生無法預期的影響。

* 查看節點間的分區配置以比較統計資料。某些節點會有較少的複本和分區，它們會建立資源使用率的不平衡。

* 如果您執行負載測試，請增加測試工具用來提交工作給叢集的執行緒數目，直到發生錯誤。對於持續性輸送量測試，請考慮讓您的測試層級保持低於同意的「上限」。如果錯誤率超過上限，錯誤會因為恢復能力而在後端資源產生成本。在這些情況下，無可避免地會降低輸送量。

* 若要模擬您的系統回應活動意外大型高載的方式，請考慮使用錯誤率執行會產生錯誤率上限的測試。這樣可在容量與恢復能力成本等方面提供您輸送量數據。

* 使用文件計數來評估您的效能設定檔，並根據您的工作負載模式回收文件。請考慮由於新增更多文件，效能設定檔可能會變更。

* 請注意 IOPS 的 SLA 以及您使用的儲存體之傳輸速率限制。不同的儲存體類型 (SSD、旋轉式媒體) 有不同的傳輸速率。

* 請注意，CPU 效能下降的原因不只是磁碟和網路活動，也包括後端應用程式可能使用鎖定和通訊的機制，以及可能造成處理器使用率過低的分散式處理。

* 執行至少兩個小時的效能測試 (而不是幾分鐘)。編製索引可能會影響效能的方式可能無法立即看見。比方說，JVM 記憶體回收統計資料和索引合併可能會隨時間變更效能設定檔。

* 請考慮索引重新整理可能會如何對資料擷取輸送量和叢集節流造成重大影響。

## 摘要

請務必了解如何隨著資料量和要求數目的增加來調整解決方案。在 Azure 上執行的 Elasticsearch 可啟用垂直和水平調整；您可以使用更多資源在更大型的 VM 上執行，而且您可以將 Elasticsearch 叢集分散在 VM 的網路上。選項的範圍令人困擾；下列何者較符合成本效益，在大量的小型 VM 上實作叢集、在具備少量大型 VM 的叢集上，或是兩者之間的某處？ 此外，每個索引應該包含多少分區，資料擷取與查詢效能之間要如何取捨？ 跨節點分散分區的方式會對資料擷取輸送量產生顯著的影響。使用多個分區可以在分區內減少內部爭用的情形，但是您必須衡量這項優點與使用許多分區可能對叢集造成的額外負荷。若要有效地回答這些問題，您應該準備好測試您的系統，以判斷最適合的策略。

對於資料擷取工作負載，磁碟 I/O 子系統的效能是重要的因素。使用 SSD 可以藉由減少寫入作業的磁碟延遲來提升輸送量。如果您在一個節點上不需要大量的磁碟空間，請考慮搭配使用標準 VM 和臨時儲存體來取代更昂貴且支援進階儲存體的 VM。

## 附錄：大量載入資料擷取效能測試

本附錄說明針對 Elasticsearch 叢集執行的效能測試。使用在一組個別的 VM 上執行的 JMeter 來執行測試。測試環境的組態詳細資料在[在 Azure 上建立適用於 Elasticsearch 的效能測試環境][]文件中說明。若要執行您自己的測試，您可以手動建立您自己的 JMeter 測試計劃，或使用個別可用的自動化測試指令碼。如需更多資訊，請參閱[執行自動化的 Elasticsearch 效能測試][]。

資料擷取工作負載會使用大量插入 API 來執行大規模的文件上傳。這個索引的目的是要模擬接收表示系統事件之記錄檔資料的儲存機制，以進行後續的搜尋和分析。每份文件都儲存在名為 *systembase* 的單一索引中，而且具有 *logs* 類型。所有文件都有相同的固定結構描述，如下表所述：

| 欄位 | Datatype | 範例 |
|---------------|---------------------|-----------------------------------|
| @timestamp | datetime | 2013-12-11T08:01:45.000Z |
| 名稱 | 字串 | checkout.payment |
| 訊息 | 字串 | 連入要求訊息 |
| severityCode | integer | 1 |
| 嚴重性 | 字串 | info |
| 主機名稱 | 字串 | sixshot |
| hostip | 字串 (IP 位址) | 10\.0.0.4 |
| pid | int | 123 |
| tid | int | 4325 |
| appId | 字串 (uuid) | {00000000-0000-0000-000000000000} |
| 應用程式名稱 | 字串 | mytestapp |
| appVersion | 字串 | 0\.1.0.1234 |
| 類型 | int | 5 |
| 子類型 | int | 1 |
| correlationId | guid | {00000000-0000-0000-000000000000} |
| 作業系統 | 字串 | Linux |
| osVersion | 字串 | 4\.1.1 |
| 參數 | [ ] | {key:value,key:value} |

您可以使用下列要求來建立索引。*Number\_of\_replicas*、*refresh\_interval* 和 *number\_of\_shards* 設定和以下所示的許多測試中的值不同。

> [AZURE.IMPORTANT] 索引已卸除並在執行每個測試之前重新建立。

```http
PUT /systembase
{
	"settings" : {
		"number_of_replicas": 1,
		"refresh_interval": "30s",
		"number_of_shards": "5"
	},
	"logs" : {
		"properties" : {
			"@timestamp": {
			"type": "date",
			"index" : "not_analyzed"
			},
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"message": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"severityCode": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"severity": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostname": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"pid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"tid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"appId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appName": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appVersion": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"type": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"subtype": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"correlationId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"os": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"osVersion": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"parameters": {
				"type": "string",     
				"index" : "not_analyzed"
			}
		}
	}
}
```

每個大量插入批次都包含 1000 個文件。每個文件都根據 *severityCode*、*hostname*、*hostip*、*pid*、*tid*、*appName*、*appVersion*、*type*、*subtype* 和 *correlationId* 等欄位的隨機值組合產生，以及根據 *name*、*message*、*severity*、*os*、*osVersion*、*parameters*、*data1* 和 *data2* 等欄位的一組固定字詞隨機選擇文字產生。若要最大化成功輸入量，請仔細選取用來上傳資料的用戶端應用程式執行個體數目。若要啟用叢集並減少整體結果中任何暫時故障的影響，請執行測試兩個小時。此時，某些測試會上傳將近 15 億個文件。

使用在 JMeter 測試計劃中新增至執行緒群組的自訂 JUnit 要求取樣器來動態產生資料。使用 Eclipse IDE 中的 JUnit 測試案例範本建立 JUnit 程式碼。

> [AZURE.NOTE] 如需如何建立 JMeter 之 JUnit 測試的相關資訊，請參閱[部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能][]文件。

下列程式碼片段顯示測試 Elasticsearch 1.7.3 的 Java 程式碼。請注意，在此範例中的 JUnit 測試類別名為 *ElasticsearchLoadTest2*：

```java
/* Java */
package elasticsearchtest2;

	import static org.junit.Assert.*;

	import org.junit.*;

	import java.util.*;

	import java.io.*;

	import org.elasticsearch.action.bulk.*;
	import org.elasticsearch.common.transport.*;
	import org.elasticsearch.client.transport.*;
	import org.elasticsearch.common.settings.*;
	import org.elasticsearch.common.xcontent.*;

	public class ElasticsearchLoadTest2 {

		private String [] names={"checkout","order","search","payment"};
		private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
		private String [] severity={"info","warning","transaction","verbose"};
		private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

		private String hostname = "";
		private String indexstr = "";
		private String typestr = "";
		private int port = 0;
		private int itemsPerInsert = 0;
		private String clustername = "";
		private static Random rand=new Random();

		@Before
		public void setUp() throws Exception {
		}

		public ElasticsearchLoadTest2(String paras) {
		* Paras is a string containing a set of comma separated values for:
			hostname
			indexstr
			typestr
			port
			clustername
			node
			itemsPerInsert
		*/

			// Note: No checking/validation is performed

			String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
			String[] items = paras.split(delims);

			hostname = items[0];
			indexstr = items[1];
			typestr = items[2];
			port = Integer.parseInt(items[3]);
			clustername = items[4];
			itemsPerInsert = Integer.parseInt(items[5]);

			if (itemsPerInsert == 0)
				itemsPerInsert = 1000;
			}

		@After
		public void tearDown() throws Exception {
		}

		@Test
		public void BulkBigInsertTest() throws IOException {

			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();
				Random random = new Random();
				char[] exmarks = new char[12000];
				Arrays.fill(exmarks, 'x');
				String dataString = new String(exmarks);

				for(int i=1; i &lt; itemsPerInsert; i++){
					random.nextInt(10);
					int host=random.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[random.nextInt(names.length)])
						.field("message", messages[random.nextInt(messages.length)])
						.field("severityCode", random.nextInt(10))
						.field("severity", severity[random.nextInt(severity.length)])
						.field("hostname", "Hostname"+host)
						.field("hostip", "10.1.0."+host)
						.field("pid",random.nextInt(10))
						.field("tid",random.nextInt(10))
						.field("appId", apps[random.nextInt(apps.length)])
						.field("appName", "application" + host)
						.field("appVersion", random.nextInt(5))
						.field("type", random.nextInt(6))
						.field("subtype", random.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
						.field("data1",dataString)
						.field("data2",dataString)
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}

		@Test
		public void BulkDataInsertTest() throws IOException {
			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();

				for(int i=1; i&lt; itemsPerInsert; i++){
					rand.nextInt(10);
					int host=rand.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[rand.nextInt(names.length)])
						.field("message", messages[rand.nextInt(messages.length)])
						.field("severityCode", rand.nextInt(10))
						.field("severity", severity[rand.nextInt(severity.length)])
						.field("hostname", "Hostname" + host)
						.field("hostip", "10.1.0."+host)
						.field("pid",rand.nextInt(10))
						.field("tid",rand.nextInt(10))
						.field("appId", apps[rand.nextInt(apps.length)])
						.field("appName", "application"+host)
						.field("appVersion", rand.nextInt(5))
						.field("type", rand.nextInt(6))
						.field("subtype", rand.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}
	}
```

私人的 *String* 陣列 *names*、*messages*、*severity*和 *apps* 包含一組較少的值，項目會從其中隨機選取。每個文件的其餘資料項目會在執行階段產生。

接收 *String* 參數的建構函式是從 JMeter 叫用，在字串中傳遞的值指定為 JUnit 要求取樣器組態的一部分。這個 JUnit 測試的 *String* 參數必須包含下列資訊：

* **Hostname**。這是 Azure 負載平衡器的名稱或 IP 位址。負載平衡器會在叢集中嘗試將要求分散到各資料節點。如果您不使用負載平衡器，您可以在叢集中指定節點的位址，但所有要求會導向至該節點，這樣可能會導致它變成瓶頸。

* **Indexstr**。這是新增 JUnit 測試時產生資料所在之索引的名稱。如果您依上述內容建立索引，此值應為 *systembase*。

* **Typestr**。這是儲存資料所在之索引中的類型。如果您依上述內容建立索引，此值應為 *logs*。

* **Port**。這是要連接到主機上的連接埠。在大部分的情況下，應該設定為 9300 (Elasticsearch 用來接聽用戶端 API 要求的連接埠；連接埠 9200 只適用於 HTTP 要求)。

* **Clustername**。這是包含索引之 Elasticsearch 叢集的名稱。

* **ItemsPerInsert**。這是指出要在每個大量插入批次中新增之文件數目的數字參數。預設批次大小為 1000。

您指定資料做為 JUnit 要求頁面中的建構函式字串，用來在 JMeter 中設定 JUnit 取樣器。下圖顯示一個範例：

![](media/guidance-elasticsearch/data-ingestion-image22.png)

*BulkInsertTest* 和 *BigBulkInsertTest* 方法會執行產生及上傳資料的實際工作。兩種方法很類似；它們會連接到 Elasticsearch 叢集，然後再建立一批次的文件 (由 *ItemsPerInsert* 建構函式字串參數判斷)。文件利用 Elasticsearch 大量 API 新增至索引。兩種方法的差異在於每個文件中的 *data1* 和 *data2* 字串欄位會從 *BulkInsertTest* 方法中的上傳省略，但是在 *BigBulkInsertTest* 方法中會利用 12000 個字元的字串填入。請注意，您使用 JMeter 中 JUnit 要求頁面的 [測試方法] 方塊 (在上圖中反白顯示) 來選取哪一種執行方法。

> [AZURE.NOTE] 此處所提供的範例程式碼會使用 Elasticsearch 1.7.3 傳輸用戶端程式庫。如果您使用 Elasticsearch 2.0.0 或更新版本，您必須針對選取的版本使用適當的程式庫。如需 Elasticsearch 2.0.0 傳輸用戶端程式庫的詳細資訊，請參閱 Elasticsearch 網站上的[傳輸用戶端](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html)頁面。

[在 Azure 上設定 Elasticsearch 的恢復與復原]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[在 Azure 上建立適用於 Elasticsearch 的效能測試環境]: guidance-elasticsearch-creating-performance-testing-environment.md
[執行自動化的 Elasticsearch 效能測試]: guidance-elasticsearch-running-automated-performance-tests.md
[部署 JMeter JUnit 取樣器以測試 Elasticsearch 效能]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->