<properties 
	pageTitle="從內部部署的 HDFS 移動資料 | Azure Data Factory" 
	description="深入了解如何使用 Azure Data Factory 從內部部署的 HDFS 移動資料" 
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

# 使用 Azure Data Factory 從內部部署的 HDFS 移動資料
本文將概述如何使用 Azure Data Factory 中的複製活動將資料從內部部署 HDFS 移動到另一個資料存放區。本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文呈現使用複製活動移動資料的一般概觀以及支援的資料存放區組合。

Data Factory 目前只支援將資料從內部部署 HDFS 移動到其他資料存放區，而不支援將資料從其他資料存放區移動到內部部署 HDFS。


## 啟用連線
Data Factory 服務支援使用資料管理閘道器連接至內部部署 HDFS。請參閱[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文來了解資料管理閘道器和設定閘道器的逐步指示。即使 HDFS 裝載於 Azure IaaS VM 中，您還是需要運用閘道器與其連接。

雖然您可以在與 HDFS 相同的內部部署機器或 Azure VM 上安裝閘道器，仍建議您在個別的機器或 Azure IaaS VM 上安裝，以避免資源爭用，並且具有更佳的效能。當您在不同機器上安裝閘道器時，機器應該能夠存取具有 HDFS 的機器。

## 範例：將資料從內部部署 HDFS 複製到 Azure Blob

此範例示範如何將資料從內部部署 HDFS 複製到 Azure Blob 儲存體。不過，您可以在 Azure Data Factory 中使用複製活動，**直接**將資料複製到[這裡](data-factory-data-movement-activities.md#supported-data-stores)所說的任何接收器。
 
此範例具有下列 Data Factory 實體：

1.	[OnPremisesHdfs](#hdfs-linked-service-properties) 類型的連結服務。
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 類型的連結服務。
3.	[FileShare](#hdfs-dataset-type-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4.	具有使用 [FileSystemSource](#hdfs-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從內部部署 HDFS 中的查詢結果複製到 Blob。範例後面的各節會說明這些範例中使用的 JSON 屬性。

在第一個步驟中，請根據[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文中的指示，設定資料管理閘道器。

**HDFS 連結服務** 此範例會使用 Windows 驗證。請參閱 [HDFS 連結服務](#hdfs-linked-service-properties)章節以了解您可以使用的各種不同類型的驗證。

	{
	    "name": "HDFSLinkedService",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
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

**HDFS 輸入資料集** 此資料集是指 HDFS 資料夾 DataTransfer/UnitTest/。管線會將其資料夾中的所有檔案複製到目的地。

設定 “external”: ”true” 和指定 externalData 原則 (選擇性) 即可通知 Data Factory 服務：這是 Data Factory 外部的資料表而且不是由 Data Factory 中的活動所產生。
	
	{
	    "name": "InputDataset",
	    "properties": {
	        "type": "FileShare",
	        "linkedServiceName": "HDFSLinkedService",
	        "typeProperties": {
	            "folderPath": "DataTransfer/UnitTest/"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}




**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。資料夾路徑會使用開始時間的年、月、日和小時部分。

	{
	    "name": "OutputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

此管線包含複製活動，該活動已設定為使用上述輸入和輸出資料集並排定為每小時執行。在管線 JSON 定義中，**source** 類型設為 **FileSystemSource**，而 **sink** 類型設為 **BlobSink**。針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。
	
	{
	    "name": "pipeline",
	    "properties":
	    {
	        "activities":
	        [
	            {
	                "name": "HdfsToBlobCopy",
	                "inputs": [ {"name": "InputDataset"} ],
	                "outputs": [ {"name": "OutputDataset"} ],
	                "type": "Copy",
	                "typeProperties":
	                {
	                    "source":
	                    {
	                        "type": "FileSystemSource"
	                    },
	                    "sink":
	                    {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy":
	                {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## HDFS 連結服務屬性

下表提供 HDFS 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- | 
| 類型 | 類型屬性必須設為：**Hdfs** | 是 | 
| Url | 到 HDFS 的 URL | 是 |
| encryptedCredential | 存取認證的 [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) 輸出。 | 否 |
| userName | Windows 驗證的使用者名稱。 | 是 (適用於 Windows 驗證)
| password | Windows 驗證的密碼。 | 是 (適用於 Windows 驗證)
| authenticationType | Windows 或匿名。 | 是 |
| gatewayName | Data Factory 服務應該用來連接到 HDFS 的閘道器名稱。 | 是 |   

如需為內部部署 HDFS 設定認證的詳細資料，請參閱[設定認證和安全性](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)。

### 使用匿名驗證

	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "userName": "hadoop",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### 使用 Windows 驗證
	
	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}
 


## HDFS 資料集類型屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **typeProperties** 區段都不同，可提供資料存放區中的資料位置資訊。**FileShare** 資料集類型 (包含 HDFS 資料集) 的 typeProperties 區段具有下列屬性。

屬性 | 說明 | 必要
-------- | ----------- | --------
folderPath | 資料夾的路徑。範例：myfolder<p>使用逸出字元 ‘ \\ ’ 做為字串中的特殊字元。例如：若為 folder\\subfolder，指定 folder\\subfolder，若為 d:\\samplefolder，指定 d:\\samplefolder。</p><p>您可以將其與 **partitionBy** 結合，使資料夾路徑以配量開始/結束日期時間為基礎。</p> | 是
fileName | 如果您想要資料表參考資料夾中的特定檔案，請指定 **folderPath** 中的檔案名稱。如果您未指定此屬性的任何值，資料表會指向資料夾中的所有檔案。<p>沒有為輸出資料集指定 fileName 時，所產生的檔案名稱會是下列格式：</p><p>Data.<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | 否
partitionedBy | partitionedBy 可以用來指定時間序列資料的動態 folderPath 和 filename。例如，folderPath 可針對每小時的資料進行參數化。 | 否
fileFilter | 指定要用來在 folderPath (而不是所有檔案) 中選取檔案子集的篩選器。<p>允許的值為：* (多個字元) 和 ? (單一字元)。</p><p>範例 1："fileFilter": "*.log"</p>範例 2："fileFilter": 2014-1-?.txt"</p><p>**請注意**：fileFilter 適用於輸入 FileShare 資料集</p> | 否
| compression | 指定此資料的壓縮類型和層級。支援的類型為：**GZip**、**Deflate** 和 **BZip2**，而支援的層級為：**最佳**和**最快**。請注意，目前不支援 **AvroFormat** 的資料壓縮設定。如需詳細資訊，請參閱[壓縮支援](#compression-support)一節。 | 否 |
| format | 支援兩種格式類型：**TextFormat**、**AvroFormat**。您需要將格式底下的 type 屬性設定為這些值。如果格式為 TextFormat，您可以指定格式的其他選擇性屬性。如需詳細資訊，請參閱以下[指定 TextFormat](#specifying-textformat) 一節。 | 否


> [AZURE.NOTE] 無法同時使用檔名和 fileFilter。


### 運用 partionedBy 屬性

如上所述，您可以利用 partitionedBy 指定時間序列資料的動態 folderPath 和 filename。您可以使用 Data Factory 巨集和系統變數以及可指出給定資料配量的邏輯時間週期的 SliceStart、SliceEnd 來這麼做。

請參閱「[建立資料集](data-factory-create-datasets.md)」、「[排程和執行](data-factory-scheduling-and-execution.md)」以及「[建立管線](data-factory-create-pipelines.md)」等文章，以進一步了解時間序列資料集、排程和配量。

#### 範例 1：

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

在上述範例中，{Slice} 會取代成 Data Factory 系統變數 SliceStart 的值 (使用指定的格式 (YYYYMMDDHH))。SliceStart 是指配量的開始時間。每個配量的 folderPath 都不同。例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### 範例 2：

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

在上述範例中，SliceStart 的年、月、日和時間會擷取到 folderPath 和 fileName 屬性所使用的個別變數。

### 指定 TextFormat

如果格式設為 **TextFormat**，您可以在 **Format** 區段中指定下列**選擇性**屬性。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| columnDelimiter | 在檔案中做為資料行分隔符號的字元。目前只允許一個字元。此標記是選擇性的。預設值是逗號 (,)。 | 否 |
| rowDelimiter | 在檔案中做為資料列分隔符號的字元。目前只允許一個字元。此標記是選擇性的。預設值是下列任一項：[“\\r\\n”, “\\r”,” \\n”]。 | 否 |
| escapeChar | <p>用來逸出內容中顯示之資料行分隔符號的特殊字元。此標記是選擇性的。沒有預設值。您為此屬性指定的字元不得超過一個。</p><p>例如，如果您以逗號 (,) 做為資料行分隔符號，但您想要在文字中使用逗號字元 (例如：“Hello, world”)，您可以定義 ‘$’ 做為逸出字元，並在來源中使用字串 “Hello$, world”。</p><p>請注意，您無法同時為資料表指定 escapeChar 和 quoteChar。</p> | 否 | 
| quoteChar | <p>用來引用字串值的特殊字元。引號字元內的資料行和資料列分隔符號會被視為字串值的一部分。此標記是選擇性的。沒有預設值。您為此屬性指定的字元不得超過一個。</p><p>例如，如果您以逗號 (,) 做為資料行分隔符號，但您想要在文字中使用逗號字元 (例如：<Hello  world>)，您可以定義 ‘"’ 做為引用字元，並在來源中使用字串 <"Hello, world">。這個屬性同時適用於輸入和輸出資料表。</p><p>請注意，您無法同時為資料表指定 escapeChar 和 quoteChar。</p> | 否 |
| nullValue | <p>用來代表 Blob 檔案內容中 null 值的字元。此標記是選擇性的。預設值為 “\\N”。</p><p>例如，根據上述範例，Blob 中的 “NaN” 會在複製到 SQL Server 時轉換成 null 值。</p> | 否 |
| encodingName | 指定編碼名稱。如需有效編碼名稱的清單，請參閱：[Encoding.EncodingName 屬性](https://msdn.microsoft.com/library/system.text.encoding.aspx)。例如：windows-1250 或 shift\_jis。預設值為 UTF-8。 | 否 | 

#### 範例
下列範例顯示 TextFormat 的一些格式屬性。

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

若要使用 escapeChar 而不是 quoteChar，請以下列內容取代具有 quoteChar 的行：

	"escapeChar": "$",

### 指定 AvroFormat
如果格式設為 AvroFormat，您不需要在 typeProperties 區段內的 Format 區段中指定任何屬性。範例：

	"format":
	{
	    "type": "AvroFormat",
	}

若要在 Hive 資料表中使用 Avro 格式，您可以參考 [Apache Hive 的教學課程](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)。

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## HDFS 複製活動類型屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。

另一方面，活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同，而在複製活動的案例中，可用的屬性會根據來源與接收的類型而有所不同。

在複製活動的案例中，如果來源的類型為 **FileSystemSource**，typeProperties 區段將提供下列屬性：

**FileSystemSource** 支援下列屬性：

| 屬性 | 說明 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 | True/False (預設值為 False)| 否 |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0224_2016-->