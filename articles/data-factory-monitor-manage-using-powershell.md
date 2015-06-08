<properties 
	pageTitle="教學課程： 建立和監視使用 Azure PowerShell 的 Azure 資料 factory" 
	description="了解如何使用 Azure PowerShell 來建立和監視 Azure 資料處理站。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# 教學課程： 建立和監視的資料處理站使用 Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

 [開始使用 Azure 資料 Factory][adf-get-started] 教學課程會示範如何建立和監視 Azure 資料處理站使用 [Azure 預覽入口網站][azure-preview-portal]。在本教學課程中，您將建立和使用 Azure PowerShell cmdlet 來監視 Azure 資料處理站。您在本教學課程中建立的資料處理站中的管線會將資料從 Azure blob 用於 Azure SQL database。

> [AZURE.NOTE]這篇文章並未涵蓋所有的資料處理站指令程式。請參閱 [資料 Factory 指令程式參考][cmdlet-reference] 資料 Factory 指令程式的完整文件。

##必要條件
除了教學課程概觀 ＞ 主題中所列的必要條件，您需要有 Azure PowerShell 安裝在電腦上。如果您沒有它已經，下載並安裝 [Azure PowerShell][download-azure-powershell] 在電腦上。

##本教學課程內容
下表列出本教學課程與及其描述的一部分，您將執行的步驟。

