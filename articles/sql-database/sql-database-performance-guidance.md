<properties
	pageTitle="單一資料庫的 Azure SQL Database 效能指引"
	description="本主題提供指引以幫助您判斷哪一個服務層適合您的應用程式，並提供微調應用程式以充分利用 Azure SQL Database 的建議。"
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/29/2015"
	ms.author="jroth" />

# 單一資料庫的 Azure SQL Database 效能指引

## 概觀

Microsoft Azure SQL Database 有三個[服務層](sql-database-service-tiers.md)，基本、標準和高階。這些服務層全都會嚴密區隔提供給您的 Azure SQL Database 的資源，並保證可預測的效能。為您的資料庫保證的輸送量會從基本層、標準層、高階層一路提高。

>[AZURE.NOTE]2015 年 9 月將會淘汰商務和 Web 服務層。如需詳細資訊，請參閱 [Web 和 Business Edition 終止常見問題集](https://msdn.microsoft.com/library/azure/dn741330.aspx)。如需將現有的 Web 及商務資料庫升級至新服務層的詳細資訊，請參閱[將 SQL Database Web/商務資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md)。

本文提供指引以幫助您判斷哪一個服務層適合您的應用程式，並提供微調應用程式以充分利用 Azure SQL Database 的建議。

>[AZURE.NOTE]本文著重在 SQL Database 中單一資料庫的效能指引。如需彈性資料庫集區的相關效能指引，請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。不過請注意，本文中許多單一資料庫的微調建議可應用於彈性集區內的資料庫，且具有類似的效能優勢。

**作者：**Conor Cunningham、Kun Cheng、Jan Engelsberg

**技術檢閱者：** Morgan Oslake、Joanne Marone、Keith Elmore、José Batista-Neto、Rohit Nayak

## Azure SQL Database 背景

若要了解基本、標準和高階服務層如何增強 Azure SQL Database 服務，最好了解整體 Azure SQL Database。您可以選擇 Azure SQL Database 的幾個原因。其中一個原因是避開冗長的採購與安裝硬體週期。Azure SQL Database 可讓您即時建立和卸除資料庫，毋須等候訂單核准、電腦送達、電源和冷卻系統升級或安裝完成。Microsoft 會藉由根據每個資料中心的彙總需求預先佈建硬體，來處理這些挑戰並大幅減少從構想到解決方案所需的時間。這樣可以為您的企業在手動購買和部署硬體方面省下數個禮拜或數個月。

Microsoft 也包含 Azure SQL Database 中的許多自動管理功能，例如自動 HA 和內建管理。

### 自動高可用性 (HA) 
 Azure SQL Database 會為每個使用者資料庫保存至少三個複本，並且具有自動將每項變更同步認可至複本仲裁的邏輯。這可確保任何單一電腦失敗不會造成資料遺失。此外，每個複本會放在不同硬體機架，如此一來電源或網路交換器的耗損都不會影響您的資料庫。最後，如果電腦遺失，還有自動重建複本的邏輯，即使電腦狀況不良，也可讓系統自動保留所需的健全狀況內容。這些機制可避免今日安裝和設定高可用性解決方案所需的耗時程序。預先設定資料庫的 HA 解決方案可以解決使用傳統技術建置關鍵任務資料庫方案的另一個關鍵的麻煩。

### 內建管理 
 Azure SQL Database 是做為服務執行的。這表示每個資料庫都有定義的執行時間目標，避免冗長的維護停機時間。Microsoft 提供服務的單一廠商解決方案，表示如果發生任何問題，只需要呼叫一家公司。Microsoft 也持續更新服務、新增功能、容量，並設法改善您在我們每次更新中的經驗。更新會以透明的方式進行，並且不需要停機時間，這表示更新已整合到我們正常的 HA 容錯移轉機制。這可讓您在我們公告新功能可供使用的同時立即使用，而不用等待某個伺服器在未來的停機時間升級。

所有這些功能都會在所有服務層提供，從每個月幾美元的低項目價格點開始。這個價格遠低於購買和執行您自己的伺服器，這表示即使最小的專案也可以使用 Azure 而不需花一大筆錢。

## 服務層有什麼不同？
服務層共有三種：基本、標準和高階。每個服務層都有一或多個效能等級，透過可預測的方式提供執行資料庫的能力。此能力會在[資料庫交易單位 (DTU)](sql-database-technical-overview.md#understand-dtus) 中說明。

基本服務層的設計目的是每小時提供每個資料庫良好的效能可預測性。基本資料庫的 DTU 設計目的是提供充足的資源給小型資料庫，而不需多個良好的並行效能要求。

標準服務層提供更佳的效能可預測性，並提高類似工作群組和 web 應用程式等具有多個並行要求的資料庫的標準。使用標準服務層資料庫可讓您根據可預測的效能，每分鐘調整資料庫應用程式的大小。

高階服務層在效能方面最引人注目的功能是針對每個高階資料庫，每秒提供可預測的效能。使用高階服務層可讓您根據該資料庫的尖峰負載，調整資料庫應用程式的大小，並且避免效能差異導致小型查詢，在延遲敏感作業中花費比預期還長的時間。此模型可大幅簡化應用程式所需的開發與產品驗證週期，這些應用程式必須提出尖峰資源需求、效能差異或查詢延遲的相關強式陳述式。

高階服務層和標準層類似，可根據客戶所需的隔離提供不同效能等級的選項。

標準和高階的效能等級設定可讓您只支付您所需的容量，並且在工作負載變更時向上或向下調整容量。比方說，如果資料庫工作負載在返校購物季期間非常忙碌，您可能會在那段時期提高資料庫的效能等級，在尖峰時段結束後降低效能等級。這可讓您依據商務季節性最佳化您的雲端環境，藉以將支出降到最低。此模型也非常適合軟體產品發行週期。測試小組可能會在執行測試回合時配置容量並在測試完成時釋放該容量。這些容量要求完全符合您需要多少容量就支付多少的模型，而且可以避免將支出花費在很少使用的專用資源上。這樣所提供的效能經驗更接近許多 Microsoft 客戶搭配 SQL Server 使用的傳統專用硬體模型。這應該可以讓更大的應用程式集更輕鬆地在 Azure SQL Database 上執行。

如需服務層、效能等級和 DTU 的詳細資訊，請參閱 [Azure SQL Database 服務層和效能等級](sql-database-service-tiers.md)。

## 使用服務層的原因

雖然每個工作負載各有不同，服務層的目的是要在各種不同的效能等級提供高效能可預測性。它們可讓對其資料庫有大規模資源需求的客戶在更加專用的運算環境中工作。

### 基本服務層使用案例：

- **開始使用 Azure SQL Database**：開發中的應用程式通常不需要高效能等級。基本資料庫提供低價格點的資料庫開發理想環境。
- **具有單一使用者的資料庫**：將單一使用者與資料庫相關聯的應用程式通常沒有高需求並行存取和效能。具有這些需求的應用程式適合基本服務層。

### 標準服務層使用案例：

- **具有多個並行要求的資料庫**：一次提供多個使用者服務的應用程式，例如中等流量的網站或需要較大量資源的部門應用程式，都適合標準服務層。

### 高階服務層使用案例：

- **高尖峰負載**：需要大量 CPU、記憶體或 IO 才能完成其作業的應用程式。例如，如果已知資料庫作業會長時間佔用數個 CPU 核心，就比較適合使用高階資料庫。
- **許多並行要求**：某些資料庫應用程式會提供服務給許多並行要求，例如提供服務給具有高流量的網站時。基本和標準服務層都有並行要求數量的限制。需要更多連接的應用程式必須挑選適當的保留大小才能處理最大數目的所需要求。
- **低延遲**：某些應用程式必須保證在最短的時間內傳回資料庫回應。如果呼叫指定的預存程序做為更廣泛客戶作業的一部分，可能會要求呼叫在 20 毫秒 (也就是 99% 的時間) 內傳回回應。這類應用程式將受益於高階資料庫，藉此確定運算能力可供使用。

您需要的確切等級取決於每個資源維度的尖峰負載需求。有些應用程式可能會使用微量資源，但卻對另一個資源有大量需求。

如需服務層的詳細資訊，請參閱 [Azure SQL Database 服務層和效能等級](sql-database-service-tiers.md)。

## 服務層的功能和限制
每個服務層和效能層級都關聯到不同的限制和效能特性。下表描述單一資料庫的這些特性。

[AZURE.INCLUDE [SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table.md)]

下列各節提供上表中各區域的詳細資訊。

### 資料庫大小上限

**資料庫大小上限**就是以 GB 為單位的資料庫大小限制。

### DTU

**DTU** 是指資料庫交易單位。它是 SQL Database 中的測量單位，代表以實際量值為基礎的資料庫相對能力：資料庫交易。這是由一組線上交易處理 (OLTP) 要求的典型作業所組成，其測量方式是在完全載入的情況下每秒有多少次交易完成。若要取得有關 DTU 的詳細資訊，請參閱 [了解 DTU](sql-database-technical-overview.md#understand-dtus)。如需有關如何測量 DTU 的詳細資訊，請參閱[基準測試概觀](sql-database-benchmark-overview.md)。

### 時間點還原

**時間點還原**是將資料庫還原到之前時間點的能力。您的服務層會決定您可以還原到多少天前的時間。如需詳細資訊，請參閱[從使用者錯誤復原 Azure SQL Database](sql-database-user-error-recovery.md)。

### 災害復原

**災害復原**是指在主要的 SQL Database 發生中斷時進行復原的能力。

*異地還原*會提供給所有服務層使用，且不須額外付費。在發生中斷時，您可以使用最新的異地備援備份將資料庫還原到任何 Azure 區域。

生效的標準異地複寫會提供類似災害復原的功能，但復原點目標 (RPO) 會低很多。例如，若使用異地還原，RPO 會少於一小時 (換句話說，所備份的內容最舊不會超過一小時)。但對於異地複寫來說，RPO 少於 5 秒。

如需詳細資訊，請參閱[業務續航力概觀](sql-database-business-continuity.md)。

### XTP 記憶體內部儲存體上限
**XTP 記憶體內部儲存體上限**是指可供高階資料庫的[記憶體內部 OLTP 預覽](sql-database-in-memory.md)使用的儲存體數量上限。您可以使用 Azure 入口網站或 **sys.dm\_db\_resource\_stats** 檢視來監視記憶體內部儲存體的使用情形。如需有關監視的詳細資訊，請參閱[監視 XTP 記憶體內部儲存體](sql-database-in-memory-oltp-monitoring.md)。

>[AZURE.NOTE]記憶體內部 OLTP 預覽目前只支援用於單一資料庫，並不支援用於彈性資料庫集區中的資料庫。

### 並行要求數上限

**並行要求數上限**是指資料庫中同時執行的並行使用者/應用程式要求數目上限。若要查看並行要求數目，請在 SQL Database 上執行下列 Transact-SQL 查詢：

	SELECT COUNT(*) AS [Concurrent_Requests] 
	FROM sys.dm_exec_requests R

如果您要分析內部部署 SQL Server 資料庫的工作負載，請修改此查詢來篩選您要分析的特定資料庫。比方說，如果您擁有名為 MyDatabase 的內部部署資料庫，則下列 Transact-SQL 查詢會傳回該資料庫中並行要求的計數。

	SELECT COUNT(*) AS [Concurrent_Requests] 
	FROM sys.dm_exec_requests R
	INNER JOIN sys.databases D ON D.database_id = R.database_id
	AND D.name = 'MyDatabase'

請注意，這只是單一時間點的快照。若要進一步了解您的工作負載，您必須收集一段時間內的許多範例，才能了解您的並行要求需求。

### 並行登入數上限

**並行登入數上限**代表同一時間嘗試登入資料庫的使用者或應用程式數目限制。請注意，即使這些用戶端使用相同的連接字串，服務仍會驗證每一個登入。因此，如果有十位使用者同時以相同使用者名稱和密碼連接到資料庫，將會有十個並行登入。這項限制只適用於登入和驗證期間。所以如果相同的十位使用者是依序連接到資料庫，並行登入數目絕對不會高於一。

>[AZURE.NOTE]這項限制目前不適用於彈性資料庫集區中的資料庫。

沒有任何查詢或 DMV 可以向您顯示並行登入計數或歷程記錄。您可以分析您的使用者和應用程式模式以了解登入頻率。您也可以在測試環境中執行真實世界的負載，藉此確定您不會達到本主題中所說的這項限制或其他限制。

### 工作階段數上限

**工作階段數上限**是指並行開啟的資料庫連線數上限。使用者登入時就會建立工作階段，並持續保持作用直到使用者登出或工作階段逾時。若要查看目前的作用中工作階段數目，請在 SQL Database 上執行下列 Transact-SQL 查詢：

	SELECT COUNT(*) AS [Sessions]
	FROM sys.dm_exec_connections

如果您要分析內部部署 SQL Server 的工作負載，請修改查詢以專注於特定資料庫。如果您要將該資料庫移至 Azure SQL Database，這可幫助您判斷它可能的工作階段需求。

	SELECT COUNT(*)  AS [Sessions]
	FROM sys.dm_exec_connections C
	INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
	INNER JOIN sys.databases D ON (D.database_id = S.database_id)
	WHERE D.name = 'MyDatabase'

同樣地，這些查詢傳回的是某一時間點的計數，因此收集一段時間內的多個範例可讓您充分了解您的工作階段使用量。

若要進行 SQL Database 分析，您也可以查詢 **sys.resource\_stats**，使用 **active\_session\_count** 資料行取得工作階段的歷史統計資料。在下面的監視一節中有提供更多關於使用這個檢視的資訊。

## 監視資源使用量
有兩種檢視可供您監視 SQL Database 相對於其服務層的資源使用量：

- [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)

>[AZURE.NOTE]您也可以使用 Azure 管理入口網站來檢視資源使用量。如需範例，請參閱[服務層 - 監視效能](sql-database-service-tiers.md#monitoring-performance)。

### 使用 sys.dm\_db\_resource\_stats
[sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) 檢視存在於每一個 SQL Database，並且會提供相對於服務層的最新資源使用量資料。每隔 15 秒鐘就會記錄一次 CPU、資料 IO、記錄檔寫入和記憶體的平均百分比，並且會維持一個小時。

因為此檢視會提供更細微的資源使用量資訊，您應該先使用 **sys.dm\_db\_resource\_stats ** 來進行任何現狀分析或疑難排解。例如，下列查詢會顯示目前的資料庫在上一小時的平均和最大資源使用量：

	SELECT  
	    AVG(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
	    MAX(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
	    AVG(avg_data_io_percent) AS 'Average Data IO In Percent', 
	    MAX(avg_data_io_percent) AS 'Maximum Data IO In Percent', 
	    AVG(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
	    MAX(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
	    AVG(avg_memory_usage_percent) AS 'Average Memory Usage In Percent', 
	    MAX(avg_memory_usage_percent) AS 'Maximum Memory Usage In Percent' 
	FROM sys.dm_db_resource_stats;  

如需其他查詢的資訊，請參閱 [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) 中的範例。

### 使用 sys.resource\_stats

**主要**資料庫中的 [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) 檢視會提供在 SQL Database 的特定服務層和效能層級內監視其效能使用情形的其他資訊。這項資料每隔五分鐘就會收集一次，並且會維持大約 14 天。這個檢視更適合用於進行 SQL Database 資源使用量的長期歷史分析。

下圖顯示在一週中 P2 效能等級之高階資料庫每小時的 CPU 資源使用率。這張特別的圖從星期一開始，顯示 5 個工作天，然後是較少發生在應用程式的週末。

![SQL DB 資源使用率](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

從資料中，這個資料庫目前相對於 P2 效能等級的尖峰 CPU 負載剛好超過 50% 的 CPU 使用率 (星期二中午)。如果 CPU 是應用程式的資源設定檔中的主要因素，您可能會決定 P2 是正確的效能等級，以確保永遠符合工作負載。如果應用程式預期會隨著時間成長，應該預留一些額外的資源緩衝，讓應用程式永遠不會達到上限。這有助於避免因資料庫沒有足夠能力來有效處理要求所造成的客戶可見錯誤，尤其是在延遲敏感的環境中 (例如支援可根據資料庫呼叫結果繪製網頁之應用程式的資料庫)。

值得注意的是其他應用程式類型可能會以不同方式解譯相同的圖形。例如，如果應用程式嘗試每天處理薪資資料而且具有同一張圖表，這種「批次工作」模型可能會在 P1 效能等級中正常執行。相較於 P2 效能等級的 200 個 DTU，P1 效能等級有 100 個 DTU。這表示 P1 效能等級提供的效能是 P2 效能等級的一半。因此 P2 中 50% 的 CPU 使用率等於 P1 中的 100% CPU 使用率。只要應用程式沒有逾時，就算重要任務花了 2 個小時或 2.5 個小時才能完成也沒關係，只要它在今天內完成即可。這個類別中的應用程式或許只需要使用 P1 效能等級。一天中有好幾個時段的資源使用量較低，您可以善用這個事實，表示任何「巨量尖峰」都可能溢出到當天稍後的一個低谷。只要工作可以每天及時完成，P1 效能等級可能就很適合這類應用程式 (並節省經費)。

Azure SQL Database 會在每個伺服器之**主要**資料庫的 **sys.resource\_stats** 檢視中公開每個作用中資料庫的耗用資源資訊。資料表中的資料會以 5 分鐘的間隔彙總。利用基本、標準和高階服務層，資料可能要花 5 分鐘以上的時間才會出現在資料表中，表示此資料比較適合進行歷程記錄分析而不是近乎即時的分析。查詢 **sys.resource\_stats** 檢視會顯示資料庫的近期歷程記錄以驗證挑選的保留是否在必要時提供所需的效能。

>[AZURE.NOTE]您必須連接到邏輯 SQL Database 伺服器的**主要**資料庫才能在下列範例中查詢 **sys.resource\_stats**。

下列範例會示範如何公開此檢視中的資料：

	SELECT TOP 10 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![系統資源統計資料](./media/sql-database-performance-guidance/sys_resource_stats.png)

下列範例示範您可以藉由使用 **sys.resource\_stats** 目錄檢視來了解 SQL Database 資源使用量的不同方式。

>[AZURE.NOTE]目前的 V12 資料庫中已變更 **sys.resource\_stats** 的某些資料行，因此下列範例中的範例查詢可能會產生錯誤。本主題日後更新時將會提供可解決此問題的新版查詢。

1. 例如，若要查看 "userdb1" 資料庫在過去一週的資源使用量，您可以執行下列查詢。
	
		SELECT * 
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND 
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;
	
2. 為了評估您的工作負載與效能等級的符合程度，您必須鑽研資源度量的每個不同層面：CPU、讀取、寫入、背景工作角色數目和工作階段數目。以下是使用 sys.resource\_stats 修訂過的查詢，可報告這些資源度量的平均值和最大值。
	
		SELECT 
		    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
		    max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
		    avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent',
		    max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent',
		    avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
		    max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
		    avg(active_session_count) AS 'Average # of Sessions',
		    max(active_session_count) AS 'Maximum # of Sessions',
		    avg(active_worker_count) AS 'Average # of Workers',
		    max(active_worker_count) AS 'Maximum # of Workers'
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
3. 利用上述各項資源度量的平均值和最大值等資訊，您可以評估您的工作負載與您所選之效能等級的符合程度。在大部分情況下，來字 sys.resource\_stats 的平均值可提供您對目標大小所使用的理想基準。它應該是您主要的量尺。例如，如果您使用標準服務層搭配 S2 效能等級，CPU、讀取和寫入的平均使用率百分比會低於 40%，背景工作角色平均數目會低於 50，工作階段平均數目會低於 200，您的工作負載可能符合 S1 效能等級。要看到您的資料庫是否符合背景工作角色和工作階段限制範圍內非常容易。若要查看資料庫在 CPU、讀取和寫入方面是否符合較低的效能等級，您要將較低效能等級的 DTU 數目除以目前效能等級的 DTU 數目，並將結果乘以 100：
	
	**S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
	
	此結果是以百分比表示之兩個效能等級的相對效能差異。如果您的使用率未超過這個百分比，您的工作負載可能符合較低的效能等級。不過，您也需要查看所有範圍的資源使用量值，並以百分比判斷資料庫工作負載符合較低效能等級的頻率。下列查詢會根據上面計算的 40% 臨界值，輸出每個資源維度的相符百分比。
	
		SELECT 
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	根據您的資料庫服務等級目標 (SLO)，您可以決定您的工作負載是否符合較低的效能等級。如果您的資料庫工作負載 SLO 是 99.9%，且上述查詢針對三個資源維度傳回的值都大於 99.9，您的工作負載非常可能符合較低的效能等級。
	
	查看相符百分比也可讓您深入了解是否必須移到下一個較高的效能等級來滿足您的 SLO。例如，"userdb1" 會顯示過去一週的下列使用率。
	
	| 平均 CPU 百分比 | 最大 CPU 百分比 |
	|---|---|
	| 24\.5 | 100\.00 |
	
	平均 CPU 大約是效能等級限制的四分之一，完全符合資料庫的效能等級。不過，此最大值會顯示資料庫達到效能等級的限制。您需要移至下一個較高的效能等級嗎？ 您必須再次查看工作負載達到 100% 的次數並將其與您的資料庫工作負載 SLO 做比較。
	
		SELECT 
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	如果上述查詢針對三個資源維度傳回的值小於 99.9，您應該考慮移到下一個較高的效能等級或使用應用程式微調技術減少 Azure SQL Database 的負載。
	
4. 上述練習也應該將您預計的未來工作負載增加納入考量。

## 微調您的應用程式

在傳統的內部部署 SQL Server 中，執行初始容量規劃的程序通常會和在生產環境中執行應用程式的程序分開。換句話說，採購硬體以及和執行 SQL Server 相關聯的授權會先完成，而效能微調則會在稍後完成。使用 Azure SQL Database 時，一般建議 (也因為您是每個月付費，在盡可能有利的情況下) 交錯執行和微調應用程式的程序。容量隨選的付費模型可讓您調整應用程式，以使用目前需要的最少資源，而不是根據對應用程式的未來成長計劃猜測來大量過度佈建 (這通常是不正確的，因為他們必須預測遙遠的未來)。請注意，某些客戶可能會決定不微調應用程式，而改為選擇過度佈建硬體資源。當您不想在應用程式忙碌時期變更關鍵應用程式時，這種方法可能會有幫助。微調應用程式使用 Azure SQL Database 中的服務層時可以最小化資源需求並降低每月帳單。

### 應用程式特性

雖然服務層的設計可以改善應用程式的效能穩定性和可預測性，仍然有一些最佳做法可以微調您的應用程式，以更充分利用此功能。雖然許多應用程式可以只藉由切換至較高的效能等級或服務層就能看到顯著的效能提升，但是並非所有應用程式都可以不需要額外的微調就能獲益。具有下列特性的應用程式也應該考慮在使用 Azure SQL Database 時進行額外的應用程式微調，以進一步改善效能。

- **因為「多對話」行為而使效能變慢的應用程式**：這包括產生過多對網路延遲敏感的資料存取作業的應用程式。這類應用程式可能需要修改以減少 Azure SQL Database 資料存取作業的數目。比方說，應用程式可能會藉由使用某些技術來改善，例如一併批次處理特定查詢或將查詢移至預存程序。如需詳細資訊，請參閱後續的＜批次處理查詢＞一節。
- **整部單一電腦無法支援具有大量工作負載的資料庫**：超過最高高階效能等級資源的資料庫並不適用。這些資料庫可能會受益於向外擴充工作負載。如需詳細資訊，請參閱後續的＜跨資料庫分區化＞和＜功能資料分割＞等節。
- **包含非最佳查詢的應用程式**：未經適當微調查詢的應用程式，尤其是在資料存取層中者，可能無法如預期般受益於選擇較高的效能等級。這包括缺少 WHERE 子句、具有遺漏的索引或具有過時統計資料的查詢。這些應用程式會受益於標準查詢效能微調技術。如需詳細資訊，請參閱後續的＜遺漏索引＞和＜查詢微調/提示＞等節。
- **具有非最佳資料存取設計的應用程式**：具有內在資料存取並行問題的應用程式，例如死結，可能無法受益於選擇較高的效能等級。應用程式開發人員應該考慮藉由使用 Azure 快取服務或其他快取技術來快取用戶端的資料，以減少對 Azure SQL Database 的往返作業。請參閱後續的應用程式層快取一節。

## 微調技術
本節將說明一些技術，您可以用來微調 Azure SQL Database 以獲取應用程式的最佳效能，並且盡可能在最小的效能等級中執行。許多技術符合傳統的 SQL Server 微調最佳做法，但是有些技術則是專屬於 Azure SQL Database。在某些情況下，傳統的 SQL Server 技術都可以藉由檢查資料庫已耗用的資源來尋找要進一步微調的區域，以擴充成也適用於 Azure SQL Database。

### 查詢效能深入解析與索引顧問
SQL Database 在 Azure 入口網站中提供兩種工具來分析及修正資料庫的效能問題：

- [查詢效能深入解析](sql-database-query-performance.md)
- [索引顧問](sql-database-index-advisor.md)

請參閱前面的連結，以取得關於這些工具及其使用方式的詳細資訊。接下來關於遺漏索引和查詢微調的兩節將會提供其他方式來手動找出並更正類似的效能問題。我們建議您先嘗試入口網站中的工具，以便更有效率地診斷並更正問題。若是特殊情況，請使用手動微調方法。

### 遺漏的索引
OLTP 資料庫效能中常見的問題與實體資料庫設計相關。資料庫結構描述的設計和轉移通常不會經過大規模測試 (無論是在負載或資料數量)。不幸的是，查詢計劃的效能可能只有小規模可以接受，但是在面臨實際執行等級的資料數量時可能會大幅降低。此問題最常見的來源是因為缺少適當的索引來滿足篩選或查詢中的其他限制。這種狀況通常會在索引搜尋可以滿足時以資料表掃描的形式呈現。

下列範例會建立一個案例，選取的查詢計劃會在搜尋可以滿足時包含掃描。

	DROP TABLE dbo.missingindex;
	CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO dbo.missingindex(col2) VALUES (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION;
	GO
	SELECT m1.col1 
	FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1) 
	WHERE m1.col2 = 4;

![具有遺漏索引的查詢計劃](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database 包含協助提示資料庫管理員如何尋找和修正常見遺漏索引狀況的功能。Azure SQL Database 內建的動態管理檢視 (DMV) 會查看查詢編譯，其中的索引會大幅減少執行查詢的估計成本。執行查詢期間，它會追蹤每個查詢計劃的執行頻率，以及執行查詢計劃和其中存在該索引之假設查詢計劃之間的落差。這樣可讓資料庫管理員快速推測哪些實體資料庫設計變更可能會改善指定資料庫和其實際工作負載的整體工作負載成本。

>[AZURE.NOTE]在使用 DMV 來尋找遺漏索引之前，請先檢閱[查詢效能深入解析與索引顧問](query-performance-insight-and-index-advisor.md)一節。

下列查詢可用來評估潛在的遺漏索引。

	SELECT CONVERT (varchar, getdate(), 126) AS runtime, 
	    mig.index_group_handle, mid.index_handle, 
	    CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact * 
	            (migs.user_seeks + migs.user_scans)) AS improvement_measure, 
	    'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' + 
	              CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + ' 
	              (' + ISNULL (mid.equality_columns,'') 
	              + CASE WHEN mid.equality_columns IS NOT NULL 
	                          AND mid.inequality_columns IS NOT NULL 
	                     THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
	              + ')' 
	              + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement, 
	    migs.*, 
	    mid.database_id, 
	    mid.[object_id]
	FROM sys.dm_db_missing_index_groups AS mig
	INNER JOIN sys.dm_db_missing_index_group_stats AS migs 
	    ON migs.group_handle = mig.index_group_handle
	INNER JOIN sys.dm_db_missing_index_details AS mid 
	    ON mig.index_handle = mid.index_handle
	ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

在此範例中，建議使用下列索引。

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

一旦建立之後，同一個 SELECT 陳述式現在會挑選不同的計劃，使用搜尋而不是掃描，更有效率地執行，如同下列查詢計劃所示。

![具有已更正索引的查詢計劃](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

重要的觀念是共用商品系統的 IO 容量通常比專用的伺服器電腦受到更多限制。因此，重點在於要最小化不必要的 IO，才能在 Azure SQL Database 的服務層中，在每個效能等級的 DTU 內充分利用此系統。適當的實體資料庫設計選項可以大幅改善個別查詢的延遲、您在每個縮放單位中可處理的並行要求輸送量，並最小化滿足查詢所需的成本。如需有關遺漏索引 DMV 的詳細資訊，請參閱 [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx)。

### 查詢微調/提示
Azure SQL Database 內的查詢最佳化工具非常類似傳統的 SQL Server 查詢最佳化工具。許多微調查詢和了解查詢最佳化工具之推論模型限制的最佳做法也適用於 Azure SQL Database。Azure SQL Database 中的微調查詢可以因為減少彙整資源需求而享有額外的獲益，並且能夠以低於未經微調查詢的成本執行應用程式，因為它可以在較低的效能等級中執行。

SQL Server 中也適用於 Azure SQL Database 的一個常見範例是關於如何在編譯期間「探查」參數以嘗試建立更佳的計劃。參數探查是一個程序，其中查詢最佳化工具希望產生較佳的查詢計劃，在編譯查詢時會考量目前的參數值。雖然這個策略產生的查詢計劃通常在速度上明顯優於不知道參數值就編譯的計劃，但是目前的 SQL Server/Azure SQL Database 行為並不完美 – 有些情況是未探查參數，而有些情況是已探查參數，但產生的計劃對工作負載中的整組參數值而言並不是最理想的。Microsoft 包括查詢提示 (指示詞)，可讓您更刻意地指定意圖及覆寫參數探查的預設行為。使用提示通常可以修正指定客戶工作負載的預設 SQL Server/Azure SQL Database 行為不完美的情況。

下列範例會示範查詢處理器如何產生對效能和資源需求都不盡理想的計劃，以及使用查詢提示如何降低 Azure SQL Database 上的查詢執行階段和資源需求。

以下是設定的範例。

	DROP TABLE psptest1;
	CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
	
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO psptest1(col2) values (1);
	    INSERT INTO psptest1(col2) values (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION
	CREATE INDEX i1 on psptest1(col2);
	GO
	
	CREATE PROCEDURE psp1 (@param1 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 
	    WHERE col2 = @param1
	    ORDER BY col2;
	END
	GO
	
	CREATE PROCEDURE psp2 (@param2 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
	    ORDER BY col2
	    OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
	END
	GO
	
	CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
	GO

設定程式碼會建立一個包含扭曲資料散發的資料表。最佳的查詢計劃會根據選取的參數而有差異。不幸的是，計劃快取行為並不一定會根據最常見的參數值重新編譯查詢，這就表示即使不同的計劃會是較平均的計劃選項，仍有可能快取到不盡理想的計劃並用於許多值。然後它會建立兩個預存程序，除了其中一個包含特殊的查詢提示以外完全相同 (以下說明理由)。

**範例 (第 1 部分)：**

	-- Prime Procedure Cache with scan plan
	EXEC psp1 @param1=1;
	TRUNCATE TABLE t1;
	
	-- Iterate multiple times to show the performance difference
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp1 @param1=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

**範例 (第 2 部分 - 請等候 10 分鐘之後再嘗試此部分，使其在產生的遙測資料中截然不同)：**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;
	
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

此範例中的每個部分都嘗試執行參數化的 insert 陳述式 1000 次 (以在測試資料集上產生足夠的負載)。執行預存程序時，查詢處理器會檢查第一次編譯 (也稱為參數「探查」) 期間傳遞至程序的參數值。即使參數值不同，還是會快取產生的計劃並用於稍後的叫用。因此，最佳的計劃可能不會用於所有情況。有時候客戶必須引導最佳化工具挑選計劃，相較於第一次編譯查詢時的特定案例，此計劃較適合平均案例。在此範例中，初始計劃會產生「掃描」計劃，此計劃會讀取所有資料列來尋找符合參數的每個值。

![查詢微調](./media/sql-database-performance-guidance/query_tuning_1.png)

因為我們以值 1 執行此程序，所以產生的計劃會是 1 的最佳選擇，但是對資料表中的其他值而言未必是最佳的。如果您隨機挑選每個計劃，所產生的行為可能不是所需的行為，因為計劃將執行的更慢而且需要更多資源來執行。

以 "SET STATISTICS IO ON" 執行測試會顯示在此範例的幕後完成邏輯掃描工作 – 您可以看到計劃完成了 1148 次讀取 (如果平均案例是只要傳回一個資料列，這是沒有效率的)。

![查詢微調](./media/sql-database-performance-guidance/query_tuning_2.png)

此範例的第二個部分使用查詢提示告訴最佳化工具，在編譯處理程序期間使用特定值。在此情況下，它會強制查詢處理器忽略傳遞做為參數的值，並改為假設 "UNKNOWN”，這表示資料表中具有平均頻率的值 (忽略扭曲)。產生的計劃是以搜尋為基礎的計劃，平均而言將會比範例第 1 部份的計劃更快速並使用較少資源。

![查詢微調](./media/sql-database-performance-guidance/query_tuning_3.png)

藉由檢查 **sys.resource\_stats** 資料表可以看到這個影響 (請注意：從您執行測試的時間到資料填入資料表的時間將會發生延遲)。在此範例中，第 1 部分會在 22:25:00 時間範圍期間執行，而第 2 部分會在 22:35:00 執行。請注意，較早的時間範圍在該時間範圍內使用的資源比較晚的時間範圍還多 (因為計劃效率改善)。

	SELECT TOP 1000 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![查詢微調](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE]雖然這裡使用的範例已刻意簡化，次佳參數的影響還是很明顯，尤其是對於較大型的資料庫。在極端的情況下，快速和緩慢案例之間的差異可介於數秒到數小時之間。

您可以檢查 **sys.resource\_stats** 來判斷指定測試使用的資源比另一個測試多或少。比較資料時，在 **sys.resource\_stats** 檢視中以足夠的時間隔開測試，讓它們不會分組在同一個 5 分鐘的時間範圍內。此外，請注意練習的目標是要最小化使用的總資源，而不是最小化尖峰資源本身。一般而言，最佳化一段延遲的程式碼也會減少資源耗用量。請確定在任何應用程式中考量的變更實際上都是必要的，而且不會讓使用應用程式的任何人在使用查詢提示時對客戶體驗造成負面影響。

如果工作負載包含一組重複的查詢，擷取並驗證這些計劃選項的最適化通常是合理的，因為它可能會讓主控資料庫所需的資源大小單位降到最低。一旦驗證過後，偶爾重新檢查這些計劃可確保它們不會降級。如需有關查詢提示的詳細資訊，請參閱[查詢提示 (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)。

### 跨資料庫分區化
因為 Azure SQL Database 會在商用硬體上執行，所以單一資料庫的容量限制通常會比傳統的內部部署 SQL Server 安裝更低。因此，有許多客戶在他們不符合 Azure SQL Database 中的單一資料庫限制時，使用分區化技術在多個資料庫散佈資料庫作業。今天在 Azure SQL Database 上使用分區化技術的大部分客戶都會在跨多個資料庫的單一維度上分割其資料。此方法包括了解 OLTP 應用程式通常會執行的交易只會在結構描述內套用到一個資料列或一小組的資料列。

>[AZURE.NOTE]SQL Database 現在提供可協助分區化的程式庫。如需詳細資訊，請參閱[彈性資料庫用戶端程式庫概觀](sql-database-elastic-database-client-library.md)。

例如，如果資料庫包含客戶、訂單及訂單詳細資料 (如 SQL Server 隨附的傳統範例 Northwind 資料庫中所示)，則這項資料可以透過利用相關訂單和訂單詳細資料等資訊來分組客戶，並保證它會保留在單一資料庫內，藉以分割至多個資料庫。應用程式會跨資料庫分割不同的客戶，跨多個資料庫有效分配負載。這樣不但能讓客戶避免資料庫大小上限，也能讓 Azure SQL Database 處理明顯大於不同效能等級限制的工作負載，前提是每個個別資料庫符合其 DTU。

雖然資料庫分區化不會減少方案的彙整資源容量，但這項技術可以非常有效地支援分配到多個資料庫的非常大型方案，並允許每個資料庫在不同的效能等級執行以支援具有高資源需求且非常大型的「有效」資料庫。

### 功能資料分割
SQL Server 使用者通常會在單一資料庫內結合許多功能。例如，如果應用程式包含管理商店庫存的邏輯，該資料庫可能包含的邏輯會與庫存、追蹤訂單、預存程序和管理月底報告的索引/具體化檢視以及其他功能相關聯。這項技術提供的好處是可以輕鬆管理備份等作業的資料庫，但也需要您設定硬體大小來跨所有應用程式的功能處理尖峰負載。

在 Azure SQL Database 中使用的向外延展架構中，將應用程式的不同功能分割至不同資料庫很有幫助。這樣可讓它們全都獨立延展。隨著應用程式變得更忙碌 (以及在資料庫上取得更多負載)，這樣可讓系統管理員為應用程式內的每個功能獨立選擇效能等級。在此限制下，此架構可藉由跨多部電腦分配負載，讓應用程式變得比單一商用電腦可處理的應用程式還大。

### 批次處理查詢
對於利用高度頻繁的特定查詢方式存取資料的應用程式而言，大量的回應時間都花費在應用程式層和和 Azure SQL Database 層之間的網路通訊上。即使應用程式和 Azure SQL Database 都位於相同的資料中心，兩者之間的網路延遲還是可能因為大量資料存取作業而放大。若要減少這些資料存取作業的網路往返，應用程式開發人員應該考慮選擇批次處理特定查詢或將其編譯成預存程序。批次處理特定查詢可以將多個查詢當做一個大型批次，在單一往返中傳送到 Azure SQL Database。在預存程序中編譯特定查詢可以達到和批次處理相同的結果。使用預存程序也能讓您獲益，因為在 Azure SQL Database 中快取查詢計劃的機會增加，利於相同預存程序的後續執行。

某些應用程式是寫入密集型的。有時候，可以藉由考慮如何一併批次處理寫入來減少資料庫上的 IO 總負載。這通常與在預存程序及特定查詢內使用明確的交易而不是自動認可的交易一樣容易。如需可供使用之不同技術的評估，請參閱 [Azure 中 SQL Database 應用程式的批次處理技術](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)。實驗您自己的工作負載來尋找正確的批次處理模型，請仔細了解指定的模型在交易一致性保證上可能稍有不同。要尋找可最小化資源使用的適當工作負載，就必須找出一致性與效能權衡取捨的正確組合。

### 應用程式層快取
有些資料庫應用程式包含大量讀取工作負載。可以利用快取層來減少資料庫上的負載，並且可能降低支援使用 Azure SQL Database 之資料庫所需的效能等級。[Azure Redis 快取](https://azure.microsoft.com/services/cache)可讓具有大量讀取工作負載的客戶一次讀取資料 (或可能每個應用程式層電腦讀曲一次，取決於設定方式) 並將該資料儲存在 Azure SQL Database 外部。如此就能減少資料庫負載 (CPU 和讀取 IO)，但會對交易一致性造成影響，因為相較於資料庫中的資料，從快取讀取的資料可能是過期的。雖然許多應用程式中的不一致性尚可接受，但是並非所有工作負載都是這樣。請充分了解任何應用程式的需求，然後再採用應用程式層快取策略。

## 結論

Azure SQL Database 中的服務層可讓您提升您在雲端建置的應用程式類型。與努力的應用程式微調結合，您可以讓您的應用程式功能強大且可預測效能。本文概述最佳化資料庫的資源耗用量的建議技術，可完全符合其中一個效能等級。微調是雲端模型中持續的活動，而服務層與其效能等級可讓系統管理員將 Microsoft Azure 平台上的效能最大化同時將成本降到最低。

<!---HONumber=Nov15_HO1-->