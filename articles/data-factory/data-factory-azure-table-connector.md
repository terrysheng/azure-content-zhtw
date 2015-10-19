<properties 
	pageTitle="從 Azure 資料表來回移動資料 | Azure Data Factory" 
	description="了解如何使用 Azure Data Factory 從 Azure 資料表儲存體來回移動資料。" 
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
	ms.date="10/06/2015" 
	ms.author="spelluru"/>

# 使用 Azure Data Factory 從 Azure 資料表來回移動資料

本文概述如何在 Azure Data Factory 中使用複製活動，在 Azure 資料表與其他資料存放區之間移動資料。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

## 範例：從 Azure 資料表複製資料到 Azure Blob

下列範例顯示：

1.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務 (同時用於資料表和 Blob)。
2.	[AzureTable](#azure-table-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。 
3.	具有使用 [AzureTableSource](#azure-table-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。 

此範例會每小時將 Azure 資料表中屬於預設資料分割的資料複製到 Blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure 儲存體連結服務：**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure 資料表輸入資料集：**

此範例假設您已在 Azure 資料表中建立資料表 "MyTable"。
 
設定 “external”: ”true” 和指定 externalData 原則即可通知 Azure Data Factory：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

	{
	  "name": "AzureTableInput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
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

**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。資料夾路徑會使用開始時間的年、月、日和小時部分。

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **AzureTableSource**，而 **sink** 類型設為 **BlobSink**。使用 **AzureTableSourceQuery** 屬性指定的 SQL 查詢會每小時從預設分割中選取要複製的資料。

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
		    "end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
				{
	        		"name": "AzureTabletoBlob",
	        		"description": "copy activity",
	        		"type": "Copy",
	        		"inputs": [
	          			{
		            		"name": "AzureTableInput"
		        		}
	        		],
	        		"outputs": [
	          			{
	          	  			"name": "AzureBlobOutput"
		          		}
		        	],
		        	"typeProperties": {
		          		"source": {
		            		"type": "AzureTableSource",
				            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
    	    		},
		        	"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},				
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
				}
		     ]	
		}
	}

## 範例：從 Azure Blob 複製資料到 Azure 資料表

下列範例顯示：

1.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務 (同時用於資料表和 Blob)
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureTable](#azure-table-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。 
4.	具有使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 和 [AzureTableSink](#azure-table-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。 


此範例會每小時將屬於時間序列的資料從 Azure Blob 複製到 Azure 資料表資料庫中的資料表。範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure 儲存體 (同時適用於 Azure 資料表和 Blob) 連結服務：**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Blob 輸入資料集：**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。資料夾路徑使用開始時間的年、月、日部分，而檔案名稱使用開始時間的小時部分。“external”: “true” 設定會通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。
	
	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
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

**Azure 資料表輸出資料集：**

此範例會將資料複製到 Azure 資料表中名為 "MyTable" 的資料表。您應該在Azure 資料表中建立此資料表，其資料行的數目如您預期 Blob CSV 檔案要包含的數目。此資料表會每小時加入新的資料列。

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **BlobSource**，而 **sink** 類型設為 **AzureTableSink**。


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoTable",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureTableOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "AzureTableSink",
	            "writeBatchSize": 100,
	            "writeBatchTimeout": "01:00:00"
	          }
	        },
	        "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },						
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Azure 儲存體連結服務屬性

您可以使用 Azure 儲存體連結服務，將 Azure 儲存體帳戶連結至 Azure Data Factory。下表提供 Azure 儲存體連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| 類型 | type 屬性必須設為：AzureStorage | 是 |
| connectionString | 針對 connectionString 屬性指定連接到 Azure 儲存體所需的資訊。您可以從 Azure 入口網站取得 Azure 儲存體的 connectionString。 | 是 |

## Azure 資料表資料集類型屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。**AzureTable** 類型資料集的 **typeProperties** 區段具有下列屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| tableName | Azure 資料表資料庫執行個體中連結服務所參照的資料表名稱。 | 是

## Azure 資料表複製活動類型屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

**AzureTableSource** 在 typeProperties 區段中支援下列屬性：

屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | 使用自訂查詢來讀取資料。 | <p>Azure 資料表查詢字串。</p>**範例：****<br/> "azureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'" <br/><br/>"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00\_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00\_9999}\\')', SliceStart)" | No azureTableSourceIgnoreTableNotFound | 指出是否忍受資料表不存在的例外狀況。 | TRUE<br/>FALSE | No |

