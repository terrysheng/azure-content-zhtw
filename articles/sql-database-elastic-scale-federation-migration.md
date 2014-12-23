<properties title="Federations Migration" pageTitle="同盟移轉" description="Outlines the steps to migrate an existing app built with Federations feature to Elastic Scale model." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

#同盟移轉 

Azure SQL Database 同盟功能將於 2015 年 9 月隨著 Web/Business 版本一起淘汰。屆時，利用「同盟」功能的應用程式將停止執行。為了確保成功移轉，強烈建議儘快開始移轉，以便充分的規劃和執行。本文件提供同盟移轉公用程式的內容、範例和簡介，以說明如何成功地將目前的同盟應用程式，順暢地移轉至 [Azure SQL DB Elastic Scale 預覽 API](http://go.microsoft.com/?linkid=9862592)。本文的目標是透過建議的步驟引導您移轉同盟應用程式，而不需移動任何資料。

將現有同盟應用程式移轉至使用 Elastic Scale API 的應用程式有三個主要步驟。

1. [從同盟根建立分區對應管理員] 
2. [修改現有的應用程式]
3. [切換移出現有的同盟成員]
    

### 移轉範例工具
為了協助完成此程序，已建立[同盟移轉公用程式](http://go.microsoft.com/?linkid=9862613)。此公用程式會完成步驟 1 和 3。

## <a name="create-shard-map-manager"></a>從同盟根建立分區對應管理員
移轉同盟應用程式的第一個步驟，就是將同盟根的中繼資料複製到分區對應管理員的建構。 

![Clone the federation root to the shard map manager][1]
 
在測試環境中從現有的同盟應用程式開始。
 
使用**同盟移轉公用程式**，將同盟根中繼資料複製到 Elastic Scale [分區對應管理員](http://go.microsoft.com/?linkid=9862595)的建構。類似於同盟根，分區對應管理員資料庫是獨立的資料庫，其中包含分區對應 (亦即，同盟)、分區 (亦即，同盟成員) 的參考和個別的範圍對應。 

將同盟根複製到分區對應管理員會建立中繼資料的複本和轉譯。不會更改同盟根上的中繼資料。請注意，使用同盟移轉公用程式來複製同盟根是時間點作業，對同盟根或分區對應所做的任何變更，將不會反映在其他各自的資料存放區。如果在測試新的 API 時變更同盟根，同盟移轉公用程式可用來整理分區對應以呈現目前的狀態。 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 修改現有的應用程式 

準備好分區對應管理員，並將同盟成員和範圍註冊到分區對應管理員之後 (透過移轉公用程式完成)，就可以修改現有的同盟應用程式來使用 Elastic Scale API。如上圖所示，透過 Elastic Scale API 的應用程式連接將透過分區對應管理員，遞送至適當的同盟成員 (現在也是分區)。將同盟成員對應至分區對應管理員可讓應用程式的兩個版本 (一個使用同盟，另一個使用 Elastic Scale) 並行執行以驗證功能。   

在移轉應用程式期間，現有的應用程式需要進行兩項重大的修改。


#### 變更 1：具現化分區對應管理員物件： 

與同盟不同，Elastic Scale API 會透過 **ShardMapManager** 類別來與分區對應管理員互動。**ShardMapManager** 物件和分區對應的具現化方式如下：
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 變更 2：將連接遞送到適當的分區

使用同盟時，使用 USE FEDERATION 命令建立特定同盟成員的連接，如下所示：  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

使用 Elastic Scale API 時，透過[資料相依路由](./sql-database-elastic-scale-data-dependent-routing.md)並搭配 **RangeShardMap** 類別的 **OpenConnectionForKey** 方法，以建立特定分區的連接。 

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

本節包含必要的步驟，但可能無法解決所有發生的移轉案例。如需詳細資訊，請參閱 [Elastic Scale 的概念式概觀](./sql-database-elastic-scale-introduction.md)和 [API 參考資料](http://go.microsoft.com/?linkid=9862604) (英文)。

## 切換移出現有的同盟成員 

![Switch out the federation members for the shards][3]

一旦加入 Elastic Scale API 來修改應用程式之後，移轉同盟應用程式的最後一個步驟就是 **SWITCH OUT** (切換移出) 同盟成員 (如需詳細資訊，請參閱 MSDN 參考的 [ALTER FEDERATION (Azure SQL Database](http://msdn.microsoft.com/zh-tw/library/azure/hh597475.aspx))。針對特定的同盟成員發出 **SWITCH OUT** 的最終結果就是移除所有同盟條件約束和中繼資料，使同盟成員變成一般 Azure SQL Database，與任何其他 Azure SQL Database 沒有差別。 

請注意，針對同盟成員發出 **SWITCH OUT** 是單向作業，無法復原。一旦執行，產生的資料庫就不能加回到同盟，而且 USE FEDERATION 命令將不再適用於此資料庫。 

為了執行切換，ALTER FEDERATION 命令中已加入另一個引數來 SWITCH OUT (切換移出) 同盟成員。雖然可以針對個別的同盟成員發出此命令，但同盟移轉公用程式能夠以程式設計的方式逐一查看每個同盟成員，並執行切換作業。 

對所有現有的同盟成員執行切換之後，應用程式就完成移轉。  
同盟移轉公用程式能夠： 

1.    將同盟根複製到分區對應管理員。您可以選擇將現有的分區對應管理員放在新的 Azure SQL 資料庫 (建議) 或現有的同盟根資料庫。
2.    針對同盟中的所有同盟成員發出 SWITCH OUT。


##功能比較  
雖然 Elastic Scale 提供許多其他功能 (例如，[多分區查詢](./sql-database-elastic-scale-multishard-querying.md)、[分割及合併分區](./sql-database-elastic-scale-overview-split-and-merge.md)、[分區彈性](./sql-database-elastic-scale-elasticity.md)、[用戶端快取](./sql-database-elastic-scale-shard-map-management.md)等)，但有一些在 Elastic Scale 中不支援但值得注意的同盟功能。
  

- 使用 **FILTERING=ON**。Elastic Scale 目前不支援資料列層級的篩選。有一項因應措施是在對分區發出的查詢中建立篩選邏輯，如下所示： 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

產生的結果同於：

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Elastic Scale **分割**功能未完全上線。在分割作業時，每個個別 Shardlet 在移動期間會離線。
- Elastic Scale 分割功能需要手動佈建資料庫和管理結構描述。

## 其他考量

* Web 和 Business 版本及同盟將於 2015 年秋季停用。在移轉同盟應用程式的過程中，強烈建議在 Basic、Standard 和 Premium 版本上執行效能測試。 

* 在同盟成員上執行 SWITCH OUT 陳述式，可讓產生的資料庫利用所有 Azure SQL 資料庫功能 (亦即，新版本、備份、PITR、稽核等。) 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[從同盟根建立分區對應管理員]:#create-shard-map-manager
[修改現有的應用程式]:#Modify-the-Existing-Application
[切換移出現有的同盟成員]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
