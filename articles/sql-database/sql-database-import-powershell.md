<properties 
    pageTitle="使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database" 
    description="使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# 使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)


本文提供使用 PowerShell 匯入 BACPAC 以建立 Azure SQL Database 的說明。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。如需詳細資訊，請參閱[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)中的備份封裝 (.bacpac)。

資料庫是透過由 Azure 儲存體 Blob 容器匯入的 BACPAC 來建立。如果您的 Azure 儲存體沒有 .bacpac 檔案，可依照[建立和匯出 Azure SQL Database 的 BACPAC](sql-database-export-powershell.md) 中的步驟，加以建立。

> [AZURE.NOTE]Azure SQL Database 會自動為每個使用者資料庫建立並維護可供還原的備份。如需詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。


若要匯入 SQL Database，您需要下列項目：

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- 您要還原之資料庫的 .bacpac 檔案 (BACPAC)。BACPAC 必須位於 [Azure 儲存體帳戶 (傳統)](storage-create-storage-account.md) Blob 容器內。


> [AZURE.IMPORTANT]本文包含適用於 Azure PowerShell 版本的命令，適用版本最新至*但不包括*版本 1.0 和更新版本。您可以使用 **Get-Module azure | format-table version** 命令來檢查 Azure PowerShell 的版本。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

	Add-AzureAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID。您可以複製上一個步驟中顯示資訊的訂用帳戶 ID，或者，如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊，以取得詳細資訊。當您的訂用帳戶 ID 執行了以下 Cmdlet 之後：

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行 **Select-AzureSubscription** 之後，您會返回 PowerShell 提示字元。如果您有一個以上的訂用帳戶，您可以執行 **Get-AzureSubscription** 並確認所選取的訂用帳戶顯示 **IsCurrent : True**。


## 為您的環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和儲存體帳戶的特定值。

伺服器名稱必須為目前存在於上一個步驟所選取之訂用帳戶中的伺服器，而且是您要在其中建立資料庫的目的地伺服器。

資料庫名稱是您想要為新資料庫命名的名稱。

    $ServerName = "servername"
    $DatabaseName = "databasename"


下列變數是來自您 BACPAC 所在的儲存體帳戶。在 [Azure 入口網站](https://portal.azure.com)中，瀏覽您的儲存體帳戶以取得這些值。您可以在儲存體帳戶的刀鋒視窗中，依序按一下 [**所有設定**] 與 [**金鑰**]，以找到主要便捷鍵。

Blob 名稱是您想要用來建立資料庫之現有的 .bacpac 檔案名稱。您需要包含 .bacpac 副檔名。

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## 建立您的伺服器與儲存體帳戶的指標

執行 **Get-Credential** Cmdlet 時會開啟一個視窗，以詢問您的使用者名稱和密碼。請輸入您想要在其中建立資料庫的 SQL Server 系統管理員登入和密碼 (上述的 $ServerName) ，而不是您的 Azure 帳戶使用者名稱和密碼。

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## 匯入資料庫

這個命令會將匯入資料庫要求提交給服務。視資料庫大小而定，匯入作業可能需要一些時間才能完成。

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## 監視作業的進度

執行 **Start-AzureSqlDatabaseImport** 之後，您即可檢查要求的狀態。

如果您在要求之後立即檢查狀態，通常會傳回**擱置**或**執行中**狀態，並提供目前完成的百分比，您可以多次執行這項作業，直到在輸出中看到**狀態：已完成**為止。

執行此命令時，會提示您輸入密碼。請輸入您的 SQL Server 系統管理員登入和密碼。


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## SQL Database PowerShell 還原指令碼


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## 後續步驟

- [連接 SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)




## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->