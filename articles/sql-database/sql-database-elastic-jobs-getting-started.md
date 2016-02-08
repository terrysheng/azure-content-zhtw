<properties
	pageTitle="開始使用彈性資料庫工作"
	description="如何使用彈性資料庫工作"
	services="sql-database"
	documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="sidneyh; ddove" />

# 開始使用彈性資料庫工作

Azure SQL database 的彈性資料庫工作 (預覽) 可讓您跨越多個資料庫可靠地執行 T-SQL 指令碼，同時自動重試並提供最終完成保證。如需有關彈性資料庫工作功能的詳細資訊，請參閱[功能概觀頁](sql-database-elastic-jobs-overview.md)。

本主題會延伸[彈性資料庫工具入門](sql-database-elastic-scale-get-started.md)中可找到的範例。完成時，您將會：了解如何建立和管理工作，該工作管理一組相關資料庫。

## 必要條件

下載並執行[彈性資料庫工具範例入門](sql-database-elastic-scale-get-started.md)。

## 使用範例應用程式建立分區對應管理員

在這裡，您將建立分區對應管理員以及數個分區，接著插入資料至分區。若您的分區設定中已有分區資料，則可以略過下列步驟並移至下一節。

1. 建置並執行**彈性資料庫工具入門**範例應用程式。遵循步驟，直到[下載及執行範例應用程式](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)小節的步驟 7。在步驟 7 結束時，您會看到下列的命令提示字元：

	![命令提示字元][1]

2.  在命令視窗中，輸入 "1"，然後按 **Enter**。這會建立分區對應管理員，並加入兩個分區到伺服器。接著輸入 "3"，然後按 **Enter**；重複此動作四次。這會在您的分區中插入範例資料列。

