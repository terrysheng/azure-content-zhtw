<properties 
	pageTitle="在 Azure 資料 Factory 中使用複製活動的範例" 
	description="提供使用 Azure 資料 factory 中複製活動範例。" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# 在 Azure 資料 Factory 中使用複製活動的範例
您可以在管線中使用 [**複製活動**]，以批次方式將資料從來源複製到接收器 (目的地)。本主題提供了幾個範例資料 Factory 管線中使用複製活動。如需「複製活動」的詳細概觀及其支援的核心案例，請參閱[使用 Azure Data Factory 複製資料][adf-copyactivity]。

## 將資料從內部部署 SQL Server 資料庫複製到 Azure blob
在此範例中會定義輸入資料表和輸出資料表，並且在管線內的「複製活動」中使用這些資料表，將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。

### 假設
這個範例假設您已經有下列 Azure 資料 Factory 成品：

* 名為 **ADF** 的資源群組。
* 名為 **CopyFactory** 的 Azure Data Factory。
* 名為資料管理閘道器 **mygateway** 建立且已上線。  

### 建立連結的來源在內部部署 SQL Server 資料庫服務
在此步驟中，您會建立名為連結的服務 **MyOnPremisesSQLDB** ，以指到內部部署 SQL Server 資料庫。

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

請注意：

- **類型** 設為 **OnPremisesSqlLinkedService**。
- **connectionString** 設為 SQL Server 資料庫的連接字串。 
- **gatewayName** 會設定為資料管理閘道器已在內部部署電腦上安裝和註冊 Azure 資料 Factory 服務入口網站的名稱。 

