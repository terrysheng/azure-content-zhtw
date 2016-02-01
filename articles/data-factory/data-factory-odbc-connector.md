<properties 
	pageTitle="從 ODBC 資料存放區移動資料 | Azure Data Factory" 
	description="深入了解如何使用 Azure Data Factory 從 ODBC 資料存放區移動資料" 
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

# 使用 Azure Data Factory 從 ODBC 資料存放區移動資料
本文將概述如何使用 Azure Data Factory 中的複製活動將資料從內部部署 ODBC 資料存放區移動到另一個資料存放區。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data Factory 目前只支援將資料從內部部署 ODBC 資料存放區移動到其他資料存放區，而不支援將資料從其他資料存放區移動到內部部署 ODBC 資料存放區。

## 啟用連線
Data Factory 服務支援使用資料管理閘道器連接至內部部署 ODBC 來源。請參閱[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文來了解資料管理閘道器和設定閘道器的逐步指示。即使 ODBC 資料存放區裝載於 Azure IaaS VM 中，您還是需要運用閘道器與其連接。

雖然您可以在與 ODBC 資料存放區相同的內部部署機器或 Azure VM 上安裝閘道器，仍建議您在個別的機器或 Azure IaaS VM 上安裝，以避免資源爭用，並且具有更佳的效能。當您在不同機器上安裝閘道器時，機器應該能夠存取具有 ODBC 資料存放區的機器。

除了資料管理閘道器，您也需要在閘道器機器上的資料存放區安裝 ODBC 驅動程式。

## 範例：從 ODBC 資料存放區複製資料到 Azure Blob

下列範例顯示：

1.	[OnPremisesOdbc](#odbc-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
3.	[RelationalTable](#odbc-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [RelationalSource](#odbc-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從 ODBC 資料存放區中的查詢結果複製到 Blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

在第一個步驟中，請根據[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文中的指示，設定資料管理閘道器。

**ODBC 連結服務** 此範例會使用 Windows 驗證。請參閱 [ODBC 連結服務](#odbc-linked-service-properties)章節以了解您可以使用的各種不同類型的驗證。

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "connectionString": "Driver={SQL Server};Server=servername; Database=<database>;",
	            "userName": "<domain>\<user>",
	            "password": "<password>",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Azure 儲存體連結服務**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**ODBC 輸入資料集**

此範例假設您已在 ODBC 資料庫中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 和指定 externalData 原則即可通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}



**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。資料夾路徑會使用開始時間的年、月、日和小時部分。

	{
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**具有複製活動的管線**

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **RelationalSource**，而 **sink** 類型設為 **BlobSink**。針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。
	
	{
	    "name": "CopyODBCToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## ODBC 連結服務屬性

下表提供 ODBC 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- | 
| 類型 | 類型屬性必須設為：**OnPremisesOdbc** | 是 |
| connectionString | 連接字串的非存取認證部分，以及選擇性的加密認證。請參閱以下範例。 | 是
| 認證 | 以驅動程式特定屬性-值的格式指定的連接字串的存取認證部分，例如“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”。 | 否
| authenticationType | 用來連接到 ODBC 資料存放區的驗證類型。可能的值為：匿名、基本和 Windows。 | 是 | 
| username | 如果您使用基本或 Windows 驗證，請指定使用者名稱。 | 否 | 
| password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 | 否 | 
| gatewayName | Data Factory 服務應該用來連接到 ODBC 資料存放區的閘道器名稱。 | 是 |


如需為內部部署 ODBC 資料存放區設定認證的詳細資料，請參閱[設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)。

### 使用基本驗證

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### 使用基本驗證與加密認證
您可以使用 [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (1.0 版的 Azure PowerShell) Cmdlet 或 [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 或舊版的 Azure PowerShell) 來加密認證。

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### 使用 Windows 驗證

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "connectionString": "Driver={SQL Server};Server=servername; Database=TestDatabase;",
	            "userName": "<domain>\<user>",
	            "password": "<password>",
	            "gatewayName": "mygateway"
	        }
	    }
	} 


### 使用匿名驗證

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## ODBC 資料集類型屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。**RelationalTable** 資料集類型的 typeProperties 區段 (包含 ODBC 資料集) 具有下列屬性

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| tableName | ODBC 資料存放區中連結服務所參照的資料表名稱。 | 是 | 

## ODBC 複製活動類型屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動的案例中，如果來源類型為 **RelationalSource** (包含 ODBC)，則 typeProperties 區段可使用下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| query | 使用自訂查詢來讀取資料。 | SQL 查詢字串。例如：select * from MyTable。 | 是 | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### ODBC 的類型對應

如同[資料移動活動](data-factory-data-movement-activities.md)一文所述，複製活動會使用下列 2 個步驟的方法，執行自動類型轉換，將來源類型轉換成接收類型。

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 ODBC 資料存放區移動資料時，ODBC 資料類型會對應至 .NET 類型，如 [ODBC 資料類型對應](https://msdn.microsoft.com/library/cc668763.aspx)主題中所述。


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0121_2016-->