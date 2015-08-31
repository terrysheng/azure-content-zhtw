### 類型轉換範例
下列範例適用於使用類型轉換從 Blob 複製資料到 Azure SQL。

假設 Blob 資料集採用 CSV 格式而且包含 3 個資料行。其中一個是含有自訂日期時間格式 (使用法文縮寫星期幾名稱) 的日期時間資料行。

您將定義 Blob 來源資料集以及資料行的類型定義，如下所示。

	{
	    "name": "AzureBlobTypeSystemInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
			}
	    }
	}

對於上述從 SQL 類型到 .NET 類型的類型對應資料表，您會使用下列結構描述來定義 Azure SQL 資料表。

| 資料行名稱 | SQL 類型 |
| ----------- | -------- |
| userid | bigint |
| 名稱 | 文字 |
| lastlogindate | datetime |

接下來，您將以下列方式定義 Azure SQL 資料集。注意：您不需要指定具有類型資訊的「結構」區段，因為類型資訊已在基礎資料存放區中指定。

	{
	    "name": "AzureSQLOutput",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

在此情況下，Data Factory 會自動進行類型轉換，包括將資料從 Blob 移到 Azure SQL 時，含有自訂 datetime 格式 (使用 fr-fr 文化特性) 的 Datetime 欄位。

<!---HONumber=August15_HO8-->