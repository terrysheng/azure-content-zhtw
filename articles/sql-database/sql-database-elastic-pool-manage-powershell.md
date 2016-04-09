<properties 
    pageTitle="管理彈性資料庫集區 (PowerShell) | Microsoft Azure" 
    description="了解如何使用 PowerShell 管理彈性資料庫集區。"  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# 監視和管理彈性資料庫集區 (PowerShell) 

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


了解如何使用 PowerShell Cmdlet 管理[彈性資料庫集區](sql-database-elastic-pool.md)。

如需常見的錯誤碼，請參閱 [SQL Database 用戶端應用程式的 SQL 錯誤碼：資料庫連線錯誤和其他問題](sql-database-develop-error-messages.md)。

> [AZURE.NOTE] 彈性資料庫集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。如果您有 SQL Database V11 伺服器，您可以在單一步驟中[使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server-portal.md)。

您必須是執行 Azure PowerShell 1.0 或更高版本。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。




## 在彈性資料庫集區內建立新的彈性資料庫

若要直接在集區內建立新的資料庫，請使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) Cmdlet，並設定 **ElasticPoolName** 參數。


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## 將獨立資料庫移入彈性資料庫集區

若要將現有資料庫移入集區，請使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) Cmdlet，並設定 **ElasticPoolName** 參數。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## 變更彈性資料庫集區的效能設定

若要變更彈性資料庫集區的效能設定，請使用 [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) Cmdlet。

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## 取得彈性資料庫集區作業的狀態

您可以追蹤彈性資料庫集區作業的狀態，包括使用 [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) Cmdlet 建立和更新作業。

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## 取得將彈性資料庫移入和移出彈性資料庫集區的狀態

您可以追蹤彈性資料庫集區作業的狀態，包括使用 [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) Cmdlet 建立和更新作業。

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 取得彈性資料庫集區的使用量資料

可以用資源集區限制的百分比來擷取的度量：

* 平均 CPU 使用率：cpu\_percent 
* 平均 IO 使用率：data\_io\_percent 
* 平均記錄檔使用率：log\_write\_percent 
* 平均記憶體使用率：memory\_percent 
* 平均 eDTU 使用量 (做為 CPU/IO/記錄檔使用率的最大值) - DTU\_percent 
* 並行使用者要求 (背景工作) 的數目上限：max\_concurrent\_requests 
* 並行使用者工作階段的數目上限：max\_concurrent\_sessions 
* 彈性集區的總儲存體大小：storage\_in\_megabytes 


度量資料粒度/保留期限：

* 系統會以 5 分鐘的資料粒度傳回資料。  
* 資料會保留 14 天。  


此 Cmdlet 和 API 會將一次呼叫中可擷取的資料列限制為 1,000 個 (大約是 3 天份且資料粒度為 5 分鐘的資料)。但可以用不同的開始/結束時間間隔來多次呼叫此命令，以擷取更多資料。


擷取度量：

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

重複呼叫並附加資料來取得其他日期：

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
格式化資料表：

    $table = Format-MetricsAsTable $metrics 

匯出為 CSV 檔案：

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## 取得彈性資料庫的資源消耗度量

除了以下的語意差異外，這些 API 與目前用於監視獨立資料庫之資源使用率的 (V12) API 相同。

* 這個擷取的 API 度量，會以針對該彈性資料庫集區所設定之每個 databaseDtuMax (或是 CPU、IO 等基礎度量的相等上限) 的百分比來表示。例如，這些度量有其中一項的使用率為 50%，則表示特定資源的消耗量佔該資源在父彈性資料庫集區中，每個資料庫上限限制的 50%。 

取得度量：

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

視需要重複呼叫並附加資料，來取得其他日期：

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

格式化資料表：

    $table = Format-MetricsAsTable $metrics 

匯出為 CSV 檔案：

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## 監視和管理彈性資料庫集區 PowerShell 範例


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 後續步驟

- [建立彈性工作](sql-database-elastic-jobs-overview.md) 彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。


## 彈性資料庫參考

如需關於彈性資料庫和彈性資料庫集區的詳細資訊，包括 API 和錯誤詳細資料，請參閱[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。

<!---HONumber=AcomDC_0323_2016-->