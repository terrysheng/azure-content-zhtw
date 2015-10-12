<properties 
    pageTitle="使用 PowerShell 建立和匯出 Azure SQL Database 的 BACPAC" 
    description="使用 PowerShell 建立和匯出 Azure SQL Database 的 BACPAC" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/23/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 建立和匯出 SQL Database 的 BACPAC

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


本文說明如何使用 PowerShell 手動匯出您的 SQL Database BACPAC。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。如需詳細資訊，請參閱[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)中的備份封裝 (.bacpac)。

> [AZURE.NOTE]Azure SQL Database 會自動為每個使用者資料庫建立備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

BACPAC 會匯出至 Azure 儲存體 Blob 容器，以供您在作業順利完成之後下載。


若要完成本文，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- Azure SQL Database。如果沒有 SQL Database，請遵循本文中以下的步驟：[建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- 用來儲存 BACPAC 的 [Azure 儲存體帳戶](storage-create-storage-account.md)與 Blob 容器。目前，儲存體帳戶必須使用傳統的部署模型，因此建立儲存體帳戶時，請選擇**傳統**。
- Azure PowerShell。您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) 來下載和安裝 Azure PowerShell 模組。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

	Add-AzureAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID 或訂用帳戶名稱 (**-SubscriptionName**)。您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。當您的訂用帳戶執行了以下 Cmdlet 之後：

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行 **Select-AzureSubscription** 之後，您會返回 PowerShell 提示字元。如果您有一個以上的訂用帳戶，您可以執行 **Get-AzureSubscription** 並確認您要使用的訂用帳戶顯示 **IsCurrent: True**。


## 為您的特定環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和儲存體帳戶的特定值。

將伺服器和資料庫名稱取代為帳戶中目前存在的伺服器和資料庫。針對 blob 名稱，請輸入將建立的 BACPAC 檔名。輸入認何您想命名 BACPAC 檔案的任何名稱，但您必須包含.bacpac 副檔名。

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetobackup"
    $BlobName = "filename.bacpac"

在 [Azure Preview 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶以取得這些值。您可以在儲存體帳戶的刀鋒視窗中，依序按一下 [所有設定] 與 [金鑰]，以找到主要存取金鑰。

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## 建立您的伺服器與儲存體帳戶的指標

執行 **Get-Credential** Cmdlet 時會開啟一個視窗，以詢問您的使用者名稱和密碼。輸入 SQL Server 的系統管理員登入和密碼 (而不是 Azure 帳戶的使用者名稱和密碼)。

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## 匯出您的資料庫

這個命令會將匯出資料庫要求提交給服務。視資料庫大小而定，匯出作業可能需要一些時間才能完成。

    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## 監視匯出作業的進度

執行 **Start-AzureSqlDatabaseExport** 之後，您即可檢查要求的狀態。如果您在要求之後立即執行此作業，通常會傳回 [狀態：擱置] 或 [狀態：執行中]，您可以多次執行這項作業，直到在輸出中看到 [狀態：已完成] 為止。

執行此命令時，會提示您輸入密碼。請輸入您的 SQL Server 系統管理員密碼。


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## 匯出 SQL Database PowerShell 指令碼


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## 後續步驟

- [匯入 Azure SQL Database](sql-database-import-powershell.md)


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO1-->