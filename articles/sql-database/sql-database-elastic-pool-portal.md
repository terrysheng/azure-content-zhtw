<properties
	pageTitle="建立可調整的彈性資料庫集區 | Microsoft Azure"
	description="如何將可調整的彈性資料庫集區新增至 SQL Database 組態，以便更輕鬆地進行多個資料庫的系統管理與資源共用。"
	keywords="可調整資料庫,資料庫組態"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/12/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# 在 Azure 入口網站中建立 SQL 資料庫的可調整彈性資料庫集區

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

本文將說明如何使用 Azure 入口網站建立可調整的[彈性資料庫集區](sql-database-elastic-pool.md)。具有彈性資料庫集區的 SQL Database 組態可簡化多個資料庫的系統管理及資源共用。

> [AZURE.NOTE] 彈性資料庫集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。如果您有 SQL Database V11 伺服器，您可以在單一步驟中[使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server-powershell.md)。


## 先決條件

* SQL Database V12 伺服器上的資料庫。如果沒有，請參閱[建立您的第一個 Azure SQL Database](sql-database-get-started.md)，在 5 分鐘內建立一個資料庫。 
* 或者，如果您已有 SQL Database V11 伺服器，您可以[在入口網站中升級至 V12](sql-database-v12-plan-prepare-upgrade.md)，然後再依照這些指示建立集區。


## 步驟 1：建立新的集區

將新的集區加入至伺服器，以建立彈性資料庫集區。您可以將多個集區新增至伺服器，但無法將來自不同伺服器的資料庫新增到相同的集區。


1. 在 [Azure 入口網站](http://portal.azure.com/)中，按一下 [SQL Server]。
2. 選取含有您要加入集區之資料庫的伺服器。
3. 按一下 [新增集區] (或如果您看到訊息說明有建議的集區，請按一下該集區，即可輕鬆地檢閱並建立最適合您的伺服器資料庫的集區)。有關建議的其他詳細資料如下所示。


    ![將集區加入伺服器](./media/sql-database-elastic-pool-portal/new-pool.png)


4. 在 [彈性資料庫集區] 刀鋒視窗中，您可以保留預設名稱，或輸入新集區的名稱。

    ![設定彈性集區](./media/sql-database-elastic-pool-portal/configure-elastic-pool.png)


### 建議的彈性資料庫集區

瀏覽至 SQL Database 伺服器，您可能會看到一則訊息，表示伺服器有建議的彈性資料庫集區 (僅限 V12)。

### 我為什麼會收到建議？

SQL Database 服務會評估使用量歷程記錄，並在比使用單一資料庫更符合成本效益時，建議您使用一或多個彈性資料庫集區。

![建議的集區](./media/sql-database-elastic-pool-portal/recommended-pool.png)

每個建議是以最適合集區的伺服器資料庫的唯一子集設定。

每個集區建議都包含下列資訊：

- 集區 (基本、標準或進階) 的價格層。
- 適當的集區 eDTU 數量。
- 彈性資料庫最小/最大 eDTU 設定。
- 建議的資料庫清單。

建議彈性資料庫集區時，服務會計算過去 30 天的遙測。對於被視為彈性資料庫集區候選項目的資料庫，它必須存在至少 7 天。已在彈性資料庫集區中的資料庫不會被視為彈性資料庫集區建議候選項目。

服務會評估將每個服務層中的單一資料庫移至同一層的彈性資料庫集區的資源需求和成本效益。例如，會評估伺服器上的所有 Standard 資料庫是否適合 Standard 彈性集區。這表示服務不會進行跨層建議，例如將 Standard 資料庫移到 Premium 集區。


### 建立建議的集區

1. 按一下訊息來查看建議的集區清單。
1. 按一下集區以查看詳細的建議。
2. 檢閱建議中的所有設定並全盤接受，或編輯建議以符合您特定的商務需求。


## 步驟 2：選擇定價層

集區的定價層決定了集區中彈性資料庫可用的功能，還有最大數目的 eDTU (eDTU MAX)，以及每個資料庫可用的儲存體 (GB)。如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools)。

