<properties
   pageTitle="轉換現有的資料庫以使用彈性資料庫工具"
   description="建立分區對應管理員來轉換分區化資料庫，以使用彈性資料庫工具"
   services="sql-database"
   documentationCenter=""
   authors="SilviaDoomra"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/29/2016"
   ms.author="SilviaDoomra"/>

# 轉換現有的資料庫以使用彈性資料庫工具

如果您有現有的擴充分區化解決方案，則可以使用此處所述的技巧，善用彈性資料庫工具。在轉換之後，您可以使用[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)和[分割合併工具](sql-database-elastic-scale-overview-split-and-merge.md)。

您可以使用 [.NET Framework 用戶端程式庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)或在 [Azure SQL DB - 彈性資料庫工具指令碼](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)中找到的 PowerShell 指令碼，來實作這些技巧。以下範例會使用 PowerShell 指令碼。

共有四個步驟：

1. 準備分區對應管理員資料庫。
2. 建立分區對應。
3. 準備個別分區。  
2. 新增對應至分區對應。

如需 ShardMapManager 的詳細資訊，請參閱[分區對應管理](sql-database-elastic-scale-shard-map-management.md)。如需彈性資料庫工具的概觀，請參閱[彈性資料庫功能概觀](sql-database-elastic-scale-introduction.md)。

## 準備分區對應管理員資料庫
您可以使用新的或現有的資料庫做為分區對應管理員。此作業只需要執行一次。

## 步驟 1︰建立分區對應管理員
請注意，做為分區對應管理員的資料庫不應該是與分區相同的資料庫。

	# Create a shard map manager. 
	New-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 
	#<server_name> and <smm_db_name> are the server name and database name 
	# for the new or existing database that should be used for storing 
	# tenant-database mapping information.

### 擷取分區對應管理員

在建立之後，您可以使用 Cmdlet 擷取分區對應管理員。每次需要使用 ShardMapManager 物件，則需要此步驟。

	# Try to get a reference to the Shard Map Manager  
	$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
	-Password '<password>' 
	-SqlServerName '<server_name>' 
	-SqlDatabaseName '<smm_db_name>' 

  
## 步驟 2：建立分區對應

選擇是建立下列其中一個模型︰

1. 每個資料庫的單一租用戶 
2. 每個資料庫的多個租用戶 (兩種類型)︰
	3. 範圍對應
	4. 清單對應
 

如果您使用的是單一租用戶資料庫模型，請使用清單對應。單一租用戶模型會指派每個租用戶一個資料庫。這是適用於 SaaS 開發人員的有效模式，因為它會簡化管理。

![清單對應][1]

相反地，多租用戶資料庫模型會將數個租用戶指派給單一資料庫，而且您可以跨多個資料庫散發租用戶的群組。當預期每個租用戶的資料量很小時，這是可行的模型。在此模型中，我們可以使用*範圍對應*將租用戶的範圍指派給資料庫。
 

![範圍對應][2]

您也可以使用清單對應來實作多租用戶資料庫模型，以將多個租用戶指派給單一資料庫。例如，DB1 是用來儲存租用戶 ID 1 和 5 的相關資訊，而 DB2 是用來儲存租用戶 7 和租用戶 10 的資料。

![單一資料庫上的多個租用戶][3]


## 步驟 2，選項 1︰建立清單對應的分區對應
使用 ShardMapManager 物件建立分區對應。

	# $ShardMapManager is the shard map manager object. 
	$ShardMap = New-ListShardMap -KeyType $([int]) 
	-ListShardMapName 'ListShardMap' 
	-ShardMapManager $ShardMapManager 
 
 
## 步驟 2，選項 2︰建立範圍對應的分區對應

請注意，若要利用此對應模式，租用戶 ID 值需要是連續的範圍，而且可接受範圍中有間距，方法為只在建立資料庫時略過範圍。

	# $ShardMapManager is the shard map manager object 
	# 'RangeShardMap' is the unique identifier for the range shard map.  
	$ShardMap = New-RangeShardMap 
	-KeyType $([int]) 
	-RangeShardMapName 'RangeShardMap' 
	-ShardMapManager $ShardMapManager 

## 步驟 2，選項 3︰單一資料庫上的清單對應
設定此模式也需要建立清單對應，如步驟 2，選項 1 所示。

## 步驟 3︰準備個別分區

將每個分區 (資料庫) 新增至分區對應管理員。這會準備個別資料庫以儲存對應資訊。在每個分區上執行此方法。
	 
	Add-Shard 
	-ShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>'
	# The $ShardMap is the shard map created in step 2.
 

## 步驟 4︰新增對應

新增對應取決於您所建立的分區對應種類。如果已建立清單對應，則會新增清單對應。如果已建立範圍對應，則會新增範圍對應。

### 步驟 4 選項 1︰對應清單對應的資料

新增清單對應來對應每個租用戶的資料。

	# Create the mappings and associate it with the new shards 
	Add-ListMapping 
	-KeyType $([int]) 
	-ListPoint '<tenant_id>' 
	-ListShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 

### 步驟 4 選項 2︰對應範圍對應的資料

新增所有租用戶 ID 範圍的範圍對應 – 資料庫關聯︰

	# Create the mappings and associate it with the new shards 
	Add-RangeMapping 
	-KeyType $([int]) 
	-RangeHigh '5' 
	-RangeLow '1' 
	-RangeShardMap $ShardMap 
	-SqlServerName '<shard_server_name>' 
	-SqlDatabaseName '<shard_database_name>' 


### 步驟 4 選項 3︰對應單一資料庫上多個租用戶的資料

對於每個租用戶，執行 Add-ListMapping (上面的選項 1)。


## 檢查對應

您可以使用下列命令來查詢現有分區以及與其相關聯之對應的相關資訊 ︰

	# List the shards and mappings 
	Get-Shards -ShardMap $ShardMap 
	Get-Mappings -ShardMap $ShardMap 

## 摘要

一旦完成安裝後，您就可以開始使用彈性資料庫用戶端程式庫。您也可以使用[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)和[多分區查詢](sql-database-elastic-scale-multishard-querying.md)。

## 後續步驟


從 [Azure SQL DB 彈性資料庫工具指令碼](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)取得 PowerShell 指令碼。

這些工具也會在 GitHub 上︰[Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools)。

使用分割合併工具，在多租用戶模型與單一租用戶模型之間來回移動資料。請參閱[分割合併工具](sql-database-elastic-scale-get-started.md)。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 

<!---HONumber=AcomDC_0330_2016-->