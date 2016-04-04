<properties 
	pageTitle="從 Azure Data Factory 叫用 MapReduce 程式" 
	description="了解如何從 Azure Data Factory，在 Azure HDInsight 叢集上執行 MapReduce 程式以處理資料。" 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# 從 Data Factory 叫用 MapReduce 程式
Data Factory [管線](data-factory-create-pipelines.md)中的 HDInsight MapReduce 活動會在[您自己的](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[隨選](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)的以 Windows/Linux 為基礎的 HDInsight 叢集上執行 MapReduce 程式。本文是根據[資料轉換活動](data-factory-data-transformation-activities.md)一文，它呈現資料轉換和支援的轉換活動的一般概觀。

## 簡介 
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文說明如何使用「HDInsight MapReduce 活動」。
 
若要了解如何使用 HDInsight 的 Pig 和 Hive 活動，在 Windows/Linux 的 HDInsight 叢集上從 Azure Data Factory 管線執行 Pig/Hive 指令碼，請參閱 [Pig](data-factory-pig-activity.md) 和 [Hive](data-factory-hive-activity.md) 文章。

## 「HDInsight MapReduce 活動」的 JSON 

在 HDInsight 活動的 JSON 定義中：
 
1. 將 **activity** 的 **type** 設為 **HDInsight**。
3. 指定 **className** 屬性的類別名稱。
4. 為 **jarFilePath** 屬性指定包含檔案名稱的 JAR 檔案路徑。
5. 為 **jarLinkedService** 屬性指定連結服務，此連結服務參考包含 JAR 檔案的 Azure Blob 儲存體。   
6. 在 **arguments** 區段中，為 MapReduce 程式指定所有引數。在執行階段，您會看到 MapReduce 架構的幾個額外的引數 (例如: mapreduce.job.tags)。若要區分在 MapReduce 引數中所使用您的引數，請考慮同時使用選項和值做為引數，如下列範例所示 (-s、--input、--output 等等...這些選項後面緊跟著其值)。

		{
		    "name": "MahoutMapReduceSamplePipeline",
		    "properties": {
		        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		        "activities": [
		            {
		                "type": "HDInsightMapReduce",
		                "typeProperties": {
		                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
		                    "jarLinkedService": "StorageLinkedService",
		                    "arguments": [
		                        "-s",
		                        "SIMILARITY_LOGLIKELIHOOD",
		                        "--input",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
		                        "--output",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
		                        "--maxSimilaritiesPerItem",
		                        "500",
		                        "--tempDir",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
		                    ]
		                },
		                "inputs": [
		                    {
		                        "name": "MahoutInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "MahoutOutput"
		                    }
		                ],
		                "policy": {
		                    "timeout": "01:00:00",
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "MahoutActivity",
		                "description": "Custom Map Reduce to generate Mahout result",
		                "linkedServiceName": "HDInsightLinkedService"
		            }
		        ],
		        "start": "2014-01-03T00:00:00Z",
		        "end": "2014-01-04T00:00:00Z",
		        "isPaused": false,
		        "hubName": "mrfactory_hub",
		        "pipelineMode": "Scheduled"
		    }
		}
	
	

您可以使用「HDInsight MapReduce 活動」，在 HDInsight 叢集上執行任何 MapReduce Jar 檔案。在下列管線的範例 JSON 定義中，已設定讓「HDInsight 活動」執行 Mahout JAR 檔案。

## GitHub 上的範例
您可以從 [GitHub 上的 Data Factory 範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample)下載使用「HDInsight MapReduce 活動」的範例。

## 執行字數統計程式
本範例中的管線會在 Azure HDInsight 叢集上執行字數統計 Map/Reduce 程式。

### 連結的服務
首先，建立連結的服務，將 Azure HDInsight 叢集使用的 Azure 儲存體連結到 Azure Data Factory。如果您複製/貼上下列程式碼，請記得使用 Azure 儲存體的名稱和金鑰來取代**帳戶名稱**和**帳戶金鑰**。

#### Azure 儲存體連結服務

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Azure HDInsight 連結服務
接著，建立連結的服務，將 Azure HDInsight 叢集連結到 Azure Data Factory。如果您複製/貼上下列程式碼，請使用您的 HDInsight 叢集的名稱來取代 **HDInsight 叢集名稱**，並變更使用者名稱和密碼值。

	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### 資料集

#### 輸出資料集
此範例中的管線不需要取得任何輸入。您必須指定「HDInsight MapReduce 活動」的輸出資料集。這只是驅動管線排程所需的空資料集。

	{
	    "name": "MROutput",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "WordCountOutput1.txt",
	            "folderPath": "example/data/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### 管線
此範例中的管線只含有一個類型為 HDInsightMapReduce 的活動。JSON 中的幾個重要屬性如下：

屬性 | 注意事項
:-------- | :-----
類型 | 類型必須設為 **HDInsightMapReduce**。 
className | 類別的名稱是：**wordcount**
jarFilePath | 包含上述類別之 Jar 檔案的路徑。如果您複製/貼上下列程式碼，請記得變更叢集的名稱。 
jarLinkedService | 包含 Jar 檔案的 Azure 儲存體連結服務。這是與 HDInsight 叢集相關聯的儲存體。 
引數 | 字數統計程式會採用輸入和輸出兩個引數。輸入檔為 davinci.txt 檔案。
frequency/interval | 這些屬性的值符合輸出資料集。 
linkedServiceName | 表示您先前建立的 HDInsight 連結服務。   

	{
	    "name": "MRSamplePipeline",
	    "properties": {
	        "description": "Sample Pipeline to Run the Word Count Program",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "wordcount",
	                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "/example/data/gutenberg/davinci.txt",
	                        "/example/data/WordCountOutput1"
	                    ]
	                },
	                "outputs": [
	                    {
	                        "name": "MROutput"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "MRActivity",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-03T00:00:00Z",
	        "end": "2014-01-04T00:00:00Z"
	    }
	}

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#monitor-pipelines
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Classic Portal]: http://portal.azure.com
 

<!---HONumber=AcomDC_0323_2016-->