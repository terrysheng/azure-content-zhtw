<properties
   pageTitle="SQL 資料倉儲中的並行存取和工作負載管理 | Microsoft Azure"
   description="了解 Azure SQL 資料倉儲中的並行存取和工作負載管理以開發解決方案。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/04/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 資料倉儲中的並行存取和工作負載管理
若要在大型 SQL 資料倉儲提供可預測的效能，請實作機制以管理工作負載並行存取和運算資源指派。

本文介紹並行存取和工作負載管理的概念；說明如何實作這兩種功能，以及如何在資料倉儲中控制它們。

>[AZURE.NOTE] SQL 資料倉儲支援多使用者工作負載，不支援多租用戶工作負載。

## 並行
請務必了解 SQL 資料倉儲中的並行存取由兩個概念控制；**並行查詢** 和 **並行存取插槽**。

並行查詢相當於在同一時間執行的查詢數目。SQL 資料倉儲支援高達 32 個**並行查詢**。無論是序列查詢 (單一執行緒) 或平行查詢 (多執行緒)，每個查詢執行都會視為單一查詢。這是固定的上限，可套用至所有服務層級和所有查詢。

並行存取插槽是更為動態的概念，相對於資料倉儲的資料倉儲單位 (DWU) 服務等級目標。當您增加配置給 SQL 資料倉儲的 DWU 數目，就會指派更多計算資源。不過，增加 DWU 也會增加可用**並行存取插槽**的數目。

一般來說，每個同時執行的查詢都會取用一或多個並行存取插槽。確切的插槽數目取決於三個因素：

1. SQL 資料倉儲的 DWU 設定
2. 使用者隸屬的**資源類別**
3. 查詢或作業是否由並行存取插槽模型所控管

