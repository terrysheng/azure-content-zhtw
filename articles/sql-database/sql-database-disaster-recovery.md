<properties 
   pageTitle="SQL Database 嚴重損壞修復" 
   description="了解如何使用 Azure SQL Database 的作用中異地複寫、標準異地複寫和異地還原功能，從區域資料中心中斷或失敗情況復原資料庫。" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/09/2015"
   ms.author="elfish"/>

# 從中斷情況復原 Azure SQL Database

Azure SQL Database 提供下列功能，以從中斷復原：

- 主動式異地複寫 [(部落格)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- 標準異地複寫 [(部落格)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- 異地還原 [(部落格)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- 新的異地複寫功能 [(部落格)](https://azure.microsoft.com/blog/azure-sql-database-geo-replication-october-2015-update/)

若要了解如何為嚴重損壞情況做準備，以及何時可復原資料庫，請瀏覽我們的[業務續航力的設計](sql-database-business-continuity-design.md)頁面。

##何時起始復原 

復原作業會影響應用程式。它需要變更 SQL 連接字串，並且可能會導致永久的資料遺失。因此，只有在中斷情況可能持續超過應用程式的 RTO 時，才應該執行這項作業。將應用程式部署至生產環境之後，您應該定期監視應用程式健全狀況，並利用下列資料點判斷是否需要復原：

1. 從應用程式層到資料庫的連接發生永久性失敗。
2. 您的 Azure 入口網站顯示有關區域中影響廣泛之事件的警示。

> [AZURE.NOTE]復原資料庫之後，您可以遵循[在復原後設定資料庫](#postrecovery)指南，設定資料庫以供使用。

## 容錯移轉至異地複寫的次要資料庫
> [AZURE.NOTE]您必須進行設定，以取得可用於容錯移轉的次要資料庫。異地複寫僅適用於標準和高階資料庫。了解[如何設定異地複寫](sql-database-business-continuity-design.md)

###Azure 入口網站
使用 Azure 入口網站終止與地理複寫次要資料庫的連續複製關聯性。

1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 巡覽至您的資料庫，然後加以選取。 
4. 在資料庫分頁底部選取 [異地複寫對應]。
4. 在 [次要] 下，以滑鼠右鍵按一下含有您要復原之資料庫名稱的資料列，然後選取 [容錯移轉]。

###PowerShell
使用 [Set-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx) Cmdlet，進而以 PowerShell 起始容錯移轉至異地複寫次要資料庫。
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###REST API 
使用 REST 以程式設計方式起始容錯移轉至次要資料庫。

1. 使用[取得複寫連結](https://msdn.microsoft.com/library/mt600778.aspx)作業取得特定次要資料庫的複寫連結。
2. 使用[設定次要資料庫做為主要資料庫](https://msdn.microsoft.com/library/mt582027.aspx)，且允許資料遺失，容錯移轉至次要資料庫。 

## 使用異地還原進行復原

如果發生資料庫中斷的情況，您可以使用異地還原，從資料庫最新的異地備援備份來復原資料庫。

> [AZURE.NOTE]復原資料庫會建立新的資料庫。請務必確定您要復原到的伺服器有足夠的 DTU 容量供新的資料庫使用。您可以[連絡支援人員](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)，要求增加此配額。

###Azure 入口網站
若要使用 Azure 入口網站中的異地還原還原 SQL Database，請使用下列步驟。

1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [新增]，然後依序選取 [資料和儲存體] 和 [SQL Database]。
2. 選取 [備份] 做為來源，然後選取您要從中復原的異地備援備份。
3. 指定其餘資料庫內容，然後按一下 [建立]。
4. 資料庫還原程序就會開始，您可以使用畫面左側的 [通知] 監視程序。

###PowerShell 
若要使用異地還原搭配 PowerShell 還原 SQL Database，請使用 [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx) Cmdlet 啟動異地還原要求。

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###REST API 

使用 REST 可以程式設計方式執行資料庫復原。

1.	使用[列出可復原的資料庫](http://msdn.microsoft.com/library/azure/dn800984.aspx)作業來取得可復原的資料庫清單。
	
2.	使用[取得可復原的資料庫](http://msdn.microsoft.com/library/azure/dn800985.aspx)作業來取得您要復原的資料庫。
	
3.	使用[建立資料庫復原要求](http://msdn.microsoft.com/library/azure/dn800986.aspx)作業來建立復原要求。
	
4.	使用[資料庫作業狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)作業來追蹤復原狀態。
 
## 在復原之後設定資料庫<a name="postrecovery"></a>

這份工作檢查清單可以用來幫助您準備產生復原的資料庫。

### 更新連接字串

請確認您應用程式的連接字串是指向剛復原的資料庫。如果有以下其中一種情況，請更新連接字串：

  + 復原的資料庫使用的名稱與來源資料庫不同
  + 復原的資料庫和來源伺服器位於不同的伺服器

如需變更連接字串的詳細資訊，請參閱[連接至 Azure SQL Database：重要的建議](sql-database-connect-central-recommendations.md)。
 
### 修改防火牆規則
請確認伺服器層級和資料庫層級的防火牆規則，並確定已啟用您用戶端電腦或 Azure 與伺服器以及剛復原之資料庫的連接。如需詳細資訊，請參閱[如何：進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)。

### 確認伺服器登入和資料庫使用者

確認應用程式使用的所有登入，是否都在主控您已復原資料庫的伺服器上。重新建立缺少的登入，並將適當的已復原資料庫之權限授與登入。如需詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入](sql-database-manage-logins.md)。

請確認已復原資料庫中的每位資料庫使用者，是否都和有效的伺服器登入相關聯。使用 ALTER USER 陳述式，將使用者對應至有效的伺服器登入。如需詳細資訊，請參閱 [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486)。


### 設定遙測警示

確認現有的警示規則設定是否對應至復原的資料庫。如果有以下其中一種情況，請更新設定：

  + 復原的資料庫使用的名稱與來源資料庫不同
  + 復原的資料庫和來源伺服器位於不同的伺服器

如需資料庫警示規則的詳細資訊，請參閱[接收警示通知](insights-receive-alert-notifications.md)和[追蹤服務健全狀況](insights-service-health.md)。


### 啟用稽核

如果需要稽核才能存取您的資料庫，則您必須在資料庫復原之後啟用稽核。用戶端應用程式必須在 *.database.secure.windows.net 的模式中使用安全連接字串，才能有良好的稽核指標。如需詳細資訊，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing-get-started.md)。

<!---HONumber=Nov15_HO4-->