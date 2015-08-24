<properties 
	pageTitle="彈性資料庫工作概觀" 
	description="說明彈性資料庫工作服務" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="ddove; sidneyh" />

# 使用 PowerShell 建立和管理 SQL Database 彈性資料庫工作 (預覽)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## 概觀

**彈性資料庫工作**功能 (預覽) 可讓您可靠地執行 Transact-SQL (T-SQL) 指令碼或跨資料庫群組套用 DACPAC，資料庫包含資料庫的自訂定義集合、[彈性資料庫集區 (預覽)](sql-database-elastic-pool.md) 中的所有資料庫，或分區集合 (使用[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)建立)。在預覽中，**彈性資料庫工作**是目前的客戶裝載 Azure 雲端服務，可以執行臨機操作和排程的管理工作，稱為「工作」。使用這個功能，您可以輕易且可靠地跨大規模管理大量 Azure SQL Database，方法是執行 Transact-SQL 指令碼以執行管理作業，例如結構描述變更、認證管理、參考資料更新、效能資料收集或租用戶 (客戶) 遙測收集。如需彈性資料庫工作的詳細資訊，請參閱[彈性資料庫工作概觀](sql-database-elastic-jobs-overview.md)。

使用適用於**彈性資料庫工作**的 PowerShell API，您可以彈性地定義要針對哪個資料庫執行哪個指令碼。目前，透過 Azure 入口網站的**彈性資料庫工作**功能具有精簡功能集，並且限制為針對彈性資料庫集區執行。

**彈性資料庫工作** (預覽) 使用多個 Azure 元件來定義要執行的工作、定義何時執行工作、執行工作、追蹤工作成功或失敗，以及選擇性地為傳回結果查詢指定結果目的地。因為此預覽中包括的 Powershell API 在透過入口網站初始預覽之後包含其他功能，建議您安裝最新的**彈性資料庫工作**元件。如果已安裝，您只要升級**彈性資料庫工作**元件即可。如需從 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs) 安裝的詳細資訊，請參閱[安裝彈性資料庫工作元件](sql-database-elastic-jobs-service-installation.md)。

本文將說明如何建立所需一切以建立和管理**彈性資料庫工作**，但建立 Azure 訂用帳戶除外。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。本主題會延伸[彈性資料庫工具入門](sql-database-elastic-scale-get-started.md)中可找到的範例。當完成時，您將了解如何建立和管理工作，以針對**分區集**定義的分區資料庫群組和自訂集合資料庫執行管理作業。

