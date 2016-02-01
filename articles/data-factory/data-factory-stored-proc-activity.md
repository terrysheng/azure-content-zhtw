<properties 
	pageTitle="SQL Server 預存程序活動" 
	description="深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 或 Azure SQL 資料倉儲中的預存程序。" 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# SQL Server 預存程序活動

您可以在 Data Factory [管線](data-factory-create-pipelines.md)中使用 SQL Server 預存程序活動，以叫用下列其中一個資料存放區中的預存程序。


- Azure SQL Database 
- Azure SQL 資料倉儲  
- 您的企業或 Azure VM 中的 SQL Server 資料庫。您必須在位於裝載資料庫的同一部電腦上或個別電腦上安裝資料管理閘道，以避免與資料庫競用資源。資料管理閘道器是一套透過安全且可管理的方式，將內部部署資料來源/Azure VM 中裝載的資料來源連結至雲端服務的軟體。如需資料管理閘道的詳細資訊，請參閱[在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文。 

本文是根據[資料轉換活動](data-factory-data-transformation-activities.md)一文，它呈現資料轉換和支援的轉換活動的一般概觀。

## 語法
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## 語法詳細資料

屬性 | 說明 | 必要
-------- | ----------- | --------
名稱 | 活動的名稱 | 是
說明 | 說明活動用途的文字 | 否
類型 | SqlServerStoredProcedure | 是
輸入 | 輸入資料集必須可供使用 (「就緒」狀態)，才能執行預存程序活動。將預存程序活動與其他活動鏈結時，此活動的輸入僅用於相依性管理。在預存程序中輸入資料集無法做為參數取用 | 否
輸出 | 預存程序活動產生的輸出資料集。請確定輸出資料表會使用連結的服務，該服務將 Azure SQL Database 或 Azure SQL 資料倉儲連結至 Data Factory。預存程序活動中的輸出，可以做為傳遞預存程序活動結果的方式，以進行後續的處理，及/或在此活動與其他活動鏈結時，用於相依性管理 | 是
storedProcedureName | 指定 Azure SQL Database 或 Azure SQL 資料倉儲中預存程序的名稱，由輸出資料表使用的連結的服務代表。 | 是
storedProcedureParameters | 指定預存程序參數的值 | 否

## 範例逐步解說

### 範例資料表與預存程序
1. 在您的 Azure SQL Database 中，使用 SQL Server Management Studio 或任何其他您很熟悉的工具，來建立下列**資料表**。Datetimestamp 資料行是產生對應識別碼的日期和時間。 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	Id 是可唯一識別的，而 datetimestamp 資料行是產生對應識別碼的日期和時間。
	![範例資料](./media/data-factory-stored-proc-activity/sample-data.png)

2. 建立下列**預存程序**，將資料插入 **sampletable**。

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT]參數 (在此範例中是 DateTime) 的**名稱**和**大小寫**必須與下列管線/活動 JSON 中指定的參數相符。在預存程序定義中，請務必使用 **@** 做為參數前置詞。
	
