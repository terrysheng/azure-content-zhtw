<properties
	pageTitle="SQL Database 效能和選項：服務層 | Microsoft Azure"
	description="比較服務層的 SQL Database 效能和商務持續性功能，適當地平衡成本與功能。"
	keywords="資料庫選項, 資料庫效能"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="03/25/2016"
	ms.author="jeffreyg"/>

# SQL Database 選項和效能：了解每個服務層中可用的項目

[Azure SQL Database](sql-database-technical-overview.md) 有多個服務層可處理不同的工作負載。您可以在應用程式零停機的情況下，隨時變更服務層。您也可以使用已定義的特性和價格[建立單一資料庫](sql-database-get-started.md)。或者，您可以藉由[建立彈性資料庫集區](sql-database-elastic-pool-create-portal.md)來管理多個資料庫。在這兩種情況下，服務層包含**基本**、**標準**和**進階**。這些層中的資料庫選項類似於單一資料庫和彈性集區，但彈性集區有其他考量。本文會提供單一資料庫和彈性資料庫的服務層詳細資料。

## 服務層和資料庫選項
基本、標準和高階服務層都具備 99.99% 的執行時間 SLA，並且提供可預測的效能、彈性的商務持續性選項、安全性功能，以及小時計費。下表提供最適用於不同應用程式工作負載的服務層範例。

| 服務層 | 目標工作負載 |
|---|---|
| **基本** | 最適合於小型資料庫，通常會在指定的時間內支援單一的作用中作業。範例包括用於開發或測試，或者不常使用之小規模應用程式的資料庫。 |
| **標準** | 適用於大多數雲端應用程式，可支援多個並行查詢。範例包括工作群組或 Web 應用程式。 |
| **高級** | 針對高交易量而設計，可支援大量並行使用者，且需要最高層級商務持續性功能。範例包括支援任務關鍵性應用程式的資料庫。 |

>[AZURE.NOTE] Web 和 Business Edition 均已淘汰。如果您打算繼續使用 Web 和 Business Edition，請閱讀[終止常見問題集](https://azure.microsoft.com/pricing/details/sql-database/web-business/)。

## 單一資料庫服務層和效能等級
若為單一資料庫，每個服務層內有多個效能等級。您可以彈性選擇最符合您工作負載需求的層級。如果您需要相應增加或相應減少，可以很輕鬆地變更資料庫服務層，**您的應用程式完全不需要停機**。 如需詳細資訊，請參閱[變更資料庫服務層和效能層級](sql-database-scale-up.md)。

此處所列的效能特性會套用至使用 [SQL Database V12](sql-database-v12-whats-new.md) 建立的資料庫。如果 Azure 中的基礎硬體裝載了多個資料庫，您的資料庫仍然會有一組資源，且您資料庫的預期效能特性不會受到影響。

[AZURE.INCLUDE [SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table.md)]

若要進一步了解 DTU，請參閱本主題中的 [DTU 區段](#understanding-dtus)。

>[AZURE.NOTE] 如需此服務層資料表中所有其他資料列的詳細說明，請參閱[服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。

## eDTU 中的彈性集區服務層和效能
除了建立及調整單一資料庫之外，您也可以選擇管理[彈性集區](sql-database-elastic-pool.md)中的多個資料庫。彈性集區中的所有資料庫會共用一組通用資源。您可使用*彈性資料庫交易單位* (eDTU) 來測量效能特性。如同單一資料庫，集區有三個服務層：**基本**、**標準**和**進階**。集區的這三個服務層仍會定義整體效能限制與多項功能。

集區可讓彈性資料庫共用和取用 DTU 資源，無須指派特定效能等級給集區中的資料庫。例如，標準集區中的單一資料庫可從使用 0 個 eDTU 到您設定集區時設定的最大資料庫 eDTU。如此可讓多個具有不同工作負載的資料庫有效使用整個集區中的可用 eDTU。如需詳細資訊，請參閱[彈性集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

下表說明集區服務層的特性。

[AZURE.INCLUDE [彈性資料庫的 SQL DB 服務層資料表](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

集區中的每個資料庫也須遵循該服務層的單一資料庫特性。例如，標準集區的工作階段數上限為每個集區 2,400 - 28,800 個，但該集區中的個別資料庫的資料庫限制為 300 個工作階段 (如上一節中指定的單一基本資料庫限制)。

## 了解 DTU

[AZURE.INCLUDE [SQL DB DTU 說明](../../includes/sql-database-understanding-dtus.md)]

## 後續步驟
- 請在 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/)進一步了解這些層的定價。
- 了解[彈性資料庫集區](sql-database-elastic-pool-guidance.md)和[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。
- 了解如何[監視、管理彈性集區和調整其大小](sql-database-elastic-pool-manage-portal.md)和[監視單一資料庫的效能](sql-database-single-database-monitor.md)。
- 如果您認識了 SQL Database 各個層，可以[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)親身體驗，然後了解[如何建立您的第一個 SQL Database](sql-database-get-started.md)。

<!---HONumber=AcomDC_0330_2016-->