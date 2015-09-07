<properties 
	pageTitle="從 Oracle 來回移動資料 | Azure Data Factory"
	description="了解如何使用 Azure Data Factory 從內部部署 Oracle 資料庫來回移動資料。"
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
	ms.date="08/26/2015"
	ms.author="spelluru"/>

# 使用 Azure Data Factory 將資料移至內部部署 Oracle 

本文概述如何使用資料處理站複製活動將資料從 Oracle 移動到另一個資料存放區。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

## 範例：從 Oracle 複製資料到 Azure Blob

下列範例顯示：

1.	[OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
3.	[OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。 
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5.	具有使用 [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) 做為來源和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 做為接收之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將資料從內部部署 Oracle 資料庫中的資料表移動到 Blob。如需下列範例中所使用之各種屬性的詳細資訊，請參閱範例後章節中不同屬性的文件。

**Oracle 連結服務：**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Azure Blob 儲存體連結服務：**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Oracle 輸入資料集：**

此範例假設您已在 Oracle 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 和指定 externalData 原則即可通知 Azure Data Factory：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。資料夾路徑會使用開始時間的年、月、日和小時部分。
	
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

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **RelationalSource**，而 **sink** 類型設為 **BlobSink**。利用 **oracleReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \'{0:yyyy-MM-dd HH:mm}\' AND timestampcolumn < \'{1:yyyy-MM-dd HH:mm}\'', WindowStart, WindowEnd)"
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

## Oracle 連結服務屬性

下表提供 Oracle 連結服務專屬 JSON 元素的描述。

屬性 | 說明 | 必要
-------- | ----------- | --------
類型 | 類型屬性必須設為 **OnPremisesOracle** | 是
connectionString | 針對 connectionString 屬性指定連接到 Oracle 資料庫執行個體所需的資訊。 | 是 
gatewayName | 將用來連接到內部部署 Oracle 伺服器的閘道器名稱 | 是

如需設定內部部署 Oracle 資料來源認證的詳細資料，請參閱[設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)。
## Oracle 資料集類型屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (Oracle、Azure Blob、Azure 資料表等)。
 
每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。OracleTable 資料集類型的 typeProperties 區段具有下列屬性。

屬性 | 說明 | 必要
-------- | ----------- | --------
tableName | Oracle 資料庫中連結服務所參照的資料表名稱。 | 是

## Oracle 複製活動類型屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

**附註：**複製活動只會採用一個輸入，而且只會產生一個輸出。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動的案例中，如果來源的類型為 SqlSource，則 typeProperties 區段有下列可用屬性：

屬性 | 說明 |允許的值 | 必要
-------- | ----------- | ------------- | --------
oracleReaderQuery | 使用自訂查詢來讀取資料。 | SQL 查詢字串。 
例如：select * from MyTable<p>若未指定，執行的 SQL 陳述式即為 select * from MyTable</p> | 否

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Oracle 的類型對應

如同[資料移動活動](data-factory-data-movement-activities.md)一文所述，複製活動會使用下列 2 個步驟的方法，執行自動類型轉換，將來源類型轉換成接收類型：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Oracle 移動資料時，將使用下列從 Oracle 資料類型到 .NET 類型的對應，反之亦然。

Oracle 資料類型 | .NET Framework 資料類型
---------------- | ------------------------
BFILE | 位元組
BLOB | 位元組
CHAR | String
CLOB | String
日期 | DateTime
FLOAT | 十進位
INTEGER | 十進位
間隔年至月 | Int32
間隔日至秒鐘 | TimeSpan
長 | String
長 RAW | 位元組
NCHAR | String
NCLOB | String
數字 | 十進位
NVARCHAR2 | String
RAW | 位元組
ROWID | String
時間戳記 | DateTime
本地時區的時間戳記 | DateTime
時區的時間戳記 | DateTime
不帶正負號的整數 | Number
VARCHAR2 | String
XML | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO9-->