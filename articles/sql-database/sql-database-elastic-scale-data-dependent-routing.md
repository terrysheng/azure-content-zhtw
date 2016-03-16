<properties 
	pageTitle="資料依存路由 | Microsoft Azure" 
	description="如何在 .NET 應用程式中將 ShardMapManager 類別用於資料相依路由 (Azure SQL Database 的彈性資料庫的一項功能)" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="torsteng;sidneyh"/>

#資料相依路由

**資料相依路由**是可使用查詢中的資料，將要求路由至適當的資料庫。這是使用分區化資料庫時的一種基本模式。要求內容也可能會用於路由要求，特別是如果分區化索引鍵不是查詢的一部分。在使用資料相依路由的應用程式中，每個特定的查詢或交易會限制每個要求只能存取單一資料庫。針對 SQL Azure Database Elastic 工具，此路由會在 ADO.NET 應用程式中使用 **[ShardMapManager 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)**來完成。

應用程式不需要在分區化環境中追蹤不同的連接字串或與不同資料片段相關聯的 DB 位置。相反地，[分區對應管理員](sql-database-elastic-scale-shard-map-management.md)會根據分區對應中的資料和分區化索引鍵的值 (應用程式要求的目標)，在必要時開啟正確資料庫的連接。此索引鍵通常是 *customer\_id*、*tenant\_id*、*date\_key*，或作為資料庫要求基本參數的其他一些特定的識別項)。

如需詳細資訊，請參閱 [Scaling Out SQL Server with Data Dependent Routing (使用資料相依路由相應放大 SQL Server)](https://technet.microsoft.com/library/cc966448.aspx)。

## 下載用戶端程式庫

若要取得類別，請安裝[彈性資料庫用戶端程式庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。

## 在資料相依路由應用程式中使用 ShardMapManager 

應用程式應該在初始化期間，使用 Factory 呼叫 **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** 來具現化 **ShardMapManager**。此範例中會初始化 **ShardMapManager** 及其包含的特定 **ShardMap**。這個範例示範 GetSqlShardMapManager 和 [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx) 方法。

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### 盡可能使用最低權限的認證來取得分區對應

如果應用程式不會自行操作分區對應，用於 Factory 方法中的認證在**全域分區對應**資料庫上應該只有唯讀權限。這些認證通常不同於用來對分區對應管理員開啟連接的認證。另請參閱[用來存取彈性資料庫用戶端程式庫的認證](sql-database-elastic-scale-manage-credentials.md)。

## 呼叫 OpenConnectionForKey 方法

**[ShardMap.OpenConnectionForKey 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx))** 傳回的 ADO.Net 連接可根據 **key** 參數的值，對適當的資料庫發出命令。**ShardMapManager** 會將分區資訊快取在應用程式中，因此這些要求通常不需要針對**全域分區對應**資料庫進行資料庫尋查。

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* **key** 參數做為分區對應中的查閱索引鍵，以決定要求的適當資料庫。 

* **connectionString** 只用來傳遞使用者認證給所需的連接。此 *connectionString* 中不含任何資料庫名稱或伺服器名稱，因為此方法會使用 **ShardMap** 來決定資料庫和伺服器。

* 若分區對應所在的環境可能變更，且資料列可能會移動到其他的資料庫成為分割或合併作業的結果，則 **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** 應設為 **ConnectionOptions.Validate**。這牽涉到在將連接傳遞至應用程式之前，對目標資料庫上的本機分區對應 (不是全域分區對應) 的簡短查詢。

如果本機分區對應驗證失敗 (表示快取不正確)，分區對應管理員會查詢全域分區對應來取得查閱的新正確值、更新快取，然後取得並傳回適當的資料庫連接。

唯有當應用程式在線上，分區對應的變更是非預期之時，才使用 **ConnectionOptions.None**。在此情況下，快取的值可假定為永遠正確，可放心地略過對於目標資料庫的額外往返驗證呼叫。這會減少資料庫流量。也可透過組態檔中的值來設定 **connectionOptions**，以指出在一段時間內是否預期有分區化變更。

此範例會利用名為 **customerShardMap** 的 **ShardMap** 物件，使用整數索引鍵值 **CustomerID**。

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

**OpenConnectionForKey** 方法會傳回新的已開啟連接至正確的資料庫。以這種方式使用連接仍可充分利用 ADO.Net 連接共用。只要一次一個分區就可滿足交易和要求，則已經使用 ADO.Net 的應用程式中只需要如此修改。

如果您的應用程式將非同步程式設計與 ADO.Net 搭配使用，則也可以使用 **[OpenConnectionForKeyAsync 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)**。其行為相當於 ADO.Net ****[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)** 方法的資料相依路由。

## 與暫時性錯誤處理整合 

在雲端中開發資料存取應用程式時，最佳做法就是確保應用程式會攔截暫時性錯誤，並且將作業重試數次之後才會擲回錯誤。[暫時性錯誤處理](https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx)) 中討論雲端應用程式的暫時性錯誤處理。
 
暫時性錯誤處理可以自然地與資料相依路由模式並存。主要需求是重試整個資料存取要求，包括用以取得資料相依路由連接的 **using** 區塊。上述範例可以改寫如下 (請注意反白顯示的變更)。

### 範例 – 資料相依路由與暫時性錯誤處理 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // 連接到分區取得客戶識別碼。
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // 執行簡單的命令 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Update completed"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


當您建置彈性資料庫範例應用程式時，自動會下載實作暫時性錯誤處理所需的封裝。另外也可以從[企業程式庫 - 暫時性錯誤處理應用程式區塊](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/) (英文) 取得封裝。請使用 6.0 版或更新版本。

## 交易一致性 

對於分區範圍內的所有作業，都保證交易式屬性。例如，透過資料相依路由提交的交易，都在連接的目標分區範圍內執行。目前無法將多個連接編列到交易中，因此對於跨分區執行的作業，不提供交易式保證。

## 後續步驟
若要中斷連結分區或重新附加分區，請參閱[使用 RecoveryManager 類別來修正分區對應問題](sql-database-elastic-database-recovery-manager.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0211_2016-->