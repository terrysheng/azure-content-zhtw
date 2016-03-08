<properties 
    pageTitle="使用 PowerShell 新增 SQL Database 設定| Microsoft Azure" 
    description="了解如何使用 PowerShell 建立新的 SQL Database。透過 PowerShell cmdlet 可以管理一般資料庫設定工作。" 
    keywords="建立新的 sql database,資料庫設定"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# 建立新的 SQL Database 並使用 PowerShell Cmdlet 執行一般資料庫設定工作 

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


了解如何建立新的 SQL Database 並使用 PowerShell Cmdlet 執行一般資料庫設定工作。


若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

- 如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。


## 設定您的認證並選取您的訂用帳戶

既然您已在執行 Azure 資源管理員模組，便可以存取建立 SQL Database 所需的所有必要 Cmdlet。

您必須先建立 Azure 帳戶的存取權，以便執行以下 Cmdlet，然後您會看到要輸入認證的登入畫面。請使用與登入 Azure 入口網站相同的電子郵件和密碼。

	Add-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID。您可以複製上一個步驟中的資訊，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureRmSubscription** Cmdlet，然後從結果集中複製所需的訂用帳戶資訊。當您的訂用帳戶執行了以下 Cmdlet 之後：

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行 **Select-AzureRmSubscription** 之後，您會返回 PowerShell 提示字元。如果您有一個以上的訂用帳戶，您可以執行 **Get-AzureRmSubscription** 並確認您要使用的訂用帳戶顯示 **IsCurrent: True**。

## 資料庫設定：建立資源群組、伺服器和防火牆規則

現在您有權在您選取的 Azure 訂用帳戶下執行 Cmdlet，因此下一步是建立含有伺服器的資源群組，以在伺服器中建立資料庫。為了使用您選擇的任何有效位置，您可以編輯下一個命令。執行 **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** 以取得有效位置的清單。

執行下列命令以建立新的資源群組：

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

成功建立新的資源群組之後，您會在畫面上看到包含 **ProvisioningState : Succeeded** 的資訊。


### 建立伺服器 

SQL Database 會建立在 Azure SQL Database 伺服器內。執行 **New-AzureRmSqlServer** 以建立新的伺服器。用您的伺服器名稱取代 ServerName。這必須是所有 Azure SQL Server 的唯一名稱，因此伺服器名稱若被佔用，您就會收到錯誤訊息。另外值得注意的是，此命令可能需要數分鐘才能完成。您可以編輯此命令以使用您選擇的任何有效位置，但您應該使用您在上一個步驟中建立資源群組時使用的相同位置。

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

當您執行此命令時，會隨即開啟向您詢問 [使用者名稱] 和 [密碼] 的視窗。這不是您的 Azure 認證，請輸入要用於新伺服器之系統管理員認證的使用者名稱和密碼。

成功建立伺服器後，會出現伺服器詳細資料。

### 設定伺服器防火牆規則以允許存取伺服器

建立可存取伺服器的防火牆規則。執行以下命令，用對您電腦有效的值，取代開頭和結尾 IP 位址。

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

成功建立防火牆規則後，會出現規則詳細資料。

若要允許其他 Azure 服務存取伺服器，則新增防火牆規則並且將 tartIpAddress 和 EndIpAddress 都設為 0.0.0.0。請注意，這可讓來自任何 Azure 訂用帳戶的 Azure 流量存取伺服器。

如需詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。


## 建立新的 SQL Database

現在您已擁有資源群組、伺服器和設定完成的防火牆規則，便可以存取伺服器。

下列命令會在具有 S1 效能層級的標準服務層建立新的 (空白) SQL Database︰


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


成功建立資料庫後，會出現資料庫詳細資料。

## 建立新的 SQL Database PowerShell 指令碼

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## 後續步驟
建立新的 SQL Database 並執行基本的資料庫設定工作之後，您就可以執行下列作業：

- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)


## 其他資源

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0302_2016-->