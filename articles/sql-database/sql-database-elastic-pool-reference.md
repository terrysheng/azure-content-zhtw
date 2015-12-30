<properties 
	pageTitle="Azure SQL 彈性資料庫集區參考" 
	description="這份參考提供了彈性資料庫集區文章和可程式性資訊的連結與詳細資料。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# SQL Database 彈性資料庫集區參考

對於擁有數十、數百或甚至數千個資料庫的 SaaS 開發人員而言，彈性資料庫集區可以簡化在整個資料庫群組中，進行建立、維護及管理流程的效能和成本。

這份參考提供了彈性資料庫集區文章和可程式性資訊的連結與詳細資料。

## 概觀

彈性資料庫集區是彈性資料庫輸送量單位 (eDTU) 的集合，也是由多個資料庫共用的儲存體 (GB)。您可以隨時在集區中加入和移除彈性資料庫。集區中的彈性資料庫只會利用集區中它們需要的資源，而集區也只會針對需要這些資源的作用中資料庫釋出可用資源。如需判斷您的資料庫是否因彈性資料庫集區而受益的協助，請參閱[彈性資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。



## 建立和管理彈性資料庫集區的必要條件


- 只有 Azure SQL Database V12 伺服器才可以使用彈性資料庫集區。若要升級至 V12 並將資料庫直接移轉到集區，請參閱[升級至 Azure SQL Database V12](sql-database-upgrade-server-powershell.md)。
- 只有針對 Azure 資源管理員使用 [Azure 入口網站](https://portal.azure.com)、PowerShell 和 .NET 用戶端程式庫 (REST API 的包裝函式) 才支援建立和管理彈性資料庫集區；[傳統入口網站](https://manage.windowsazure.com/)和服務管理命令不受支援。
- 此外，還支援使用 Transact-SQL 建立新的彈性資料庫，並將現有的資料庫移入和移出彈性資料庫集區。



## 目前預覽版本注意事項


- 每個集區有最大數目的資料庫和集區 eDTU：

    | 服務層 | 每個集區的最大資料庫* | 每個集區的最大 eDTU* |
    | :-- | :-- | :-- |
    | 基本 | 200 | 1200 | 
    | 標準 | 200 | 1200 |
    | 高級 | 50 | 1500 |

    ****每個集區的資料庫數和集區 eDTU 數的目前限制預期會增加。***




## 文章清單

以下文章可協助您開始使用彈性資料庫和彈性工作：

| 文章 | 說明 |
| :-- | :-- |
| [SQL Database 彈性資料庫集區](sql-database-elastic-pool.md) | 彈性資料庫集區的概觀 |
| [價格和效能考量](sql-database-elastic-pool-guidance.md) | 如果評估使用彈性資料庫集區是否符合成本效益 |
| [使用 Azure 入口網站建立和管理 SQL Database 彈性資料庫集區](sql-database-elastic-pool-portal.md) | 如何使用 Azure 入口網站建立和管理彈性資料庫集區 |
| [使用 PowerShell 建立和管理 SQL Database 彈性資料庫集區](sql-database-elastic-pool-powershell.md) | 如何使用 PowerShell Cmdlet 建立和管理彈性資料庫集區 |
| [使用 Azure SQL Database Library for .NET 建立和管理 SQL Database](sql-database-elastic-pool-powershell.md) | 如何使用 C# 建立和管理彈性資料庫集區 |
| [彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md) | 彈性工作服務的概觀，此服務可在集區中的所有彈性資料庫內執行 T-SQL 指令碼 |
| [安裝彈性資料庫工作元件](sql-database-elastic-jobs-service-installation.md) | 如何安裝彈性資料庫工作服務 |
| [建立彈性工作服務所需的使用者](sql-database-elastic-jobs-add-logins-to-dbs.md) | 若要執行彈性資料庫工作指令碼，必須將具有適當權限的使用者加入至集區中的每個資料庫。 |
| [如何解除安裝彈性資料庫工作元件](sql-database-elastic-jobs-uninstall.md) | 嘗試安裝彈性資料庫工作服務失敗時加以復原 |



## 命名空間和端點詳細資料
彈性資料庫集區是 Microsoft Azure SQL Database 中 "ElasticPool" 類型的 Azure 資源管理員資源。

- **namespace**：Microsoft.Sql/ElasticPool
- 用於 REST API 呼叫的 **management-endpoint** (資源管理員)：https://management.azure.com



## 彈性資料庫集區屬性

| 屬性 | 說明 |
| :-- | :-- |
| creationDate | 建立集區的日期。 |
| databaseDtuMax | 集區中單一資料庫可使用的最大 eDTU 數。資料庫最大 eDTU 不等於資源保證。最大 eDTU 會套用至集區中的所有資料庫。 |
| databaseDtuMin | 集區中單一資料庫能夠保證的最小 eDTU 數。資料庫最小 eDTU 可設定為 0。最小 eDTU 會套用至集區中的所有資料庫。請注意，集區中的資料庫數目和資料庫最小 eDTU 的乘積不能超過集區本身的 eDTU。 |
| Dtu | 集區中所有資料庫共用的 eDTU 數。 |
| edition | 集區的服務層。集區中的每個資料庫均有此版本。 |
| elasticPoolId | 集區執行個體的 GUID。 |
| elasticPoolName | 集區的名稱。此名稱是其父伺服器的唯一相對名稱。 |
| location | 建立集區的資料中心位置。 |
| state | 如果訂閱的帳單付款有拖欠，則狀態為「已停用」，反之為「就緒」。 |
| storageMB | 集區的儲存體限制 (MB)。集區中的任何單一資料庫可以使用的上限為標準版的儲存體限制 (250 GB)，但集區中的所有資料庫使用的儲存體總計不能超過此集區限制。 |


## 彈性集區和彈性資料庫的 eDTU 和儲存體限制

集區的儲存體限制取決於集區的 eDTU 量。

| 屬性 | 基本 | 標準 | 高級 |
| :-- | :-- | :-- | :-- |
| dtu | **100**、200、400、800、1200 | **100**、200、400、800、1200 | **125**、250、500、1000、1500 |
| databaseDtuMax | **5** | 10、20、50、**100** | **125**、250、500、1000 |
| databaseDtuMin | **0**、5 | **0**、10、20、50、100 | **0**、125、250、500、1000 |
| storageMB* | **10000 MB**、20000 MB、40000 MB、80000 MB、120000 MB | **100 GB**、200 GB、400 GB、800 GB、1200 GB | **62.5 GB**、125 GB、250 GB、500 GB、750 GB |
| 每個 DTU 的儲存體 | 100 MB | 1 GB | .5 GB |
| 每個集區的最大資料庫 | 200 | 200 | 50 |

預設值為**粗體**。

**API 中單位為 MB，而非 GB。





## 背景工作和工作階段限制

支援彈性集區中所有資料庫的並行背景工作數上限和並行工作階段數上限，是取決於集區的 eDTU 設定：

| eDTU | 並行背景工作數上限 | 並行工作階段數上限 |
| :-- | :-- | :-- |
| 100 (基本/標準)、125 (進階) | 200 | 2,400 |
| 200 (基本/標準)、250 (進階) | 400 | 4,800 |
| 400 (基本/標準)、500 (進階) | 800 | 9,600 |
| 800 (基本/標準)、1,000 (進階) | 1,600 | 19,200 |
| 1,200 基本/標準)、1,500 (進階) | 2,400 | 28,800 |


## Azure 資源管理員限制

Azure SQL Database V12 伺服器位於資源群組中。

- 每個資源群組最多可以有 800 部伺服器。
- 每部伺服器最多可以有 800 個彈性集區。


## 彈性集區的作業延遲

- 每個資料庫的保證 eDTU 數 (databaseDtuMin) 或每個資料庫的最大 eDTU 數 (databaseDtuMax) 變更作業通常在 5 分鐘內即可完成。 
- 集區之 eDTU/儲存體限制 (storageMB) 的變更作業，則需視集區中所有資料庫使用的總空間量而定。變更作業平均每 100 GB 會在 90 分鐘以內完成。舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則集區 eDTU/儲存體限制變更作業的預期延遲時間會少於 3 小時。 



## PowerShell、REST API 和 .NET 用戶端程式庫

有幾個 PowerShell Cmdlet 和 REST API 命令可用來建立及管理彈性集區。如需詳細資訊和程式碼範例，請參閱[使用 PowerShell 建立和管理 SQL Database 彈性資料庫集區](sql-database-elastic-pool-powershell.md)和[使用 C# 建立和管理 SQL Database](sql-database-client-library.md)。



| [PowerShell Cmdlet](https://msdn.microsoft.com/library/mt163521.aspx) | [REST API 命令](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [建立彈性資料庫集區](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [設定彈性資料庫集區的效能設定](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [刪除彈性資料庫集區](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [取得彈性資料庫集區及其屬性值](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [取得彈性資料庫集區作業的狀態](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRmSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [取得彈性資料庫集區中的資料庫](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolDatabaseActivity]() | [取得將資料庫移入和移出集區的狀態](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

您可以使用 TRANSACT-SQL 執行下列彈性資料庫管理工作：

| 工作 | 詳細資料 |
| :-- | :-- |
| 建立新的彈性資料庫 (直接在集區中) | [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) |
| 將現有的資料庫移入和移出集區 | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx) |
| 取得集區的資源使用量統計資料 | [sys.elastic\_pool\_resource\_stats (Azure SQL Database)](https://msdn.microsoft.com/library/mt280062.aspx) |


## 計費和定價資訊

彈性資料庫集區會依據下列特性計費：

- 彈性集區在建立時就會開始計費，即使集區中沒有資料庫也一樣。 
- 彈性集區會以每小時計費。這和單一資料庫效能等級的計量頻率相同。 
- 如果彈性集區調整為新的 eDTU 量，則在調整作業完成之前，不會根據新的 eDTU 量來計算集區費用。這會遵循與變更獨立資料庫的效能等級相同的模式。 


- 彈性集區的價格是以集區的 eDTU 數為計算基礎。彈性集區的價格與其中所使用的彈性資料庫無關。
- 價格的計算方式為 (集區的 eDTU 數) x (每 eDTU 的單價)。

在同一個服務層中，彈性集區的 eDTU 單價大於獨立資料庫的 DTU 單價。如需詳細資訊，請參閱 [SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)。

## 彈性資料庫集區錯誤

| 錯誤號碼 | 錯誤嚴重性 | 錯誤格式 | 錯誤說明 | 錯誤原因 | 錯誤修正動作 |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | 彈性集區已達到其儲存體限制。彈性集區的儲存體使用量不能超過 (%d) MB。 | 彈性集區空間限制 (MB)。 | 當彈性集區達到儲存體限制時，嘗試將資料寫入資料庫。 | 請考慮盡可能增加彈性集區的 DTU 數以提高其儲存體限制、減少彈性集區中個別資料庫所使用的儲存體量，或是從彈性集區移除資料庫。 |
| 10929 | EX_USER | %s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。請參閱 [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) 以取得協助。或者，請稍後再試一次。 | 每個資料庫的最小 DTU、每個資料庫的最大 DTU | 彈性集區中的所有資料庫並行背景工作 (要求) 總數試圖超過集區限制。 | 請考慮盡可能增加彈性集區的 DTU 數以提高其背景工作數的限制，或是從彈性集區移除資料庫。 |
| 40844 | EX_USER | 伺服器 '%ls' 上的資料庫 '%ls' 是彈性集區中的 '%ls' 版資料庫，且無法有連續複製關聯性。 | 資料庫名稱、資料庫版本、伺服器名稱 | 針對彈性集區中的非高階資料庫發出了 StartDatabaseCopy 命令。 | 敬請期待 |
| 40857 | EX_USER | 找不到伺服器 '%ls' 的彈性集區，彈性集區名稱: '%ls'。 | 伺服器名稱、彈性集區名稱 | 指定的彈性集區不存在於指定的伺服器中。 | 請提供有效的彈性集區名稱。 |
| 40858 | EX_USER | 彈性集區 '%ls' 已存在於伺服器 '%ls' 中 | 彈性集區名稱、伺服器名稱 | 指定的彈性集區已存在於指定的邏輯伺服器中。 | 請提供新的彈性集區名稱。 |
| 40859 | EX_USER | 彈性集區不支援服務層 '%ls'。 | 彈性集區服務層 | 指定的服務層不支援彈性集區佈建。 | 請提供正確的版本，或者將服務層保留空白，以使用預設的服務層。 |
| 40860 | EX_USER | 彈性集區 '%ls' 和服務目標 '%ls' 的組合無效。 | 彈性集區名稱、服務層級目標名稱 | 如果服務目標指定為 'ElasticPool'，才能同時指定彈性集區和服務目標。 | 請指定正確的彈性集區和服務目標組合。 |
| 40861 | EX_USER | 資料庫版本 '%.*ls' 不能不同於彈性集區服務層版本 '%.*ls'。| 資料庫版本、彈性集區服務層 | 資料庫版本不同於彈性集區服務層。| 請不要指定不同於彈性集區服務層的資料庫版本。請注意，不需要指定資料庫版本。|
| 40862 | EX\_USER | 如果已指定彈性集區服務目標，則必須指定彈性集區名稱。| 無 | 彈性集區服務目標未唯一識別彈性集區。| 如果使用彈性集區服務目標，請指定彈性集區名稱。|
| 40864 | EX\_USER | 彈性集區的 DTU 對於服務層 '%.*ls' 必須至少是 (%d) DTU。| 彈性集區的 DTU；彈性集區服務層。| 嘗試設定低於最小限制的彈性集區的 DTU。| 請以至少最小限制重試設定彈性集區的 DTU。|
| 40865 | EX\_USER | 彈性集區的 DTU 對於服務層 '%.*ls' 不能超過 (%d) DTU。| 彈性集區的 DTU；彈性集區服務層。| 嘗試設定高於最大限制的彈性集區的 DTU。| 請重設彈性集區的 DTU 不大於最大限制。|
| 40867 | EX\_USER | 每個資料庫最大 DTU 對於服務層 '%.*ls' 必須至少是 (%d)。| 每個資料庫最大 DTU；彈性集區服務層 | 嘗試設定低於支援限制的每個資料庫最大 DTU。| 請考慮使用支援想要的設定的彈性集區服務層。|
| 40868 | EX\_USER | 每個資料庫最大 DTU 對於服務層 '%.*ls' 不能超過 (%d)。| 每個資料庫最大 DTU；彈性集區服務層。| 嘗試設定高於支援限制的每個資料庫最大 DTU。| 請考慮使用支援想要的設定的彈性集區服務層。|
| 40870 | EX\_USER | 每個資料庫最小 DTU 對於服務層 '%.*ls' 不能超過 (%d)。| 每個資料庫最小 DTU；彈性集區服務層。| 嘗試設定高於支援限制的每個資料庫最小 DTU。| 請考慮使用支援想要的設定的彈性集區服務層。|
| 40873 | EX\_USER | 資料庫數目 (%d) 和每個資料庫最小 DTU (%d) 不能超過彈性集區 (%d) 的 DTU。| 彈性集區中的資料庫數；每個資料庫最小 DTU；彈性集區的 DTU。| 嘗試在彈性集區中指定超過彈性集區 DTU 的資料庫最小 DTU。| 請考慮增加彈性集區的 DTU，或減少每個資料庫最小 DTU，或減少彈性集區中的資料庫數目。|
| 40877 | EX\_USER | 無法刪除彈性集區，除非它未包含任何資料庫。| 無 | 彈性集區包含一或多個資料庫，因此無法刪除。| 請從彈性集區中移除資料庫以便刪除彈性集區。|
| 40881 | EX\_USER | 彈性集區 '%.*ls' 已達到其資料庫計數限制。對於具有 (%d) DTU 的彈性集區，彈性集區的資料庫計數限制不能超過 (%d)。| 彈性集區的名稱；彈性集區的資料庫計數限制；資源集區的 eDTU。| 當到達彈性集區的資料庫計數限制時，嘗試建立或新增資料庫到彈性集區。| 可行的話，請考慮增加彈性集區的 DTU，以增加其資料庫限制，或從彈性集區中移除資料庫。|
| 40889 | EX\_USER | 彈性集區 '%.*ls' 的 DTU 或儲存空間限制不能減少，因為這樣無法為其資料庫提供足夠的儲存空間。| 彈性集區的名稱。| 嘗試減少低於其儲存空間使用量的彈性集區儲存空間限制。| 請考慮減少彈性集區中個別資料庫的儲存空間使用量，或從集區中移除資料庫，以減少其 DTU 或儲存空間限制。|
| 40891 | EX\_USER | 每個資料庫最小 DTU (%d) 不能超過每個資料庫最大 DTU (%d)。| 每個資料庫最小 DTU；每個資料庫最大 DTU。| 嘗試設定高於每個資料庫最大 DTU 的每個資料庫最小 DTU。| 請確定每個資料庫最小 DTU 不會超過每個資料庫最大 DTU。|
| TBD | EX\_USER | 彈性集區中個別資料庫的儲存空間大小不能超過 '%.*ls' 服務層彈性集區允許的最大大小。| 彈性集區服務層 | 資料庫的最大大小超過彈性集區服務層允許的最大大小。| 請設定彈性集區服務層允許之最大大小限制內的資料庫最大大小。|

<!--------HONumber=AcomDC_1210_2015--->