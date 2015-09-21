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
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# 使用 PowerShell 將 Azure SQL Server 升級至 V12
 

本文說明如何使用定價層和彈性集區建議，將 SQL Database Server 升級至 V12。



## 必要條件 

若要使用 PowerShell 將伺服器升級至 V12，您必須安裝 Azure PowerShell 並加以執行，然後視您可能需要的版本將其切換為資源管理員模式，才能存取 Azure 資源管理員 PowerShell Cmdlet。

您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 來下載和安裝 Azure PowerShell 模組。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

建立和管理 Azure SQL Database 時所需的 Cmdlet，都位於 Azure 資源管理員模組中。當您開始使用 Azure PowerShell 時，系統會依預設匯入 Azure 模組中的 Cmdlet。若要切換至 Azure 資源管理員模組，請使用 **Switch-AzureMode** Cmdlet。

	Switch-AzureMode -Name AzureResourceManager

如果您收到警告，說明「Switch-AzureMode cmdlet 已經過時，未來版本將移除」。 您可以略過它並移至下一節。

如需詳細資訊，請參閱[將 Windows PowerShell 與資源管理員搭配使用](../powershell-azure-resource-manager.md)。



## 設定您的認證

若要針對 Azure 訂用帳戶執行 PowerShell Cmdlet，您必須先建立至 Azure 帳戶的存取權。執行以下項目，然後您會看到要輸入認證的登入畫面。請使用與登入 Azure 入口網站相同的電子郵件和密碼。

	Add-AzureAccount

成功登入後，您應該會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


## 選取您的 Azure 訂用帳戶

若要選取使用的訂用帳戶，您必須提供訂用帳戶識別碼 (**-SubscriptionId**) 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中的資訊，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊。

使用您的訂用帳戶資訊執行下列 Cmdlet，以設定您目前的訂用帳戶：

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

會針對您在上方剛選取的訂用帳戶執行下列命令。

## 取得建議

若要取得伺服器升級建議，請執行下列 Cmdlet：

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

如需詳細資訊，請參閱 [Azure SQL Database 彈性資料庫集區建議](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations)和 [Azure SQL Database 定價層建議](sql-database-service-tier-advisor.md)。



## 開始升級

若要開始升級伺服器，請執行下列 Cmdlet：

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


當您執行此命令時，升級程序將會開始。您可以自訂建議的輸出，並提供此 Cmdlet 的已編輯建議。


## 升級 Azure SQL Server


    # Adding the account
    #
    Add-AzureAccount
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName 
    $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## 自訂升級對應

如果建議不適合您的伺服器和商務案例，您可以選擇資料庫的升級方式，並將它們對應到單一或彈性資料庫。

將資料庫升級至彈性資料庫集區：

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


將資料庫升級至單一資料庫：

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## 相關資訊

- [Azure SQL Database 資源管理員 Cmdlet](https://msdn.microsoft.com/library/mt163521.aspx)
- [Azure SQL Database 服務管理 Cmdlet](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO2-->