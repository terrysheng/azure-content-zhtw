<properties 
    pageTitle="建立彈性資料庫集區 (PowerShell) | Microsoft Azure" 
    description="了解如何藉由建立彈性資料庫集區來管理多個資料庫，進而使用 PowerShell 來相應放大Azure SQL Database 資源。" 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# 建立彈性資料庫集區 (PowerShell) 

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


了解如何使用 PowerShell Cmdlet 建立[彈性資料庫集區](sql-database-elastic-pool.md)。

> [AZURE.NOTE] 彈性資料庫集區目前為預覽版，且僅能搭配 SQL Database V12 伺服器使用。如果您有 SQL Database V11 伺服器，您可以在單一步驟中[使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server-portal.md)。


您必須是執行 Azure PowerShell 1.0 或更高版本。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。



## 建立彈性資料庫集區

[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) Cmdlet 會建立彈性資料庫集區。

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## 在彈性資料庫集區內建立新的彈性資料庫

若要直接在集區內建立新的資料庫，請使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) Cmdlet，並設定 ElasticPoolName 參數。


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## 將獨立資料庫移入彈性資料庫集區

若要將現有資料庫移入集區，請使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) Cmdlet，並設定 ElasticPoolName 參數。

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## 建立彈性資料庫集區 PowerShell 範例

此指令碼會建立新的伺服器，因此當它提示您輸入使用者名稱和密碼時，請輸入新伺服器 (而非 Azure 認證) 的系統管理員登入和系統管理員密碼。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## 後續步驟

- [管理集區](sql-database-elastic-pool-manage-powershell.md)
- [建立彈性工作](sql-database-elastic-jobs-overview.md) 彈性工作有助於對集區中任意數目的資料庫執行 T-SQL 指令碼。


## 彈性資料庫參考

如需關於彈性資料庫和彈性資料庫集區的詳細資訊，請參閱[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。

<!---HONumber=AcomDC_0316_2016-->