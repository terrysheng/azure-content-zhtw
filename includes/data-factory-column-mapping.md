## 使用轉譯程式規則的資料行對應
資料行對應可用於指定將來源資料表「 結構 」中指定資料行對應至接收器資料表 「 結構 」 中指定資料行的方式。複製活動的 **typeProperties** 區段中可使用 **ColumnMapping** 屬性。

資料行對應支援下列案例：

1.	來源資料表「結構」中的所有資料行皆對應至接收器資料表「結構」中的所有資料行。
2.	來源資料表「結構」中的資料行子集對應至接收器資料表「結構」中的所有資料行。

下列狀況為錯誤狀況，且將導致例外狀況：

1.	接收器資料表「結構」中的資料行數量多於或少於對應中所指定的數量。
2.	重複的對應。
3.	SQL 查詢結果中沒有對應中所指定的資料行名稱。

## 資料行對應範例
> [AZURE.NOTE]下面的範例是針對 Azure SQL 和 Azure Blob，但同樣也適用於任何支援矩形資料表的資料存放區。您必須調整下面範例中的資料集與連結服務定義，以指向相關資料來源中的資料。

### 範例 1 – 從 Azure SQL 到 Azure Blob 的資料行對應
在此範例中，輸入資料表有一個結構，且指向 Azure SQL 資料庫中的 SQL 資料表。

	{
	    "name": "AzureSQLInput",
	    "properties": {
	        "structure": 
	         [
	           { "name": "userid"},
	           { "name": "name"},
	           { "name": "group"}
	         ],
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": "1",
	        },
			"external": true,
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": "3"
	            }
			}
	    }
	}

在此範例中，輸出資料表有一個結構，且指向 Azure Blob 儲存體中的 Blob。

	{
	    "name": " AzureBlobOutput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "myuserid"},
	                { "name": "myname" },
	                { "name": "mygroup"}
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
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

活動的 JSON 如下所示。來源的資料行使用 **Translator** 屬性，對應至接收器中的資料行 (**columnMappings**)。

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": " AzureSQLInput"  } ],
	    "outputs":  [ { "name": " AzureBlobOutput" } ],
	    "typeProperties":    {
	        "source":
	        {
	            "type": "SqlSource"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "Translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
	        }
	    },
	   "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

**資料行對應流程：**

![資料行對應流程](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### 範例 2 – 利用 SQL 查詢從 Azure SQL 至 Azure Blob 的資料行對應
在此範例中，使用 SQL 查詢從 Azure SQL 擷取資料，而非只在 「 結構 」 區段中指定資料表名稱和資料行名稱。

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": " AzureSQLInput"  } ],
	    "outputs":  [ { "name": " AzureBlobOutput" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "Translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
	        }
	    },
	    "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

在此情況下，查詢結果會先對應至來源「 結構 」 中所指定的資料行。接下來，來源 「 結構 」 中的資料行會使用 columnMappings 中指定的規則對應至接收器 「 結構 」 中的資料行。假設該查詢傳回 5 個資料行、 2 個額外的資料行 ，之後才是來源 「 結構 」 中所指定的資料行。

**資料行對應流程**

![資料行對應流程 -2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)

<!---HONumber=August15_HO6-->