3.  [Azure 入口網站](https://portal.azure.com)應該會在您的 v12 伺服器中顯示三個新的資料庫：

	![Visual Studio 確認][2]

	目前，我們將建立自訂資料庫集合，反映分區對應中的所有資料庫。這可讓我們建立和執行工作，跨分區新增新資料表。

在這裡我們通常會建立分區對應目標，使用 **New-AzureSqlJobTarget** Cmdlet。分區對應管理員資料庫必須設定為資料庫目標，然後將特定分區對應指定為目標。相反地，我們列舉伺服器中的所有資料庫，並且將資料庫新增至 master 資料庫除外的新的自訂集合。

##建立自訂集合並將伺服器中的所有資料庫新增至 master 除外的自訂集合目標。


	$customCollectionName = "dbs_in_server"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
	$ResourceGroupName = "ddove_samples"
	$ServerName = "samples"
	$dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
	$dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
	
## 針對跨資料庫執行建立 T-SQL 指令碼

	$scriptName = "NewTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
	BEGIN
		CREATE TABLE Test(
			TestId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

##建立工作以跨自訂資料庫群組執行指令碼

	$jobName = "create on server dbs"
	$scriptName = "NewTable"
	$customCollectionName = "dbs_in_server"
	$credentialName = "ddove66"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job


##執行工作 

下列 PowerShell 指令碼可以用來執行現有的工作：

更新下列變數以反映要執行的所需工作名稱：

	$jobName = "create on server dbs"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## 擷取單一工作執行狀態

使用相同 **Get-AzureSqlJobExecution** Cmdlet 搭配 **IncludeChildren** 參數，以檢視子工作執行的狀態，也就是根據工作目標之每個資料庫的每個工作執行特定狀態。

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## 檢視跨多個工作執行的狀態

**Get-AzureSqlJobExecution** Cmdlet 具有多個選用參數，可用來顯示多個工作執行、透過提供的參數篩選。以下示範一些使用 Get-AzureSqlJobExecution 的可能方式：

擷取所有作用中最上層工作執行：

	Get-AzureSqlJobExecution

擷取所有最上層工作執行，包括非使用中工作執行：

	Get-AzureSqlJobExecution -IncludeInactive

擷取已提供工作執行 ID 的所有子工作執行，包括非使用中工作執行：

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

擷取使用排程/工作組合建立的所有工作執行，包括非使用中工作：

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

擷取以指定的分區對應為目標的所有工作，包括非使用中工作：

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

擷取以指定的自訂集合為目標的所有工作，包括非使用中工作：

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
擷取特定工作執行內的工作作業執行的清單：

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

擷取工作作業執行詳細資料：

下列 PowerShell 指令碼可用來檢視工作作業執行的詳細資料，在偵錯執行失敗時特別有用。

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## 擷取工作作業執行內的失敗

JobTaskExecution 物件包括作業生命週期的屬性和訊息屬性。如果工作作業執行失敗，生命週期屬性將設為*失敗*，且訊息屬性將設為產生的例外狀況訊息和其堆疊。如果工作不成功，務必檢視指定作業不成功的工作作業的詳細資料。

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## 等候工作執行完成

下列 PowerShell 指令碼可以用來等候工作作業完成：

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## 建立自訂執行原則

彈性資料庫工作支援建立自訂執行原則，可以在啟動作業時套用。
  
執行原則目前允許定義：

* 名稱：執行原則的識別碼。
* 工作逾時：彈性資料庫工作取消工作之前的總時間。
* 初始重試間隔：第一次重試之前等候的間隔。
* 最大重試間隔：要使用的重試間隔端點。
* 重試間隔輪詢係數：用來計算重試之間下一個間隔的係數。使用下列公式：(初始重試間隔) * Math.pow ((間隔輪詢係數), (重試次數) -2)。 
* 嘗試上限：工作內執行的重試嘗試數目上限。

預設的執行原則會使用下列值：

* 名稱：預設執行原則
* 工作逾時：1 週
* 初始重試間隔：100 毫秒
* 最大重試間隔：30 分鐘
* 重試間隔係數：2
* 嘗試上限：2,147,483,647

建立想要的執行原則：

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### 更新自訂執行原則

更新要更新之想要的執行原則：

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## 取消工作

彈性資料庫工作支援取消工作要求。如果彈性資料庫工作偵測到目前正在執行工作的取消要求，它會嘗試停止工作。

彈性資料庫工作有兩種不同的方式可以執行取消作業：

1. 取消目前正在執行的作業：如果在作業正在執行時偵測到取消，將會在目前正在執行的作業層面內嘗試取消。例如：當嘗試取消時，如果有長時間執行查詢目前正在執行，將會嘗試取消查詢。
2. 取消作業重試：如果控制執行緒在啟動作業執行之前偵測到取消，控制執行緒會避免啟動作業，並且將要求宣告為已取消。

如果針對父工作要求工作取消，則會對父工作和其所有子工作執行取消要求。
 
若要提交取消要求，請使用 **Stop-AzureSqlJobExecution** Cmdlet 並設定 **JobExecutionId** 參數。

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## 依據名稱和工作的歷程記錄刪除工作

彈性資料庫工作支援非同步刪除工作。工作可以標示為刪除，系統將會在工作的工作執行皆已完成之後，刪除工作和其所有工作歷程記錄。系統不會自動取消作用中的工作執行。

而是必須叫用 Stop-AzureSqlJobExecution 以取消作用中的工作執行。

若要觸發工作刪除，請使用 **Remove-AzureSqlJob** Cmdlet 並設定 **JobName** 參數。

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## 建立自訂資料庫目標
自訂資料庫目標可以在彈性資料庫工作中定義，可用來直接執行或包含在自訂資料庫群組內。由於**彈性資料庫集區**尚未透過 PowerShell API 直接支援，您只需建立自訂資料庫目標和自訂資料庫集合目標，它們包含集區中的所有資料庫。

設定下列變數以反映所需的資料庫資訊：

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## 建立自訂資料庫集合目標
可以定義自訂資料庫集合目標，以跨多個已定義資料庫目標執行。建立資料庫群組之後，資料庫可以與自訂集合目標相關聯。

設定下列變數以反映所需的自訂集合目標組態：

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### 將資料庫新增至自訂資料庫集合目標

資料庫目標可以與自訂資料庫集合目標相關聯，以建立資料庫群組。每當建立以自訂資料庫集合目標為目標的工作時，都會擴展為以關聯至執行中的群組的資料庫為目標。

將所需的資料庫新增至特定自訂集合：

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### 檢閱自訂資料庫集合目標內的資料庫

使用 **Get-AzureSqlJobTarget** Cmdlet 以擷取自訂資料庫集合目標內的子資料庫。
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### 建立工作以跨自訂資料庫集合目標執行指令碼

使用 **New-AzureSqlJob** Cmdlet 以根據自訂資料庫集合目標定義的資料庫群組建立工作。彈性資料庫工作會將工作展開成多個子工作，每個子工作對應至與自訂資料庫集合目標相關聯的資料庫，並且確保指令碼會針對每個資料庫執行。再次重申，很重要的是指令碼具有等冪處理重試的彈性。

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## 跨資料庫的資料集合

**彈性資料庫工作**支援跨資料庫群組執行查詢，並將結果傳送至指定的資料庫資料表。可以在事實之後查詢資料表，以查看每個資料庫的查詢結果。這提供跨多個資料庫執行查詢的非同步機制。例如其中一個資料庫暫時無法使用的失敗案例是透過重試自動處理。

如果指定的目的地資料表尚未存在以對應於傳回的結果集的結構描述，則會自動建立。如果指令碼執行傳回多個結果集，彈性資料庫工作只會將第一個傳送至提供的目的地資料表。

下列 PowerShell 指令碼可以用來執行指令碼，將其結果收集至指定的資料表。此指令碼假設已建立 T-SQL 指令碼，它會輸出單一結果集，且自訂資料庫集合目標已建立。

設定下列項目以反映所需的指令碼、認證和執行目標：

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### 建立和啟動資料庫集合案例的工作
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## 使用工作觸發程序建立工作執行的排程

下列 PowerShell 指令碼可以用來建立重複排程。這個指令碼使用分鐘間隔，但是 New-AzureSqlJobSchedule 也支援 -DayInterval、-HourInterval、-MonthInterval 和 -WeekInterval 參數。您可以藉由傳遞 -OneTime，建立僅執行一次的排程。

建立新的排程：

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### 建立工作觸發程序，讓工作在時間排程時執行

可以定義工作觸發程序，讓工作根據時間排程執行。下列 PowerShell 指令碼可以用來建立工作觸發程序。

設定下列變數以對應所需的工作和排程：

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### 移除排程的關聯，以停止工作的排程執行

若要透過工作觸發程序中止工作重複執行，可以移除工作觸發程序。使用 **Remove-AzureSqlJobTrigger** Cmdlet，移除工作觸發程序以停止工作根據排程執行。

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## 匯入彈性資料庫查詢結果到 Excel

 您可以從查詢的結果匯入到 Excel 檔案。

1. 啟動 Excel 2013。
2. 	瀏覽至 [**資料**] 功能區。
3. 	按一下 [**從其他來源**]，然後按一下 [**從 SQL Server**]。

	![從其他來源的 Excel 匯入][5]
4. 	在 [**資料連線精靈**] 中，輸入伺服器名稱和登入認證。然後按 [下一步]。
5. 	在對話方塊 [**選取包含您想要的資料的資料庫**] 中，選取 **ElasticDBQuery** 資料庫。
6. 	在清單檢視中選取 [**客戶**] 資料表，然後按 [**下一步**]。然後按一下 [**完成**]。
7. 	在 [**匯入資料**] 表單中，於 [**選取您要在活頁簿中檢視此資料的方式**] 下，選取 [**資料表**]，然後按一下 [**確定**]。

儲存在不同分區中、來自 [客戶] 資料表的所有資料列會填入 Excel 工作表。

## 後續步驟
您現在可以使用 Excel 強大資料功能。您可以使用具備您的伺服器名稱、資料庫名稱和認證的連接字串來連接您的 BI 和資料整合工具至彈性查詢資料庫。請確定 SQL Server 可支援做為您的工具的資料來源。您可以參考彈性查詢資料庫和外部資料表，就如同您會使用您的工具連接的任何其他 SQL Server 資料庫和 SQL Server 資料表。

### 成本
使用彈性資料庫查詢功能不另行收費。不過，目前這項功能僅適用於 Premium 資料庫做為端點，但分區可以是任何服務層。

如需價格資訊，請參閱 [SQL Database 價格詳細資料](https://azure.microsoft.com/pricing/details/sql-database/)。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=AcomDC_0128_2016-->