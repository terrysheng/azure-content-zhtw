<properties 
	pageTitle="分區對應管理 | Microsoft Azure" 
	description="如何使用彈性資料庫用戶端程式庫 ShardMapManager" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="ddove;sidneyh"/>

# 分區對應管理

在分區化資料庫環境中，[**分區對應**](sql-database-elastic-scale-glossary.md)會維護資訊，以便讓應用程式根據**分區化索引鍵**的值，連線到正確的資料庫。了解這些對應建構的方式，是分區對應管理的基本。針對 Azure SQL Database，使用可在[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)中找到的 [ShardMapManager 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)來管理分區對應。
 

## 分區對應和分區對應方式
 
### 分區化索引鍵支援的 .Net 型別

Elastic Scale 支援下列 .Net Framework 型別作為分區化索引鍵：

* integer
* long
* guid
* byte  
* datetime
* timespan
* datetimeoffset

### 清單和範圍分區對應
建構分區對應時可以選擇使用**個別分區化索引鍵值的清單**，或使用**分區化索引鍵值的範圍**。

###清單分區對應
**分區**包含 **Shardlet**，Shardlet 至分區的對應是由分區對應所維護。**清單分區對應**是個別索引鍵值 (識別 Shardlet) 與資料庫 (做為分區) 之間的關聯。**清單對應**是明確且不同的索引鍵值，可對應到相同資料庫。例如，索引鍵 1 對應到 Database A，索引鍵值 3 和 6 都參照 Database B。

| 金鑰 | 分區位置 |
|-----|----------------|
| 1 | Database\_A |
| 3 | Database\_B |
| 4 | Database\_C |
| 6 | Database\_B |
| ... | ... |
 

### 範圍分區對應 
在**範圍分區對應**中，由一組 **[低值, 高值)** 描述索引鍵範圍，其中*低值*是範圍內的最小索引鍵，*高值*是高於該範圍的第一個值。

例如，**[0, 100)** 包含所有大於或等於 0 且小於 100 的整數。請注意，多個範圍可指向相同的資料庫，而且可支援不相連的範圍 (例如 [100, 200) 和 [400, 600) 都指向下面範例中的資料庫 C。)

| 金鑰 | 分區位置 |
|-----------|----------------|
| [1,50) | Database\_A |
| [50,100) | Database\_B |
| [100,200) | Database\_C |
| [400,600) | Database\_C |
| ... | ...            

上述每個資料表都是 **ShardMap** 物件的概念範例。每個資料列是個別 **PointMapping** (適用於清單分區對應) 或 **RangeMapping** (適用於範圍分區對應) 物件的簡化範例。

## 分區對應管理員 

在用戶端程式庫中，分區對應管理員是分區對應的集合。**ShardMapManager** 執行個體所管理的資料會保留在三個位置：

1. **全域分區對應 (GSM)**：您指定資料庫作為其所有分區對應及對應的儲存機制。自動會建立特殊的資料表和預存程序來管理資訊。這通常是一個小型資料庫且很少存取，且不應該用於應用程式的其他需求。資料表位於一個名為 **\_\_ShardManagement** 的特殊結構描述中。

2. **本機分區對應 (LSM)**：您指定作為分區的每個資料庫會修改成包含數個小型資料表和特殊預存程序，其中包含並管理該分區特有的分區對應資訊。這項資訊與 GSM 中的資訊重複，並可讓應用程式驗證快取的分區對應資訊，而不會對 GSM 造成任何負擔；應用程式使用 LSM 判斷快取的對應是否仍然有效。每個分區上對應至 LSM 的每個資料表，也會在結構描述 **\_\_ShardManagement** 中。

3. **應用程式快取**：每個存取 **ShardMapManager** 物件的應用程式執行個體會維護其對應的本機記憶體內部快取。它會儲存最近擷取的路由資訊。

## 建構 ShardMapManager

