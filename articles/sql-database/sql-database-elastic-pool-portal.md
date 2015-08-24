<properties 
	pageTitle="建立和管理 SQL Database 彈性資料庫集區" 
	description="建立單一資源集區供 Azure SQL Database 的群組共用。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/12/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# 建立和管理 SQL Database 彈性資料庫集區

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

本文將說明如何使用[預覽入口網站](https://portal.azure.com)建立彈性資料庫集區。

對於大量的資料庫而言，彈性資料庫集區可簡化建立、維護和管理流程的效能與成本。
 

> [AZURE.NOTE]彈性資料庫集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。

 


## 必要條件

若要建立彈性資料庫集區，您需要下列項目：

- Azure 訂用帳戶！ 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database V12 伺服器。如果沒有 V12 伺服器，請遵循以下文章中的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。



## 建立彈性資料庫集區

將新的集區加入至伺服器，以建立彈性資料庫集區。您可以將多個集區加入至伺服器，但可以與每個集區相關聯的伺服器只有一 (1) 部。此外，可以將伺服器上的所有或部分資料庫加入至集區。


1.	選取 SQL Database V12 伺服器 (其中含有您想加入至集區的資料庫)。
2.	選取 [SQL Server] 刀鋒視窗頂端的 [新增集區]，以建立集區。

   ![建立彈性集區][1]

## 設定彈性資料庫集區

設定集區的方式為，設定定價層、加入資料庫，以及設定集區的效能特性。

*當您選取 [新增集區] 命令時，必須選取 [預覽版條款]，並完成 [預覽版條款] 刀鋒視窗中的項目，藉此接受預覽版的條款。各個訂用帳戶只需要接受條款一次。*

   ![設定彈性集區][2]


## 設定價格層

彈性資料庫集區的價格層會判斷集區中彈性資料庫可用的功能，還有最大數目的 eDTU (eDTU MAX)，以及每個資料庫可用的儲存體 (GB)。如需詳細資料，請參閱[彈性集區和彈性資料庫的 eDTU 和儲存體限制](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)。

>[AZURE.NOTE]目前在預覽中，您無法在建立彈性資料庫集區的價格層之後進行變更。若要變更現有彈性集區的價格層，在所需的價格層中建立新的彈性集區，並且將彈性資料庫移轉至這個新的集區。

   ![定價層][9]


## 彈性資料庫集區價格層建議

SQL Database 服務會評估使用量歷程記錄，並在比使用單一資料庫更符合成本效益時，建議您使用一或多個彈性資料庫集區。根據您的資料庫工作負載建議具有星號 (![星號][10]) 的價格層。

如果建議一個以上的價格層，則表示應該建立多個彈性資料庫集區。每個建議是以最適合集區的伺服器資料庫的唯一子集設定。

除了只建議彈性資料庫集區價格層之外，每個集區建議包含下列項目：

- 集區 (基本、標準或進階) 的價格層。
- 適當的集區 eDTU 數量。
- 資料庫最小/最大 eDTU 設定。  
- 建議的資料庫清單。

建議彈性資料庫集區時，服務會計算過去 30 天的遙測。對於被視為彈性資料庫集區候選項目的資料庫，它必須存在至少 7 天。已在彈性資料庫集區中的資料庫不會被視為彈性資料庫集區建議候選項目。

服務會評估將每個服務層中的單一資料庫移至同一層的彈性資料庫集區的資源需求和成本效益。例如，會評估伺服器上的所有 Standard 資料庫是否適合 Standard 彈性集區。這表示服務不會進行跨層建議，例如將 Standard 資料庫移到 Premium 集區。

>[AZURE.NOTE]Web 和 Business 資料庫會根據其使用量歷程記錄和資料庫大小，對應到其中一個新的基本、標準或進階層。對應至新層對適當的集區建議 Web 和 Business 資料庫。

<br><br>

### 新增資料庫

不論何時，您都可以選取想要包含在集區中的特定資料庫。當您建立新的集區時，Azure 會建議可在集區中獲益的資料庫，並標示這些資料庫以供納入。您可以加入伺服器上所有可用的資料庫，或視需要選取或清除初始清單中的資料庫。

   ![新增資料庫][5]

當您選取要加入至集區的資料庫時，必須符合下列條件：

- 集區必須要有資料庫的放置空間 (已經包含的資料庫數目不得為最大數目)。更明確地說，集區必須要有足夠的可用 eDTU，才能涵蓋每個資料庫的 eDTU 保證。例如，如果群組的 eDTU 保證是 400，而每個資料庫的 eDTU 保證是 10，則集區中允許的資料庫數目上限為 40 (400 個 eDTU 除以每個資料庫保證的 10 個 eDTU = 最多 40 個資料庫)。
- 集區中必須要有資料庫目前所使用的功能。 


### 設定效能

您可以藉由設定集區和集區中彈性資料庫的效能參數，來設定集區的效能。請記住，彈性資料庫設定會套用到集區中的所有資料庫。

   ![設定彈性集區][3]

您可以設定三種參數以定義集區效能：集區的 eDTU 保證、集區中彈性資料庫的最小 eDTU (eDTU MIN) 和最大 eDTU (eDTU MAX)。下表說明每個參數，並提供一些如何設定這些參數的指引。如需特定可用值設定，請參閱[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。

| 效能參數 | 說明 |
| :--- | :--- |
| **POOL eDTU** - 集區的 eDTU 保證 | 集區的 eDTU 保證是保證的可用 eDTU 數目，且會由集區中的所有資料庫共用這些 eDTU。<br>佈建特定大小的群組 eDTU 保證時，應該考慮群組的 eDTU 使用量記錄。或者，也可以根據每個資料庫所需的 eDTU 保證，以及在同時作用中資料庫的使用量，來設定這個大小。集區的 eDTU 保證也與集區的可用儲存體量相互關聯，您配置給集區的每個 eDTU，都會成為固定數量的資料庫儲存體。<br> **我應該將集區的 eDTU 保證設定為？** <br>您至少應該將集區的 eDTU 保證設為 ([資料庫數目] x [每個資料庫的 DTU 平均使用量]) |
| **eDTU MIN** - 每個資料庫的 eDTU 保證 | 每個資料庫的 eDTU 保證是集區中單一資料庫能夠保證的 eDTU 數。例如，在 Standard 集區中您可以將這項保證設定為 0、10、20、50 或 100 個 eDTU，或者您可以選擇不提供保證給群組中的資料庫 (eDTU MIN = 0)。<br> **我應該將每個資料庫的 eDTU 保證設定為？** <br>通常，每個資料庫的 eDTU 保證 (DTU MIN) 設為 0 和 ([每個資料庫的平均使用量]) 之間的任何數量。每個資料庫的 eDTU 保證是全域設定，會設定集區中所有資料庫的 eDTU 保證。 |
| **eDTU MAX** - 每個資料庫的 eDTU 上限 | 每個資料庫的 eDTU 上限是集區中單一資料庫可使用的最大 eDTU 數。將每個資料庫的 eDTU 上限設定得夠高，才能有效處理資料庫可能會遇到的高負載量或暴增量。您可以將此上限設為系統限制的最大值，這會取決於集區的價格層 (Premium 為 1000 個 eDTU)。此上限的特定大小應該配合群組中資料庫的尖峰使用量模式。某種程度的群組過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。<br> **我應該將每個資料庫的 eDTU 上限設定為？** <br> 請將每個資料庫的 eDTU MAX 或 eDTU 上限設為 ([資料庫尖峰使用量])。例如，假設每個資料庫的尖峰使用量是 50 個 DTU，且群組中的 100 個資料庫只有 20% 會同時暴增到尖峰。如果每個資料庫的 eDTU 上限設為 50 個 eDTU，則以 5 倍的量過量使用集區，並將群組的 eDTU 保證 (集區 eDTU) 設為 1,000 個 eDTU 都是合理的。此外值得注意的是，eDTU 上限不等於資料庫的資源保證，這只是情況許可時能達到的 eDTU 上限而已。 |


## 將資料庫加入至彈性資料庫集區

建立集區後，您可以在集區內或在集區外新增或移除資料庫，方法是在 [**新增資料庫**] 頁面上選取或清除資料庫。


## 監視和管理彈性資料庫集區

建立彈性資料庫集區後，您可以瀏覽至現有集區的清單，然後選取所需的集區，藉此監視和管理入口網站中的集區。

建立集區後，您可以：

- 選取 [**設定集區**] 以變更集區 eDTU 和每個資料庫的 eDTU 設定。
- 選取 [建立工作]，然後建立彈性工作以管理集區中的資料庫。彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。
- 選取 [管理工作] 以管理現有的彈性工作。



![監視彈性集區][8]




![監視彈性集區][4]

當您選取現有的集區時，會看見集區的資源使用率。按一下 [資源使用率] 圖表，開啟 [度量] 刀鋒視窗，便可在此處自訂圖表和設定警示。


![資源使用率][6]

按一下 [編輯圖表] 加入參數，以便輕鬆地檢視集區的遙測資料。


![編輯圖表][7]




## 後續步驟
建立彈性資料庫集區後，可以藉由建立彈性工作來管理集區中的資料庫。彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。



## 相關參考

- [SQL Database 彈性集區](sql-database-elastic-pool.md)
- [使用 PowerShell 建立 SQL Database 彈性集區](sql-database-elastic-pool-powershell.md)
- [使用 C# 建立和管理 SQL Database](sql-database-client-library.md)
- [彈性資料庫參考](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png
[9]: ./media/sql-database-elastic-pool-portal/pricing-tier.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=August15_HO7-->