> [AZURE.NOTE] 值得注意的是，不是每個查詢都由並行存取插槽查詢規則所控管。但大多數使用者查詢是如此。某些查詢和作業完全不會耗用並行存取插槽。這些查詢和作業仍然受限於並行查詢限制，這就是為什麼要同時描述這兩個規則。如需詳細資料，請參閱下節的[資源類別例外狀況](#exceptions)。

下表描述並行查詢和並行存取插槽的限制。其中假設您的查詢受資源控管。

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| 並行存取插槽耗用量 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 並行查詢上限 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 並行存取插槽上限 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |

SQL 資料倉儲查詢工作負載必須維持在這些臨界值內。如果有 32 個以上的並行查詢，或超過並行存取插槽的數目，查詢就會排入佇列直到可以滿足兩個臨界值。

## 工作負載管理

SQL 資料倉儲會以**資料庫角色**的形式公開四個不同的資源類別，做為其工作負載管理實作的一部分。

角色如下：

- smallrc
- mediumrc
- largerc
- xlargerc

資源類別是 SQL 資料倉儲工作負載管理中不可或缺的一部分。它們控管配置給查詢的運算資源。

根據預設，每位使用者都是小型資源類別 - smallrc 的成員。不過，任何使用者都可以加入至一或多個較高的資源類別。一般來說，SQL 資料倉儲將採用最高的角色成員資格來執行查詢。將使用者加入至較高的資源類別會增加該使用者的資源，但它也會耗用更大的並行存取插槽；可能會限制您的並行存取。這是因為多個資源配置給一個查詢時，系統必須限制其他查詢所耗用的資源。天下沒有免費的午餐。

較高的資源類別所控制最重要的資源就是記憶體。大部分有意義大小的資料倉儲資料表都會使用叢集資料行存放區索引。這通常會提供資料倉儲工作負載的最佳效能，而維護工作負載是一項記憶體密集的作業。使用較高的資源類別通常相當有助於索引重建等資料管理作業。

SQL 資料倉儲已透過使用資料庫角色來實作資源類別。若要成為較高資源類別的成員並增加您的記憶體和優先順序，只需要將資料庫使用者加入至先前所述的其中一個角色/資源類別。

### 資源類別成員資格

您可以使用 `sp_addrolemember` 和 `sp_droprolemember` 程序，將自己新增和移除至工作負載管理資料庫角色。請注意，您需要 `ALTER ROLE` 權限才能執行此動作。您不能使用 ALTER ROLE DDL 語法。您必須使用上述的預存程序。本文最後的 [管理使用者) [#managing-users] 一節提供如何建立登入和使用者的完整範例。

> [AZURE.NOTE] 相較於在工作負載管理群組內外新增使用者，通常更容易的做法是透過永久指派給較高資源類別的個別登入/使用者，起始更密集的作業。

### 記憶體配置

下表詳細說明每個查詢可用的記憶體增加；取決於套用至執行查詢之始用者的資源類別：：

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| 可用記憶體 (每個 dist) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |

### 並行存取插槽耗用量

此外，如上所述，指派給使用者的資源類別愈高，並行存取插槽的耗用量就愈大。下表記錄查詢在指定資源類別中的並行存取插槽耗用量。

<!--
| Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| 耗用量 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 並行查詢上限 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 並行存取插槽上限 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

### 例外狀況

更高資源類別的成員資格有時不會改變指派給查詢或作業的資源。這通常發生於執行動作所需的資源不足。在這些情況下，一律會使用預設或小型資源類別 (smallrc)，而不考慮指派給使用者的資源類別。例如，`CREATE LOGIN` 一律會在 smallrc 中執行。執行此作業所需的資源非常少，因此沒必要將查詢納入並行存取插槽模型中。預先配置大量記憶體給這個動作太浪費。從並行存取插槽模型中排除 `CREATE LOGIN`，SQL 資料倉儲會更有效率。

下面**是**由資源類別控管的陳述式和作業清單：

- INSERT-SELECT
- UPDATE
- 刪除
- SELECT (當查詢使用者資料表時)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT
- 載入資料
- 資料移動服務 (DMS) 進行的資料移動作業

下列陳述式**不**接受資源類別：

- CREATE TABLE
- ALTER TABLE ...SWITCH PARTITION
- ALTER TABLE ...SPLIT PARTITION
- ALTER TABLE ...MERGE PARTITION
- DROP TABLE
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE STATISTICS
- UPDATE STATISTICS
- DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE USER
- ALTER USER
- DROP USER
- CREATE PROCEDURE
- ALTER PROCEDURE
- DROP PROCEDURE
- CREATE VIEW
- DROP VIEW
- INSERT VALUES
- SELECT (從系統檢視和 DMV)
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

> [AZURE.NOTE] 值得強調的是，只針對動態管理檢視和目錄檢視執行的 `SELECT` 查詢**不是**由資源類別控管。

請務必記得，大部分的使用者查詢很可能由資源類別控管。一般來說，使用中的查詢工作負載必須落在並行查詢和並行存取插槽臨界值內，除非已被平台明確排除。一般使用者無法選擇從並行存取插槽模型中排除查詢。一旦超越任何一個臨界值，查訊就會開始排入佇列。排入佇列的查詢將根據提交時間的優先順序解決。

### 內部

在 SQL 資料倉儲的工作負載管理幕後，情況有點複雜。資源類別會以動態方式對應至資源管理員內的一組一般工作負載管理群組。使用的群組將取決於倉儲的 DWU 值。不過，總共有八個由 SQL 資料倉儲所使用的工作負載群組。如下：

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

這 8 個群組會對應至並行存取插槽耗用量

| 工作負載群組 | 並行存取插槽對應 | 優先順序對應 |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | 中型 |
| SloDWGroupC01 | 2 | 中型 |
| SloDWGroupC02 | 4 | 中型 |
| SloDWGroupC03 | 8 | 中型 |
| SloDWGroupC04 | 16 | 高 |
| SloDWGroupC05 | 32 | 高 |
| SloDWGroupC06 | 64 | 高 |
| SloDWGroupC07 | 128 | 高 |

因此，比例而言，如果 DW500 是您的 SQL 資料倉儲目前的 DWU 設定，則作用中的工作負載群組會對應至資源類別，如下所示：

| 資源類別 | 工作負載群組 | 已使用的並行存取插槽 | 重要性 |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | 中型 |
| mediumrc | SloDWGroupC02 | 4 | 中型 |
| largerc | SloDWGroupC03 | 8 | 中型 |
| xlargerc | SloDWGroupC04 | 16 | 高 |

若要從資源管理員的觀點查看詳細資料中記憶體資源配置的差異，請使用下列查詢：

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
	WHERE   wg.name like 'SloDWGroup%'
	AND     rp.name = 'SloDWPool'
)
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

> [AZURE.NOTE] 上述查詢也可以在疑難排解時，用來分析工作負載群組的作用中和歷史的使用情況

## 工作負載管理範例

本節提供其他範例，說明如何管理使用者和偵測排入佇列中的查詢。

### 管理使用者

若要授與使用者 SQL 資料倉儲的存取權，他們需要先登入。

開啟 SQL 資料倉儲之主要資料庫的連接，並執行下列命令：

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

> [AZURE.NOTE] 您最好在 Azure SQL Database 和 SQL 資料倉儲建立登入主要資料庫的使用者。此層級有兩個可用的伺服器角色，需要登入才能在主要資料庫中擁有使用者以授與成員資格。這些角色為 `Loginmanager` 和 `dbmanager`。在 Azure SQL Database 和 SQL 資料倉儲中，這些角色會授與管理登入以及建立資料庫的權限。這與 SQL Server 有所不同。如需詳細資料，請參閱[管理 Azure SQL Database 中的資料庫和登入]。

一旦已建立登入，現在必須新增使用者帳戶。

開啟 SQL 資料倉儲資料庫的連接，然後執行下列命令：

```
CREATE USER newperson FOR LOGIN newperson
```

完成之後，必須授與權限給使用者。下列範例會在 SQL 資料倉儲資料庫上授與 `CONTROL`。在資料庫層級的 `CONTROL` 相當於 SQL Server 中的 db\_owner。

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

若要查看工作負載管理角色，請使用下列查詢：

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

若要將使用者加入至增加工作負載管理角色，請使用下列查詢：

```
EXEC sp_addrolemember 'largerc', 'newperson'
```

若要從工作負載管理角色移除使用者，請使用下列查詢：

```
EXEC sp_droprolemember 'largerc', 'newperson'
```

> [AZURE.NOTE] 您不可能從 smallrc 移除使用者。

若要查看哪些使用者是指定角色的成員，請使用下列查詢：

```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

### 排入佇列的查詢偵測
若要識別保留在並行存取佇列中的查詢，您可以一律參考 `sys.dm_pdw_exec_requests` DMV。

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

SQL 資料倉儲具有特定的等候類型以測量並行存取。

如下：

- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType 指的是並行存取插槽架構之外的查詢。DMV 查詢及 `SELECT @@VERSION` 這類的系統函數是本機查詢的範例。

UserConcurrencyResourceType 指的是並行存取插槽架構之內的查詢。針對使用者資料表的查詢代表會使用此資源類型的範例。

DmsConcurrencyResourceType 指的是資料移動作業所產生的等候。

備份資料庫時，可以看到 BackupConcurrencyResourceType。此資源類型的最大值為 1。如果在同一時間要求多個備份，其他備份會排入佇列。


若要執行目前以排入佇列之要求的分析，以找出要求正在等候哪些資源，請參考 `sys.dm_pdw_waits` DMV。

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

若只檢視由指定查詢取用的資源等候，您可以參考 `sys.dm_pdw_resource_waits` DMV。資源等候時間只會測量等候提供資源的時間，與訊號等候時間相反，後者是基礎 SQL Server 將查詢排程到 CPU 所需的時間。

```
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID()
;
```

最後，SQL 資料倉儲會提供 `sys.dm_pdw_wait_stats` DMV 給等候的歷史趨勢分析。

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## 後續步驟
如需更多開發祕訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[管理 Azure SQL Database 中的資料庫和登入]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->