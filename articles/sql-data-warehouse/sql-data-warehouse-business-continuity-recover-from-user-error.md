<properties
   pageTitle="從 SQL 資料倉儲中的使用者錯誤復原資料庫 | Microsoft Azure"
   description="從 SQL 資料倉儲中的使用者錯誤復原資料庫的步驟"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/17/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# 從 SQL 資料倉儲中的使用者錯誤復原資料庫

SQL 資料倉儲提供兩個核心功能，可從造成非預期之資料損毀或刪除的使用者錯誤復原。

- 還原即時資料庫
- 還原已刪除的資料庫

這兩個功能會在相同的伺服器上還原至新的資料庫。

有兩個不同的 API 支援 SQL 資料倉儲資料庫還原：Azure PowerShell 和 REST API。您可以使用其中一個來存取 SQL 資料倉儲還原功能。

## 復原即時資料庫
當使用者錯誤造成非預期的資料修改時，您可以在保留期限內將資料庫還原到任何還原點。即時資料庫的資料庫快照集至少每 8 小時會作用一次，並且保留 7 天。

### PowerShell

使用 Azure PowerShell 可以程式設計方式執行資料庫還原。如要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。您可以執行 Get-Module -ListAvailable -Name Azure 來檢查您的版本。本文是根據 Microsoft Azure PowerShell 1.0.4 版所撰寫。

若要還原資料庫，請使用 [Start-AzureSqlDatabaseRestore][] Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 列出資料庫的還原點 (需要 Azure 資源管理模式)。
5. 使用 RestorePointCreationDate 挑選出想要的還原點。
6. 將資料庫還原到想要的還原點。
7. 監視還原的進度。

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>").RestorePointCreationDate)[-10 .. -1]

	# Or for all restore points
	Get-AzureRmSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database name to restore
(Get-AzureRmSqlDatabase -ServerName "<YourServerName>" -ResourceGroupName "<YourResourceGroupName>").DatabaseName | where {$_ -ne "master" }
#or
Get-AzureRmSqlDatabase -ServerName "<YourServerName>" –ResourceGroupName "<YourResourceGroupName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabaseName "<YourDatabaseName>" -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為上述 Powershell Cmdlet 中的 -ServerName。

### REST API
使用 REST 可以程式設計方式執行資料庫還原。

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 使用[建立資料庫還原要求][]作業來開始還原。
3. 使用[資料庫作業狀態][]作業來追蹤還原狀態。

還原完成後，您可以設定為遵循[完成復原的資料庫][]指南來使用復原的資料庫。

## 復原已刪除的資料庫
若資料庫已刪除，您可以將已刪除的資料庫還原至刪除的時間點。Azure SQL 資料倉儲會在卸除資料庫前採用資料庫快照集，並保留 7 天。

### PowerShell
使用 Azure PowerShell 以程式設計方式還原已刪除的資料庫。如要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。

若要還原已刪除的資料庫，請使用 [Start-AzureSqlDatabaseRestore][] Cmdlet。

1. 開啟 Microsoft Azure PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之已刪除資料庫的訂用帳戶。
4. 從已刪除資料庫的清單中找出資料庫及其刪除日期。

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. 取得特定已刪除的資料庫，並開始進行還原。

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為上述 Powershell Cmdlet 中的 -ServerName。

### REST API
使用 REST 可以程式設計方式執行資料庫還原。

1.	使用[列出可還原的已卸除資料庫][]作業來列出所有可還原的已刪除資料庫。
2.	使用[取得可還原的已卸除資料庫][]作業來取得您想要還原之已刪除資料庫的詳細資料。
3.	使用[建立資料庫還原要求][]作業來開始還原。
4.	使用[資料庫作業狀態][]作業來追蹤還原狀態。

還原完成後，您可以設定為遵循[完成復原的資料庫][]指南來使用復原的資料庫。


## 後續步驟
若要深入了解其他 Azure SQL Database 版本的業務持續性功能，請閱讀 [Azure SQL Database 業務持續性概觀][]。


<!--Image references-->

<!--Article references-->
[Azure SQL Database 業務持續性概觀]: sql-database/sql-database-business-continuity.md
[完成復原的資料庫]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[建立資料庫還原要求]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[資料庫作業狀態]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[取得可還原的已卸除資料庫]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可還原的已卸除資料庫]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0224_2016-->