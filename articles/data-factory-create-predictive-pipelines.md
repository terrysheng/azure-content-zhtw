<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Data Factory - 使用 Data Factory 和 Machine Learning 來建立預測管線 | Azure" description="描述如何使用 Azrue 資料處理站與 Azure Machine Learning 建立預測管線" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# 使用 Azure Data Factory 和 Azure Machine Learning 來建立預測管線 
您可以在 Azure Data Factory 管線內將已發佈的 [Azure Machine Learning][azure-machine-learning] 模型實作化。這些管線稱為預測管線。若要建立預測管線，您將需要：

-	已發佈之工作區模型的「API 金鑰」和批次評分 URL (請參閱下圖)。
-	存放要評分之輸入 CSV 檔案的 Azure Blob 儲存體。
-	將存放評分結果 CSV 檔案的 Azure Blob 儲存體。

	![Machine Learning Dashboard][machine-learning-dashboard]

	AzureMLLinkedService 之批次評分 URL 的取得方式如上圖所示，但要去掉 '**help**':  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

「預測管線」****包含下列部分：

-	輸入和輸出資料表
-	Azure 儲存體和 Azure ML 連結服務
-	具有「Azure ML 批次評分活動」的管線

## 範例



1. 為您的 Azure 儲存體建立連結的服務如果評分輸入和輸出檔案會在不同的儲存體帳戶中，您將需要兩個連結的服務。以下是 JSON 範例：

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 建立輸入和輸出 Azure Data Factory 資料表。請注意，不同於一些其他 Data Factory 資料表，這些必須同時包含 **folderPath** 和 **fileName** 值。您可以使用資料分割，讓每個批次執行 (每一個資料配量) 處理或產生唯一的輸入和輸出檔案。您將可能需要包含某個上游活動，以將輸入轉換成 CSV 檔案格式，並將它放在每個配量的儲存體帳戶中。在該情況下，您將不會包含下面範例所示的 "waitOnExternal" 設定，而您的 ScoringInputBlob 將會是不同「活動」的輸出資料表。

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. 此輸出範例使用資料分割來為每一個配量執行建立一個唯一輸出路徑。如果沒有此唯一輸出路徑，活動就會覆寫檔案。

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. 建立一個下列類型的連結服務：**AzureMLLinkedService**，並提供 API 金鑰和模型批次評分 URL。
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 最後，撰寫一個包含 **AzureMLBatchScoringActivity** 的管線。它將會從輸入資料表取得輸入檔案的位置、呼叫 AzureML 批次評分 API，然後將批次評分輸出複製到輸出資料表中指定的 Blob。不同於一些其他 Data Factory 活動，AzureMLBatchScoringActivity 只能有一個輸入和一個輸出資料表。

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## 另請參閱

文章 | 描述
------ | ---------------
[Azure Data Factory 簡介][adf-introduction] | 本文提供 Azure Data Factory 服務及它所支援之案例的概觀。
[開始使用 Azure Data Factory][adf-getstarted] | 本文提供基本教學課程，內含建立和監視範例 Data Factory 的逐步指示。
[讓您的管線能夠與內部部署資料搭配使用][use-onpremises-datasources] | 本文提供逐步解說，示範如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。
[使用 Pig 和 Hive 搭配 Data Factory][use-pig-and-hive-with-data-factory] | 本文提供逐步解說，示範如何使用「HDInsight 活動」來執行 hive/pig 指令碼，以處理輸入資料來產生輸出資料。
[教學課程：使用 Data Factory 移動及處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文提供逐步解說，內含建立自訂活動並在管線中使用此活動的逐步指示。
[Data Factory 問題疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 問題進行疑難排解。您可以透過產生一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說 
[Azure Data Factory 開發人員參考][developer-reference] |＜開發人員參考＞提供 Cmdlet、JSON 指令碼、函式等等的完整參考內容。 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/zh-tw/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
