<properties
   pageTitle="使用 DMV 監視工作負載 | Microsoft Azure"
   description="了解如何使用 DMV 監視工作負載。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/06/2015"
   ms.author="sahajs"/>

# 使用 DMV 監視工作負載

本文說明如何使用動態管理檢視 (DMV)，在 Azure SQL 資料倉儲中監視工作負載及調查查詢執行。




## 權限

在 SQL 資料倉儲中，查詢動態管理檢視需要 **VIEW DATABASE STATE** 權限。**VIEW DATABASE STATE** 權限會傳回目前資料庫中所有物件的相關資訊。若要授與 **VIEW DATABASE STATE** 權限給特定的資料庫使用者，請執行下列查詢：

```

GRANT VIEW DATABASE STATE TO database_user;

```




## 監視連接

您可以使用 *sys.dm\_pdw\_nodes\_exec\_connections* 檢視，擷取與 Azure SQL 資料倉儲資料庫建立連接的資訊。此外，*sys.dm\_exec\_sessions* 檢視有助於擷取所有作用中使用者的連接資訊。

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


使用下列查詢可擷取目前的連接資訊。

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## 調查查詢執行
您可能會遇到查詢沒有完成，或執行時間超過預期的情況。在此情況下，您可以使用下列步驟收集資料，並縮小問題的範圍。



### 步驟 1：尋找要調查的查詢

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

儲存查詢的要求 ID。


  
### 步驟 2：檢查查詢是否正在等候資源

```

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


上述查詢的結果會顯示要求的等候狀態。

- 如果查詢正在等候另一個查詢的資源，則狀態會是 **AcquireResources**。
- 如果查詢具有全部的所需資源，且不是在等待中，則狀態會是 **Granted**。此時可繼續調查查詢步驟。




### 步驟 3：尋找執行時間最長的查詢步驟

使用要求 ID 可擷取所有分散式查詢階段的清單。藉由查看總經過時間，尋找長時間執行的步驟。

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.
 
SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

儲存長時間執行步驟的 Step Index。

檢查長時間執行之查詢步驟的 *operation\_type* 資料行：- 若為 **SQL 作業**請繼續進行步驟 4a: OnOperation、RemoteOperation、ReturnOperation - 若為**資料移動作業**請繼續進行步驟 4b：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation



### 步驟 4a：尋找 SQL 步驟的執行進度

使用要求 ID 和 Step Index，將 SQL Server 查詢散發的相關資訊，擷取為查詢中的一部分 SQL 步驟。儲存節點 ID 和 SPID。

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


使用下列查詢可在特定節點上擷取 SQL 步驟的 SQL Server 執行計畫。

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace node_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### 步驟 4a：尋找 DMS 步驟的執行進度

使用要求 ID 和 Step Index，擷取在每個散發上執行的資料移動步驟資訊。

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- 檢查 *total\_elapsed\_time* 資料行，查看是否有特定散發，在資料移動上比其他散發用了更多時間。 
- 如果是長時間執行的散發，請檢查 *rows\_processed* 資料行，查看從該散發移動的資料列數是否遠多過其他散發。這麼做可看到有資料扭曲情況的查詢。





## 調查資料扭曲

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


此查詢的結果會顯示儲存在您資料庫中，每組 60 個散發內的資料表資料列數目。為了達到最佳效能，分散式資料表中的資料列應該平均分配在所有散發中。若要深入了解，請參閱[資料表設計][]。



## 後續步驟
如需更多管理 SQL 資料倉儲的秘訣，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[管理概觀]: sql-data-warehouse-overview-manage.md
[資料表設計]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=August15_HO7-->