步驟 | 說明
-----| -----------
[步驟 1： 建立 Azure 的資料處理站](#CreateDataFactory) | 在此步驟中，您將建立名為 Azure 資料 factory **ADFTutorialDataFactoryPSH**。 
[步驟 2： 建立連結的服務](#CreateLinkedServices) | 在此步驟中，您將建立兩個連結的服務： **StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 連結 Azure 儲存體和 AzureSqlLinkedService 連結 ADFTutorialDataFactoryPSH Azure SQL database。
[步驟 3： 建立輸入和輸出資料集](#CreateInputAndOutputDataSets) | 在此步驟中，您會定義兩個資料集 (* * EmpTableFromBlob * * 和 **EmpSQLTable**)，做為輸入和輸出資料表 **複製活動** 中您將在下一個步驟建立 ADFTutorialPipeline。
[步驟 4： 建立和執行管線](#CreateAndRunAPipeline) | 在此步驟中，您將建立名為管線 **ADFTutorialPipeline** 資料 factory 中： **ADFTutorialDataFactoryPSH**...管線將會有 **複製活動** ，會將資料從 Azure blob 輸出 Azure 資料庫的資料表。
[步驟 5： 監視資料集和管線](#MonitorDataSetsAndPipeline) | 在此步驟中，您將會監視資料集和管線，在此步驟中使用 Azure PowerShell。

## <a name="CreateDataFactory"></a>步驟 1： 建立 Azure 的資料處理站
在此步驟中，您可以使用 Azure PowerShell 建立名為 Azure 資料處理站 **ADFTutorialDataFactoryPSH**。

1. 啟動 **Azure PowerShell** 並執行下列命令。保持開啟 Azure PowerShell 到本教學課程結束為止。如果您關閉並重新開啟，您需要再次執行這些命令。
	- 執行 **Add-azureaccount** 並輸入使用者名稱和您用來登入 Azure 預覽入口網站的密碼。  
	- 執行 **Get-azuresubscription** 若要檢視此帳戶的所有訂閱。
	- 執行 **Select-azuresubscription** 來選取您想要使用的訂閱。此訂用帳戶應該與您在「Azure Preview 入口網站」中使用的相同。 
2. 切換至 **AzureResourceManager** 模式為 Azure 資料 Factory 指令程式會在此模式中使用。

		Switch-AzureMode AzureResourceManager 
3. 建立名為 Azure 資源群組： **ADFTutorialResourceGroup** 藉由執行下列命令。
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	在本教學課程的步驟假設您使用資源群組名稱 **ADFTutorialResourceGroup**。如果您使用的不同資源群組，您必須取代 ADFTutorialResourceGroup 在本教學課程中使用它。 
4. 執行 **新增 AzureDataFactory** 指令程式可建立名為資料處理站： **ADFTutorialDataFactoryPSH**。  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤： **資料 factory 名稱"ADFTutorialDataFactoryPSH"不是使用**, ，變更名稱 (例如，yournameADFTutorialDataFactoryPSH)。使用此名稱來取代 ADFTutorialFactoryPSH 時執行本教學課程中的步驟。

## <a name="CreateLinkedServices"></a>步驟 2： 建立連結的服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、 Azure SQL Database 或包含輸入的資料，或將資料處理站管線的輸出資料儲存在內部部署 SQL Server 資料庫。計算服務是處理輸入的資料和產生的輸出資料的服務。

在此步驟中，您將建立兩個連結的服務： **StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 連結服務連結的 Azure 儲存體帳戶和 AzureSqlLinkedService 連結至資料處理站的 Azure SQL database： **ADFTutorialDataFactoryPSH**。將資料從 StorageLinkedService 中的 blob 容器中 AzureSqlLinkedService 的 SQL 資料表到本教學課程稍後，您將建立管線。

### 建立 Azure 儲存體帳戶的連結服務
1.	建立名為 JSON 檔案 **StorageLinkedService.json** 中 **C:\ADFGetStartedPSH** 含有下列內容。如果不存在，請建立 ADFGetStartedPSH 的資料夾。

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	在 **Azure PowerShell**, ，切換到 **ADFGetStartedPSH** 資料夾。 
3.	您可以使用 **新增 AzureDataFactoryLinkedService** 指令程式來建立連結的服務。這個指令程式和您在本教學課程中使用其他資料處理站 cmdlet 需要您將值傳給 **ResourceGroupName** 和 **DataFactoryName** 參數。或者，您可以使用 **Get AzureDataFactory** 取得 DataFactory 物件，並傳遞物件，而不需要輸入 ResourceGroupName 和 DataFactoryName 每次您執行 cmdlet。執行下列命令，以指定的輸出 **Get AzureDataFactory** cmdlet 的變數： **$df**。 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	現在，執行 **新增 AzureDataFactoryLinkedService** 指令程式來建立連結的服務： **StorageLinkedService**。

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	如果您還沒執行 **Get AzureDataFactory** 指令程式，並指派至輸出 **$df** 變數時，您就必須指定 ResourceGroupName 和 DataFactoryName 參數的值，如下所示。
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	如果您關閉 Azure PowerShell，在本教學課程，您將有執行 Get AzureDataFactory cmdlet 啟動 Azure PowerShell 來完成教學課程的下一次。

### 建立 Azure SQL Database 的連結服務
1.	建立名為 AzureSqlLinkedService.json 含有下列內容的 JSON 檔案。

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	執行下列命令以建立連結的服務。 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]確認已選取 **允許 Azure 服務存取** 設定 Azure SQL server 已開啟。若要確認，並將它開啟，請執行下列動作：
	>
	> <ol>
<li>按一下 <b>瀏覽</b> 中樞在左方和按一下 <b>SQL 伺服器</b>。</li>
<li>選取您的伺服器，然後按一下 [ <b>設定</b> 上 <b>SQL SERVER</b> 分頁。</li>
<li>在 <b>設定</b> blade 中，按一下 <b>防火牆</b>。</li>
<li>在 <b>Firewalll 設定</b> blade 中，按一下 <b>ON</b> 的 <b>允許 Azure 服務存取</b>。</li>
<li>按一下 <b>ACTIVE</b> 切換至左邊 」 中樞 <b>資料 Factory</b> 上的刀鋒。</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>步驟 3： 建立輸入和輸出資料表

在上一個步驟中，您可以建立連結的服務 **StorageLinkedService** 和 **AzureSqlLinkedService** 連結至資料處理站的 Azure 儲存體帳戶和 Azure SQL database： **ADFTutorialDataFactoryPSH**。在此步驟中，您將建立代表輸入和輸出資料複製活動，您會在下一個步驟建立的管線中的資料表。

資料表是矩形的資料集，而且是目前唯一受支援的資料集類型。本教學課程中的輸入的資料表是指 blob 容器，在 Azure 儲存體該 StorageLinkedService 指向和 Azure SQL database 中的 SQL 資料表 AzureSqlLinkedService 指向輸出資料表參考。

### 準備教學課程所需的 Azure Blob 儲存體和 Azure SQL 資料庫
如果您已經完成從教學課程，請略過此步驟 [開始使用 Azure 資料 Factory][adf-get-started] 發行項。

您需要執行下列步驟來準備此教學課程中的 Azure blob 儲存體和 Azure SQL database。
 
* 建立 blob 容器，名為 **adftutorial** 在 Azure blob 儲存體， **StorageLinkedService** 指向。 
* 建立及上傳文字檔， **emp.txt**, ，做為要 blob **adftutorial** 容器。 
* 建立了名為 **emp** Azure SQL 資料庫中 Azure SQL 資料庫 **AzureSqlLinkedService** 指向。


1. 啟動 [記事本]，貼上下列文字，並將它儲存成 **emp.txt** 至 **C:\ADFGetStartedPSH** 硬碟機上的資料夾。 

        John, Doe
		Jane, Doe
				
2. 使用工具，例如 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/) 建立 **adftutorial** 容器以及上傳 **emp.txt** 檔案的容器。

    ![Azure 儲存體總管][image-data-factory-get-started-storage-explorer]
3. 使用下列 SQL 指令碼來建立 **emp** Azure SQL 資料庫資料表中的。  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	如果您必須在電腦上安裝 SQL Server 2014： 請遵循指示從 [步驟 2： 連接到 SQL Database 管理 Azure SQL Database 的使用 SQL Server Management Studio][sql-management-studio] 文件，以連線到您的 Azure SQL server 並執行 SQL 指令碼。

	如果您必須在電腦上安裝 Visual Studio 2013： 在 Azure 預覽入口網站 ([http://portal.azure.com](http://portal.sazure.com))，按一下 **瀏覽** 中樞在左側，按一下 **SQL 伺服器**, ，選取您的資料庫，然後按一下 **在 Visual Studio 中開啟** 連接到您的 Azure SQL server 並執行指令碼的工具列上的按鈕。如果您的用戶端來存取 Azure SQL server 不允許，您必須設定防火牆以允許來自您的電腦 (IP 位址) 存取 Azure SQL server。請參閱上面的步驟來設定您的 Azure SQL server 的防火牆。
		
### 建立輸入資料表 
資料表是矩形的資料集，並具有的結構描述。在此步驟中，您將建立一個名為資料表 **EmpBlobTable** ，它會指向所代表的 Azure 儲存體中的 blob 容器 **StorageLinkedService** 連結服務。這個 blob 容器 (* * adftutorial * *) 包含在檔案中的輸入的資料： **emp.txt**。

1.	建立名為 JSON 檔案 **EmpBlobTable.json** 中 **C:\ADFGetStartedPSH** 資料夾含有下列內容：

		{
	    	"name": "EmpTableFromBlob",
	        "properties":
	        {
	            "structure":  
	                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "StorageLinkedService"
	            },
	            "availability": 
	            {
	                "frequency": "hour",
	                "interval": 1,
	                "waitonexternal": {}
	                }
	        }
		}
	
	請注意：
	
	- 位置 **類型** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設為 **StorageLinkedService**。 
	- **folderPath** 設為 **adftutorial** 容器。您也可以指定資料夾內的 Blob 的名稱。由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。  
	- 格式 **類型** 設為 **TextFormat**
	- 有兩個欄位中的文字檔案 – **FirstName** 和 **LastName** – 以逗號字元分隔 (* * [columnDelimiter * *)	
	-  **可用性** 設為 **每小時** (* * 頻率 * * 會設定為 **小時** 和 **間隔** 設為 **1** )，因此資料處理站服務會尋找輸入資料每隔一小時在 blob 容器中的根資料夾中 (* * adftutorial * *) 指定。

	如果您沒有指定 **fileName** 的 **輸入** **資料表**, ，所有檔案/blob 從輸入資料夾 (* * folderPath * *) 會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。請參閱範例檔案 [教學課程][adf-tutorial] 範例。
 
	如果您未指定 **檔名** 的 **輸出資料表**, ，則產生的檔案中的 **folderPath** 下列格式命名： 資料。 < Guid >.txt (範例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。)。

	若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需詳細資訊 JSON 內容。

2.	執行下列命令以建立資料處理站資料表。

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### 建立輸出資料表
在這段的步驟，您將建立的輸出資料表中名為 **EmpSQLTable** 指向 SQL 資料表 (* * emp * *) 由 Azure SQL database 中 **AzureSqlLinkedService** 連結服務。管線會將資料複製到輸入 blob **emp** 資料表。

1.	建立名為 JSON 檔案 **EmpSQLTable.json** 中 **C:\ADFGetStartedPSH** 含有下列內容的資料夾。
		
		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSQLTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

     請注意：
	
	* 位置 **類型** 設為 **AzureSQLTableLocation**。
	* **linkedServiceName** 設為 **AzureSqlLinkedService**。
	* **tablename** 設為 **emp**。
	* 有三個資料行 – **識別碼**, ，**FirstName**, ，和 **LastName** – emp 資料表在資料庫中，但識別碼是識別資料行，因此您必須只指定 **FirstName** 和 **LastName** 這裡。
	*  **可用性** 設為 **每小時** (* * 頻率 * * 設 **小時** 和 **間隔** 設 **1**)。資料處理站服務將會產生輸出的資料配量中的每個小時 **emp** Azure SQL database 中的資料表。

2.	執行下列命令以建立資料處理站資料表。
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>步驟 4： 建立和執行管線
在此步驟中，您會建立具有管線 **複製活動** 使用 **EmpTableFromBlob** 做為輸入和 **EmpSQLTable** 做為輸出。

1.	建立名為 JSON 檔案 **ADFTutorialPipeline.json** 中 **C:\ADFGetStartedPSH** 資料夾含有下列內容： 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	請注意：

	- 在 [活動] 區段中，則只有一個活動其 **類型** 設為 **CopyActivity**。
	- 輸入活動設定為 **EmpTableFromBlob** 和輸出活動設定為 **EmpSQLTable**。
	- 在 **轉換** ] 區段中， **BlobSource** 指定做為來源類型和 **SqlSink** 指定為接收型別。

	> [AZURE.NOTE]取代的值 **開始** 屬性與目前的日期和 **結束** 隔天的值。同時開始和結束日期時間必須在 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如： 2014年-10-14T16:32:41Z。 **結束** 時間是選擇性的但是我們將在本教學課程中使用它。如果您沒有指定值 **結束** 屬性，它會計算為"* * 啟動 + 48 小時 * *"。若要無限期地執行管線，請指定 **9/9/9999** 做為值 **結束** 屬性。在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
	> [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	執行下列命令以建立資料處理站資料表。 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

## <a name="MonitorDataSetsAndPipeline"></a>步驟 5： 監視的資料集和管線
在此步驟中，您將使用 Azure PowerShell 來監視怎麼在 Azure 資料處理站。

1.	執行 **Get AzureDataFactory** 和指派給變數 $df 的輸出。

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	執行 **Get AzureDataFactorySlice** 以取得詳細資料的所有配量 **EmpSQLTable**, ，這是輸出資料表的管線。

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]取代年份、 月份和日期部分 **StartDateTime** 參數目前的年、 月和日。這應該符合 **開始** 管線 JSON 中的值。

	您應該會看到 24 配量，一個用於從 12 AM 當天的每小時到隔天的上午 12。
	
	**第一個扇區：**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**最後一個配量：**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	執行 **Get AzureDataFactoryRun** 以取得詳細資料的活動執行 **特定** 配量。值變更 **StartDateTime** 參數以符合 **開始** 上述輸出配量的時間。值 **StartDateTime** 必須在 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如： 2014年-03-03T22:00:00Z。

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	您應該會看到如下所示的輸出：

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

> [AZURE.NOTE]請參閱 [資料 Factory 指令程式參考][cmdlet-reference] 資料 Factory 指令程式的完整文件。

## 後續步驟

文章 | 說明
------ | ---------------
[複製資料與 Azure 資料 Factory 複製活動][copy-activity] | 這篇文章提供詳細的描述 **複製活動** 您在本教學課程中使用。 
[讓您使用的內部資料的管線][use-onpremises-datasources] | 這篇文章已示範如何從其中複製資料的逐步解說 **在內部部署 SQL Server 資料庫** 至 Azure blob。 
[使用 Pig 和 Hive 與資料處理站][use-pig-and-hive-with-data-factory] | 此發行項的逐步解說中，示範如何使用 **HDInsight 活動** 執行 **hive/pig** 指令碼來處理輸入的資料來產生輸出資料。
[教學課程： 移動及處理序使用資料處理站的記錄檔][adf-tutorial] | 這篇文章提供 **端對端逐步解說** ，示範如何實作 **真實世界的案例** 使用 Azure 資料 Factory 從記錄檔的資料轉換成見解。
[在資料處理站中使用自訂活動][use-custom-activities] | 本文章提供逐步解說中建立的逐步指示與 **自訂活動** 並在管線中使用它。 
[資料處理站的問題進行疑難排解][troubleshoot] | 本文將說明如何 **疑難排解** Azure 資料處理站所發出。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure 資料 Factory 指令程式參考][cmdlet-reference] | 此參考內容有詳細介紹所有 **資料 Factory cmdlet**。
[Azure 資料工廠開發人員參考資料][developer-reference] | 開發人員參考資料具有完整的參考內容 cmdlet、 JSON 指令碼、 函式等等... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: powershell-install-configure.md
[data-factory-create-sql-database]: sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir-->