**ShardMapManager** 物件是使用 [Factory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx) 模式所建構。**[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** 方法接受 **ConnectionString** 形式的認證 (包含保留 GSM 的伺服器名稱和資料庫名稱)，並傳回 **ShardMapManager** 的執行個體。

對於每個應用程式定義域，**ShardMapManager** 應該只具現化一次 (在應用程式的初始化程式碼內)。**ShardMapManager** 可以包含任意數目的分區對應。雖然單一分區對應可能足夠用於許多應用程式，但有時幾組不同的資料庫會用於不同的結構描述或做為特殊用途；在這些情況下，最好使用多個分區對應。

在這段程式碼中，應用程式會以 [TryGetSqlShardMapManager 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)嘗試開啟現有的 **ShardMapManager**。如果代表全域 **ShardMapManager** (GSM) 的物件尚不存在資料庫內，用戶端程式庫會使用 [CreateSqlShardMapManager 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)在其中建立這些物件。

    // Try to get a reference to the Shard Map Manager 
 	// via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 
或者，您也可以使用 Powershell 建立新的分區對應管理員。範例請見[這裡](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)。

### 分區對應系統管理認證

一般而言，負責管理和操作分區對應的應用程式，不同於使用分區對應來安排連線的分區對應。

對於管理分區對應 (加入或變更分區、分區對應、分區對應等等) 的應用程式，您必須使用**在 GSM 資料庫和每一個做為分區的資料庫上擁有讀取/寫入權限的認證**，以具現化 **ShardMapManager**。這些認證必須允許在輸入或變更分區對應資訊時，能夠在 GSM 和 LSM 中寫入資料表，也必須允許在新的分區上建立 LSM 資料表。

### 只影響中繼資料 

用來填入或變更 **ShardMapManager** 資料的方法不會改變分區本身中儲存的使用者資料。比方說，**CreateShard**、**DeleteShard**、**UpdateMapping** 等方法只會影響分區對應中繼資料。它們不會移除、新增或改變分區中所包含的使用者資料。相反地，這些方法是設計來搭配其他作業一起使用，例如，您可能執行這些作業來建立或移除實際的資料庫，或將資料列從一個分區移至另一個分區，以重新平衡分區化環境。(彈性資料庫工具隨附的**分割合併**工具會使用這些 API，以及協調分區之間實際的資料移動。) 請參閱[使用彈性資料庫分割合併工具來縮放](sql-database-elastic-scale-overview-split-and-merge.md)。

## 填入分區對應範例
 
填入特定分區對應的作業順序範例如下所示。程式碼會執行下列步驟：

1. 在分區對應管理員內建立新的分區對應。 
2. 將兩個不同分區的中繼資料加入至分區對應。 
3. 加入各種不同的索引鍵範圍對應，並顯示分區對應的整體內容。 

已撰寫的程式碼讓方法可以在發生錯誤時重新執行。每個要求會測試是否已經存在分區或對應，然後才嘗試建立。程式碼假設在字串 **shardServer** 所參考的伺服器中，已建立名為 **sample\_shard\_0**、**sample\_shard\_1** 和 **sample\_shard\_2** 的資料庫。

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
	                                 shardServer, 
	                                 "sample_shard_0"), 
	                                 out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
	                                        shardServer, 
	                                        "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
	                                shardServer, 
	                                "sample_shard_1"), 
	                                out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
	                                         shardServer, 
	                                        "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                      new RangeMappingCreationInfo<long>
	                      (new Range<long>(0, 50), 
	                      shard0, 
	                      MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
或者，您也可以使用 PowerShell 指令碼來達成相同的結果。部分 PowerShell 範例請見[這裡](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)。

一旦已填入分區對應，就可建立或調整資料存取應用程式來使用對應。除非**對應配置**需要變更，否則就不必再次填入或操作對應。

## 資料相依路由 

通常是需要資料庫連接來執行應用程式特定資料作業的應用程式，才會使用分區對應管理員。這些連線都必須與正確的資料庫相關聯。這稱為**資料相依路由**。對於這些應用程式，請使用對 GSM 資料庫具有唯讀存取的認證，從 Factory 具現化分區對應管理員物件。稍後連接的個別要求會提供連接到適當分區資料庫所需的認證。

請注意，這些應用程式 (使用以唯讀認證開啟的 **ShardMapManager**) 將無法變更對應或對應方式。針對這些需求，請建立管理專用應用程式或 PowerShell 指令碼，以提供稍早所述較高權限的認證。請參閱[用來存取彈性資料庫用戶端程式庫的認證](sql-database-elastic-scale-manage-credentials.md)。

如需詳細資訊，請參閱[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)。

## 修改分區對應 

您可以使用不同方式來變更分區對應。下列所有方法會修改中繼資料 (描述分區和其對應)，但他們不實際修改分區中的資料，也不會建立或刪除實際的資料庫。如下所述，分區對應上的某些作業可能需要與管理動作協調，這些動作會實際移動資料，或是新增和移除資料庫 (做為分區)。

這些方法一起做為建置組塊，可用於修改分區化資料庫環境中的整體資料分佈。

* 若要新增或移除分區：請使用 [Shardmap 類別的](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx) **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)** 和 **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)**。 
    
    代表目標分區的伺服器和資料庫必須存在，才能執行這些作業。這些方法完全不影響資料庫本身，只影響分區對應中的中繼資料。

