<properties 
   pageTitle="SQL Database 使用者錯誤復原" 
   description="了解如何使用 Azure SQL Database 的時間點還原 (PITR) 功能，從使用者錯誤、意外的資料損毀或已刪除的資料庫復原。" 
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
   ms.date="02/09/2016"
   ms.author="elfish"/>

# 從使用者錯誤復原 Azure SQL Database

Azure SQL Database 提供兩個核心功能，從使用者錯誤或非預期的資料修改復原。

- 時間點還原 
- 還原已刪除的資料庫

您可以在此[部落格文章](https://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)進一步了解這些功能。

Azure SQL Database 一律會還原至新的資料庫。所有「基本」、「標準」和「高階」資料庫都提供這些還原功能。

##時間點還原

發生使用者錯誤或非預期的資料修改時，時間點還原可用來將資料庫還原至資料庫保留期限內的任何時間點。

「基本」資料庫有 7 天的保留期，「標準」資料庫有 14 天的「保留期」，「高階」資料庫則有 35 天的保留期。若要深入了解資料庫保留，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

> [AZURE.NOTE] 還原資料庫會建立新的資料庫。請務必確定您要還原到的伺服器有足夠的 DTU 容量供新的資料庫使用。您可以[連絡支援人員](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)，要求增加此配額。

###Azure 入口網站
> [AZURE.NOTE] 針對彈性資料庫集區中的資料庫，Azure 入口網站只支援時間點還原至相同集區。如果您想將資料庫做為獨立資料庫進行時間點還原，請使用 REST API。

若要在 Azure 入口網站中使用「還原時間點」，請按照下列步驟執行。

1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Database]。
3. 瀏覽至您的資料庫，然後選取它。
4. 在資料庫刀鋒視窗的頂端，選取 [還原]。
5. 指定資料庫名稱、時間點，然後按一下 [建立]。
6. 資料庫還原程序就會開始，您可以使用畫面左側的 [通知] 監視程序。

###PowerShell
使用 PowerShell 以程式設計方式搭配 [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) Cmdlet 執行還原時間點。如需詳細的逐步執行，請[觀看此程序的影片](https://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/)。

> [AZURE.IMPORTANT] 本文包含適用於 Azure PowerShell 版本的命令，適用版本可高達*但不限於*版本 1.0 和更新版本。您可以使用 **Get-Module azure | format-table version** 命令來檢查 Azure PowerShell 的版本。

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###REST API 
使用 REST 可以程式設計方式執行資料庫還原。若要這樣做，請使用 [[建立資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)] 作業來建立還原要求，並將 [**建立模式**] 指定為 **PointInTimeRestore**。

##還原已刪除的資料庫
若資料庫被刪除，Azure SQL Database 可讓您將已刪除的資料庫還原至刪除的時間點。Azure SQL Database 會將已刪除的資料庫備份，儲存一段資料庫保留期間的時間。

已刪除資料庫的保留期間取決於資料庫所在的服務層，或是資料庫存在的天數，兩者中較少的天數。若要深入了解資料庫保留，請閱讀我們的[商務持續性概觀](sql-database-business-continuity.md)。

> [AZURE.NOTE] 還原資料庫會建立新的資料庫。請務必確定您要還原到的伺服器有足夠的 DTU 容量供新的資料庫使用。您可以[連絡支援人員](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)，要求增加此配額。

###Azure 入口網站
若要使用 Azure 入口網站來還原已刪除的資料庫，請按照下列步驟執行。

1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 [瀏覽]，然後選取 [SQL Server]。
3. 瀏覽至您的伺服器，然後選取它。
4. 在伺服器的刀鋒視窗中向下捲動至 [**作業**]，按一下 [**已刪除的資料庫**] 圖格。
5. 按一下您想要還原的已刪除資料庫。
6. 指定資料庫名稱，然後按一下 [建立]。
7. 資料庫還原程序就會開始，您可以使用畫面左側的 [通知] 監視程序。

###PowerShell
若要使用 PowerShell 還原已刪除的資料庫，請使用 [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) Cmdlet。如需詳細的逐步執行，請[觀看此程序的影片](https://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/)。

1. 從已刪除資料庫的清單中找出已刪除的資料庫及其刪除日期。
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. 取得特定已刪除的資料庫，並開始進行還原。

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###REST API 
使用 REST 可以程式設計方式執行資料庫還原。

1.	使用[列出可還原的已刪除資料庫](http://msdn.microsoft.com/library/azure/dn509562.aspx)操作來列出所有可還原的已刪除資料庫。
	
2.	使用[取得可還原的已刪除資料庫](http://msdn.microsoft.com/library/azure/dn509574.aspx)操作來取得您想要還原之已刪除資料庫的詳細資訊。

3.	使用[建立資料庫還原要求](http://msdn.microsoft.com/library/azure/dn509571.aspx)操作來開始還原。
	
4.	使用[資料庫操作狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx)操作來追蹤還原狀態。

<!---HONumber=AcomDC_0224_2016-->