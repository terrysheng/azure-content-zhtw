<properties 
	pageTitle="同盟移轉" 
	description="概述將使用同盟功能建立的現有應用程式移轉至 Elastic Scale 模型的步驟。" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

#同盟移轉 

Azure SQL 資料庫同盟功能即將於 2015 年 9 月的 Web/企業版中淘汰。屆時，採用同盟功能的應用程式將停止執行。若要確保成功移轉，強烈建議儘快進行移轉，以進行充分的規劃和執行。本文件提供同盟移轉公用程式的內容、範例和簡介，以說明如何成功地將目前的同盟應用程式，順暢地移轉至 [Azure SQL DB Elastic Scale 預覽 API](http://go.microsoft.com/?linkid=9862592)。本文件的目的是引導您進行建議的步驟來移轉同盟應用程式，完全不需要任何資料移動。

將現有同盟應用程式移轉到使用 Elastic Scale API 的過程分為三個主要步驟。

1. [從同盟根資料庫建立分區對應管理員 ] 
2. [修改現有應用程式]
3. [切換移出現有的同盟成員]
    

### 移轉範例工具
為了協助完成此程序，已建立 [同盟移轉公用程式](http://go.microsoft.com/?linkid=9862613)。此公用程式將完成步驟 1 和 3。 

## <a name="create-shard-map-manager"></a>從同盟根資料庫建立分區對應管理員
移轉同盟應用程式的第一個步驟是將同盟根資料庫的中繼資料移轉到分區對應管理員的建構。 

![Clone the federation root to the shard map manager][1]
 
首先在測試環境中處理現有的同盟應用程式。
 
使用 [**同盟移轉公用程式**]，將同盟根中繼資料複製到 Elastic Scale [分區對應管理員](http://go.microsoft.com/?linkid=9862595) 的建構。類似於同盟根，分區對應管理員資料庫是獨立的資料庫，其中包含分區對應 (亦即，同盟)、分區 (亦即，同盟成員) 的參考和個別的範圍對應。 

將同盟根資料庫複製到分區對應管理員就是複製和轉譯中繼資料。在同盟根資料庫不會有任何中繼資料變更。請注意，使用同盟移轉公用程式複製同盟根資料庫是一種時間點作業，同盟根資料庫或分區對應的任何變更都不會在其他個別資料存放區中反映。如果同盟根資料庫在測試新 API 期間出現變更，同盟移轉公用程式可用來重新整理分區對應，以呈現最新狀態。 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 修改現有應用程式 

使用分區對應管理員，並且向分區對應管理員註冊同盟成員和範圍 (透過移轉公用程式完成) 之後，即可修改現有同盟應用程式來使用 Elastic Scale API。如上圖所示，透過 Elastic Scale API 進行的應用程式連線將透過分區對應管理員連接到適當的同盟成員 (也就是分區)。將同盟成員對應到分區對應管理員可以啟用兩種版本的應用程式 (一個使用同盟，而另一個使用 Elastic Scale) 並行執行，以便確認功能。   

移轉應用程式期間，將對現有應用程式進行兩個核心修改。


#### 變更 1：將分區對應管理員個體化： 

不同於同盟，Elastic Scale API 會透過 **ShardMapManager** 類別與分區對應管理員互動。**ShardMapManager** 物件和分區對應的個體化可透過以下方式完成：
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 變更 2：連線到適當的分區 

使用同盟後，即可透過 USE FEDERATION 命令連線到特別的同盟成員，如下所示：  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

使用 Elastic Scale API 後，將透過[資料相依路由建立特定分區的連線](sql-database-elastic-scale-data-dependent-routing.md) (藉由 **RangeShardMap** 類別的 **OpenConnectionForKey** 方法)。 

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

本小節的步驟是必要的步驟，不過可能無法解決所有發生的移轉情況。如需詳細資訊，請參閱 [Elastic Scale 的概念概觀](sql-database-elastic-scale-introduction.md) 及 [API 參考](http://go.microsoft.com/?linkid=9862604)。

## 切換移出現有同盟成員 

![Switch out the federation members for the shards][3]

加入 Elastic Scale API 修改應用程式後，同盟應用程式移轉的最後一個步驟就是 **SWITCH OUT** 同盟成員 (如需詳細資訊，請參閱 [ALTER FEDERATION 的 MSDN 參考 (Azure SQL Database](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx))。對於特定同盟成員發出 **SWITCH OUT** 陳述式的最終結果是移除呈現同盟成員成員一般 Azure SQL 資料庫的所有同盟建構和中繼資料，這和其他任何 Azure SQL 資料庫並無不同。  

請注意，對於同盟成員發出 **SWITCH OUT** 陳述式是單向作業，無法復原。執行後，最終的資料庫無法新增回同盟，而且 USE FEDERATION 命令將不再對於此資料庫有效。 

為了執行切換，已經對 ALTER FEDERATION 命令加入另一個引數，以便切換移出同盟成員。對於個別同盟成員發出該命令後，同盟移轉公用程式將提供以程式設計的方式逐一查看各個同盟成員，並執行切換移出作業。 

對於所有現有同盟成員執行切換後，應用程式的移轉隨即完成。  
同盟移轉公用程式能夠： 

1.    將同盟根資料庫複製到分區對應管理員。使用者可以選擇將現有分區對應管理員放置在新的 Azure SQL 資料庫 (建立) 或現有的同盟根資料庫。
2.    對於同盟中的所有同盟成員發出 SWITCH OUT 陳述式。


##功能比較  
雖然 Elastic Scale 提供其他許多功能 (例如，[多分區查詢](sql-database-elastic-scale-multishard-querying.md)、[分割和合併分區](sql-database-elastic-scale-overview-split-and-merge.md)、[分區彈性](sql-database-elastic-scale-elasticity.md)、[用戶端塊取](sql-database-elastic-scale-shard-map-management.md)等等)，不過許多實用的同盟功能是 Elastic Scale 所不支援的。
  

- 使用 **FILTERING=ON**。Elastic Scale 目前不支援資料列層級篩選。移轉能夠將篩選邏輯建立到按照分區發出的查詢，如下所示： 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

產生如下的結果：

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Elastic Scale **分割**功能並未完全上線。在分割作業期間，各個小分區都會在移動期間離線。
- Elastic Scale 分割功能需要手動資料庫佈建和結構描述管理。

## 其他考量

* Web 和企業版及同盟將於 2015 年秋季淘汰。在同盟應用程式的移轉中，強烈建議對於 Basic、Standard 和 Premium 版執行效能測試。 

* 對於同盟成員執行 SWITCH OUT 陳述式能夠使最終的資料庫使用所有的 Azure SQL 資料庫功能 (亦即新版本、備份、PITR、稽核等等。) 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[從同盟根資料庫建立分區對應管理員 ]:#create-shard-map-manager
[修改現有應用程式]:#Modify-the-Existing-Application
[切換移出現有的同盟成員]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png

<!--HONumber=47-->
 