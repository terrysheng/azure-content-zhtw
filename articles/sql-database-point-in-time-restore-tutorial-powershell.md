<properties 
   pageTitle="使用 Azure PowerShell 中的還原時間點來還原 Azure SQL Database" 
   description="Point in Time Restore, Microsoft Azure SQL Database, restore database, recover database, Azure PowerShell, 還原時間點, 還原資料庫," 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# 使用 Azure PowerShell 中的還原時間點來還原 Azure SQL Database

> [AZURE.SELECTOR]
- [還原時間點 - 入口網站](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [還原時間點 - REST API](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## 概觀

本教學課程會向您示範如何使用 [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 中的還原時間點來還原 Azure SQL Database。Azure SQL Database 擁有內建備份以支援 Basic、Standard 及 Premium 服務層的自助還原時間點。

還原時間點會建立新的資料庫。服務會依據還原點使用的備份自動選擇服務層。請確定您在邏輯伺服器上擁有可用額度，以建立另一個資料庫。如果您想要求增加額 度，請洽詢 [Azure 技術支援](http://azure.microsoft.com/support/options/)。

## 限制與安全性

請參閱[使用 Azure 入口網站中的還原時間點來還原 Azure SQL Database](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)。

## 作法：使用 Azure PowerShell 中的還原時間點來還原 Azure SQL Database

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

您必須使用憑證式認證來執行下列 Cmdlet。如需詳細資訊，請參閱[如何安裝及設定 Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method) 中的 < *Use the certificate method*> 一節。

1. 使用 [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) Cmdlet 取得您想要還原的資料庫。指定以下參數：
	* **ServerName**：資料庫所在之伺服器的名稱。
	* **DatabaseName**：您要還原之資料庫的名稱。	

	`PS C:\>$Database = Get-AzureSqlDatabase -ServerName "myserver" -DatabaseName "mydb"`

2. 使用 [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) Cmdlet 來開始還原。指定以下參數：	
	* **SourceDatabase**：您想要還原的來源資料庫。
	* **TargetDatabaseName**：您還原資料庫後之目標伺服器的名稱。
	* **PointInTime**：您想要還原的目標時間點。

	將傳回的內容儲存至稱為 **$RestoreRequest** 的變數中。此變數包含用來監視還原狀態的還原要求識別碼。 

	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database -TargetDatabaseName "myrestoredDB" -PointInTime "2015-01-01 06:00:00"`

還原可能需要一些時間來完成。若要監視還原狀態，請使用 [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) Cmdlet 並指定以下參數：

* **ServerName**：您還原資料庫後之目標伺服器的名稱。
* **OperationGuid**：於步驟 2 中儲存在 **$RestoreRequest** 變數中的還原要求識別碼。

	`PS C:\>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

**State** 與 **PercentComplete** 欄位會顯示還原狀態。 

## 後續步驟

如需詳細資訊，請參閱下列主題：  

[Azure SQL Database 商務持續性](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL Database 備份和還原](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database 還原時間點 (部落格)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