## 必要條件
* Azure 訂閱。如需免費試用，請參閱[免費試用一個月](http://azure.microsoft.com/pricing/free-trial/)。
* **彈性資料庫工作** PowerShell 封裝必須先下載、匯入，然後彈性資料庫工作元件隨即安裝。遵循這些步驟：[安裝彈性資料庫工作](sql-database-elastic-jobs-service-installation.md)
* Azure PowerShell 版本 >= 0.8.16。透過 [Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376) 安裝最新版本 (0.9.5)。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID (**-SubscriptionId**) 或訂用帳戶名稱 (**-SubscriptionName**)。如果您有多個訂用帳戶，則可以執行 **Get-AzureSubscription** Cmdlet，然後複製結果集中所需的訂用帳戶資訊。一旦您具有訂用帳戶資訊，請執行下列 commandlet 將此訂用帳戶設定為預設值，也就是建立和管理工作的目標：

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

建議使用 [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) 以針對彈性資料庫工作開發和執行 PowerShell 指令碼。

## 彈性資料庫工作物件

下表列出**彈性資料庫工作**的所有物件類型，以及其描述和相關 PowerShell API。

<table style="width:100%">
  <tr>
    <th>物件類型</th>
    <th>說明</th>
    <th>相關 PowerShell API</th>
  </tr>
  <tr>
    <td>認證</td>
    <td>連接到資料庫以執行指令碼或 DACPAC 的應用程式時使用的使用者名稱和密碼。<p>密碼在傳送並儲存在彈性資料庫工作資料庫之前會先行加密。密碼會由彈性資料庫工作服務透過安裝指令碼建立及上傳的認證解密。</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>指令碼</td>
    <td>用於跨資料庫執行的 Transact-SQL 指令碼。指令碼應該撰寫為等冪，因為服務將會在失敗時重試執行指令碼。
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>跨資料庫套用的<a href="https://msdn.microsoft.com/library/ee210546.aspx">資料層應用程式</a>封裝。

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>資料庫目標</td>
    <td>指向 Azure SQL Database 的資料庫和伺服器名稱。

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>分區對應目標</td>
    <td>資料庫目標和認證的組合，用來判斷彈性資料庫分區對應內儲存的資訊。
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>自訂集合目標</td>
    <td>共同用於執行的已定義資料庫群組。</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>自訂集合子目標</td>
    <td>從自訂集合參考的資料庫目標。</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Job</td>
    <td>
	<p>工作的參數的定義，可用來觸發執行或完成排程。</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>工作執行</td>
    <td>
	<p>必要的作業容器，以使用資料庫連線的認證執行指令碼或將 DACPAC 套用到目標，具有根據執行原則處理的失敗。</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>工作作業執行</td>
    <td>
	<p>完成作業的單一工作單位。</p>
	<p>如果工作作業不能成功執行，將會記錄產生的例外狀況訊息，並且建立新的比對工作作業及根據指定的執行原則執行。</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>工作執行原則</td>
    <td>
	<p>控制重試之間的工作執行逾時、重試限制和間隔。</p>
	<p>彈性資料庫工作包括預設工作執行原則，基本上會導致無限的工作作業失敗重試，每次重試之間具有間隔的指數輪詢。</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>排程</td>
    <td>
	<p>以時間為基礎的執行指定會在重複間隔發生或單一次數發生。</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>工作觸發程序</td>
    <td>
	<p>工作與排程之間的對應，以根據排程觸發工作執行。</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## 支援的彈性資料庫工作群組類型
**彈性資料庫工作**可以跨資料庫群組執行 Transact-SQL (T-SQL) 指令碼或 DACPAC 的應用程式。當提交工作以跨資料庫群組執行時，彈性資料庫工作將會「展開」工作到子工作，其中每個工作會針對群組中的單一資料庫執行要求的執行。
 
以下是目前支援的群組類型的清單：

* [分區對應](sql-database-elastic-scale-shard-map-management.md)：當提交工作以分區對應為目標時，工作會先查詢分區對應來判斷其目前的分區集，然後展開工作至子工作，比對分區對應內的每個分區。
* 自訂集合：指定以指向自訂定義資料庫集合。當提交工作以自訂集合為目標時，工作將展開工作至子工作，比對自訂集合內目前定義的每個資料庫。

## 設定彈性資料庫工作連接
載入 PowerShell 模組之後，連線必須設為彈性資料庫工作*控制資料庫*，才能使用工作 API。此 Cmdlet 的叫用將會在安裝彈性資料庫工作時，觸發認證視窗快顯要求提供的使用者名稱/密碼。本主題中提供的所有範例都假設已經執行第一個步驟。

開啟彈性資料庫工作的連線：

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## 彈性資料庫工作內的已加密認證
資料庫認證可以插入彈性資料庫工作*控制資料庫*並且加密其密碼。必須儲存認證，讓工作可以在稍後執行，包括使用工作排程。
 
加密是透過建立為安裝指令碼一部分的憑證來運作。安裝指令碼會建立憑證並將其上傳至 Azure 雲端服務，以解密已儲存的加密密碼。Azure 雲端服務稍後會在彈性資料庫工作*控制資料庫*內儲存公開金鑰，讓 PowerShell API 或 Azure 入口網站介面加密提供的密碼，而不需要在本機安裝憑證。
 
雖然認證密碼已加密並且透過彈性資料庫工作物件的唯讀存取權而受到保護，但是具有彈性資料庫工作物件讀寫存取權的惡意使用者可能會擷取密碼。認證是設計為跨工作執行重複使用。當建立連線時，認證會傳遞至目標資料庫。目前目標資料庫上沒有任何限制可用於每個認證，所以惡意使用者可能會新增在其掌控之下的資料庫目標，並且後續啟動工作以此資料庫為目標，獲得認證密碼的資訊。

彈性資料庫工作有安全性最佳作法：

* 將 API 的使用限制為受信任的個人。
* 認證應該具有執行工作作業所需的最低權限。如需詳細資訊，請參閱這個[授權和權限](https://msdn.microsoft.com/library/bb669084.aspx) SQL Server MSDN 文章。

### 跨資料庫建立工作執行的加密認證
若要建立新的加密認證，Get-Credential Cmdlet 會提示您輸入可以傳遞至 **New-AzureSqlJobCredential** Cmdlet 的使用者名稱和密碼。

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### 更新認證
若要更新現有認證以支援密碼變更的情況，請使用 **Set-AzureSqlJobCredential** Cmdlet 並且設定 **CredentialName** 參數。

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## 定義彈性資料庫分區對應目標
使用分區對應做為資料庫目標，針對分區集 (使用[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)建立) 的所有資料庫執行工作。此範例相依於您使用彈性資料庫用戶端程式庫建立分區應用程式。下載並執行[彈性資料庫工具範例入門](sql-database-elastic-scale-get-started.md)。

###使用範例應用程式建立分區對應管理員

在這裡，您將建立分區對應管理員以及數個分區，接著插入資料至分區。若您已經有分區設定，則可以略過下列步驟並移至下一節。

1. 建置並執行**彈性資料庫工具入門**範例應用程式。遵循步驟，直到[下載及執行範例應用程式](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)小節的步驟 7。在步驟 7 結束時，您會看到下列的命令提示字元：

	![命令提示字元][1]

2.  在命令視窗中，輸入 "1"，然後按 **Enter**。這會建立分區對應管理員，並加入兩個分區到伺服器。然後輸入 "3"，然後按 **Enter**；重複此動作四次。這會在您的分區中插入範例資料列。
  
3.  [Azure Preview 入口網站](https://portal.azure.com)應該在 v12 伺服器中顯示三個新的資料庫：

	![Visual Studio 確認][2]

現在建立分區對應目標，使用 **New-AzureSqlJobTarget** Cmdlet。分區對應管理員資料庫必須設定為資料庫目標，然後將特定分區對應指定為目標。

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## 針對跨資料庫執行建立 T-SQL 指令碼

在針對執行建立 T-SQL 指令碼時，強烈建議將它們建置為對於失敗等冪且恢復力強。每當執行發生失敗時，不論失敗的分類，彈性資料庫工作將重試執行指令碼。

使用 **New-AzureSqlJobContent** Cmdlet 以建立並儲存執行的指令碼，並且設定 **-ContentName** 和 **-CommandText** 參數。

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### 從檔案建立新的指令碼
如果 T-SQL 指令碼是在檔案內定義，則會使用下列指令碼來匯入指令碼：

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### 針對跨資料庫執行更新 T-SQL 指令碼  

下列 PowerShell 指令碼可以用來更新現有指令碼的 T-SQL 命令文字。

設定下列變數以反映要設定的所需指令碼定義：

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### 將定義更新為現有指令碼

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##建立工作以跨分區對應執行指令碼

下列 PowerShell 指令碼可用來針對跨彈性擴縮分區對應中每個分區的指令碼執行啟動工作。

設定下列變數以反映所需的指令碼和目標：

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

##執行工作 

下列 PowerShell 指令碼可以用來執行現有的工作：

更新下列變數以反映要執行的所需工作名稱：

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## 擷取單一工作執行狀態

使用 **Get-AzureSqlJobExecution** Cmdlet 並且設定 **JobExecutionId** 參數以檢視工作執行的狀態。

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

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

下列 PowerShell 指令碼可以用來建立重複排程。這個指令碼使用分鐘的間隔，但是 New-AzureSqlJobSchedule 也支援 -DayInterval、-HourInterval、-MonthInterval 和 -WeekInterval 參數。您可以藉由傳遞 -OneTime，建立僅執行一次的排程。

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

### 擷取繫結至時間排程的工作觸發程序

下列 PowerShell 指令碼可用來取得並顯示註冊至特定時間排程的工作觸發程序。

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### 擷取繫結至工作的工作觸發程序 

下列 PowerShell 指令碼可用來取得並顯示包含已註冊工作的排程。

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## 建立資料層應用程式部署 (DACPAC) 以跨資料庫執行

彈性資料庫工作可以用來將資料層應用程式 (DACPAC) 部署至資料庫的群組。若要建立 DACPAC，請參閱此文件。對於要將 DACPAC 部署到資料庫群組的彈性資料庫工作，DACPAC 必須能夠存取服務。建議您將建立的 DACPAC 上傳至 Azure 儲存體，並且建立 DACPAC 的已簽署 URI。

下列 PowerShell 指令碼可以用來將 DACPAC 插入彈性資料庫工作：

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### 更新資料層應用程式部署 (DACPAC) 以跨資料庫執行

彈性資料庫工作內的現有已註冊 DACPAC 可以更新以指向新的 URI。下列 PowerShell 指令碼可以用來更新現有已註冊 DACPAC 上的 DACPAC URI。

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## 建立工作以跨資料庫套用資料層應用程式部署 (DACPAC)

在彈性資料庫工作內建立 DACPAC 之後，可以建立工作以跨資料庫群組套用 DACPAC。下列 PowerShell 指令碼可以用來跨自訂資料庫集合建立 DACPAC 工作：

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=August15_HO7-->