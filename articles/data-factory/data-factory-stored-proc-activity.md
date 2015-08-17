<properties 
	pageTitle="SQL Server 預存程序活動" 
	description="深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 中的預存程序。" 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# SQL Server 預存程序活動

您可以在 Data Factory [管線](data-factory-create-pipelines.md)中使用 SQL Server 預存程序活動，以叫用 **Azure SQL** Database 中的預存程序。這篇文章是根據[資料轉換活動](data-factory-data-transformation-activities.md)文章，它呈現資料轉換和支援的轉換活動的一般概觀。

## 語法
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": “”,
        	"storedProcedureParameters": “” 
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
輸入 | 輸入必須可供使用 (「就緒」狀態)，才能執行預存程序活動 | 否
輸出 | 預存程序活動產生的輸出。請確定輸出資料表會使用連結的服務，該服務連結 Azure SQL Database 與 Data Factory。 | 是
storedProcedureName | 指定 Azure SQL Database 中預存程序的名稱，由輸出資料表使用的連結的服務代表。 | 是
storedProcedureParameters | 指定預存程序參數的值 | 否

> [AZURE.NOTE]預存程序活動的輸入僅用於相依性管理以及將此活動與其他活動鏈結。在預存程序中輸入無法做為參數取用。
 

## 範例

我們來看一下您要在具有兩個資料行的 Azure SQL Database 中建立資料表的範例：

欄 | 類型
------ | ----
ID | uniqueidentifier
Datetime | 產生對應的識別碼的日期和時間

![範例資料](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

若要在 Data Factory 管線中執行此預存程序，您需要執行下列動作：

1.	建立[連結服務](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties)以註冊 Azure SQL Database (應該執行預存程序的位置) 的連接字串。
2.	建立指向 Azure SQL Database 中輸出資料表的[資料集](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties)。現在我們要呼叫此資料集 sprocsampleout。此資料集應該參考步驟 1 中的連結服務。 
3.	在 Azure SQL Database 中建立預存程序。
4.	使用 SqlServerStoredProcedure 活動建立以下[管線](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties)，以叫用 Azure SQL Database 中的預存程序。

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		             "name": "SprocActivitySample",
		             "type": " SqlServerStoredProcedure ",
		             "outputs": [ {"name": "sprocsampleout"} ],
		             "typeproperties":
		              {
		                "storedProcedureName": "sp_sample",
		        		"storedProcedureParameters": 
		        		{
		            	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        		}
				}
		            }
		          ]
		     }
		}
5.	部署[管線](data-factory-create-pipelines.md)。
6.	使用 Data Factory 監視和管理檢視[監視管線](data-factory-monitor-manage-pipelines.md)。

> [AZURE.NOTE]在上述範例中，SprocActivitySample 沒有輸入。如果您想要鏈結這個項目與活動上游，可以使用上游活動的輸出做為此活動的輸入。在此情況下，此活動不會執行，直到上游活動完成且輸出可用 (處於就緒狀態)。輸入無法直接做為預存程序活動的參數使用。
> 
> JSON 檔案中預存程序參數的名稱和大小寫 (大寫/小寫) 必須符合目標資料庫中預存程序參數的名稱。

現在我們來考量在包含稱為「文件範例」的靜態值的資料表中，新增另一個名為「案例」的資料行。

![範例資料 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

若要這麼做，從預存程序活動傳遞「案例」參數和值。在上述範例中，typeproperties 區段如下所示：

	"typeproperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO6-->