請參閱 [在內部部署 SQL 連結服務](https://msdn.microsoft.com/library/dn893523.aspx) 如需詳細資訊 JSON 定義的內部部署 SQL 的項目連結服務。
 
### 建立連結的目的地 Azure blob 服務
在此步驟中，您會建立名為連結的服務 **MyAzureStorage** 指向 Azure blob 儲存體。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

請注意：

- **類型** 設為 **AzureStorageLinkedService**。
- **connectionString** -指定帳戶名稱和帳戶的 Azure 儲存體的金鑰。

請參閱 [Azure 儲存體連結服務](https://msdn.microsoft.com/library/dn893522.aspx) 如需詳細資訊 JSON 項目定義 Azure 儲存體連結服務。

### 輸入資料表 JSON
下列 JSON 指令碼定義參照 SQL 資料表的輸入資料表：**MyTable** 是由 **MyOnPremisesSQLDB** 連結服務定義的內部部署 SQL Server 資料庫。請注意，**name** 是 Azure Data Factory 資料表的名稱，而 **tableName** 是 SQL Server 資料庫中的 SQL 資料表名稱。

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

請注意：

- **類型** 設為 **OnPremisesSqlServerTableLocation**。
- **tableName** 設為 **MyTable**, ，其中包含來源資料。 
- **linkedServiceName** 設為 **MyOnPremisesSQLDB**, 、 連結您建立的內部部署 SQL 資料庫服務。

請參閱 [在內部部署 SQL 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) 如需詳細資訊 JSON 項目定義會參考 SQL Server 資料表的資料處理站資料表。

### 輸出資料表 JSON
下列的 JSON 指令碼會定義輸出資料表： **MyAzureBlob**, ，這指的是 Azure blob： **MyBlob** blob 資料夾中： **MySubFolder** 在 blob 容器： **MyContainer**。
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

請注意：
 
- **類型** 設為 **AzureBlobLocation**。
- **folderPath** 設為 **MyContainer/MySubFolder**, ，其中包含保存複製的資料的 blob。 
- **fileName** 設為 **MyBlob**, ，會保留輸出資料的 blob。
- **linkedServiceName** 設為 **MyAzureStorge**, 、 連結您所建立的 Azure 儲存體服務。    

請參閱 [Azure blob 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) 如需詳細資訊 JSON 項目定義會參考 Azure 的 blob 資料 Factory 資料表。

### 管線 (具有複製活動) JSON
在此範例中，管線：**CopyActivityPipeline** 使用下列屬性定義：

- **type** 屬性設為 **CopyActivity**。
- **MyOnPremTable** 指定為輸入 (**inputs** 標記)。
- **MyAzureBlob** 指定為輸出 (**outputs** 標記)。
- [**轉換**] 區段包含兩個子區段：[**來源**] 和 [**接收器**]。來源的類型設為 **SqlSource**，而接收器的類型設為 **BlobSink**。**sqlReaderQuery** 定義要在來源上執行的轉換 (投射)。如需有關所有屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

請參閱 [管線 JSON 參考](https://msdn.microsoft.com/library/dn834988.aspx) 如需詳細資訊 JSON 元素來定義資料 Factory 管線和 [支援來源與接收](https://msdn.microsoft.com/library/dn894007.aspx) 屬性的 [sqlsource] (例如： **sqlReaderQuery **在範例中) 和 BlobSink。 


## 將資料從內部檔案系統複製到 Azure blob
您可以使用複製活動可將檔案從內部檔案系統 (Windows/Linux 網路共用或 Windows 本機主機) 複製到 Azure Blob。主機可以是 Windows 或 Linux 設定 Samba 含。資料管理閘道器應該安裝在可以連接到主機的 Windows 電腦上。

### 假設
此範例假設如下：

- **主機** -裝載檔案系統的伺服器名稱是： **\contoso**。
- **資料夾** -輸入的檔案所在的資料夾名稱是： **marketingcampaign\regionaldata\ {配量} 檔案資料分割] 資料夾中名為 {配量} 例如 2014121112 (2014 年、 12 個月、 11 天、 12 個小時)。 
### 建立內部檔案系統連結服務
下列範例 JSON 可以用來建立連結的服務名稱為 **FolderDataStore** 型別的 **OnPremisesFileSystemLinkedService**。

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]請記得使用逸出字元 ' 的主應用程式和 JSON 檔案中的資料夾名稱。針對 **\Contoso**, ，使用 **\Contoso**。

請參閱 [內部檔案系統連結服務](https://msdn.microsoft.com/library/dn930836.aspx) 如需詳細資訊 JSON 項目定義的內部檔案系統連結服務。

### 建立連結的目的地 Azure blob 服務
下列範例 JSON 可以用來建立連結的服務名稱為 **MyAzureStorage** 型別的 **AzureStorageLinkedSerivce**。

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

請參閱 [Azure 儲存體連結服務](https://msdn.microsoft.com/library/dn893522.aspx) 如需詳細資訊 JSON 項目定義 Azure 儲存體連結服務。

### 建立輸入的資料表
下列 JSON 指令碼定義的輸入的資料表，是指您稍早建立的內部檔案連結的系統服務。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

請參閱 [內部檔案系統位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) 如需詳細資訊 JSON 元素來定義資料 Factory 資料表參考的內部檔案系統。

### 建立輸出可以
下列的 JSON 指令碼會定義輸出資料表： **AzureBlobDest**, ，這指的是 Azure blob： **MyBlob** blob 資料夾中： **MySubFolder** 在 blob 容器： **MyContainer**。
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

請參閱 [Azure blob 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) 如需詳細資訊 JSON 項目定義會參考 Azure 的 blob 資料 Factory 資料表。

### 建立管線
下列管線 JSON 定義具有將資料從內部檔案系統複製到目的地 Azure blob 複製活動的管線。
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

在此範例中，管線將內容複製為二進位，無需任何剖析或執行任何轉換。請注意，您可以利用 **並行** 以平行方式複製檔案的配量。當您想要移動之配量已經發生在過去，這非常有用。

> [AZURE.NOTE]具有相同的主控件，透過使用不同的使用者帳戶的 UNC 路徑的並行複製活動可能會導致錯誤，例如 「 不允許多個連接至伺服器或共用的資源的相同使用者，並使用一個以上的使用者名稱 」。這是基於安全性理由作業系統的限制。排程具有不同的閘道器複製活動或安裝在主控件中的閘道，以及使用"localhost"local"，而不是 UNC 路徑。

請參閱 [管線 JSON 參考](https://msdn.microsoft.com/library/dn834988.aspx) 如需詳細資訊 JSON 元素來定義資料 Factory 管線和 [支援來源與接收](https://msdn.microsoft.com/library/dn894007.aspx) FileSystemSource 和 BlobSink 的屬性。

### 使用檔案系統資料表的其他案例

#### 複製特定資料夾下的所有檔案
請注意，只有 **folderPath** 範例 JSON 中指定。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### 複製特定資料夾下的所有 CSV 檔案
請注意， **取值** 設為 ***.csv**。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### 複製特定的檔案
請注意， **fileFiter** 設為特定的檔案： **201501.csv**。

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## 將資料從內部部署 Oracle 資料庫複製到 Azure blob
您可以使用複製活動可將檔案從內部部署在內部部署 Oracle 資料庫複製到 Azure Blob。

### 建立連結的服務，在內部部署 Oracle 資料庫
下列 JSON 可以用來建立連結的服務指向內部 Oracle 資料庫。請注意， **類型** 設為 **OnPremisesOracleLinkedService**。

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

請參閱 [內部 Oracle 連結服務](https://msdn.microsoft.com/library/dn948537.aspx) 如需詳細資訊 JSON 項目定義在內部部署 Oracle 連結服務。

### 建立連結的目的地 Azure blob 服務
下列範例 JSON 可以用來建立連結的服務名稱為 **MyAzureStorage** 型別的 **AzureStorageLinkedSerivce**。

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

請參閱 [Azure 儲存體連結服務](https://msdn.microsoft.com/library/dn893522.aspx) 如需詳細資訊 JSON 項目定義 Azure 儲存體連結服務。

### 建立輸入的資料表
下列範例 JSON 可用來建立一個 Azure 資料 Factory 資料表，參考在內部部署 Oracle 資料庫中的資料表。請注意， **位置類型** 設為 **OnPremisesOracleTableLocation**。

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

請參閱 [內部 Oracle 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 如需詳細資訊 JSON 元素來定義參考的資料表在內部部署 Oracle 資料庫中的資料處理站資料表。

### 建立輸出資料表
下列的 JSON 指令碼會定義輸出資料表： **MyAzureBlob**, ，這指的是 Azure blob： **MyBlob** blob 資料夾中： **MySubFolder** 在 blob 容器： **MyContainer**。
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

請參閱 [Azure blob 位置屬性](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) 如需詳細資訊 JSON 項目定義會參考 Azure 的 blob 資料 Factory 資料表。

### 建立管線
下列範例管線有將資料從 Oracle 資料庫資料表複製到 Azure 儲存體 blob 的複製活動。

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

請參閱 [管線 JSON 參考](https://msdn.microsoft.com/library/dn834988.aspx) 如需詳細資訊 JSON 元素來定義資料 Factory 管線和 [支援來源與接收](https://msdn.microsoft.com/library/dn894007.aspx) OracleSource 和 BlobSink 的屬性。

## 另請參閱

- [使用 Azure Data Factory 複製資料][adf-copyactivity]
- [複製活動 - JSON 指令碼參考](https://msdn.microsoft.com/library/dn835035.aspx)
- [影片：Azure Data Factory 複製活動簡介][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir-->