>[AZURE.NOTE] 目前在預覽中，您無法在建立彈性資料庫集區的價格層之後進行變更。若要變更現有彈性集區的價格層，在所需的價格層中建立新的彈性集區，並且將彈性資料庫移轉至這個新的集區。

   ![選擇定價層](./media/sql-database-elastic-pool-portal/pricing-tier.png)

### 設定集區

設定定價層之後，請按一下 [設定集區]，即可新增資料庫、設定集區 eDTU 和儲存體 (集區 GB)，以及設定集區中彈性資料庫的最大和最小 eDTU。

## 步驟 3：將資料庫加入集區

您可以隨時在集區中新增或移除資料庫。

1. 在建立集區時，按一下 [設定集區] 刀鋒視窗上的 [新增資料庫]。
2. 選取您要新增至集區的資料庫：

    ![新增資料庫](./media/sql-database-elastic-pool-portal/add-databases.png)

    當您選取要加入至集區的資料庫時，必須符合下列條件：

    - 集區必須要有資料庫的放置空間 (已經包含的資料庫數目不得為最大數目)。更明確地說，集區必須要有足夠的可用 eDTU，才能涵蓋每個資料庫的 eDTU 保證。例如，如果群組的 eDTU 保證是 400，而每個資料庫的 eDTU 保證是 10，則集區中允許的資料庫數目上限為 40 (400 個 eDTU 除以每個資料庫保證的 10 個 eDTU = 最多 40 個資料庫)。
    - 集區中必須要有資料庫目前所使用的功能。

### 動態建議

將資料庫新增至集區之後，將會根據您所選資料庫的過去使用量，動態產生建議。這些建議將會顯示在 eDTU 和 GB 使用量圖表，以及 [設定集區] 刀鋒視窗頂端的建議橫幅中。這些建議旨在協助您為特定的資料庫建立最最佳化集區。


 ![動態建議](./media/sql-database-elastic-pool-portal/dynamic-recommendation.png)


## 步驟 4：設定集區的效能特性

您可以藉由設定集區和集區中彈性資料庫的效能參數，來設定集區的效能。請記住，**彈性資料庫設定**會套用到集區中的所有資料庫。

 ![設定彈性集區](./media/sql-database-elastic-pool-portal/configure-performance.png)

