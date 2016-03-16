## 叫用 SQL 接收器的預存程序

將資料複製到 SQL Server 或 Azure SQL/SQL Server 資料庫時，可以設定指定的預存程序並搭配其他參數來叫用。

內建的複製機制無法使用時，可利用預存程序。必須在最後一次將來源資料插入目的地資料表前完成額外處理 (合併資料行、查閱其他值、插入至多個資料表等) 時，通常會利用此程序。

您可以叫用所選的預存程序。下列範例示範如何使用預存程序來執行簡單插入至資料庫中的資料表。

**輸出資料集**

在此範例中，type 設為：SqlServerTable。將它設定為 AzureSqlTable，以配合 Azure SQL 資料庫使用。

	{
	  "name": "SqlOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlLinkedService",
	    "typeProperties": {
	      "tableName": "Marketing"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}
	
在複製活動 JSON 中定義 SqlSink 區段如下。若要在插入資料時呼叫預存程序，則需要 SqlWriterStoredProcedureName 和 SqlWriterTableType 屬性。

	"sink":
	{
	    "type": "SqlSink",
	    "SqlWriterTableType": "MarketingType",
	    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
	    "storedProcedureParameters":
	            {
	                "stringData": 
	                {
	                    "value": "str1"     
	                }
	            }
	}

在資料庫中，使用與 SqlWriterStoredProcedureName 相同的名稱定義預存程序。它會處理來自指定來源的輸入資料，並插入至輸出資料表。請注意，預存程序的參數名稱應該與資料表 JSON 檔案中定義的 tableName 相同。

	CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
	AS
	BEGIN
	    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
	    INSERT [dbo].[Marketing](ProfileID, State)
	    SELECT * FROM @Marketing
	END

在資料庫中，使用與 SqlWriterTableType 相同的名稱定義資料表類型。請注意，資料表類型的結構描述應該與輸入資料所傳回的結構描述相同。

	CREATE TYPE [dbo].[MarketingType] AS TABLE(
	    [ProfileID] [varchar](256) NOT NULL,
	    [State] [varchar](256) NOT NULL,
	)

預存程序功能使用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

<!---HONumber=Oct15_HO3-->