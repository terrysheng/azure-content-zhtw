<properties 
    pageTitle="使用 T-SQL 建立 Azure SQL Database 或將它移入彈性集區 | Microsoft Azure" 
    description="使用 T-SQL 在彈性集區中建立 Azure SQL Database。或使用 T-SQL 將資料庫移入和移出集區。" 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/01/2016"
    ms.author="sidneyh"/>

# 使用 Transact-SQL 監視和管理彈性資料庫集區  

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

使用 [Create Database (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) 和 [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) 命令建立資料庫，以及將它移入和移出彈性集區。必須先有彈性集區，您才可以使用這些命令。這些命令只會影響資料庫。無法使用 T-SQL 命令來變更新集區的建立和集區屬性 (例如最小和最大 Edtu) 的設定。


> [AZURE.NOTE] 彈性資料庫集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。如果您有 SQL Database V11 伺服器，您可以在單一步驟中[使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server-portal.md)。


## 在彈性集區中建立新的資料庫
使用 CREATE DATABASE 命令搭配 SERVICE\_OBJECTIVE 選項。

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

彈性集區中的所有資料庫都會都繼承彈性集區的服務層 (基本、標準、進階)。


## 在彈性集區之間移動資料庫
使用 ALTER DATABASE 命令搭配 MODIFY，並將 SERVICE\_OBJECTIVE 選項設定為 ELASTIC\_POOL；將名稱設定為目標集區的名稱。

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  


## 將資料庫移入彈性集區 
使用 ALTER DATABASE 命令搭配 MODIFY，並將 SERVICE\_OBJECTIVE 選項設定為 ELASTIC\_POOL；將名稱設定為目標集區的名稱。

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## 將資料庫移出彈性集區
使用 ALTER DATABASE 命令並將 SERVICE\_OBJECTIVE 設定為其中一個效能層級 (S0、S1 等)。

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## 列出彈性集區中的資料庫
使用 [sys.database\_service\_objectives 檢視](https://msdn.microsoft.com/library/mt712619)列出彈性集區中的所有資料庫。登入 master 資料庫來查詢檢視。

>[AZURE.NOTE] 目前彈性集區中資料庫的 service\_objective\_column 會傳回服務目標字串的內部權杖。這將會由字串 "ElasticPool" 取代。
>

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## 監視彈性集區的資源使用量

使用 [sys.elastic\_pool\_resource\_stats 檢視](https://msdn.microsoft.com/library/mt280062.aspx)來檢查邏輯伺服器上彈性集區的資源使用量統計資料。登入 master 資料庫來查詢檢視。

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## 監視彈性集區中資料庫的資源使用量
使用 [sys.dm\_resource\_stats 檢視](https://msdn.microsoft.com/library/dn800981.aspx)或 [sys.resource\_stats 檢視](https://msdn.microsoft.com/library/dn269979.aspx)來檢查彈性集區中資料庫的資源使用量統計資料。此程序類似於查詢任何單一資料庫的資源使用量。

## 後續步驟

建立彈性資料庫集區後，可以藉由建立彈性工作來管理集區中的彈性資料庫。彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。如需詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

<!---HONumber=AcomDC_0406_2016-->