您可以設定三個參數以定義集區效能：集區的 eDTU 保證、集區中彈性資料庫的最小 eDTU (eDTU MIN) 和最大 eDTU (eDTU MAX)。下表說明每個參數，並提供一些如何設定這些參數的指引。關於特定可用值的設定，請參閱[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。

| 效能參數 | 說明 |
| :--- | :--- |
| **POOL eDTU** - 集區的 eDTU 保證 | 集區的 eDTU 保證是保證的可用 eDTU 數目，且會由集區中的所有資料庫共用這些 eDTU。<br>佈建特定大小的群組 eDTU 保證時，應該考慮群組的 eDTU 使用量記錄。或者，也可以根據每個資料庫所需的 eDTU 保證，以及在同時作用中資料庫的使用量，來設定這個大小。集區的 eDTU 保證也與集區的可用儲存體量相互關聯，您配置給集區的每個 eDTU，都會成為固定數量的資料庫儲存體。<br> **我應該將集區的 eDTU 保證設定為？** <br>您至少應該將集區的 eDTU 保證設為 ([資料庫數目] x [每個資料庫的 DTU 平均使用量])。 |
| **eDTU MIN** - 每個資料庫的 eDTU 保證 | 每個資料庫的 eDTU 保證是集區中單一資料庫能夠保證的 eDTU 數。<br> **我應該將每個資料庫的 eDTU 保證設定為？** <br>一般來說，每個資料庫的 eDTU 保證 (eDTU MIN) 會設為 0 和 ([每個資料庫的平均使用量]) 之間的任意數量。每個資料庫的 eDTU 保證是全域設定，會設定集區中所有資料庫的 eDTU 保證。 |
| **eDTU MAX** - 每個資料庫的 eDTU 上限 | 每個資料庫的 eDTU 上限是集區中單一資料庫可使用的最大 eDTU 數。將每個資料庫的 eDTU 上限設定得夠高，才能有效處理資料庫可能會遇到的高負載量或暴增量。您可以將此上限設為系統限制的最大值，這會取決於集區的價格層 (Premium 為 1000 個 eDTU)。此上限的特定大小應該配合群組中資料庫的尖峰使用量模式。某種程度的群組過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。<br> **我應該將每個資料庫的 eDTU 上限設定為？** <br> 請將每個資料庫的 eDTU MAX 或 eDTU 上限設為 ([資料庫尖峰使用量])。例如，假設每個資料庫的尖峰使用量是 50 個 DTU，且群組中的 100 個資料庫只有 20% 會同時暴增到尖峰。如果每個資料庫的 eDTU 上限設為 50 個 eDTU，則以 5 倍的量過量使用集區，並將群組的 eDTU 保證 (集區 eDTU) 設為 1,000 個 eDTU 都是合理的。此外值得注意的是，eDTU 上限不等於資料庫的資源保證，這只是情況許可時能達到的 eDTU 上限而已。 |


## 在集區中新增和移除資料庫

建立集區之後，您可以在彈性資料庫頁面中加入或移除資料庫，在集區中加入或移除現有的資料庫page (瀏覽到您的集區，再按一下 [基本] 中的 [彈性資料庫] 連結)。

- 按一下 [新增資料庫] 以開啟您可以新增至集區的資料庫清單。

    -或-

- 選取您要從集區中移除的資料庫，然後按一下 [移除資料庫]。

![建議的集區](./media/sql-database-elastic-pool-portal/add-remove-databases.png)

## 在集區中建立新的資料庫

瀏覽到所需的集區，按一下 [建立資料庫]，在集區中建立新資料庫。

已將 SQL 資料庫設定為正確的伺服器和集區，因此您可以輸入名稱並選取資料庫選項，再按一下 [確定] 以建立新的資料庫：


   ![建立彈性資料庫](./media/sql-database-elastic-pool-portal/create-database.png)



## 監視和管理彈性資料庫集區

建立彈性資料庫集區後，您可以瀏覽至現有集區的清單，然後選取所需的集區，藉此監視和管理入口網站中的集區。

建立集區後，您可以：

- 選取 [設定集區]，依資料庫設定變更集區 eDTU 和 eDTU。
- 選取 [建立工作]，然後建立彈性工作以管理集區中的資料庫。彈性工作可讓您對集區中任意數目的資料庫執行 Transact-SQL 指令碼。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。
- 選取 [管理工作] 以管理現有的彈性工作。
- 按一下 [建立資料庫]，在集區中建立新的資料庫。
- 按一下集區設定連結，以調整每個集區的集區 eDTU 和 GB
- 按一下資料庫連結，以在集區中新增或移除資料庫。
- 按一下資料庫設定連結，以調整集區中彈性資料庫的最小和最大 eDTU。 

![監視彈性集區](./media/sql-database-elastic-pool-portal/pool-tasks.png)


當您選取現有的集區時，會看見集區的資源使用率。按一下 [資源使用率] 圖表，開啟 [度量] 刀鋒視窗，便可在此處自訂圖表和設定警示。

![監視彈性集區][4] ![資源使用率][6]

按一下 [編輯圖表] 加入參數，以便輕鬆地檢視集區的遙測資料。


![編輯圖表][7]


## 後續步驟
建立彈性資料庫集區後，可以藉由建立彈性工作來管理集區中的資料庫。彈性工作有助於對集區中任意數目的資料庫執行 Transact-SQL 指令碼。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。



## 其他資源

- [SQL Database 彈性集區](sql-database-elastic-pool.md)
- [使用 PowerShell 建立 SQL Database 彈性集區](sql-database-elastic-pool-powershell.md)
- [使用 C# 建立和管理 SQL Database](sql-database-client-library.md)
- [彈性資料庫參考](sql-database-elastic-pool-reference.md)


<!--Image references-->
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=AcomDC_0218_2016-->