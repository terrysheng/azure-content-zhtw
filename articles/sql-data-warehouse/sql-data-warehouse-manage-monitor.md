<properties
   pageTitle="使用 DMV 監視工作負載 | Microsoft Azure"
   description="了解如何使用 DMV 監視工作負載。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/29/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# 使用 DMV 監視工作負載

本文說明如何使用動態管理檢視 (DMV)，在 Azure SQL 資料倉儲中監視工作負載及調查查詢執行。

## 監視連接

[Sys.dm\_pdw\_exec\_sessions][] 檢視可讓您監視 Azure SQL 資料倉儲資料庫的連接。此檢視包含作用中工作階段，以及最近中斷連接之工作階段的歷程記錄。Session\_id 是此檢視的主索引鍵，並依序指派給每個新的登入。

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## 調查查詢執行
若要監視執行查詢，請以 [sys.dm\_pdw\_exec\_requests][] 開始。此檢視包含查詢進度，以及最近完成之查詢的歷程記錄。Request\_id 專門識別每個查詢，而且是此檢視的主索引鍵。Request\_id 依序指派給每個新的查詢。查詢這個資料表找出給定的 session\_id，將顯示給定登入的所有查詢。

在您想要調查特定查詢之執行查詢的案例中，以下是一些要遵循的常見步驟。

### 步驟 1：尋找要調查的查詢

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the 10 longest running queries
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

記下您想要調查之查詢的要求 ID。

### 步驟 2：檢查查詢是否正在等候資源

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

上述查詢的結果將顯示查詢的等候狀態。

- 如果查詢正在等候另一個查詢的資源，則狀態會是 **AcquireResources**。
- 如果查詢具有全部的所需資源，且不是在等待中，則狀態會是 **Granted**。此時可繼續調查查詢步驟。

### 步驟 3：尋找查詢計劃的最長執行步驟

使用要求 ID，從 [sys.dm\_pdw\_request\_steps][] 擷取查詢計劃步驟的清單。藉由查看總經過時間，尋找長時間執行的步驟。

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

儲存長時間執行步驟的 Step Index。

檢查長時間執行查詢步驟的 *operation\_type* 資料行：

- 針對下列 **SQL 作業**繼續執行步驟 4a：OnOperation、RemoteOperation、ReturnOperation。
- 針對下列**資料移動作業**繼續執行步驟 4b：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### 步驟 4a：尋找 SQL 步驟的執行進度

使用要求 ID 及步驟索引，從 [sys.dm\_pdw\_sql\_requests][] 擷取資訊，其中包含分散式 SQL Server 執行個體上查詢執行的詳細資料。如果查詢仍在執行，而且您想要從 SQL Server 散發中取得計劃，請記下散發 ID 和 SPID。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


如果查詢仍在執行中，則 [DBCC PDW\_SHOWEXECUTIONPLAN][] 可以用來為特定散發擷取目前執行中 SQL 步驟的 SQL Server 執行計劃。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### 步驟 4a：尋找 DMS 步驟的執行進度

使用要求 ID 和 Step Index，從 [sys.dm\_pdw\_dms\_workers][] 擷取在每個散發上執行的資料移動步驟的相關資訊。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- 檢查 *total\_elapsed\_time* 資料行，查看是否有特定散發，在資料移動上比其他散發用了更多時間。
- 如果是長時間執行的散發，請檢查 *rows\_processed* 資料行，查看從該散發移動的資料列數是否遠多過其他散發。若是如此，這可能表示基礎資料的扭曲。

## 調查資料扭曲

使用 [DBCC PDW\_SHOWSPACEUSED][] 查閱資料表所使用的空間。

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");
```

此查詢的結果會顯示儲存在您資料庫中，每組 60 個散發內的資料表資料列數目。為了達到最佳效能，分散式資料表中的資料列應該平均分配在所有散發中。

若要深入了解，請參閱[資料表設計][]。

## 後續步驟
如需 Transact-SQL 和動態管理檢視 (DMV) 的詳細資訊，請參閱[參考概觀][]。如需其他管理 SQL 資料倉儲的秘訣，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[管理概觀]: sql-data-warehouse-overview-manage.md
[資料表設計]: sql-data-warehouse-develop-table-design.md
[參考概觀]: sql-data-warehouse-overview-reference.md
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW\_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!--MSDN references-->

<!---HONumber=AcomDC_0330_2016-->