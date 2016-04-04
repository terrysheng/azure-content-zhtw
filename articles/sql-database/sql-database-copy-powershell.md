<properties 
    pageTitle="使用 PowerShell 複製 Azure SQL Database | Microsoft Azure" 
    description="使用 PowerShell 建立 Azure SQL Database 的複本" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/21/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 複製 Azure SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



下列步驟說明如何利用 PowerShell 複製 SQL Database。資料庫複製作業會使用 [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) Cmdlet，將 SQL Database 複製到新的資料庫。副本是您在同一部伺服器或不同伺服器上建立的資料庫快照備份。

> [AZURE.NOTE] Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

複製程序完成時，新的資料庫是功能完整的資料庫，獨立於來源資料庫。複製完成時，新資料庫與來源資料庫在交易上一致。資料庫副本與來源資料庫的服務層和效能層級 (定價層) 相同。複製完成之後，副本會變成功能完整的獨立資料庫。可以個別管理登入、使用者和權限。


當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。


若要完成本文，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database。如果沒有 SQL Database，請遵循本文中以下的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- Azure PowerShell。您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 來下載和安裝 Azure PowerShell 模組。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。在登入畫面中，請輸入與登入 Azure 傳統入口網站相同的電子郵件和密碼。

	Add-AzureAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。當您的訂用帳戶執行了以下 Cmdlet 之後：

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行 **Select-AzureSubscription** 之後，您會返回 PowerShell 提示字元。如果您有一個以上的訂用帳戶，您可以執行 **Get-AzureSubscription** 並確認您要使用的訂用帳戶顯示 **IsCurrent: True**。


## 為您的特定環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和伺服器的特定值。

以您的環境的值取代預留位置值：

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## 將 SQL Database 複製到相同伺服器

這個命令會將複製資料庫要求提交給服務。視資料庫大小而定，複製作業可能需要一些時間才能完成。

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## 將 SQL Database 複製到不同伺服器

這個命令會將複製資料庫要求提交給服務。視資料庫大小而定，複製作業可能需要一些時間才能完成。

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## 監視複製作業的進度

執行 **Start-AzureSqlDatabaseExport** 之後，您即可檢查複製要求的狀態。如果您在要求之後立即執行此作業，通常會傳回 [狀態：擱置] 或 [狀態：執行中]，以便您多次執行這項作業，直到您在輸出中看到 [狀態：已完成] 為止。


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## 複製 SQL Database PowerShell 指令碼

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## 後續步驟

- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)
- [將資料庫匯出至 BACPAC](sql-database-export-powershell.md)


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0323_2016-->