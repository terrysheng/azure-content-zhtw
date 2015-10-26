<properties 
	pageTitle="使用 PowerShell 將 Azure SQL Server 升級至 V12" 
	description="使用 PowerShell 將 Azure SQL Server 升級至 V12。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# 使用 PowerShell 升級至 SQL Database V12


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


本文將說明如何使用 PowerShell 升級至 SQL Database V12。

在升級至 SQL Database V12 的過程中，也必須將所有的 Web 和商務資料庫都更新至較新的服務層。下列指示包括使用定價層和彈性集區建議協助在伺服器上[更新所有的 Web 和商務資料庫](sql-database-upgrade-new-service-tiers.md)。


## 必要條件 

若要使用 PowerShell 將伺服器升級至 V12，您必須安裝 Azure PowerShell 並加以執行，然後視您可能需要的版本將其切換為資源管理員模式，才能存取 Azure 資源管理員 PowerShell Cmdlet。

> [AZURE.IMPORTANT]從 Azure PowerShell 1.0 Preview 起，不再提供 Switch-AzureMode Cmdlet，且 Azure 資源管理員模組中的 Cmdlet 已重新命名。本文中的範例使用新的 PowerShell 1.0 Preview 命名慣例。如需詳細資訊，請參閱[淘汰 Azure PowerShell 中的 Switch-AzureMode](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell) (英文)。

若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行，且由於 Switch-AzureMode 已移除，因此您應該執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 下載並安裝最新的Azure PowerShell。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。


## 設定您的認證並選取您的訂用帳戶

若要針對 Azure 訂用帳戶執行 PowerShell Cmdlet，您必須先建立至 Azure 帳戶的存取權。執行以下項目，然後您會看到要輸入認證的登入畫面。請使用與登入 Azure 入口網站相同的電子郵件和密碼。

	Add-AzureAccount

成功登入後，您應該會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。

若要選取使用的訂用帳戶，您必須提供訂用帳戶識別碼 (**-SubscriptionId**) 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中的資訊，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊。

使用您的訂用帳戶資訊執行下列 Cmdlet，以設定您目前的訂用帳戶：

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

會針對您在上方剛選取的訂用帳戶執行下列命令。

## 取得建議

若要取得伺服器升級建議，請執行下列 Cmdlet：

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

如需詳細資訊，請參閱 [Azure SQL Database 彈性資料庫集區建議](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)和 [Azure SQL Database 定價層建議](sql-database-service-tier-advisor.md)。



## 開始升級

若要開始升級伺服器，請執行下列 Cmdlet：

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


當您執行此命令時，升級程序將會開始。您可以自訂建議的輸出，並提供此 Cmdlet 的已編輯建議。


## 升級伺服器


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## 自訂升級對應

如果建議不適合您的伺服器和商務案例，您可以選擇資料庫的升級方式，並將它們對應到單一或彈性資料庫。

ElasticPoolCollection 和 DatabaseCollection 參數都是選擇性項目：
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## 相關資訊

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->