**AzureTableSink** 在 typeProperties 區段中支援下列屬性：


屬性 | 說明 | 允許的值 | 必要  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | 可供接收器使用的預設資料分割索引鍵值。 | 字串值。 | 否 
azureTablePartitionKeyName | 使用者指定的資料行名稱，其資料行值會做為資料分割索引鍵。如果未指定，則會使用 AzureTableDefaultPartitionKeyValue 做為資料分割索引鍵。 | 資料行名稱。 | 否 |
azureTableRowKeyName | 使用者指定的資料行名稱，其資料行值會做為資料列索引鍵。如果未指定，則會針對每個資料列使用 GUID。 | 資料行名稱。 | 否  
azureTableInsertType | 將資料插入 Azure 資料表的模式。 | 合併<br/>取代 | 否 
writeBatchSize | 在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中。 | 1 與 100 之間的整數 (單位 = 資料列計數) | 否 (預設值 = 100) 
writeBatchTimeout | 在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中 | (單位 = 時間範圍) 範例：“00:20:00” (20 分鐘) | 否 (預設為儲存體用戶端預設逾時值 90 秒)

### azureTablePartitionKeyName
您必須使用轉譯器 JSON 屬性將來源資料行對應至目的地資料行，才能使用目的地資料行做為 azureTablePartitionKeyName。

在下列範例中，來源資料行 DivisionID 會對應至目的地資料行：DivisionID。

	"translator": {
		"type": "TabularTranslator",
		"columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
	} 

EmpID 是指定為資料分割索引鍵。

	"sink": {
		"type": "AzureTableSink",
		"azureTablePartitionKeyName": "DivisionID",
		"writeBatchSize": 100,
		"writeBatchTimeout": "01:00:00"
	}


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Azure 資料表的類型對應

如同[資料移動活動](data-factory-data-movement-activities.md)一文所述，複製活動會使用下列 2 個步驟的方法，執行自動類型轉換，將來源類型轉換成接收類型。

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

在 Azure 資料表來回移動資料時，將使用下列 [Azure 資料表服務定義的對應](https://msdn.microsoft.com/library/azure/dd179338.aspx)：從 Azure 資料表 OData 類型到 .NET 類型，反之亦然。

| OData 資料類型 | .NET 類型 | 詳細資料 |
| --------------- | --------- | ------- |
| Edm.Binary | byte | 位元組陣列的大小最高為 64 KB。 |
| Edm.Boolean | 布林 | 布林值。 |
| Edm.DateTime | DateTime | 以國際標準時間 (UTC) 表示的 64 位元值。支援的 DateTime 範圍從西元 1601 年 1 月 1 日午夜 12:00 開始(C.E.), UTC.此範圍結束於 9999 年 12 月 31 日。 |
| Edm.Double | double | 64 位元的浮點值。 |
| Edm.Guid | Guid | 128 位元的全域唯一識別碼。 |
| Edm.Int32 | Int32 或 int | 32 位元的整數。 |
| Edm.Int64 | Int64 或 long | 64 位元的整數。 |
| Edm.String | String | UTF 16 編碼值。字串值的大小最高為 64 KB。 |

### 類型轉換範例

下列範例適用於使用類型轉換從 Azure Blob 複製資料到 Azure 資料表。

假設 Blob 資料集採用 CSV 格式而且包含 3 個資料行。其中一個是含有自訂日期時間格式 (使用法文縮寫星期幾名稱) 的日期時間資料行。

您將定義 Blob 來源資料集以及資料行的類型定義，如下所示。
	
	{
	    "name": " AzureBlobInput",
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
	            "interval": 1,
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

對於上述從 Azure 資料表 OData 類型到 .NET 類型的類型對應，您會在 Azure 資料表中定義具有下列結構描述的資料表。

**Azure 資料表結構描述：**

資料行名稱 | 類型
----------- | --------
userid | Edm.Int64
名稱 | Edm.String 
lastlogindate | Edm.DateTime

接下來，您將以下列方式定義 Azure 資料表資料集。您不需要指定含有類型資訊的「結構」區段，因為類型資訊已在基礎資料存放區中指定。

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

在此情況下，Data Factory 會自動進行類型轉換，包括將資料從 Blob 移到 Azure 資料表時，含有自訂日期時間格式 (使用 fr-fr 文化特性) 的 Datetime 欄位。



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Oct15_HO2-->