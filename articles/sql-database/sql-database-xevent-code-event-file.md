<properties 
	pageTitle="SQL Database 的 XEvent 事件檔案程式碼 | Microsoft Azure" 
	description="提供 PowerShell 和 Transact-SQL 的兩階段程式碼範例，示範 Azure SQL Database 上擴充事件中的事件檔案目標。此案例必須要有 Azure 儲存體。" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2015" 
	ms.author="genemi"/>


# SQL Database 中擴充事件的事件檔案目標程式碼


您想要完整的程式碼範例以穩健方式擷取和報告擴充事件的資訊。


在 Microsoft SQL Server 中，[事件檔案目標](http://msdn.microsoft.com/library/ff878115.aspx)是用來將事件輸出儲存到本機硬碟機的檔案。但是這類檔案並不適用於 Azure SQL Database。我們改為使用 Azure 儲存體服務來支援事件檔案目標。


本主題示範兩階段的程式碼範例：


- 使用 PowerShell 在雲端中建立 Azure 儲存體容器

- Transact-SQL：
 - 將 Azure 儲存體容器指定為事件檔案目標。
 - 建立和啟動事件工作階段等等。


## 必要條件


- Azure 帳戶和訂用帳戶。您可以註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/)。


- 您可以在當中建立資料表的任何資料庫。
 - 您可以選擇性快速[建立 **AdventureWorksLT** 示範資料庫](sql-database-get-started.md)。


- SQL Server Management Studio (ssms.exe) 在 2015 年 8 月的預覽版或更新版本。您可以從下列位置下載最新的 ssms.exe：
 - 名稱為[下載 SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx) 的主題。
 - [下載的直接連結。](http://go.microsoft.com/fwlink/?linkid=616025)
 - Microsoft 建議您定期更新 ssms.exe。在某些情況下，ssms.exe 將會每個月更新。


- 您必須安裝 [Azure PowerShell 模組](http://go.microsoft.com/?linkid=9811175)。
 - 模組提供 **New-AzureStorageAccount** 這類的命令。


## 第 1 階段：Azure 儲存體容器的 PowerShell 程式碼


這個 PowerShell 是兩階段程式碼範例的第 1 階段。

指令碼開頭的命令清除先前可能執行過的結果，而且設計為可重複執行。



1. 將 PowerShell 指令碼貼到如 Notepad.exe 的簡單文字編輯器，並將指令碼儲存為有 **.ps1** 副檔名的檔案。

2. 以系統管理員身分啟動 PowerShell ISE。

3. 在提示中輸入<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>然後按 Enter 鍵。

4. 在 PowerShell ISE 中開啟您的 **.ps1** 檔案。執行指令碼。

5. 指令碼會先啟動新的視窗讓您登入 Azure。
 - 如果您重複執行指令碼而不中斷您的工作階段，可以很方便地選擇將 **Add-AzureAccount** 命令標記為註解。


![準備好 PowerShell ISE 和安裝的 Azure 模組，以便執行指令碼。][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

$subscriptionName       = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken      = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean-up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


記下 PowerShell 指令碼結束時列印出的幾個具名值。您必須將這些值寫入第 2 階段的 Transact-SQL 指令碼。


## 第 2 階段：使用 Azure 儲存體容器的 Transact-SQL 程式碼


- 在此程式碼範例的第 1 階段中，您執行了 PowerShell 指令碼建立 Azure 儲存體容器。
- 接下來在第 2 階段中，下列 Transact-SQL 指令碼必須使用該容器。


指令碼開頭的命令清除先前可能執行過的結果，而且設計為可重複執行。


PowerShell 指令碼在結束時列印出幾個具名的值。您必須編輯 Transact-SQL 指令碼以使用這些值。在 Transact-SQL 指令碼中尋找 **TODO** 找出編輯點。


1. 開啟 SQL Server Management Studio (ssms.exe)。

2. 連接到您的 Azure SQL Database 資料庫。

3. 按一下以開啟新的查詢窗格。

4. 將下列 Transact-SQL 指令碼貼入查詢窗格。

5. 在指令碼中尋找每個 **TODO** 並進行適當的編輯。

6. 儲存並執行指令碼。


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;

GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
	DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
	(SELECT * FROM sys.symmetric_keys
		WHERE symmetric_key_id = 101)
BEGIN
	CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
	(SELECT * FROM sys.database_scoped_credentials
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
	DROP DATABASE SCOPED CREDENTIAL
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
	DATABASE SCOPED
	CREDENTIAL
		-- use '.blob.',   and not '.queue.' or '.table.' etc.
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	WITH
		IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
		-- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
		SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
	;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'gmeventsessionname240b')
BEGIN
	DROP
		EVENT SESSION
			gmeventsessionname240b
	    ON DATABASE;
END
GO


CREATE
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE

	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE 'UPDATE gmTabEmployee%'
			)
	ADD TARGET
		package0.event_file
			(
			-- TODO: Assign AzureStorageAccount name, and the associated Container name.
			-- Also, tweak the .xel file name at end, if you like.
			SET filename =
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
			)
	WITH
		(MAX_MEMORY = 10 MB,
		MAX_DISPATCH_LATENCY = 3 SECONDS)
	;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 2
	WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 13
	WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
		*, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
		CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
	FROM
		sys.fn_xe_file_target_read_file
			(
				-- TODO: Fill in Storage Account name, and the associated Container name.
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
				null, null, null
			);
GO


-------------- Step 6.  Clean up. ----------

DROP
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
	-- TODO: Assign AzureStorageAccount name, and the associated Container name.
	[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


如果目標在執行時無法附加，您就必須停止事件工作階段並重新啟動：


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## 輸出


Transact-SQL 指令碼完成時，按一下 **event\_data\_XML** 資料欄標題下的儲存格。此時會顯示一個 **<event>** 元素，顯示一個 UPDATE 陳述式。

以下是測試期間所產生的其中一個 **<event>** 元素：


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```





&nbsp;


## 轉換程式碼範例在 SQL Server 上執行


假設您想要在 Microsoft SQL Server 上執行上述的 Transact-SQL 範例。


- 為了簡單起見，您想要將 Azure 儲存體容器完全取代為簡單檔案 (例如 **C:\\myeventdata.xel**)。檔案會寫入裝載 SQL Server 之電腦的本機硬碟。


- 您不需要 **CREATE MASTER KEY** 和 **CREATE CREDENTIAL** 的任何 Transact-SQL 陳述式。


- 在 **CREATE EVENT SESSION** 陳述式的 **ADD TARGET** 子句中，您要將對 **filename=** 指派的 Http 值取代為完整路徑字串 (例如 **C:\\myfile.xel**)。
 - 不需要牽涉到任何 Azure 儲存體帳戶。


## 詳細資訊


Azure SQL Database 上擴充事件的主要主題是：

- [SQL Database 中的擴充事件](sql-database-xevent-db-diff-from-svr.md) - 是 Azure SQL Database 上擴充事件的主要主題。
 - 對比 Azure SQL Database 與 Microsoft SQL Server 之間擴充事件的不同層面。


- [SQL Database 中擴充事件的環形緩衝區目標程式碼](sql-database-xevent-code-ring-buffer.md) - 提供快速又簡單的相關程式碼範例，比較適合簡單的測試，但是對於較大的活動則較不穩定。


如需 Azure 儲存體服務中帳戶和容器的詳細資訊，請參閱：

- [如何使用 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md/)
- [命名和參考容器、Blob 及中繼資料](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [使用根容器](http://msdn.microsoft.com/library/azure/ee395424.aspx)


<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

<!---HONumber=AcomDC_1210_2015-->