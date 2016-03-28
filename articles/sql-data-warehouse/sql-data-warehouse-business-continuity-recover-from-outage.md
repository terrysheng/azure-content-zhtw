<properties
   pageTitle="從 SQL 資料倉儲中斷復原資料庫 | Microsoft Azure"
   description="從 SQL 資料倉儲中斷復原資料庫的步驟。"
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
   ms.date="03/09/2016"
   ms.author="sahajs;barbkess"/>

# 從 SQL 資料倉儲中斷復原資料庫

異地還原可從異地備援備份還原資料庫，進而建立新的資料庫。您可以在任何 Azure 區域中的任何伺服器上建立資料庫。異地還原使用異地備援備份做為其來源，因此即使因為中斷而無法存取資料庫，也能用來復原資料庫。除了從中斷復原之外，異地還原還可以用於其他案例，例如，將資料庫移轉或複製到不同的伺服器或區域。


## 何時起始復原
復原作業需要在復原時變更 SQL 連接字串，並且可能會導致永久的資料遺失。因此，只有在中斷情況可能持續超過應用程式的 RTO 時，才應該執行這項作業。利用下列資料點判斷是否需要復原︰

- 資料庫的永久性連線失敗。
- 您的 Azure 入口網站顯示有關區域中影響廣泛之事件的警示。


## 使用異地還原進行復原
復原資料庫可從最新的異地備援備份建立新的資料庫。請務必確定您要復原到的伺服器有足夠的 DTU 容量供新的資料庫使用。您可以[連絡支援人員][]，要求增加此配額。


### Azure 入口網站
1. 登入 [Azure 入口網站][]
2. 在畫面左側選取 [+ 新增]，然後依序選取 [資料和儲存體] 和 [SQL 資料倉儲]
3. 選取 [備份] 做為來源，然後選取您要從中復原的異地備援備份。
4. 指定其餘資料庫內容，然後按一下 [建立]
5. 資料庫還原程序就會開始，您可以使用 [通知] 監視程序。


### PowerShell
使用 Azure PowerShell 可以程式設計方式執行資料庫復原。如要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。您可以執行 Get-Module -ListAvailable -Name Azure 來檢查您的版本。本文是根據 Microsoft Azure PowerShell 1.0.4 版所撰寫。

若要復原資料庫，請使用 [Start-AzureSqlDatabaseRecovery][] Cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 取得您想要復原的資料庫。
5. 建立資料庫的復原要求。
6. 監視復原的進度。

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為上述 Powershell Cmdlet 中的 -ServerName。

### REST API
使用 REST 可以程式設計方式執行資料庫復原。

1. 使用[列出可復原的資料庫][]作業來取得可復原的資料庫清單。
2. 使用[取得可復原的資料庫][]作業來取得您要復原的資料庫。
3. 使用[建立資料庫復原要求][]作業來建立復原要求。
4. 使用[資料庫作業狀態][]作業來追蹤復原狀態。



## 在復原之後設定資料庫
這份檢查清單可以用來幫助您準備產生復原的資料庫。

1. **更新連接字串**：確認您用戶端工具的連接字串指向剛復原的資料庫。
2. **修改防火牆規則**：確認目標伺服器上的防火牆規則，並確定已啟用您用戶端電腦或 Azure 與伺服器以及剛復原之資料庫的連接。
3. **確認伺服器登入和資料庫使用者**：確認應用程式使用的所有登入是否都存在於主控您已復原的資料庫的伺服器上。重新建立缺少的登入，並將適當的已復原資料庫之權限授與登入。 
4. **啟用稽核**：如果需要稽核才能存取您的資料庫，則您必須在資料庫復原之後啟用稽核。

如果來源資料庫是啟用 TDE，則復原的資料庫將是啟用 TDE。


## 後續步驟
若要深入了解其他 Azure SQL Database 版本的業務持續性功能，請閱讀 [Azure SQL Database 業務持續性概觀][]。


<!--Image references-->

<!--Article references-->
[Azure SQL Database 業務持續性概觀]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[列出可復原的資料庫]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[取得可復原的資料庫]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[建立資料庫復原要求]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[資料庫作業狀態]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[Azure 入口網站]: https://portal.azure.com/
[連絡支援人員]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0316_2016-->