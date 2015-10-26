<properties 
	pageTitle="使用 Azure Machine Learning 批次執行活動建立預測管線 | Microsoft Azure" 
	description="說明如何使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線。" 
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
	ms.date="08/24/2015" 
	ms.author="spelluru"/>

# 使用 Azure Machine Learning 批次執行活動建立預測管線   
## 概觀

> [AZURE.NOTE]請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)和[建置您的第一個管線](data-factory-build-your-first-pipeline.md)文章，快速地開始使用 Azure Data Factory 服務。

Azure Data Factory 可讓您輕鬆地建立管線，運用已發佈的 [Azure Machine Learning][azure-machine-learning] Web 服務進行預測性分析。您可以透過 Azure Data Factory 利用巨量資料管線 (例如 Pig 和 Hive)，處理您從各種資料來源擷取的資料，並使用 Azure Machine Learning Web 服務，以批次方式對資料進行預測。

您可以使用 Azure Data Factory 協調資料的移動和處理作業，然後使用 Azure Machine Learning 批次執行。若要達到此目的，您必須執行下列動作：

1. 建立 Azure Machine Learning 連結服務。您需要以下項目：
	1. 用於批次執行 API 的**要求 URI**。按一下 Web 服務頁面中的**批次執行**連結 (如下所示)，即可找到要求 URI。
	1. 已發佈 Azure Machine Learning Web 服務的 **API 金鑰**。按一下已發佈的 Web 服務，即可找到 API 金鑰。 
 2. 使用 **AzureMLBatchExecution** 活動。

	![機器學習服務儀表板](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![批次 URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## 案例：使用 Web 服務輸入/輸出 (參考 Azure Blob 儲存體中的資料) 的實驗
在此案例中，Azure Machine Learning Web 服務會使用 Azure Blob 儲存體中的檔案資料執行預測，並將預測結果儲存在 Blob 儲存體中。下列 JSON 使用 AzureMLBatchExecution 活動定義 Azure Data Factory 管線。此活動以 **DecisionTreeInputBlob** 資料集做為輸入，以 **DecisionTreeResultBlob** 資料集做為輸出。**DecisionTreeInputBlob** 使用了 **webServiceInput** JSON 屬性，傳遞至 Web 服務做為輸入，而 **DecisionTreeResultBlob** 則使用了 **webServiceOutputs** JSON 屬性，傳遞至 Web 服務做為輸出。只有當資料集是活動的輸入/輸出時，才能以 Web 服務的輸入和輸出加以傳遞。


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]只有當輸入及輸出屬於 AzureMLBatchExecution 活動時，才可以當做參數傳遞至 Web 服務。例如，在上面的 JSON 片段中，DecisionTreeInputBlob 是 AzureMLBatchExecution 活動的輸入，其透過 webServiceInput 參數傳遞至 Web 服務做為輸入。

### 範例

此範例使用 Azure 儲存體來存放輸入和輸出資料。

在瀏覽此範例之前，建議您先瀏覽[透過 Data Factory 建立第一個管線][adf-build-1st-pipeline]教學課程，並在此範例中使用 Data Factory 編輯器建立 Data Factory 成品 (連結服務、資料集、管線)。
 

1. 為您的 **Azure 儲存體**建立**連結服務**。如果輸入和輸出檔案會在不同的儲存體帳戶中，您就需要兩個連結服務。以下是 JSON 範例：

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. 建立**輸入** Azure Data Factory **資料集**。請注意，與其他 Data Factory 資料集不同的是，這些資料集必須同時包含 **folderPath** 和 **fileName** 值。您可以使用資料分割，讓每個批次執行 (每一個資料配量) 處理或產生唯一的輸入和輸出檔案。您將可能需要包含某個上游活動，以將輸入轉換成 CSV 檔案格式，並將它放在每個配量的儲存體帳戶中。在該情況下，您將不需包含下面範例所示的 **external** 及 **externalData** 設定，而您的 DecisionTreeInputBlob 將會是不同活動的輸出資料集。

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
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
	
	您的輸入 csv 檔案必須要有資料行標題資料列。如果使用 [複製活動] 建立 csv 或將其移至 Blob 儲存體，則接收屬性 **blobWriterAddHeader** 應該設為 **true**。例如：
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	如果 csv 檔案沒有標頭資料列，您可能會看到下列錯誤：**活動中的錯誤：讀取字串時發生錯誤。非預期的權杖：StartObject。路徑 ''、行 1、位置 1**。
3. 建立**輸出** Azure Data Factory **資料集**。此範例使用資料分割來為每一個配量執行建立一個唯一輸出路徑。如果沒有此唯一輸出路徑，活動就會覆寫檔案。

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. 建立此類型的**連結服務**：**AzureMLLinkedService**，並提供 API 金鑰和模型批次執行 URL。
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. 最後，撰寫一個包含 **AzureMLBatchExecution** 活動的管線。它會從輸入資料集取得輸入檔案的位置，並呼叫 Azure Machine Learning 批次執行 API，然後將批次執行輸出複製到輸出資料集中指定的 Blob。 

	> [AZURE.NOTE]AzureMLBatchExecution 活動可以有零個或多個輸入，以及一個或多個輸出。

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	**開始**和**結束**日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**結束**時間可選擇性使用。如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](https://msdn.microsoft.com/library/dn835050.aspx)。

	> [AZURE.NOTE]您可自行選擇是否指定 AzureMLBatchExecution 活動的輸入。

## 案例：使用讀取器/寫入器模組參考各種儲存體資料的實驗

建立 Azure ML 實驗時的另一個常見案例，是使用讀取器和寫入器模組。讀取器模組是用來將資料載入實驗，而寫入器模組則是用於儲存您的實驗資料。如需讀取器和寫入器模組的詳細資料，請參閱 MSDN Library 上的[讀取器](https://msdn.microsoft.com/library/azure/dn905997.aspx)和[寫入器](https://msdn.microsoft.com/library/azure/dn905984.aspx)主題。

使用讀取器和寫入器模組時，較好的做法是針對這些讀取器/寫入器模組的每一個屬性，使用 Web 服務參數。這些 Web 參數可讓您在執行階段設定值。例如，建立實驗時，您可以利用讀取器模組使用 Azure SQL Database：XXX.database.windows.net。部署 Web 服務之後，您需要啟用 Web 服務的取用者，藉此指定另一個稱為 YYY.database.windows.net 的 Azure SQL Server。您可以使用 Web 服務參數來設定此值。

> [AZURE.NOTE]Web 服務的輸入和輸出與 Web 服務參數不同。在第一個案例中，您已了解如何為 Azure ML Web 服務指定輸入和輸出。在此案例中，您將傳遞 Web 服務的參數，以對應至讀取器/寫入器模組的屬性。

讓我們看看使用 Web 服務參數的案例。您已部署的 Azure Machine Learning Web 服務，其使用讀取器模組所讀取的資料，是來自其中一個受支援的 Azure Machine Learning 資料來源 (例如 Azure SQL Database)。批次執行之後，會使用寫入器模組寫入結果 (Azure SQL Database)。實驗中沒有定義任何 Web 服務的輸入和輸出。在此情況下，我們建議您為讀取器和寫入器模組設定相關 Web 服務參數。如此一來，使用 AzureMLBatchExecution 活動時，便能將讀取器/寫入器模組一併設定完畢。如以下活動 JSON 所示，在 **globalParameters** 區段中指定 Web 服務參數。


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

您也可以使用 [Data Factory 函式](https://msdn.microsoft.com/library/dn835056.aspx)傳遞 Web 服務參數的值，如下列範例所示：

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Web 服務參數區分大小寫，因此，請確定您在活動 JSON 中所指定的名稱符合 Web 服務所公開的名稱。

### 使用讀取器模組讀取 Azure Blob 中多個檔案的資料
巨量資料管線 (Pig、Hive 等) 可以產生沒有副檔名的一個或多個輸出檔案。例如，當您指定外部 Hive 資料表時，外部 Hive 資料表的資料可以儲存在 Azure Blob 儲存體中，並命名為：000000\_0。您可以在實驗中使用讀取器模組讀取多個檔案，並將該模組用於預測。

在 Azure Machine Learning 實驗中使用讀取器模組時，您可以指定 Azure Blob 做為輸入。Azure Blob 儲存體中的檔案可以是在 HDInsight 上執行的 Pig 和 Hive 指令碼所產生的輸出檔 (例如 000000\_0)。如下所示，讀取器模組可讓您設定讀取器模組的**容器、目錄或 Blob 的路徑**屬性，以指向包含這些檔案的容器/資料夾，藉此讀取檔案 (沒有副檔名)。請注意，星號 (也就是 *) **會指定容器/資料夾中的所有檔案 (也就是 data/aggregateddata/year=2014/month-6/*)**，這些檔案將做為實驗中的讀取內容。

![Azure Blob 屬性](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### 範例 
#### AzureMLBatchExecution 活動使用 Web 服務參數的管線

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
在上述 JSON 範例中：

- 已部署的 Azure Machine Learning Web 服務使用讀取器和寫入器模組，讀取 Azure SQL Database 的資料，或將資料寫入其中。此 Web 服務會公開下列 4 個參數：資料庫伺服器名稱、資料庫名稱、伺服器使用者帳戶名稱和伺服器使用者帳戶密碼。  
- **開始**和**結束**日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**結束**時間可選擇性使用。如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](https://msdn.microsoft.com/library/dn835050.aspx)。
 

## 常見問題集

**問：**我目前是使用 AzureMLBatchScoring 活動。我應該改用 AzureMLBatchExecution 活動嗎?

**答：**是。如果您使用 AzureMLBatchScoring 活動與 Azure Machine Learning 整合，建議您使用最新的 AzureMLBatchExecution 活動。我們將淘汰 AzureMLBatchScoring 活動，日後的版本會將其移除。

2015 年 8 月發行的 Azure SDK 和 Azure PowerShell 中導入了 AzureMLBatchExecution 活動。

AzureMLBatchExecution 活動不需要輸入 (如果不需要輸入相依性的話)。它也可讓您明確宣告是否使用 Web 服務輸入和 Web 服務輸出，或兩者都不使用。如果您選擇使用 Web 服務輸入/輸出，即可指定要使用的相關 Azure Blob 資料集。此外，您還能清楚指定 Web 服務所提供的 Web 服務參數值。

如果您想繼續使用 AzureMLBatchScoring 活動，請參閱 [Azure ML 批次評分活動](data-factory-create-predictive-pipelines.md)一文，以了解詳細資料。


**問：**我擁有巨量資料管線所產生的多個檔案。我可以使用 AzureMLBatchExecution 活動來處理所有檔案嗎？

**答：**可以。如需詳細資料，請參閱**使用讀取器模組讀取 Azure Blob 中多個檔案的資料**一節。


## 另請參閱

- [Azure 部落格文章：開始使用 Azure Data Factory 和 Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=Oct15_HO3-->