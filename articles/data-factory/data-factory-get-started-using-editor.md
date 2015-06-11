<properties 
	pageTitle="開始使用 Azure Data Factory" 
	description="此教學課程說明如何建立將資料從 Blob 複製到 Azure SQL 資料庫儲存個體的範例資料管線。" 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# 教學課程： 建立和監視使用資料 Factory 編輯器的資料處理站
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##本教學課程內容
本教學課程包含下列步驟：

步驟 | 說明
-----| -----------
[步驟 1： 建立 Azure 的資料處理站](#CreateDataFactory) | 在此步驟中，您將建立名為 Azure 資料 factory **ADFTutorialDataFactory**。  
[步驟 2： 建立連結的服務](#CreateLinkedServices) | 在此步驟中，您將建立兩個連結的服務： **StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 連結的 Azure 儲存體和 AzureSqlLinkedService 連結 ADFTutorialDataFactory Azure SQL 資料庫。管線的輸入的資料位於 Azure blob 儲存體中的 blob 容器，而且輸出資料會儲存在 Azure SQL 資料庫中的資料表。因此，您加入這兩個資料存放區做為連結的服務資料的處理站。      
[步驟 3： 建立輸入和輸出資料表](#CreateInputAndOutputDataSets) | 在上一個步驟中，您可以建立連結的服務參考到包含輸入/輸出資料的資料存放區。在此步驟中，您會定義兩個資料 factory 資料表- **EmpTableFromBlob** 和 **EmpSQLTable** -，代表儲存在資料存放區中的輸入/輸出資料。如 EmpTableFromBlob，您將會指定包含來源資料以及 EmpSQLTable blob 的 blob 容器，您將指定的 SQL 資料表，將輸出資料儲存。您也將指定的資料結構、 可用的資料等其他屬性... 
[步驟 4： 建立和執行管線](#CreateAndRunAPipeline) | 在此步驟中，您將建立名為管線 **ADFTutorialPipeline** ADFTutorialDataFactory 中。管線將會有 **複製活動** 複製輸入資料，從 Azure blob 輸出 Azure 的 SQL 資料表。
[步驟 5： 監視配量與管線](#MonitorDataSetsAndPipeline) | 在此步驟中，您將使用 Azure 預覽入口網站，以監視配量的輸入和輸出資料表。
 

## <a name="CreateDataFactory"></a>步驟 1： 建立 Azure 的資料處理站
在此步驟中，您可以使用 Azure 預覽入口網站建立名為 Azure 資料處理站 **ADFTutorialDataFactory**。

1.	登入之後 [Azure 預覽入口網站][azure-preview-portal], ，按一下 **新增** 從左下角中，選取 **資料分析** 中 **建立** blade 中，然後按一下 [ **資料 Factory** 中 **資料分析** 分頁。 

	![新-> DataFactory][image-data-factory-new-datafactory-menu]

6. 在 **新的資料處理站** 分頁：
	1. 輸入 **ADFTutorialDataFactory** 的 **名稱**。 
	
  		![新的資料處理站分頁][image-data-factory-getstarted-new-data-factory-blade]
	2. 按一下 **資源群組名稱** 並執行下列動作：
		1. 按一下 **建立新的資源群組**。
		2. 在 **建立資源群組** blade 中，輸入 **ADFTutorialResourceGroup** 的 **名稱** 資源群組，然後按一下 [ **確定**。 

			![建立資源群組][image-data-factory-create-resource-group]

		在本教學課程的步驟假設您使用的名稱： **ADFTutorialResourceGroup** 資源群組。若要深入了解資源群組，請參閱 [使用資源群組來管理您的 Azure 資源](resource-group-overview.md)。  
7. 在 **新的資料處理站** blade 中，請注意， **新增到儀表板** 已選取。
8. 按一下 **建立** 中 **新的資料處理站** 分頁。

	> [AZURE.NOTE]Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤： **資料 factory 名稱"ADFTutorialDataFactory"不是使用**, 、 變更資料處理站 (例如，yournameADFTutorialDataFactory) 的名稱，然後再試一次建立。執行本教學課程中的其餘步驟時使用此名稱來 ADFTutorialFactory 取代。請參閱 [資料 Factory-命名規則][data-factory-naming-rules] 命名規則的資料處理站成品的主題。
	 
	![資料處理站的名稱無法使用][image-data-factory-name-not-available]

9. 按一下 **通知** 在左方和尋找建立程序的通知中樞。按一下 **X** 關閉 **通知** 分頁已開啟。
10. 建立完成之後，您會看到 **資料 FACTORY** 分頁，如下所示。

    ![資料處理站首頁][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>步驟 2： 建立連結的服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。資料存放區可以是 Azure 儲存體、Azure SQL Database 或在內部部署 SQL Server 資料庫。

在此步驟中，您將建立兩個連結的服務： **StorageLinkedService** 和 **AzureSqlLinkedService**。StorageLinkedService 連結服務連結的 Azure 儲存體帳戶和 AzureSqlLinkedService 連結至 Azure SQL database **ADFTutorialDataFactory**。將資料從 StorageLinkedService 中的 blob 容器中 AzureSqlLinkedService 的 SQL 資料表到本教學課程稍後，您將建立管線。

### 建立 Azure 儲存體帳戶連結的服務
1.	在 **資料 FACTORY** blade 中，按一下 **作者和部署** 磚來啟動 **編輯器** 資料處理站。

	![[製作和部署] 磚][image-author-deploy-tile]

	> [AZURE.NOTE]請參閱 [資料 Factory 編輯器][data-factory-editor] 資料 Factory 編輯器的詳細概觀主題。
	 
5. 在 **編輯器**, ，按一下 **新的資料存放區** 按鈕工具列，然後選取 **Azure 儲存體** 從下拉式功能表。您應該會看到在右窗格中建立連結的 Azure 儲存體服務的 JSON 範本。

	![編輯器新增資料存放區] 按鈕][image-editor-newdatastore-button]
    
6. 取代 **accountname** 和 **accountkey** 帳戶名稱與 Azure 儲存體帳戶的帳戶金鑰值。

	![Blob 儲存體 JSON 編輯器][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需詳細資訊 JSON 內容。

6. 按一下 **部署** 部署 StorageLinkedService] 工具列上。確認您看到訊息 **連結服務建立成功** 標題列上。

	![部署編輯器 Blob 儲存體][image-editor-blob-storage-deploy]

### 建立連結的服務，Azure SQL database
1. 在 **資料 Factory 編輯器** , ，按一下 **新的資料存放區** 按鈕工具列，然後選取 **Azure SQL database** 從下拉式功能表。您應該會看到在右窗格中建立連結的 Azure SQL 服務的 JSON 範本。

	![Editr Azure SQL 設定][image-editor-azure-sql-settings]

2. 取代 **servername**, ，**databasename**, ，**username@servername**, ，和 **密碼** 與 Azure SQL server、 資料庫、 使用者帳戶和密碼的名稱。 
3. 按一下 **部署** 來建立及部署 AzureSqlLinkedService 工具列上。 
   

## <a name="CreateInputAndOutputDataSets"></a>步驟 3： 建立輸入和輸出資料表
在上一個步驟中，您可以建立連結的服務 **StorageLinkedService** 和 **AzureSqlLinkedService** 連結至資料處理站的 Azure 儲存體帳戶和 Azure SQL database： **ADFTutorialDataFactory**。在此步驟中，您會定義兩個資料 factory 資料表- **EmpTableFromBlob** 和 **EmpSQLTable** -，代表儲存在分別 StorageLinkedService 和 AzureSqlLinkedService 所參考的資料存放區中的輸入/輸出資料。如 EmpTableFromBlob，您將會指定包含來源資料以及 EmpSQLTable blob 的 blob 容器，您將指定的 SQL 資料表，將輸出資料儲存。

### 建立輸入資料表 
資料表是矩形的資料集，並具有的結構描述。在此步驟中，您將建立一個名為資料表 **EmpBlobTable** ，它會指向所代表的 Azure 儲存體中的 blob 容器 **StorageLinkedService** 連結服務。

1. 在 **編輯器** 資料處理站中，按一下 **新的資料集** 工具列上按一下按鈕 **Blob 資料表** 從下拉式功能表。 
2. 在右窗格中將 JSON 取代下列 JSON 片段： 

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
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     請注意：
	
	- 位置 **類型** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設為 **StorageLinkedService**。您在步驟 2 中建立此連結的服務。
	- **folderPath** 設為 **adftutorial** 容器。您也可以指定資料夾內的 Blob 的名稱。由於您未指定 Blob 的名稱，容器中所有 Blob 的資料都會被視為輸入資料。  
	- 格式 **類型** 設為 **TextFormat**
	- 有兩個欄位中的文字檔案 – **FirstName** 和 **LastName** – 以逗號字元分隔 (* * [columnDelimiter * *)	
	-  **可用性** 設為 **每小時** (* * 頻率 * * 會設定為 **小時** 和 **間隔** 設為 **1** )，因此資料處理站服務會尋找輸入資料每隔一小時在 blob 容器中的根資料夾中 (* * adftutorial * *) 指定。 
	

	如果您沒有指定 **fileName** 的 **輸入** **資料表**, ，所有檔案/blob 從輸入資料夾 (* * folderPath * *) 會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。請參閱範例檔案 [教學課程][adf-tutorial] 範例。
 
	如果您沒有指定 **fileName** 的 **輸出資料表**, ，則產生的檔案中的 **folderPath** 下列格式命名： 資料 & l t;Guid & g t;。txt (範例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt。)。

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

2. 按一下 **部署** 來建立和部署] 工具列上 **EmpTableFromBlob** 資料表。確認您看到 **資料表成功建立** 編輯器的標題列上的訊息。

### 建立輸出資料表
在這段的步驟，您將建立的輸出資料表中名為 **EmpSQLTable** 指向 SQL 資料表中 Azure SQL 資料庫，由 **AzureSqlLinkedService** 連結服務。

1. 在 **編輯器** 資料處理站中，按一下 **新的資料集** 工具列上按一下按鈕 **Azure SQL 資料表** 從下拉式功能表。 
2. 在右窗格中將 JSON 取代下列 JSON 片段：

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
            		"type": "AzureSqlTableLocation",
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
	* **linkedServiceName** 設為 **AzureSqlLinkedService** (您必須在步驟 2 中建立此連結的服務)。
	* **tablename** 設為 **emp**。
	* 有三個資料行 – **識別碼**, ，**FirstName**, ，和 **LastName** – emp 資料表在資料庫中，但識別碼是識別資料行，因此您必須只指定 **FirstName** 和 **LastName** 這裡。
	*  **可用性** 設為 **每小時** (* * 頻率 * * 設 **小時** 和 **間隔** 設 **1**)。資料處理站服務將會產生輸出的資料配量中的每個小時 **emp** Azure SQL database 中的資料表。


3. 按一下 **部署** 來建立和部署] 工具列上 **EmpSQLTable** 資料表。


## <a name="CreateAndRunAPipeline"></a>步驟 4： 建立和執行管線
在此步驟中，您會建立具有管線 **複製活動** 使用 **EmpTableFromBlob** 做為輸入和 **EmpSQLTable** 做為輸出。

1. 在 **編輯器** 資料處理站中，按一下 **新管線** 工具列上的按鈕。如果沒看到此按鈕，請按一下工具列 **...(省略符號)**。或者，您也可以在樹狀檢視中，以滑鼠右鍵按一下 [**管線**]，再按一下 [**新增管線**]。

	![編輯器新增管線] 按鈕][image-editor-newpipeline-button]
 
2. 在右窗格中將 JSON 取代下列 JSON 片段：

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	請注意：

	- 在 [活動] 區段中，則只有一個活動其 **類型** 設為 **CopyActivity**。
	- 輸入活動設定為 **EmpTableFromBlob** 和輸出活動設定為 **EmpSQLTable**。
	- 在 **轉換** ] 區段中， **BlobSource** 指定做為來源類型和 **SqlSink** 指定為接收型別。

	> [AZURE.NOTE]取代的值 **開始** 屬性與目前的日期和 **結束** 隔天的值。您可以指定只有日期部分，並略過的日期和時間的時間部分。例如，"2015年-02-03"，這相當於"2015年-02-03T00:00:00Z"同時開始和結束日期時間必須在 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如： 2014年-10-14T16:32:41Z。 **結束** 時間是選擇性的但是我們將在本教學課程中使用它。如果您沒有指定值 **結束** 屬性，它會計算為"* * 啟動 + 48 小時 * *"。若要無限期地執行管線，請指定 **9999-09-09** 做為值 **結束** 屬性。在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
	> [AZURE.NOTE]請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需詳細資訊 JSON 內容。

4. 按一下 **部署** 來建立和部署] 工具列上 **ADFTutorialPipeline**。確認您看到 **管線成功建立** 訊息。
5. 現在，關閉 **編輯器** 按一下刀鋒 **X**。按一下 **X** ] 以關閉工具列] 和 [樹狀結構檢視與 [ADFTutorialDataFactory 分頁。如果您看到 **將捨棄未儲存的編輯** 訊息中，按一下 **確定**。
6. 您應該要回到 **資料 FACTORY** 刀鋒伺服器 **ADFTutorialDataFactory**。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。
 
### 在圖表檢視中檢視資料 factory 
1. 在 **資料 FACTORY** blade 中，按一下 **圖表**。

	![資料處理站刀鋒圖磚][image-datafactoryblade-diagramtile]

2. 您應該會看到如下所示的圖表：

	![圖表檢視][image-data-factory-get-started-diagram-blade]

	您可以放大、 縮小、 放大到 100%，縮放以符合、 自動定位管線和資料表，以及顯示歷程資訊 (反白顯示選取之項目的上游和下游的項目)。您可以在 (輸入/輸出資料表或管線)，若要查看其屬性的物件上的雙精確度浮點數 blick。 
3. 以滑鼠右鍵按一下 **ADFTutorialPipeline** 圖表檢視中按一下 **開放的管線**。您應該會看到活動的輸入和輸出資料集以及管線中的活動。在本教學課程中，您只能有一個活動在管線中 (複製活動) 與 EmpTableBlob 做為輸入資料集和 EmpSQLTable 做為輸出資料集。   

	![開啟管線](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. 按一下 **資料 factory** 軌跡中若要回到 [圖表] 檢視的左上角。[圖表] 檢視會顯示所有的管線。在此範例中，您只建立了一個管線。
 

## <a name="MonitorDataSetsAndPipeline"></a>步驟 5： 監視的資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需有關使用適用於監視 cmdlet 的詳細資訊，請參閱 [監視和管理資料 Factory 使用 PowerShell Cmdlet][monitor-manage-using-powershell]。

1. 瀏覽至 [Azure 入口網站 (預覽)][azure-preview-portal] 如果您沒有開啟它。 
2. 如果針對分頁 **ADFTutorialDataFactory** 尚未開啟，即可開啟 **ADFTutorialDataFactory** 上 **儀表板**。 
3. 您應該會看到您在此分頁上建立之資料表和管線的計數和名稱。

	![含名稱的首頁][image-data-factory-get-started-home-page-pipeline-tables]

4. 現在，按一下 [ **資料集** 並排顯示。
5. 在 **資料集** blade 中，按一下 **EmpTableFromBlob**。這是輸入的資料表 **ADFTutorialPipeline**。

	![使用 EmpTableFromBlob 選取資料集][image-data-factory-get-started-datasets-emptable-selected]   
5. 請注意，資料配量，截至目前時間為止已產生，而且它們是 **準備** 因為 **emp.txt** 檔案存在的 blob 容器中所有的時間： **adftutorial\input**。確認任何配量顯示在 **最近無法配量** 底部區段。

	同時 **最近更新配量** 和 **最近無法配量** 清單會依照 **上次更新時間**。配量的更新時間是在下列情況中變更。
    

	-  您扇區的狀態手動更新，例如，使用 **組 AzureDataFactorySliceStatus** (或) 依序按一下 **執行** 上 **配量** 配量的分頁。
	-  配量因為執行的狀態變更 (例如啟動測試回合、 結束，而且無法執行、 測試回合結束，而且已成功，等等)。
 
	按一下標題的清單或 **...(橢圓形)** 若要查看較大配量的清單。按一下 **篩選** 篩選配量] 工具列上。
	
	若要檢視的資料配量，而是依照配量的開始/結束時間，請按一下 **(藉由配量的時間) 的資料配量** 並排顯示。

	![依時間配量的資料配量][DataSlicesBySliceTime]

6. 現在，在 **資料集** blade 中，按一下 **EmpSQLTable**。這是輸出資料表 **ADFTutorialPipeline**。

	![資料集分頁][image-data-factory-get-started-datasets-blade]



	 
6. 您應該會看到 **EmpSQLTable** 分頁，如下所示：

	![資料表索引標籤][image-data-factory-get-started-table-blade]
 
7. 請注意，資料配量，截至目前時間為止已產生，而且它們是 **準備**。任何配量顯示在 **問題配量** 底部區段。
8. 按一下 **...(省略符號)** 若要查看所有的扇區。

	![資料配量分頁][image-data-factory-get-started-dataslices-blade]

9. 按一下從清單中的任何資料配量上，您應該會看到 **資料配量** 分頁。

	![資料配量分頁][image-data-factory-get-started-dataslice-blade]
  
	如果配量不在 **準備** 狀態，您可以看到，未就緒並封鎖目前的配量，從執行中的上游配量 **上游未就緒的配量** 清單。

11. 在 **資料配量** blade 中，您應該會看到所有的活動會執行在下方的清單中。按一下 **活動執行** 查看 **活動執行詳細資料** 分頁。

	![活動執行詳細資料][image-data-factory-get-started-activity-run-details]

	
12. 按一下 **X** ，直到您回到首頁刀鋒伺服器關閉所有刀 **ADFTutorialDataFactory**。
14. (選擇性)按一下 **管線** 首頁上 **ADFTutorialDataFactory**, ，按一下 **ADFTutorialPipeline** 中 **管線** blade 中，並輸入資料表的鑽研 (* * 已使用 * *) 或輸出資料表 (* * Produced * *)。
15. 啟動 **SQL Server Management Studio**, ，連接到 Azure SQL 資料庫，並確認 [資料列會插入到 **emp** 資料庫資料表中的。

	![sql 查詢結果][image-data-factory-get-started-sql-query-results]


## 摘要 
在本教學課程中，您已建立要將資料從 Azure Blob 複製到 Azure SQL 資料庫的 Azure Data Factory。您可以使用 Azure 預覽入口網站建立的資料處理站、 連結的服務、 表格和管線。以下是您在本教學課程中執行的高階步驟：

1.	建立 Azure **資料 factory**。
2.	建立 **連結服務** 且連結資料儲存及計算 (稱為 **連結服務**) 之資料處理站。
3.	建立 **資料表** 描述輸入資料和管線的輸出資料。
4.	建立 **管線**。管線由一或多個活動所組成，會處理輸入並產生輸出。設定管線的作用中期間藉由指定 **開始** 時間和 **結束** 管線階段。作用中期間會定義將要產生資料配量的持續時間。 


> [AZURE.NOTE]如需支援的活動，請參閱 [管線和活動][msdn-activities] 主題，如需支援連結的服務，請參閱 [連結服務][msdn-linkedservices] MSDN Library 上的主題。
> 
> 若要執行本教學課程中使用 Azure PowerShell，請參閱 [建立和監視器使用 Azure PowerShell 的資料處理站][monitor-manage-using-powershell]。

## 後續步驟

文章 | 說明
------ | ---------------
[複製資料與 Azure 資料 Factory 複製活動][copy-activity] | 這篇文章提供詳細的描述 **複製活動** 您在本教學課程中使用。 
[讓您使用的內部資料的管線][use-onpremises-datasources] | 這篇文章已示範如何從其中複製資料的逐步解說 **在內部部署 SQL Server 資料庫** 至 Azure blob。 
[教學課程： 移動及處理序使用資料處理站的記錄檔][adf-tutorial] | 這篇文章提供 **端對端逐步解說** ，示範如何實作 **真實世界的案例** 使用 Azure 資料 Factory 從記錄檔的資料轉換成見解。
[資料處理站的問題進行疑難排解][troubleshoot] | 本文將說明如何 **疑難排解** Azure 資料處理站所發出。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure 資料工廠開發人員參考資料][developer-reference] | 開發人員參考資料具有完整的參考內容 cmdlet、 JSON 指令碼、 函式等等... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 