* 若要建立或移除對應至分區的點或範圍：請使用 [RangeShardMapping 類別](https://msdn.microsoft.com/library/azure/dn807318.aspx)的 **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**、**[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)**，和 [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx) 的 **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)**
    
    許多不同的點或範圍可以對應至相同的分區。這些方法只會影響中繼資料 - 不會影響分區中可能已經存在的任何資料。如果需要從資料庫移除資料，才能與 **DeleteMapping** 作業維持一致，您必須另外執行這些作業，但要搭配使用這些方法。

* 若要將現有的範圍分割成兩個，或將相鄰的範圍合併成一個：請使用 **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)** 和 **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**。

    請注意，分割及合併作業**不會變更索引鍵值所對應的分區**。分割會將現有的範圍切割成兩個部分，但會保持兩者都對應至相同的分區。合併是以兩個已對應至相同分區的相鄰範圍為對象，將它們聯合成單一範圍。在分區之間移動點或範圍本身時，需要使用 **UpdateMapping** 並配合實際資料移動來協調。您可以使用彈性資料庫工具的**分割/合併**工具，以協調分區對應變更和資料移動 (需要移動時)。

* 若要將個別的點或範圍重新對應 (或移動) 至不同的分區：請使用 **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**。

    因為資料可能需要從一個分區移至另一個分區，才能與 **UpdateMapping** 作業維持一致，您必須另外執行該動作，但要搭配使用這些方法。

* 若要讓對應上線和離線︰請使用 **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)** 和 **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)** 控制對應的線上狀態。

    只有當對應處於「離線」狀態時，才允許分區對應上的某些作業，包括 **UpdateMapping** 和 **DeleteMapping**。對應離線時，根據該對應中包含的索引鍵來提出資料相依要求會傳回錯誤。此外，當範圍第一次離線時，受影響分區的所有連線會自動終止，以避免查詢要變更的範圍時產生不一致或不完整的結果。

對應是 .Net 中不可變的物件。以上會變更對應的所有方法也會使您的程式碼中任何對它們的參考失效。為了輕鬆執行作業序列來變更對應的狀態，所有會變更對應的方法都會傳回新的對應參考，如此就能鏈結作業。例如，若要在 shardmap sm 中刪除包含索引鍵 25 的現有對應，您可以執行下列方法：

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## 加入分區 

對於已經存在的分區對應，應用程式通常只需要加入新的分區，以處理預期來自新的索引鍵或索引鍵範圍的資料。例如，以租用戶識別碼分區化的應用程式，可能需要為新的租用戶佈建新的分區，或者，每月分區化的資料可能需要在每個新月份開始之前佈建新的分區。

如果索引鍵值的新範圍尚不屬於現有的對應，而且不需要移動任何資料，則加入新的分區並將新的索引鍵或範圍與該分區產生關聯就非常簡單。如需有關加入新分區的詳細資訊，請參閱[加入新的分區](sql-database-elastic-scale-add-a-shard.md)。

不過，在需要資料移動的情況下，分割合併工具需要結合必要的分區對應更新，以協調分區之間的資料移動。如需有關使用分割合併工具的詳細資訊，請參閱[分割合併的概觀](sql-database-elastic-scale-overview-split-and-merge.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0211_2016-->