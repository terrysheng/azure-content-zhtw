<properties
	pageTitle="SQL Database 服務層"
	description="比較 Azure SQL Database 各個服務層的效能和商務持續性功能，根據停機時間的需求，在成本和功能之間找出適當的平衡。"
	services="sql-database"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="11/10/2015"
	ms.author="jroth"/>

# SQL Database 服務層
 
## 概觀
[Azure SQL Database](sql-database-technical-overview.md) 提供多個服務層來處理不同類型的工作負載。您可以選擇建立具有已定義特性和價格的[單一資料庫](sql-database-get-started.md)。或者，您可以藉由[建立彈性資料庫集區](sql-database-elastic-pool-portal.md)來管理多個資料庫。在這兩種情況下，服務層包含**基本**、**標準**和**進階**。然而這些服務層的特性，會因為您建立的是個別資料庫，或是在彈性資料庫集區中建立的資料庫，而有所不同。本文會提供這兩種內容之服務層的概觀。

## 服務層
基本、標準和高階服務層都具備 99.99% 的執行時間 SLA，並且提供可預測的效能、彈性的商務持續性選項、安全性功能，以及小時計費。下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
|---|---|
| **基本** | 最適用於小型資料庫，通常會在指定的時間內支援單一的作用中作業。範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** | 適用於大多數雲端應用程式，可支援多個並行查詢。範例包括工作群組或 Web 應用程式。 |
| **高級** | 針對高交易量而設計，可支援大量並行使用者，且需要最高層級商務持續性功能。範例包括支援任務關鍵性應用程式的資料庫。 |

>[AZURE.NOTE]Web 和商務版本正逐漸遭到淘汰。了解如何[升級 Web 和商務版本](sql-database-upgrade-new-service-tiers.md)。如果您打算繼續使用 Web 和商務版本，請閱讀[終止常見問題集](http://azure.microsoft.com/pricing/details/sql-database/web-business/)。

### 單一資料庫的服務層
單一資料庫的每個服務層內有多個效能層級，您可以彈性選擇最符合您工作負載需求的層級。如果您需要相應增加或相應減少，可以很輕鬆地在 Azure 入口網站中變更資料庫層，您的應用程式完全不需要停機。如需詳細資訊，請參閱[變更資料庫服務層和效能層級](sql-database-scale-up.md)。

此處所列的效能特性會套用至使用 [SQL Database V12](sql-database-v12-whats-new.md) 建立的資料庫。如果 Azure 中的基礎硬體裝載了多個 SQL Database，您的資料庫仍然一定會有一組資源，且個別資料庫的預期效能特性不會受到影響。

[AZURE.INCLUDE [SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table.md)]


若要進一步了解 DTU，請參閱本主題中的 [DTU 區段](#understanding-dtus)。

>[AZURE.NOTE]如需此服務層資料表中所有其他資料列的詳細說明，請參閱[服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。

### 彈性資料庫集區的服務層
除了建立及調整單一資料庫之外，您也可以選擇管理[彈性資料庫集區](sql-database-elastic-pool.md)中的多個資料庫。彈性資料庫集區中的所有資料庫會共用一組通用資源。您可使用*彈性資料庫交易單位* (eDTU) 來測量效能特性。如同單一資料庫，彈性資料庫集區有三個服務層：**基本**、**標準**和**進階**。彈性資料庫的這三個服務層仍會定義整體效能限制與多項功能。

彈性資料庫集區可讓這些資料庫共用和取用 DTU 資源，無須指派特定效能層級給集區中的資料庫。例如，標準集區中的單一資料庫可從使用 0 eDTU 到最大資料庫 eDTU (由服務層定義的 100 eDTU，或是您設定的自訂數字)。如此可讓多個具有不同工作負載的資料庫有效使用整個集區中的可用 eDTU。

下表說明彈性資料庫集區服務層的特性。

[AZURE.INCLUDE [彈性資料庫的 SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

集區中的每個資料庫也須遵循該服務層的單一資料庫特性。例如，標準集區的工作階段數上限為每個集區 2,400 - 28,800 個，但該集區中的個別資料庫的資料庫限制為 300 個工作階段 (如上一節中指定的單一基本資料庫限制)。

### 了解 DTU

[AZURE.INCLUDE [SQL DB DTU 說明](../../includes/sql-database-understanding-dtus.md)]

## 監視效能
若要監視 SQL Database 的效能，必須從您為資料庫所選擇之效能層級相關的資源使用率開始監視。此相關資料以下列方式公開：

1.	Microsoft Azure 管理入口網站。

2.	使用者資料庫，以及包含使用者資料庫之伺服器主資料庫中的動態管理檢視。

在 [Azure Preview 入口網站](https://portal.azure.com/)中，您可以透過選取資料庫，並按一下 [監視] 圖表，監視單一資料庫的使用率。如此會帶出您可變更的 [度量] 視窗，只要按一下 [編輯圖表] 按鈕即可。新增下列度量：

- CPU 百分比
- DTU 百分比
- 資料 IO 百分比
- 儲存空間百分比

新增這些度量之後，您可以在 [**度量**] 視窗中，在含有詳細資料的 [**監視**] 圖表中繼續檢視它們。這四個度量都會顯示與資料庫 **DTU** 相對的平均使用率百分比。

![服務層監視](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

您也可以在效能度量中設定警示。按一下 [**度量**] 視窗中的 [**新增警示**] 按鈕。遵循精靈的指示以設定警示。您可以選擇在度量超出或低於特定臨界值時發出警示。

例如，如果您預期資料庫中的工作負載會成長，可以選擇設定電子郵件警示，以便在資料庫的任何效能度量達到 80% 時收到警示。您可以使用此警示做為早期警告，協助您判斷何時需要切換至更高的效能層級。

效能度量也可協助您判斷您是否能夠降級至較低的效能層級。假設您使用標準 S2 資料庫，且所有效能度量皆顯示在指定時間內，資料庫平均使用率沒有超過 10%。則該資料庫可能在標準 S1 中會運作得不錯。不過，在您決定將資料庫移至較低效能層級前，請先注意暴增或大幅變動的工作負載。

您也可以透過下列系統檢視取得公開在入口網站中的相同度量：伺服器邏輯**主**資料庫中的 [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)，以及使用者資料庫中的 [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) (**sys.dm\_db\_resource\_stats** 會在每個基本、標準和進階使用者資料庫中建立。Web 和 Business Edition 資料庫會傳回空的結果集)。如果您需要在一段長時間內監視較不細微的資料，請使用 **sys.resource\_stats**。如果您需要在較小的時間範圍內監視較細微的資料，請使用 **sys.dm\_db\_resource\_stats**。如需詳細資訊，請參閱 [Azure SQL Database 效能指引](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats)。

若為彈性資料庫集區，您可以監視其中的個別資料庫，技巧如本節所述。但您也可以監視集區整體。如需詳細資訊，請參閱[監視和管理彈性資料庫集區](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool)。

## 後續步驟
請在 [SQL Database 價格](http://azure.microsoft.com/pricing/details/sql-database/)進一步了解這些層的價格。

如果您有興趣以群組形式管理多個資料庫，請考慮[彈性資料庫集區](sql-database-elastic-pool-guidance.md)，以及相關聯之[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

如果您認識了 SQL Database 各個層，可以[免費試用](http://azure.microsoft.com/pricing/free-trial/)親身體驗，然後了解[如何建立您的第一個 SQL Database](sql-database-get-started.md)！
 

<!---HONumber=Nov15_HO3-->