### 建立 Data Factory  
4. 登入 [Azure 入口網站](http://portal.azure.com/)之後，執行下列動作：
	1.	按一下左側功能表上的 [新增]。 
	2.	按一下 [建立] 刀鋒視窗中的 [資料分析]。
	3.	按一下 [資料分析] 刀鋒視窗上的 [Data Factory]。
4.	在 [新增 Data Factory] 刀鋒視窗中，輸入 **LogProcessingFactory** 做為 [名稱]。Azure Data Factory 名稱必須是全域唯一的。您必須在 Data Factory 的名稱前面加上您的名稱，才能成功建立 Factory。 
3.	如果您尚未建立任何資源群組，您必須建立資源群組。作法：
	1.	按一下 [資源群組名稱]。
	2.	在 [資源群組] 刀鋒視窗中，選取 [建立新的資源群組]。
	3.	在 [建立資源群組] 刀鋒視窗中，輸入 **ADFTutorialResourceGroup** 做為 [名稱]。
	4.	按一下 [確定]。
4.	選取資源群組之後，請確認您使用的是要在其中建立 Data Factory 的正確訂用帳戶。
5.	按一下 [新增 Data Factory] 刀鋒視窗上的 [建立]。
6.	您會看到 Data Factory 建立在 Azure 入口網站的 [開始面板] 中。在 Data Factory 成功建立後，您會看到 Data Factory 頁面，顯示 Data Factory 的內容。

### 建立 Azure SQL 連結服務  
建立 Data Factory 之後，您可以建立 Azure SQL 連結的服務，將 Azure SQL Database 連結到 Data Factory。這是包含 sampletable 資料表和 sp\_sample 預存程序的資料庫。

7.	在適用於 **SProcDF** 的 [DATA FACTORY] 刀鋒視窗中，按一下 [製作和部署]。這會啟動 Data Factory 編輯器。 
2.	在命令列上按一下 [新增資料儲存區]，然後選擇 [Azure SQL]。您應該會在編輯器中看到用來建立 Azure SQL 連結服務的 JSON 指令碼。 
4. 使用您的 Azure SQL Database 伺服器名稱來取代 **servername**、使用您在其中建立資料表和預存程序的資料庫來取代 **databasename**、使用有權存取資料庫的使用者帳戶來取代 **username@servername**，以及使用該使用者帳戶的密碼來取代 **password**。
5. 按一下命令列的 [部署]，部署連結服務。

### 建立輸出資料表
6. 在命令列上按一下 [新資料集]，然後選取 [Azure SQL]。
7. 將下列 JSON 指令碼複製/貼到 JSON 編輯器。

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. 按一下命令列上的 [部署] 來部署資料集。 

### 使用 SqlServerStoredProcedure 活動建立管線
現在，讓我們使用 SqlServerStoredProcedure 活動來建立管線。
 
9. 按一下命令列上的 **...(省略符號)**，然後按一下 [新管線]。 
9. 複製/貼上下列 JSON 程式碼片段。將 **StoredProcedureName** 設定為 **sp\_sample**。**DateTime** 參數的名稱和大小寫必須符合預存程序定義中參數的名稱和大小寫。  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. 按一下工具列上的 [部署] 來部署管線。  

### 監視管線

6. 按一下 **X** 以關閉 [Data Factory 編輯器] 刀鋒視窗、瀏覽回到 [Data Factory] 刀鋒視窗，然後按一下 [圖表]。
7. 在 [圖表檢視] 中，您會看到管線的概觀，以及在本教學課程中使用的資料集。 
8. 在 [圖表檢視] 中，按兩下 **sprocsampleout** 資料集。您將會看到就緒狀態的配量。由於配量是針對 2015/01/02 和 2015/01/03 之間的每一個小時所產生，因此，應該會有 24 個配量。 
10. 當配量處於**就緒**狀態時，請根據 Azure SQL Database 執行 **select * from sampledata** 查詢，以驗證預存程序已將資料插入資料表。

	![輸出資料](./media/data-factory-stored-proc-activity/output.png)

	如需監視 Azure Data Factory 管線的詳細資訊，請參閱[監視管線](data-factory-monitor-manage-pipelines.md) 。

> [AZURE.NOTE]在上述範例中，SprocActivitySample 沒有輸入。如果您想要鏈結此活動與活動上游 (例如預先處理)，可以使用上游活動的輸出做為此活動的輸入。在此情況下，上游活動完成且能使用上游活動的輸出 (處於就緒狀態) 之前，此活動不會執行。輸入無法直接做為預存程序活動的參數使用。

## 傳遞靜態值 
現在我們來考量在包含稱為「文件範例」的靜態值的資料表中，新增另一個名為「案例」的資料行。

![範例資料 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

若要這麼做，從預存程序活動傳遞「案例」參數和值。在上述範例中，typeproperties 區段如下：

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=AcomDC_0121_2016-->